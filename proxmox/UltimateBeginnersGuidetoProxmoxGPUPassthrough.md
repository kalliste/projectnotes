# The Ultimate Beginner's Guide to GPU Passthrough (Proxmox, Windows 10)

By `/u/cjalas` on Reddit, here is [the original Reddit post](https://www.reddit.com/r/homelab/comments/b5xpua/the_ultimate_beginners_guide_to_gpu_passthrough/)

# Ultimate Beginner's Guide to Proxmox GPU Passthrough

&gt;Welcome all, to the first installment of my **Idiot Friendly** tutorial series! I'll be guiding you through the process of configuring GPU Passthrough for your Proxmox Virtual Machine Guests. This guide is aimed at beginners to virtualization, particularly for Proxmox users. It is intended as an overall guide for passing through a GPU (or multiple GPUs) to your Virtual Machine(s). It is not intended as an all-exhaustive how-to guide; however, I will do my best to provide you with all the necessary resources and sources for the passthrough process, from start to finish. If something doesn't work properly, please check [/r/Proxmox](https://www.reddit.com/r/Proxmox), [/r/Homelab](https://www.reddit.com/r/Homelab), /r/VFIO, or [/r/linux4noobs](https://www.reddit.com/r/linux4noobs) for further assistance from the community.

# Before We Begin (Credits)

&gt;This guide wouldn't be possible without the fantastic online Proxmox community; both here on Reddit, on the official forums, as well as other individual user guides (which helped me along the way, in order to help you!). If I've missed a credit source, please let me know! Your work is appreciated.  
&gt;  
&gt;***Disclaimer:*** In no way, shape, or form does this guide claim to work for all instances of Proxmox/GPU configurations. Use at your own risk. I am not responsible if you blow up your server, your home, or yourself. Surgeon General Warning: do not operate this guide while under the influence of intoxicating substances. Do not let your cat operate this guide. You have been warned.

# Let's Get Started (Pre-configuration Checklist)

&gt;It's important to make note of all your hardware/software setup before we begin the GPU passthrough. For reference, I will list what I am using for hardware and software. This guide may or may not work the same on any given hardware/software configuration, and it is intended to help give you an overall understanding and basic setup of GPU passthrough for Proxmox *only*.  
&gt;  
&gt;***Your hardware should, at the very least, support: VT-d, interrupt mapping, and UEFI BIOS.***

&amp;#x200B;

**My Hardware Configuration:**

&gt;Motherboard: *Supermicro X9SCM-F (Rev 1.1 Board + Latest BIOS)*  
&gt;  
&gt;CPU: LGA1150 Socket, *Xeon E3-1220 (version 2)* ***^(1)***  
&gt;  
&gt;Memory: *16GB DDR3 (ECC, Unregistered)*  
&gt;  
&gt;GPU: 2x *GTX 1050 Ti 4gb, 2x GTX 1060 6gb* ***^(2)***

**My Software Configuration:**

&gt;[Latest Proxmox Build](https://www.proxmox.com/en/downloads/category/iso-images-pve) (5.3 as of this writing)  
&gt;  
&gt;Windows 10 LTSC Enterprise (Virtual Machine) ***^(3)***

**Notes:**

&gt;^(1)*On most Xeon E3 CPUs, IOMMU grouping is a mess, so some extra configuration is needed. More on this later.*  
&gt;  
&gt;^(2)*It is not recommended to use multiple GPUs of the same exact brand/model type. More on this later.*  
&gt;  
&gt;^(3)*Any Windows 10 installation ISO should work, however, try to stick to the latest available ISO from Microsoft.*

# Configuring Proxmox

&gt;This guide assumes you already have at the very least, installed Proxmox on your server and are able to login to the WebGUI and have access to the server node's Shell terminal. If you need help with installing base Proxmox, I highly recommend the [official "Getting Started" guide](https://www.proxmox.com/en/proxmox-ve/get-started) and their [official YouTube guides](https://www.youtube.com/user/ProxmoxVE).

**Step 1: Configuring the Grub**

&gt;Assuming you are using an Intel CPU, either SSH directly into your Proxmox server, or utilizing the noVNC Shell terminal under "Node", open up the ***/etc/default/grub*** file. I prefer to use ***nano***, but you can use whatever text editor you prefer.

    nano /etc/default/grub

&gt;Look for this line:

    GRUB_CMDLINE_LINUX_DEFAULT="quiet"

&gt;Then change it to look like this:  
&gt;  
&gt;**For Intel CPUs:**

    GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on"

&gt;**For AMD CPUs:**

    GRUB_CMDLINE_LINUX_DEFAULT="quiet amd_iommu=on"

&gt;**IMPORTANT ADDITIONAL COMMANDS**  
&gt;  
&gt;You might need to add additional commands to this line, if the passthrough ends up failing. For example, if you're using a similar CPU as I am (Xeon E3-12xx series), which has horrible IOMMU grouping capabilities, and/or you are trying to passthrough a single GPU.  
&gt;  
&gt;These additional commands essentially tell Proxmox not to utilize the GPUs present for itself, as well as helping to split each PCI device into its own IOMMU group. This is important because, if you try to use a GPU in say, IOMMU group 1, and group 1 also has your CPU grouped together for example, then your GPU passthrough will fail.  
&gt;  
&gt;Here are my grub command line settings:

    GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt pcie_acs_override=downstream,multifunction nofb nomodeset video=vesafb:off,efifb:off"

&gt;For more information on what these commands do and how they help:  
&gt;  
&gt;**A. Disabling the Framebuffer:** [video=vesafb:off,efifb:off](https://passthroughpo.st/explaining-csm-efifboff-setting-boot-gpu-manually/)  
&gt;  
&gt;**B. ACS Override for IOMMU groups:** [pcie\_acs\_override=downstream,multifunction](http://vfio.blogspot.com/2014/08/vfiovga-faq.html)  
&gt;  
&gt;When you finished editing ***/etc/default/grub*** run this command:

    update-grub

**Step 2: VFIO Modules**

&gt;You'll need to add a few VFIO modules to your Proxmox system. Again, using nano (or whatever), edit the file  ***/etc/modules***

    nano /etc/modules

&gt;Add the following (copy/paste) to the /etc/modules file:

    vfio
    vfio_iommu_type1
    vfio_pci
    vfio_virqfd

&gt;Then save and exit.

**Step 3:  IOMMU interrupt remapping**

&gt;I'm not going to get too much into this; all you really need to do is run the following commands in your Shell:

    echo "options vfio_iommu_type1 allow_unsafe_interrupts=1" &gt; /etc/modprobe.d/iommu_unsafe_interrupts.conf
    echo "options kvm ignore_msrs=1" &gt; /etc/modprobe.d/kvm.conf

**Step 4: Blacklisting Drivers**

&gt;We don't want the Proxmox host system utilizing our GPU(s), so we need to blacklist the drivers. Run these commands in your Shell:

    echo "blacklist radeon" &gt;&gt; /etc/modprobe.d/blacklist.conf
    echo "blacklist nouveau" &gt;&gt; /etc/modprobe.d/blacklist.conf
    echo "blacklist nvidia" &gt;&gt; /etc/modprobe.d/blacklist.conf

**Step 5: Adding GPU to VFIO**

&gt;Run this command:

    lspci -v

&gt;Your shell window should output a bunch of stuff. Look for the line(s) that show your video card. It'll look something like this:  
&gt;  
&gt;**01:00.0** VGA compatible controller: NVIDIA Corporation GP104 \[GeForce GTX 1070\] (rev a1) (prog-if 00 \[VGA controller\])  
&gt;  
&gt;**01:00.1** Audio device: NVIDIA Corporation GP104 High Definition Audio Controller (rev a1)  
&gt;  
&gt;Make note of the first set of numbers (e.g. **01:00.0** and **01:00.1**). We'll need them for the next step.  
&gt;  
&gt;Run the command below. Replace **01:00** with whatever number was next to your GPU when you ran the previous command:

    lspci -n -s 01:00

&gt;Doing this should output your GPU card's *Vendor IDs*, usually one ID for the GPU and one ID for the Audio bus. It'll look a little something like this:  
&gt;  
&gt;**01:00.0 0000: 10de:1b81 (rev a1)**  
&gt;  
&gt;**01:00.1 0000: 10de:10f0 (rev a1)**  
&gt;  
&gt;What we want to keep, are these vendor id codes: **10de:1b81** and **10de:10f0**.  
&gt;  
&gt;Now we add the GPU's vendor id's to the VFIO (***remember to replace the id's with your own!)***:

    echo "options vfio-pci ids=10de:1b81,10de:10f0 disable_vga=1"&gt; /etc/modprobe.d/vfio.conf

&gt;Finally, we run this command:

    update-initramfs -u

&gt;And restart:

    reset

&gt;Now your Proxmox host should be ready to passthrough GPUs!

# Configuring the VM (Windows 10)

&gt;Now comes the 'fun' part. It took me many, many different configuration attempts to get things *just right*. Hopefully my pain will be your gain, and help you get things done right, the first time around.

**Step 1:  Create a VM**

&gt;Making a Virtual Machine is pretty easy and self-explanatory, but if you are having issues, I suggest looking up the official Proxmox Wiki and How-To guides.  
&gt;  
&gt;For this guide, you'll need a Windows ISO for your Virtual Machine. Here's a handy guide on [how to download an ISO file directly into Proxmox](https://www.servethehome.com/directly-download-an-iso-to-proxmox-ve-for-vm-creation/). You'll want to copy ALL your .ISO files to the proper repository folder under Proxmox (including the VirtIO driver ISO file mentioned below).  
&gt;  
&gt;**Example Menu Screens**  
&gt;  
&gt;[General](https://imgur.com/XWWYHaO) =&gt; [OS](https://imgur.com/KI7jmlo) =&gt; [Hard disk](https://imgur.com/2yumM1F) =&gt; [CPU](https://imgur.com/Pp0IDdv) =&gt; [Memory](https://imgur.com/GUrojZH) =&gt; [Network](https://imgur.com/7A5557B) =&gt; [Confirm](https://imgur.com/B68E1tG)  
&gt;  
&gt;**IMPORTANT: DO NOT START YOUR VM (yet)**

**Step 1a (Optional, but RECOMMENDED):**  [Download VirtIO drivers](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/latest-virtio/virtio-win.iso)

&gt;If you follow this guide and are using VirtIO, then you'll need this ISO file of the VirtIO drivers to mount as a CD-ROM in order to install Windows 10 using VirtIO (SCSI).  
&gt;  
&gt;For the CD-Rom, it's fine if you use IDE or SATA. Make sure CD-ROM is selected as the primary boot device under the *Options* tab, when you're done creating the VM. Also, you'll want to make sure you select VirtIO (SCSI, *not* VirtIO Block) for your Hard disk and Network Adapter.

**Step 2: Enable OMVF (UEFI) for the VM**

&gt;Under your VM's ***Options*** Tab/Window, set the following up like so:

    Boot Order: CD-ROM, Disk (scsi0)
    SCSI Controller: VirtIO SCSI Single
    BIOS: OMVF (UEFI)

&gt;**Don't Forget:** When you change the BIOS from *SeaBIOS (Default)* to *OMVF (UEFI)*, Proxmox will say something about adding an EFI disk. So you'll go to your ***Hardware*** Tab/Window and do that. Add &gt; EFI Disk.

**Step 3: Edit the VM Config File**

&gt;Going back to the Shell window, we need to edit ***/etc/pve/qemu-server/&lt;vmid&gt;.conf***, where ***&lt;vmid&gt;*** is the VM ID Number you used during the VM creation (General Tab).

    nano /etc/pve/qemu-server/&lt;vmid&gt;.conf

&gt;In the editor, let's add these command lines (doesn't matter where you add them, so long as they are on new lines. Proxmox will move things around for you after you save):

    machine: q35
    cpu: host,hidden=1,flags=+pcid
    args: -cpu 'host,+kvm_pv_unhalt,+kvm_pv_eoi,hv_vendor_id=NV43FIX,kvm=off'

&gt;Save and exit the editor.

**Step 4: Add PCI Devices (Your GPU) to VM**

[Look at all those GPUs](https://preview.redd.it/nwqar7s93ko21.png?width=1159&amp;format=png&amp;auto=webp&amp;s=1b92d7c4b32fd5ff5cfdeffb6f7318054a430697)

&gt;Under the VM's ***Hardware*** Tab/Window, click on the *Add* button towards the top. Then under the drop-down menu, click *PCI Device*.  
&gt;  
&gt;[Look for your GPU in the list](https://imgur.com/PhjUxxA), and select it. On the PCI options screen, you should only need to [configure it like so](https://imgur.com/z4yVl5U):

    All Functions: YES
    Rom-Bar: YES
    Primary GPU: NO
    PCI-Express: YES (requires 'machine: q35' in vm config file)

&gt;[Here's an example image](https://imgur.com/L4ohm0e) of what your Hardware Tab/Window should look like when you're done creating the VM.

[Oopsies, make sure “All Functions” is CHECKED. ](https://preview.redd.it/7p4os0ie3ko21.png?width=1117&amp;format=png&amp;auto=webp&amp;s=7f31ce41d3e89d09f49ebbda975f9983c076bfda)

**Step 4a (Optional): ROM File Issues**

&gt;In the off chance that things don't work properly at the end, you MIGHT need to come back to this step and specify the ROM file for your GPU. This is a process unto itself, and requires some extra steps, as outlined below.  
&gt;  
&gt;**Step 4a1:**  
&gt;  
&gt;[Download](https://www.techpowerup.com/vgabios/) your GPU's ROM file  
&gt;  
&gt;OR  
&gt;  
&gt;Dump your GPU's ROM File:

    cd /sys/bus/pci/devices/0000:01:00.0/
    echo 1 &gt; rom
    cat rom &gt; /usr/share/kvm/&lt;GPURomFileName&gt;.bin
    echo 0 &gt; rom

&gt;**Alternative Methods to Dump ROM File:**  
&gt;  
&gt;a. [Using GPU-Z (recommended)](https://nvidia.custhelp.com/app/answers/detail/a_id/4188/~/extracting-the-geforce-video-bios-rom-file)  
&gt;  
&gt;b. [Using NVFlash](https://www.overclock.net/forum/69-nvidia/1523391-easy-nvflash-guide-pictures-gtx-970-980-a.html)  
&gt;  
&gt;**Step 4a2:** Copy the ROM file (if you downloaded it) to the ***/usr/share/kvm/*** directory.  
&gt;  
&gt;You can use SFTP for this, or directly through Windows' Command Prompt:

    scp /path/to/&lt;romfilename&gt;.rom myusername@proxmoxserveraddress:/usr/share/kvm/&lt;romfilename&gt;.rom

&gt;**Step 4a3:** Add the ROM file to your VM Config (EXAMPLE):

    hostpci0: 01:00,pcie=1,romfile=&lt;GTX1050ti&gt;.rom

&gt;**NVIDIA USERS**: If you're still experiencing issues, or the ROM file is causing issues on its own, you might need to patch the ROM file (particularly for NVIDIA cards). There's a great tool for patching GTX 10XX series cards here: [https://github.com/sk1080/nvidia-kvm-patcher](https://github.com/sk1080/nvidia-kvm-patcher) and here [https://github.com/Matoking/NVIDIA-vBIOS-VFIO-Patcher](https://github.com/Matoking/NVIDIA-vBIOS-VFIO-Patcher). It only works for 10XX series though. If you have something older, you'll have to patch the ROM file manually using a hex editor, which is beyond the scope of this tutorial guide.

[Example of the Hardware Tab\/Window, Before Windows 10 Installation.](https://preview.redd.it/gtoe8yos3ko21.png?width=1550&amp;format=png&amp;auto=webp&amp;s=ebc5c64f1d7c281351eceeb8f221abfe3f48bc91)

**Step 5: START THE VM!**

&gt;We're almost at the home stretch! Once you start your VM, open your noVNC / Shell Tab/Window (under the VM Tab), and you should see the Windows installer booting up. Let's quickly go through the process, since it can be easy to mess things up at this junction.

# Final Setup: Installing / Configuring Windows 10

[Copyright\(c\) Jon Spraggins \(https:\/\/jonspraggins.com\)](https://preview.redd.it/cc5qxxd34ko21.png?width=1027&amp;format=png&amp;auto=webp&amp;s=90d95104fe55718e113f7298e1bf001cd5b120bb)

&gt;If you followed the guide so far and are using VirtIO SCSI, you'll run into an issue during the Windows 10 installation, when it [tries to find your hard drive](http://www.monkeysandgorillas.com/wp-content/uploads/2014/03/MissingDriver.png). Don't worry!

[Copyright\(c\) Jon Spraggins \(https:\/\/jonspraggins.com\)](https://preview.redd.it/bosav5l74ko21.png?width=1026&amp;format=png&amp;auto=webp&amp;s=83fb4255d14147e57ae6292fa6b945651d2b1224)

**Step 1: VirtIO Driver Installation**

&gt;Simply go to your VM's ***Hardware*** Tab/Window (again), double click the CD-ROM drive file (it should currently have the Windows 10 ISO loaded), and switch the ***ISO image*** to the ***VirtIO ISO*** file.

[Copyright\(c\) Jon Spraggins \(https:\/\/jonspraggins.com\)](https://preview.redd.it/d9wn2kua4ko21.png?width=312&amp;format=png&amp;auto=webp&amp;s=9ee696bee033cdb8eeada5fd18a1c67dfe1dd343)

&gt;Tabbing back to your noVNC Shell window, click *Browse,* find your newly loaded VirtIO CD-ROM drive, and go to the ***vioscsi &gt; w10 &gt; amd64*** sub-directory. Click OK.  
&gt;  
&gt;Now the Windows installer should do its thing and load the Red Hat VirtIO SCSI driver for your hard drive. Before you start installing to the drive, go back again to the VirtIO CD-Rom, and also install your Network Adapter VirtIO drivers from ***NetKVM &gt; w10 &gt; amd64*** sub-directory.

[Copyright\(c\) Jon Spraggins \(https:\/\/jonspraggins.com\)](https://preview.redd.it/ejktrwke4ko21.png?width=1026&amp;format=png&amp;auto=webp&amp;s=dbcd631fd8245e78ec506a2dfbbe9f8ba5874b27)

&gt;**IMPORTANT #1: Don't forget to switch back the ISO file from the VirtIO ISO image to your Windows installer ISO image under the VM Hardware &gt; CD-Rom.**  
&gt;  
&gt;When you're done changing the CD-ROM drive back to your Windows installer ISO, go back to your Shell window and click *Refresh.* The installer should then have your VM's hard disk appear and have windows ready to be installed. Finish your Windows installation.  
&gt;  
&gt;**IMPORTANT #2: When Windows asks you to restart, right click your VM and hit 'Stop'. Then go to your VM's** ***Hardware*** **Tab/Window, and Unmount the Windows ISO from your CD-Rom drive. Now 'Start' your VM again.**

**Step 2: Enable Windows Remote Desktop**

&gt;If all went well, you should now be seeing your Windows 10 VM screen! It's important for us to enable some sort of remote desktop access, since we will be disabling Proxmox's noVNC / Shell access to the VM shortly. I prefer to use Windows' built-in Remote Desktop Client. [Here's a great, simple tutorial on enabling RDP access](https://www.groovypost.com/howto/setup-use-remote-desktop-windows-10/).  
&gt;  
&gt;**NOTE: While you're in the Windows VM, make sure to make note of your VM's Username, internal IP address and/or computer name.**

**Step 3: Disabling Proxmox noVNC / Shell Access**

&gt;To make sure everything is properly configured before we get the GPU drivers installed, we want to disable the built-in video display adapter that shows up in the Windows VM. To do this, we simply go to the VM's ***Hardware*** Tab/Window, and under the *Display* entry, we select *None (none)* from the drop-down list. Easy. Now 'Stop' and then 'Start' your Virtual Machine.  
&gt;  
&gt;**NOTE: If you are not able to (re)connect to your VM via Remote Desktop (using the given internal IP address or computer name / hostname), go back to the VM's** ***Hardware*** **Tab/Window, and under the PCI Device Settings for your GPU, checkmark** ***Primary GPU***\*\*. Save it, then 'Stop' and 'Start' your VM again.\*\*

**Step 4: Installing GPU Drivers**

&gt;At long last, we are almost done. The final step is to get your GPU's video card drivers installed. Since I'm using NVIDIA for this tutorial, we simply go to [http://nvidia.com](http://nvidia.com/) and browse for our specific GPU model's driver (in this case, GTX 10XX series). While doing this, I like to check Windows' ***Device Manager*** (under Control Panel) to see if there are any missing VirtIO drivers, and/or if the GPU is giving me a *Code 43 Error*. You'll most likely see the Code 43 error on your GPU, which is why we are installing the drivers. If you're missing any VirtIO (usually shows up as 'PCI Device' in Device Manager, with a yellow exclamation), just go back to your VM's ***Hardware*** Tab/Window, repeat the steps to mount your VirtIO ISO file on the CD-Rom drive, then point the Device Manager in Windows to the CD-Rom drive when it asks you to add/update drivers for the Unknown device.  
&gt;  
&gt;Sometimes just installing the plain NVIDIA drivers will throw an error (something about being unable to install the drivers). In this case, you'll have to install using NVIDIA's crappy ***GeForce Experience(tm)*** installer. It sucks because you have to create an account and all that, but your driver installation should work after that.

# Congratulations!

&gt;After a reboot or two, you should now be able to see NVIDIA Control Panel installed in your Windows VM, as well as Device Manager showing no Code 43 Errors on your GPU(s). Pat yourself on the back, do some jumping jacks, order a cake! You've done it!

[Multi-GPU Passthrough, it CAN be done!](https://preview.redd.it/uno4j21l4ko21.png?width=1954&amp;format=png&amp;auto=webp&amp;s=25835d12b7c1e196200cd748d824292b23e62ca1)

# Credits / Resources / Citations

1. [https://pve.proxmox.com/wiki/Pci\_passthrough](https://pve.proxmox.com/wiki/Pci_passthrough)
2. [https://forum.proxmox.com/threads/gpu-passthrough-tutorial-reference.34303/](https://forum.proxmox.com/threads/gpu-passthrough-tutorial-reference.34303/)
3. [https://vfio.blogspot.com/2014/08/iommu-groups-inside-and-out.html](https://vfio.blogspot.com/2014/08/iommu-groups-inside-and-out.html)
4. [https://forum.proxmox.com/threads/nvidia-single-gpu-passthrough-with-ryzen.38798/](https://forum.proxmox.com/threads/nvidia-single-gpu-passthrough-with-ryzen.38798/)
5. [https://heiko-sieger.info/iommu-groups-what-you-need-to-consider/](https://heiko-sieger.info/iommu-groups-what-you-need-to-consider/)
6. [https://heiko-sieger.info/running-windows-10-on-linux-using-kvm-with-vga-passthrough/](https://heiko-sieger.info/running-windows-10-on-linux-using-kvm-with-vga-passthrough/)
7. [http://vfio.blogspot.com/2014/08/vfiovga-faq.html](http://vfio.blogspot.com/2014/08/vfiovga-faq.html)
8. [https://passthroughpo.st/explaining-csm-efifboff-setting-boot-gpu-manually/](https://passthroughpo.st/explaining-csm-efifboff-setting-boot-gpu-manually/)
9. [http://bart.vanhauwaert.org/hints/installing-win10-on-KVM.html](http://bart.vanhauwaert.org/hints/installing-win10-on-KVM.html)
10. [https://jonspraggins.com/the-idiot-installs-windows-10-on-proxmox/](https://jonspraggins.com/the-idiot-installs-windows-10-on-proxmox/)
11. [https://pve.proxmox.com/wiki/Windows\_10\_guest\_best\_practices](https://pve.proxmox.com/wiki/Windows_10_guest_best_practices)
12. [https://docs.fedoraproject.org/en-US/quick-docs/creating-windows-virtual-machines-using-virtio-drivers/index.html](https://docs.fedoraproject.org/en-US/quick-docs/creating-windows-virtual-machines-using-virtio-drivers/index.html)
13. [https://nvidia.custhelp.com/app/answers/detail/a\_id/4188/\~/extracting-the-geforce-video-bios-rom-file](https://nvidia.custhelp.com/app/answers/detail/a_id/4188/~/extracting-the-geforce-video-bios-rom-file)
14. [https://www.overclock.net/forum/69-nvidia/1523391-easy-nvflash-guide-pictures-gtx-970-980-a.html](https://www.overclock.net/forum/69-nvidia/1523391-easy-nvflash-guide-pictures-gtx-970-980-a.html)
15. [https://medium.com/@konpat/kvm-gpu-pass-through-finding-the-right-bios-for-your-nvidia-pascal-gpu-dd97084b0313](https://medium.com/@konpat/kvm-gpu-pass-through-finding-the-right-bios-for-your-nvidia-pascal-gpu-dd97084b0313)
16. [https://www.groovypost.com/howto/setup-use-remote-desktop-windows-10/](https://www.groovypost.com/howto/setup-use-remote-desktop-windows-10/)

&gt;Thank you everyone!

[permalink](http://reddit.com/r/homelab/comments/b5xpua/the_ultimate_beginners_guide_to_gpu_passthrough/)
by *cjalas* (↑ 628/ ↓ 0)

## Comments

##### Damn it! I'm half way through writing my guide! 

The only things I have to add.

1. Don't forget the stub method. Some devices need to be stubbed at boot. Older GPU's especially. Also notable are Mellanox cards and SoundBlaster cards in my experience. Also cheap shitty old GPU's. 

2. DUAL GPU cards generally have a built in PLX bridge. Sometimes you have to passthrough the whole bridge. In the case of the R9 295x2, pass through the GPU with the outputs to your monitors (the one with the audio controller sub-device), install drivers, full hardware reboot, passthrough second GPU and bridge as 3 pcie devices, and reinstall drivers again. 

3. Nvidia cards are always better as your console cards. If you have an AMD GPU for your vm, buy a cheap Nvidia card from eBay and use for your console session. You will save yourself countless headaches. Nvidia cards work great for headless setups. 

I can elaborate on any of these points when I'm not on mobile. 

Thanks for your effort!  ⏤ by *gamebrigada* (↑ 27/ ↓ 0)
├─ When you are done just publish it... 2 well made guides can't hurt :) Good luck! ⏤ by *x_TheWolf_x* (↑ 9/ ↓ 0)
├─ Does Nvidia for console and Nvidia for pass through create problems? The blacklist Nvidia part? My mobo seems to want to use my x16 slot for monitor, but I want the x1 for my console.  ⏤ by *Failboat88* (↑ 1/ ↓ 0)
├── That is up to your Mobo. Look for a setting in bios such as "primary GPU". Some motherboards have a lot of features around it like mine and I can select any pcie slot for the primary GPU. Others unfortunately don't.  ⏤ by *gamebrigada* (↑ 1/ ↓ 0)
├─── I need an igpu to change it. I'm using the e3 1231v3 I think. The x1 can't be selected.

What about the blacklist part? Does that not impact the console GPU? ⏤ by *Failboat88* (↑ 1/ ↓ 0)
├──── Most server mobo's I've seen have really good options for primary GPU in bios. Some of them are hidden behind other features. iGPU is not required by any means. Update BIOS, they may have added the feature later. For example my HP Z840, they added bifurcation and GPU select into BIOS 2 years after release. 

&amp;#x200B;

No, console GPU is always the first one booted and the one displays the grub screen, it's selected before bootloader. If you simply blacklist or stub out the primary, you just make a headless system. I haven't seen any way to reroute console session to another GPU in linux, but I'm sure its possible.  ⏤ by *gamebrigada* (↑ 1/ ↓ 0)
├─ Please what u mean by the console session do u mean to buy a nvidia gpu card to let it for proxmox i mean for the host and the other card is for the vm ⏤ by *Big_Ad_9987* (↑ 1/ ↓ 0)
├── Yup. You can run headless but it solves problems if you have a cheap card as your Proxmox gpu. ⏤ by *gamebrigada* (↑ 1/ ↓ 0)
├─── Thank u very much ⏤ by *Big_Ad_9987* (↑ 1/ ↓ 0)
├─── I post this post in the proxmox reddit group but there is no response so can u please answer me if u don’t mind 

Hi guys as the title said i’am a noob one and i want to gather some information from u guys so is it proxmox without gpu passthrough is a type two hypervisor i mean if my vm don’t have a direct access to the gpu then it’s like i have a simple vm running with virtualbox or any other type 2 hypervisor and if i wrong so why we need gpu passthrough when by default my vm have a direct access to my gpu from the beginning i mean in that case or generally gpu passthrough what is his role and why we have it like an option and thanks guys ⏤ by *Big_Ad_9987* (↑ 1/ ↓ 0)
├──── Hypervisor type has nothing to do with how the GPU is configured. It has to do with whether the hypervisor runs on top of an operating system, or if it runs directly on the hardware. Proxmox uses KVM for virtualization which is technically in a league of its own. Since KVM is a kernel module in Linux, its technically a hosted hypervisor since it runs on top of Linux. However, since it is engrained at a low level into the OS, and mostly uses CPU hardware virtualization support (Intel VTx/AMD-V), it is also considered a type 1 hypervisor. Sometimes people refer to KVM  as type 1.5. Although KVM can also run as a type 2 hypervisor under some conditions.

You can run proxmox underneath your OS, and passthrough hardware to the VM that you need in your VM. For example your GPU. This does not change hypervisor type, but is somewhat complicated. When the original OS boots up, it boots up all of its connected hardware. The GPU boots into its own BIOS, and starts running its firmware awaiting instruction from the CPU. The driver then handles all of the communications to the GPU. Because of this, there are some complications with passing through hardware to a VM. Since a driver expects the hardware to be in a very specific state after bootup, and the state has been altered by the host operating system (proxmox), proxmox must get that hardware back into its just-booted state. A lot of hardware supports a soft-reboot that gets the hardware back into that state ready for driver initialization. However a lot of companies specifically disable this functionality to segregate datacenter hardware and consumer hardware. To overcome this issue, you can tell the proxmox kernel to ignore that hardware and not initialize it, which leaves it in unaltered state until the virtual machine boots and the driver within takes over.  This is known as blacklisting or stubbing.

If you do want to do GPU passthrough, I usually recommend having a cheap Nvidia card that you configure in bios if possible to be the primary GPU. This way, Proxmox will boot and take over that GPU. Then whatever GPU you want to use for passthrough is available to reboot back and forth without issue. If you don't do this, there are many cases where the GPU you are passing through cannot be reset without a hardware reset. Which becomes obnoxious having to reboot everything including other virtual machines if you are running any.

As far as why? The best reason I've heard is to give the middle finger to Microsoft who refuses to give us decent hardware passthrough support. It does exist, but its either behind hardware/license limitations, or simply too hard to implement and live with. A lot of people also want to run other operating systems either together on the same system or alternate between. Some flavor of KVM like Proxmox is a great, possibly the best way, to run hackintosh with an AMD GPU with no real limitations. One other reason that I mostly used this tech for is to run multiple gaming PC's in one. My girlfriend doesn't game much so I don't want to build her a gaming PC. However when she does, we play somewhat simple games together. So instead of building her a PC, I installed a second GPU in my PC and ran proxmox on it. Whenever she wanted to game, I simply decreased the CPU/Memory settings of the VM that has access to my GPU, and boot the VM with her GPU. This makes for a very seamless gaming experience for two people without having to have two completely separate computers. LinusTechTips did this and took it to the extreme for many systems. Its also just a really cool technology that is fairly well implemented across the board. I ran into some issues setting it all up, and we had some USB hardware malfunctions here and there, but for the most part it was flawless. Really goes to show how much spare CPU capacity your system has while gaming. The other reason I use Proxmox with hardware passthrough is to setup a hypervisor similar to HyperV, where I have a hypervisor underneath Windows on a workstation. This gives me a daily workstation, with lots of capacity to virtualize outside of the tech bounds of HyperV. ⏤ by *gamebrigada* (↑ 10/ ↓ 0)
├───── Really i appreciate it my friend u give many information u are the who i need thank u a lot ⏤ by *Big_Ad_9987* (↑ 1/ ↓ 0)
└────

##### Seems like this guide is a litte outdated/over complicated. Check the [Proxmox PCI(e) Passthrough in 2 minutes](https://www.reddit.com/r/Proxmox/comments/lcnn5w/proxmox_pcie_passthrough_in_2_minutes/) guide instead. ⏤ by *Cowderwelz* (↑ 9/ ↓ 0)
##### &gt;args: -cpu 'host,+kvm_pv_unhalt,+kvm_pv_eoi,hv_vendor_id=NV43FIX,kvm=off'

This bit is pointless as Proxmox already does this for us, the -cpu line generated by Proxmox looks like this just by setting "cpu: host":

&gt;-cpu 'host,+kvm_pv_unhalt,+kvm_pv_eoi,hv_vendor_id=proxmox,hv_spinlocks=0x1fff,hv_vapic,hv_time,hv_reset,hv_vpindex,hv_runtime,hv_relaxed,hv_synic,hv_stimer,hv_tlbflush,hv_ipi,kvm=off'

The critical bits are setting hv_vendor_id to literally anything but the default ("proxmox" works fine) and "kvm=off". You can see the command Proxmox generates with "qm showcmd 100" (where 100 is your VM ID). (i.e. Proxmox already hides itself from Nvidia out of the box)

The graphics card passthrough should have ",x-vga=on" added. ⏤ by *thenickdude* (↑ 8/ ↓ 0)
##### [deleted] ⏤ by *[deleted]* (↑ 3/ ↓ 0)
├─ I can only speak to Steam In Home Streaming. You’re looking at at least a 30% hit on performance, especially if using WiFi along with virtualization. The biggest thing is you’ll want to provide your VM with as much RAM and CPU resources as possible.  ⏤ by *cjalas* (↑ 6/ ↓ 0)
└────

##### Thank you so much for this. I finally got it working.

&amp;#x200B;

A further thing to add, I am using a Ryzen 7 CPU and for this to work I needed to allocate all cores to the VM, otherwise Windows would install extremely slowly and I would only get a blank screen after the install reboots. ⏤ by *grantonstar* (↑ 3/ ↓ 0)
##### A note for everyone... The linked VirtIO driver ISO file in this great HowTo is NOT the stable version it is the "latest" hence potentially buggy version. If you want the Stable ones use the below link. I know it's well behind the latest 171 version (as of today) but nothing after 141 has been listed as Stable. 

 [https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/virtio-win-0.1.141-1/virtio-win-0.1.141.iso](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/virtio-win-0.1.141-1/virtio-win-0.1.141.iso) ⏤ by *thesugarat* (↑ 3/ ↓ 0)
##### I followed this guide to the T. However, there was something missing. I thought that I needed to get the rom files for my GPUs, both NVIDIA an HP 3060 and an EVGA 3070.  However, I was wrong. It didn't help any. However, in many different ways this method "added" to this guide HELPED alot. I can do multi-passthrough. It feels good. Here is the addition that truly made it work 100%

&amp;#x200B;

\[GRUB\_CMDLINE\_LINUX\_DEFAULT="quiet intel\_iommu=on pcie\_acs\_override=downstream,multifunction video=efifb:off video=vesa:off vfio-pci.ids=10de:13bb,10de:0fb vfio\_iommu\_type1.allow\_unsafe\_interrupts=1 kvm.ignore\_msrs=1 modprobe.blacklist=radeon,nouveau,nvidia,nvidiafb,nvidia-gpu"\]

&amp;#x200B;

I just added everything to the grub file as a preboot method. Soon as I did that, I even got the Windows Installation screen already passed through to the monitors before adding the drivers. Now, I am blazing. I can actually do the gaming thing I wanted with the kids here and run multiple servers and passthrough what I need to. Credit goes to  [https://andrewferguson.net/](https://andrewferguson.net/) as this was the missing part of this whole thing that took me so many hours to find the solution. I have kept every note I created so I can do this in less than 20 minutes now and for each new computer I work with, if the card is compatable it will work every single time. WOW. I skipped the $1,500 consultation or being with a morgonaut because he only wants people to look at him, maybe talk to a few potential dates but he is a great showman he has great music but not straight to the point. Visit that website if you have done everything in this guide and are stuck ... or you can just use the line I searched so hard for.    AND.... EVERYTHING RUNS INSTANTLY, NOT starting the VMs and waiting 45 seconds and the instant responses tell me that everything is setup perfectly. ⏤ by *SeaArtichoke5382* (↑ 3/ ↓ 0)
├─ Did you keep the edits made to the files (e.g. the echo commands)?? Or did you only use the edit to the grub file? ⏤ by *LostITguy0_0* (↑ 1/ ↓ 0)
└────

##### How many GPUs do you have laying around man? ⏤ by *Probatus* (↑ 2/ ↓ 0)
├─ All of them ⏤ by *cjalas* (↑ 14/ ↓ 0)
└────

##### Man, I don't know how to thank you. 
I spent way too long trying to set up something on my hp DL 360 G7, and could never get it to work. 

Even after going the RMRR patch, passthrough was giving me significant issues. 

I think I was able to get the GPU to show up on the VM once, and then I kept getting errors on it after rebooting the VM. 

I'm not done yet - still installing Windows, but, this looks very promising! ⏤ by *ThinkOrdinary* (↑ 2/ ↓ 0)
##### Thnx, must try it with those pair of dusty quadros from drawer.... ⏤ by *pppjurac* (↑ 2/ ↓ 0)
##### I followed the guide and received a code 43, I found the command needs to be slightly modified from:  


video=vesafb:off,efifb:off -&gt; video=vesafb:off video=efifb:off  
After this was changed, code 43 was removed and plugging in HDMI into the video card displayed the VM output :) ⏤ by *LordCorgo* (↑ 2/ ↓ 0)
├─ worked for me thank you a lot ⏤ by *Orhayb* (↑ 2/ ↓ 0)
├─ Didn't work for me :( I got a RTX2060 and get error code 43 every time a second or two after the driver is installed. Spent a good few hours on this already. Anyone got any suggestions? ⏤ by *EngineWorried9767* (↑ 1/ ↓ 0)
├── &gt;machine: q35  
cpu: host,hidden=1,flags=+pcid  
args: -cpu 'host,+kvm\_pv\_unhalt,+kvm\_pv\_eoi,hv\_vendor\_id=NV43FIX,kvm=off'  


ever figured it out. Having that problem on my 2070 rn ⏤ by *Th3_LibtarD3stroyer* (↑ 1/ ↓ 0)
├─── I figured it out using this guide and several others.

Apparently Reddit has a character limit of 1000 for a post so here you go.

My re-write of this guide with everything I learned along the way:

[https://github.com/TechaNima/ProxBox/blob/main/Tutorial](https://github.com/TechaNima/ProxBox/blob/main/Tutorial) ⏤ by *TechaNima* (↑ 2/ ↓ 0)
├──── Thanks I'll try it out again tonight ⏤ by *Th3_LibtarD3stroyer* (↑ 1/ ↓ 0)
└────

##### Hey, my site is referenced here. Neat. ⏤ by *Brbcan* (↑ 2/ ↓ 0)
##### THANK YOU SOO MUCH FOR SHARING THIS! ⏤ by *SpectralSolid* (↑ 2/ ↓ 0)
##### This guide helped me understand linux more and I once, I learned how to go into the cfg files and look at them, everything is working now both graphics cards are passthru enabled, on and I even run additional VMs. This is so cool. I will never use my computer the same way again. Thank you guys ⏤ by *SeaArtichoke5382* (↑ 2/ ↓ 0)
##### Thank you VERY MUCH for this guy. You saved me from having to rely on Morganaut haha if I got that spelled right. I think they are not gonna help us without unwanted comments having to webcam with the person. It's so neutral, straight to the point, I knew that I could do it and I was determined that after I read this that I would be multi-passthru and although at first I failed, I tried it again, as again I was determined to succeed and after a 24 hour marathon on one binge and a few nights at it, I was able to do both GPUs and unfortunately I have not the onboard option but if I had, I could achieve all 3. I was blown away. I wish I had a threadripper but I do have a Ryzen 9 with 24 cpus a 12-core. I am satisfied knowing that it can be done. It has been an easy road thanks to you guys. Please, let me know if anything, that I can do to promote you guys. I can't believe that I almost considered paying a somebody to "show me the "new way" and it isn't new at all. LOL THANK YOU GUYS ⏤ by *DexterDJ2* (↑ 2/ ↓ 0)
##### I've gone through just about every guide and they all seem to be missing somthing specific to my setup. I finally found this link and I now have:  


Proxmox &gt; LXC &gt; Docker (Plex) with GPU transcoding.   


https://jocke.no/2022/02/23/plex-gpu-transcoding-in-docker-on-lxc-on-proxmox/ ⏤ by *procheeseburger* (↑ 2/ ↓ 0)
##### Hey all - is there an updated guide for 2023? ⏤ by *ComfySofa69* (↑ 2/ ↓ 0)
├─ I JUST got it working on a 4090 tonight. If you are doing nvidia I can try and help out some? ⏤ by *RiffyDivine2* (↑ 1/ ↓ 0)
├── Hi there....yeah could probably do with some help.....ive been posting asking but nothing back yet...ive got an A2000 12gb im using...ive not got as far as the vgpu splitting stuff yet and in all fairness ive got it working but, i want to be able to get to the VM from out on the net....so ive got vnc installed (registered) but, i cant change the resolution...im fixed at 1200x800 no matter what...for a couple of reasons...1. i think its tied to the console (same res) and b. in the device manager theres just the a2000 and the default microsoft adapter...normally vnc has its own driver in there....i could use rdp to get to it but VNC is a little safer as its encrpyted. Cheers. ⏤ by *ComfySofa69* (↑ 1/ ↓ 0)
└────

##### please add `echo "blacklist amdgpu" &gt;&gt; /etc/modprobe.d/blacklist.conf` to **Step 4**

On modern kernels (5.15+) `GRUB_CMDLINE_LINUX_DEFAULT="quiet initcall_blacklist=sysfb_init nomodeset video=vesafb:off video=efifb:off video=simplefb:off"` is sufficient

and `echo "options vfio_iommu_type1 allow_unsafe_interrupts=1" &gt; /etc/modprobe.d/iommu_unsafe_interrupts.conf` should only be used as a last resort, not a default. Look in `dmesg` for :      `No interrupt remapping support. Use the module param "allow_unsafe_interrupts" to enable VFIO IOMMU support on this platform`

see [https://vfio.blogspot.com/2014/08/vfiovga-faq.html](https://vfio.blogspot.com/2014/08/vfiovga-faq.html) question 8 ⏤ by *brb78* (↑ 2/ ↓ 0)
##### I may have spoke too soon.

I've dont seem to have a "none" option under display on the hardware tab.
I'm now stuck at a "start boot option" screen on proxmox.

 ⏤ by *ThinkOrdinary* (↑ 1/ ↓ 0)
├─ It should be the very last option in the drop down menu. Are you on the latest Proxmox?

If not, you can always modify the VM's .conf file.

Under **Datacenter &gt; Nodes &gt; pve** (or whatever your name is) &gt; ***Shell***

&amp;#x200B;

    nano /etc/pve/qemu-server/&lt;vmid&gt;.conf

Where ***&lt;vmid&gt;*** is your VM's number (usually starts with *100*). Hit enter, then look down the file and add to a new line:

    vga: none

Ctrl+X (if using *nano*), it'll ask if you want to overwrite the file buffer, type Y. Then hit enter. ⏤ by *cjalas* (↑ 1/ ↓ 0)
├──  

yeah, i'm using 5.3-5, this is all i can find.

https://imgur.com/a/CSutdlY

I'll try with the command line. ⏤ by *ThinkOrdinary* (↑ 1/ ↓ 0)
├── Okay, thanks again for the guide  - 

I cant seem to disable to display from the menu, or the config file. 

Even setting it with the config file lets me still use noVNC. ⏤ by *ThinkOrdinary* (↑ 1/ ↓ 0)
├─── Have you tried going into the VM via Remote Desktop anyways? Mine still worked (somehow) by unchecking "primary GPU" for the pci settings on the video card, and I was able to rdp in and it showed a default vga display driver as well as my nvidia gtx card inside the windows VM device manager.  ⏤ by *cjalas* (↑ 1/ ↓ 0)
├──── Okay, I just got it working and booting consistently.

I see the GPU under device manager, but it has the error 43, even after installing the drivers.

Any tips? ⏤ by *ThinkOrdinary* (↑ 1/ ↓ 0)
├───── Did you install the latest drivers ?

Also make sure your display is set to none. 

And make sure you've set the "args" settings in your VMs config file.  ⏤ by *cjalas* (↑ 1/ ↓ 0)
├────── i've tried the latest drivers, currently trying to patch the rom.

i get this error when setting vga to none.

&gt;root@pve:/etc/pve/qemu-server# qm start 110
vm 110 - unable to parse value of 'vga' - format error
type: value 'none' does not have a value in the enumeration 'cirrus, qxl, qxl2, qxl3, qxl4, serial0, serial1, serial2, serial3, std, virtio, vmware'
vm 110 - unable to parse value of 'vga' - format error
type: value 'none' does not have a value in the enumeration 'cirrus, qxl, qxl2, qxl3, qxl4, serial0, serial1, serial2, serial3, std, virtio, vmware'


this is my config file

&gt;agent: 1
args: -cpu 'host,+kvm_pv_unhalt,+kvm_pv_eoi,hv_vendor_id=NV43FIX,kvm=off'
bios: ovmf
boot: dcn
bootdisk: scsi0
cores: 8
cpu: host,hidden=1,flags=+pcid
efidisk0: ssd:vm-110-disk-1,size=128K
hostpci0: 09:00,x-vga=1,romfile=gpuPATCHED.rom,pcie=1
ide0: local:iso/virtio-win-0.1.164.iso,media=cdrom,size=362130K
ide2: none,media=cdrom
machine: q35
memory: 49152
name: GPU
net0: virtio=36:EA:28:85:47:03,bridge=vmbr0
numa: 1
ostype: win10
scsi0: ssd:vm-110-disk-0,backup=0,cache=writeback,iothread=1,size=190G,ssd=1
scsihw: virtio-scsi-single
smbios1: uuid=9fb63fac-42ee-4087-8e3f-c308e888a5a4
sockets: 1
vmgenid: 6b4ec63e-3cae-4311-894c-907ee7c0a308
vga: none



Thanks again for all the help! ⏤ by *ThinkOrdinary* (↑ 1/ ↓ 0)
├─────── Make sure it's on a new line. If it is and still not working, then it's beyond my ability. Maybe ask around here or /r/Proxmox or /r/vfio. Sorry bud.  ⏤ by *cjalas* (↑ 1/ ↓ 0)
├──────── no worries, i'll try again on a fresh install, and then maybe again on esxi. I appreciate all the help!  ⏤ by *ThinkOrdinary* (↑ 1/ ↓ 0)
├──── /u/cjalas sorry for the tag, I just have a question I haven't been able to find an answer to neither on reddit/proxmox forums neither in proxmox documentation. The "Primary GPU" checkbox, what exactly does it mean? I have only one GPU for now, want to pass it to a windows machine, this will leave Proxmox without a GPU, does that mean that I have to check "Primary GPU" as the GPU I'm passing is the primary gpu of the entire system? ⏤ by *robearded* (↑ 1/ ↓ 0)
└────

##### Hey, after I disable in-built VGA of VM, VM cant boot anymore, how so ? ⏤ by *XHellAngelX* (↑ 1/ ↓ 0)
##### Can someone ELI5 this to me? ⏤ by *socrates1975* (↑ 1/ ↓ 0)
##### Nice guide. I looked up your motherboard, how are you plugging in the graphics cards? They don’t seem like they have x16 slots, unless I’m missing something? ⏤ by *chunkypot* (↑ 1/ ↓ 0)
├─ X8 to x16 riser cables ⏤ by *cjalas* (↑ 1/ ↓ 0)
├── Thanks, any recommendations? ⏤ by *chunkypot* (↑ 1/ ↓ 0)
└────

##### Can you limit each VMs GPU usage with Proxmox? ⏤ by *BeastMiners* (↑ 1/ ↓ 0)
##### Heyo

I've followed the instructions step by step, but when I set the "machine=q35" in the VM-configuration the network fails. After removing the q35-command everything is back to normal... Does anyone know what I've missed? ⏤ by *Snapky* (↑ 1/ ↓ 0)
##### How does this compare to windows server GPU pass through?

That seems a lot simpler to set up ⏤ by *jorgp2* (↑ -1/ ↓ 0)
├─ I wouldn’t know; this is for Proxmox.  ⏤ by *cjalas* (↑ 8/ ↓ 0)
└────

##### Remember to try different bios settings, in my case this was blocking boot, had to enable igpu before it worked. ⏤ by *BlackFireAlex* (↑ 1/ ↓ 0)
##### If you're trying to do this with an x470 motherboard you need to enable SVM mode in Advanced CPU Core Settings per this [forum post](https://forum.proxmox.com/threads/ryzen-2700-x470-kvm-virtualisation-configured-but-not-available.50063/) ⏤ by *ItzDaWorm* (↑ 1/ ↓ 0)
##### I know this guide is old but a lot is still relevant.

I had to put the module parameters in grub. Updating the RAM disk never worked for me:

    GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt kvm.ignore_msrs=1 vfio-pci.ids=10de:0fc8,10de:0e1b"

I also use the MSIInteruptEnabler.exe to change the interrupt handling. ⏤ by *r3jjs* (↑ 1/ ↓ 0)
├─ What is the MSI Interrupt Enabler?  


I am having trouble passing my 3080....Wondering if the config you mention might help ⏤ by *ZaxLofful* (↑ 1/ ↓ 0)
├── &gt;MSIInteruptEnabler.exe

&amp;#x200B;
https://github.com/TechtonicSoftware/MSIInturruptEnabler ⏤ by *r3jjs* (↑ 1/ ↓ 0)
├─── Can you explain it more to me, what is it doing? ⏤ by *ZaxLofful* (↑ 1/ ↓ 0)
└────

##### All I can say is thank you! You and this guide has saved me so much time and headache. I would have saved more headache if my pre-existing windows 10 VM worked with this. My Windows 11 VM worked just fine but had issues with the 10 one, not sure why and don’t care at this point. Got my Plex running in a Win 11 VM with a GPU passed through to it. Thank you again. ⏤ by *pcandmacguy* (↑ 1/ ↓ 0)
├─ So just to be clear - you followed this exact guide for Windows 11? Also, if you don't mind what version of Proxmox are you running? ⏤ by *mono_void* (↑ 1/ ↓ 0)
├── I believe it was 7.2, currently my server is down for maintenance and moving. ⏤ by *pcandmacguy* (↑ 1/ ↓ 0)
└────

##### Thank you so much !!!!!!

big love for this ! finally everything works as it should ⏤ by *Robbe_Hoskens* (↑ 1/ ↓ 0)
##### Thank you for this guide, server with E3 CPU with AMD GPU sends its regards. ⏤ by *owner_cz* (↑ 1/ ↓ 0)
##### Followed to the T. Doesn't work for me for shit. Everything indicates the gpu is passed thru. Add it to the VM. Boot and it's never there! Changed a million settings - tried everything every website says to try. I've accepted the fact that this will never work for me. I've tried it on several machines over the YEARS. Never ever works. ⏤ by *scewing* (↑ 1/ ↓ 0)
##### Such a fabulous guide, you have done a great service to the homelab community. Thank you! ⏤ by *MildButWild* (↑ 1/ ↓ 0)
##### This is amazing. I needed to add the extra commands to the GRUB\_CMDLINE\_LINUX\_DEFAULT but after that everything works perfectly! Thank you for putting this together! ⏤ by *rogvid* (↑ 1/ ↓ 0)
##### Is anyone having issues with the network connection dropping on their gaming VM?

I have made a post here:

https://www.reddit.com/r/Proxmox/comments/x1a4u9/gpu\_passthrough\_vm\_constantly\_dropping\_network/ ⏤ by *fromage9747* (↑ 1/ ↓ 0)
##### Do I really need UEFI BIOS motherboard? I have server motherboard x79. Virt-d is enabled but can\`t get same result. ⏤ by *Thick-Neighborhood94* (↑ 1/ ↓ 0)
##### Just wanted to say thank you for putting this together!  I tried a few other guides, but this was the most straightforward!

Can't believe you guys were doing this 4 years ago 🤯 ⏤ by *gootecks* (↑ 1/ ↓ 0)
##### Hello, I have Lenovo G50-80, Would you pleae advise, How to enable "IOMMU" from BIOS,

or is there any equivalant of that, 

***What am I trying to achieve*** : GPU Passthrouh '**intel hd graphics 5500**' from Proxmox to Virtual Machine Ubuntu.

&amp;#x200B;

**My PROXMOX out of 'lspci'**

    root@lab:~# lspci  -v -s  $(lspci | grep ' VGA ' | cut -d" " -f 1)
00:02.0 VGA compatible controller: Intel Corporation HD Graphics 5500 (rev 09) (prog-if 00 [VGA controller])
        Subsystem: Lenovo HD Graphics 5500
        Flags: bus master, fast devsel, latency 0, IRQ 52
        Memory at d0000000 (64-bit, non-prefetchable) [size=16M]
        Memory at c0000000 (64-bit, prefetchable) [size=256M]
        I/O ports at 5000 [size=64]
        Expansion ROM at 000c0000 [virtual] [disabled] [size=128K]
        Capabilities: [90] MSI: Enable+ Count=1/1 Maskable- 64bit-
        Capabilities: [d0] Power Management version 2
        Capabilities: [a4] PCI Advanced Features
        Kernel driver in use: i915
        Kernel modules: i915 ⏤ by *ashyvampire91* (↑ 1/ ↓ 0)
##### I have a question about your GRUB settings. You provide helpful links to sources for your ACS Override setting and for Disabling Framebuffer. But I noticed in your specific GRUB settings you also have "nofb" and "modeset" between your ACS Override and Framebuffer arguments. Can you explain what those are and why you used them? Do they belong to the ACS Override argument or to the Disable Framebuffer argument? Thanks ⏤ by *CommunicationFit9122* (↑ 1/ ↓ 0)
##### Instead of buying a second Computer, I am considering spending that solely on the Threadripper combo now and if I can afford an EPYC server I will but oh my Lordy, I am not paper swole for such equipment just yet. ⏤ by *DexterDJ2* (↑ 1/ ↓ 0)
##### I followed these instructions to get passthrough on an Nvidia GTX 970, but was finding I was having a lot of trouble with audio degradation when connecting the VM to an external speaker source with HDMI. After lot of testing and troubleshooting, I found the solution was to [edit registry to manually enable MSI-mode on my Nvidia card and all associated HD audio devices](https://www.underealm.com/tech/2019/05/nvidia-drivers-and-msi-support-in-windows/).

The site was immensely helpful to me, though it appears there also a tool you can use to automatically activate MSI: [https://github.com/TechtonicSoftware/MSIInturruptEnabler](https://github.com/TechtonicSoftware/MSIInturruptEnabler).

Just posting this so no one else hopefully has to spend a week trying to solve a similar problem in their downtime.

TL;DR - If you encounter poor audio, guest crashing, video driver problems, or other weirdness on your VM after following this guide, try enabling MSI-mode. ⏤ by *Dezmancer* (↑ 1/ ↓ 0)
##### Is this still current? ⏤ by *RiffyDivine2* (↑ 1/ ↓ 0)
##### Hey. I'm trying to make this work with an Fuji R9 Fury .. Everythings works, i mean the part of installation of the windows, device manager show a PCI Device. Even GPU-Z show the data from the card, but instead of showing his name, shows Microsoft device with AMD logo lol Tryed already to install AMD drivers for the card, and it says not compatible :( Any tip?! ⏤ by *Xcat20* (↑ 1/ ↓ 0)
##### holy even after 4 years I still got it first try thank you so much!!!! ⏤ by *AdministrativeCost40* (↑ 1/ ↓ 0)
##### I have a problem. When I reboot the machine after updating the initramfs, the system hangs at startup.  
My graphics card is a 6800XT.

0c:00.0 VGA compatible controller: Advanced Micro Devices, Inc. \[AMD/ATI\] Navi 21 \[Radeon RX 6800/6800 XT / 6900 XT\] (rev c1) (prog-if 00 \[VGA controller\])

lspci -n -s 0c:00  
0c:00.0 0300: 1002:73bf (rev c1)  
0c:00.1 0403: 1002:ab28  
0c:00.2 0c03: 1002:73a6  
0c:00.3 0c80: 1002:73a4

options vfio-pci ids=1002:73bf,1002:ab28,1002:73a6,1002:73a4 disable\_vga=1

Can someone help? ⏤ by *Predatux* (↑ 1/ ↓ 0)
├─ Could you ever solve this? I am stuck with my 6900 XT.... ⏤ by *Xclsd* (↑ 1/ ↓ 0)
└────

##### for someone who get stuck with the **error code: 43 on the drivers** linst in Windows VM:just keep in mind in my case i use this with my 1060 it can work with others also.

hopfuly it will help someone else in future:

1. **first off** go to your windowsVM and install GPU-Z and get the Grafikbios from the card
2. **there is 2 Options :** 1. then dump it on our Proxmox server on this location:
   1. dump it on our Proxmox server to edit your bios for example on:  /root/
   2. or keep this file
   3. **you need python 2 or 3 to on the maschien were u patch your GBIOS**
3. use this script: [https://raw.githubusercontent.com/Marvo2011/NVIDIA-vBIOS-VFIO-Patcher/master/nvidia\_vbios\_vfio\_patcher.py](https://raw.githubusercontent.com/Marvo2011/NVIDIA-vBIOS-VFIO-Patcher/master/nvidia_vbios_vfio_patcher.py)
4. Run this on ur Terminal: python nvidia\_vbios\_vfio\_patcher.py -i **YOUR\_GBIOS**.rom -o **NAME\_OF\_PATCHED\_GBIOS**.rom
5. get the patched rom to your Proxmox Server on this location: /usr/share/kvm/NAME\_OF\_PATCHED\_GBIOS.rom
6. After that edit your VM config with: nano /etc/pve/qemu-server/&lt;VMID&gt;.conf and **append** ,romfile=Palit.GTX1050Ti\_Patched.rom **to** hostpci0 **line**

you can also read this thread hier: [https://forum.proxmox.com/threads/nvidia-gtx-1050ti-error-43-code-43.75553/page-2](https://forum.proxmox.com/threads/nvidia-gtx-1050ti-error-43-code-43.75553/page-2) ⏤ by *No_Yesterday_3990* (↑ 1/ ↓ 0)
##### Would i be able to see the vm from the hdmi ports on the gpu ⏤ by *Gameselect1* (↑ 1/ ↓ 0)
##### #STARTING WITH PROXMOX 8 (KERNEL 6.x) THE ONLY THINGS YOU NEED TO DO ARE:

* adding pcie_acs_override=multifunction (or override,multifunction if your UEFI has no ACS toggel) to /etc/kernel/cmdline
* load the vfio, vfio_iommu_type1, vfio_pci, vfio_virqfd kernel modules
* echo "options kvm ignore_msrs=1" &gt; /etc/modprobe.d/kvm.conf
* proxmox-boot-tool refresh
* add the PCIE device to the VM

NO DRIVER BLACKLISTING, NO vfio.conf AND NO FRAMEBUFFER DISABLING

[Source](https://old.reddit.com/r/Proxmox/comments/vvzryq/how_to_disable_simplefb_for_gpu_passthrough_with/ifnh77v/) ⏤ by *FaySmash* (↑ 1/ ↓ 0)
├─ Recon this works for Nvidia/AMD gpu's and AMD/Intel CPU's?

Be handy if so as I am building an Intel machine with 2 Nvidia GPU's for transcoding at the moment ⏤ by *XcOM987* (↑ 1/ ↓ 0)
├── from what I've come across so far, yes ⏤ by *FaySmash* (↑ 2/ ↓ 0)
├─── Yep, first GPU working as passthrough, just waiting for the second GPU to arrive to test if dual GPU's also work, only thing I had to do in addition to your notes was enable IMMOU

Cheers for the heads up ⏤ by *XcOM987* (↑ 2/ ↓ 0)
├─── Awesome, thanks ⏤ by *XcOM987* (↑ 1/ ↓ 0)
└────

##### I am running a HP Z440 with Xeon E5-2690 v4. vt-x and VTd is enabled in the Bios but I still only have one IOMMU group and Proxmox gives me the "No IOMMU detected, please activate it.See Documentation for further information."

Anyone experienced the same issue and has a fix?

Thanks ⏤ by *EngineWorried9767* (↑ 1/ ↓ 0)
├─ im having the same problem rn. have you figured it out yet? ⏤ by *Deadcamper21* (↑ 1/ ↓ 0)
└────

##### My GPU Gigabyte 1050 does not work. In my windows, i cannot found the GPU, but my hardware on proxmox is already have PCI for GPU. How can I know the PCI config is work ? ⏤ by *Creepy_Newspaper_300* (↑ 1/ ↓ 0)
##### ## Proxmox 8 - Dual GPU passthrough - AMD 6800XT + Nvidia A2000

The Nvidia worked great without issue following the guide but had a lot of problems with the AMD.

The solution was to

* **Exclude pci-ids of the AMD card from the vfio.conf** (yes you read that right)
* `softdep amdgpu pre: vfio vfio_pci` extra line in vfio.conf
* resize bar and 4g decoding must be **disabled** in bios
* the cmdline must have `pcie_acs_override=downstream,multifunction` and `initcall_blacklist=sysfb_init`
* In the VM settings ballooning ram and Primary GPU must be **unselected**

I documented my config in the [Proxmox forums](https://forum.proxmox.com/threads/proxmox-8-amd-6800xt-and-nvidia-a2000-dual-gpu-passthrough.131035/) ⏤ by *rpntech* (↑ 1/ ↓ 0)
