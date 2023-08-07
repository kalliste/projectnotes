
# Notes on GPU passthrough in Proxmox

There are some good sources out there but still had to do some work and experimentation

Probably use x86-64-v2-AES as your CPU type.

## /etc/pve/qemu-server/102.conf
```
args: -cpu 'x86-64-v2-AES,+kvm_pv_unhalt,+kvm_pv_eoi,hv_vendor_id=NV43FIX,kvm=off"
cpu: x86-64-v2-AES,hidden=1
hostpci0: 0000:0b:00,pcie=1
```

## /etc/default/grub

Ours is a Threadripper machine so we are enabling amd_iommu instead of intel_iommu. We also added `initcall_blacklist=sysfb_init systemd.unified_cgroup_hierarchy=0`

```
     10 GRUB_CMDLINE_LINUX_DEFAULT="quiet amd_iommu=on initcall_blacklist=sysfb_init systemd.unified_cgroup_hierarchy=0 vfio_pci.ids=10de:17f0,10de:0fb0"
```

Beyond that, [this guide on Reddit covered the rest](https://www.reddit.com/r/homelab/comments/b5xpua/the_ultimate_beginners_guide_to_gpu_passthrough/)

We saved [a copy of the guide here](UltimateBeginnersGuidetoProxmoxGPUPassthrough.md)


