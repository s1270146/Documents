# エディタ

## Neovim

### 概要

openSUSE Tumbleweed で `LazyVim` を使うための前提ツールを入れる。

参考

- <https://build.opensuse.org/package/show/openSUSE:Factory/neovim>
- <https://www.lazyvim.org/>

### 関連ツール

#### lazygit

```bash
sudo zypper install lazygit
```

#### Rust と tree-sitter

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
cargo install --locked tree-sitter-cli
```

#### fzf

```bash
sudo zypper install fzf
```

#### ripgrep

```bash
sudo zypper install ripgrep
```
