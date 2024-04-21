

### Systemwide neovim

```
sudo apt install neovim
update-alternatives --set vi /usr/bin/nvim
update-alternatives --set vim /usr/bin/nvim
```


### User neovim

```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
. ~/.cargo/env
cargo install --git https://github.com/MordechaiHadad/bob.git
bob install 0.8.3
bob use 0.8.3
mkdir -p ~/.local/bin
cd ~/.local/bin
ln -s ~/.local/share/bob/nvim-bin/nvim nvim
# ln -s ~/.local/share/bob/nvim-bin/nvim vim
# ln -s ~/.local/share/bob/nvim-bin/nvim vi
echo "alias vi='nvim'" >> ~/.bashrc
echo "alias vim='nvim'" >> ~/.bashrc
. ~/.profile
```


SpaceVim

```
mkdir -p ~/.config
curl -sLf https://spacevim.org/install.sh | bash
cd ~/.SpaceVim.d
```


My SpaceVim config

```
mkdir -p ~/.SpaceVim.d
wget https://raw.githubusercontent.com/kalliste/projectnotes/main/deployment/dot-SpaceVim.d-init.toml -O ~/.SpaceVim.d/init.toml
```


See also:
https://stackoverflow.com/questions/1878974/redefine-tab-as-4-spaces



https://spacevim.org/layers/lang/markdown/

```
npm -g install remark
npm -g install remark-cli
npm -g install remark-stringify
npm -g install remark-frontmatter
npm -g install wcwidth
```
