
sudo apt install neovim

update-alternatives --set vi /usr/bin/nvim
update-alternatives --set vim /usr/bin/nvim

mkdir -p ~/.config
curl -sLf https://spacevim.org/install.sh | bash
cd ~/.SpaceVim.d
vi init.toml

mkdir -p ~/.SpaceVim.d





