# Editor

## neovim

<https://build.opensuse.org/package/show/openSUSE:Factory/neovimn>
[https://www.lazyvim.org](https://www.lazyvim.org/)

requirementをインストールしていく
lazygit
```bash
sudo zypper install lazygit
```

tree-sitterをインストールするためにcargo(rustup)をインストールする
<https://rust-lang.org/tools/install/>

```bash
curl --proto '=https' --tlsv1.2 -sSf [https://sh.rustup.rs](https://sh.rustup.rs/) | sh
cargo install --locked tree-sitter-cli
```

fzf
```bash
sudo zypper install fzf
```

ripgrep
```bash
sudo zypper install ripgrep
```
