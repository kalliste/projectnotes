

## Ubuntu common packages

apt install git tmux wget net-tools vim unzip p7zip unattended-upgrades neovim command-not-found
apt install cmake build-essential cargo
# apt install rar


## Ubuntu fail2ban

apt install fail2ban


## PostgreSQL on Ubuntu

https://www.digitalocean.com/community/tutorials/how-to-install-postgresql-on-ubuntu-22-04-quickstart
https://www.postgresql.org/docs/current/sql-alterrole.html

apt install libpostgresql-jdbc-java postgresql postgresql-contrib
sudo -i -u postgres
createuser --interactive
createdb DATABASE
sudo -u postgres psql
ALTER USER foo WITH PASSWORD 'bar';


## Ubuntu Automatic Updates

https://help.ubuntu.com/community/AutomaticSecurityUpdates

sudo dpkg-reconfigure --priority=low unattended-upgrades


## Ubuntu samba domain controller

https://www.considerednormal.com/2022/11/samba-based-active-directory-on-ubuntu-22-04/



## Cockpit


https://cockpit-project.org/faq.html#error-message-about-being-offline
https://wiki.samba.org/index.php/GSOC_cockpit_samba_ad_dc
https://garrett.github.io/cockpit-project.github.io/external/wiki/Proxying-Cockpit-over-NGINX


# Certbot

sudo apt install snapd
sudo snap install core; sudo snap refresh core
sudo apt remove certbot
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot

certbot --nginx -d your.domain.tld

# Linode

https://www.linode.com/docs/products/compute/compute-instances/guides/install-a-custom-distribution/


# Youtrack

https://gist.github.com/anhtran/3cd7bca74553b5c55f8e5212c05898d0


# Teamcity

https://www.jetbrains.com/help/teamcity/configuring-proxy-server.html


# Keycloak

https://blog.yarsalabs.com/hosting-a-production-ready-keycloak-server/

https://kaeruct.github.io/posts/how-to-use-lets-encrypt-certificates-with-keycloak.html

https://dmc.datical.com/administer/configure-keycloak-ldap.htm

https://www.keycloak.org/docs/latest/server_admin/index.html#_user-storage-federation

https://www.keycloak.org/server/keycloak-truststore


https://stackoverflow.com/questions/54078590/querying-samba-ad-server-with-ldapsearch-fails-with-ldap-sasl-bindsimple-can


We don't actually need all this any more probably:
https://soundsessential.medium.com/setting-up-a-keycloak-server-for-authenticating-to-filemaker-part-10-keycloak-16-ssl-82b636f49194
https://soundsessential.medium.com/setting-up-a-keycloak-server-for-authenticating-to-filemaker-part-3-installing-a-ssl-certificate-24ca3003e287


# vimrc

" Only do this part when compiled with support for autocommands.
if has("autocmd")
    " Use filetype detection and file-based automatic indenting.
    filetype plugin indent on

    " Use actual tab chars in Makefiles.
    autocmd FileType make set tabstop=8 shiftwidth=8 softtabstop=0 noexpandtab
endif

" For everything else, use a tab width of 4 space chars.
set tabstop=4       " The width of a TAB is set to 4.
                    " Still it is a \t. It is just that
                    " Vim will interpret it to be having
                    " a width of 4.
set shiftwidth=4    " Indents will have a width of 4.
set softtabstop=4   " Sets the number of columns for a TAB.
set expandtab       " Expand TABs to spaces.


