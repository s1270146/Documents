# Dev Tools

## Lazygit

github: [lazygit](https://github.com/jesseduffield/lazygit?tab=readme-ov-file#debian-and-ubuntu)
```bash
LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" | \grep -Po '"tag_name": *"v\K[^"]*')
curl -Lo lazygit.tar.gz "https://github.com/jesseduffield/lazygit/releases/download/v${LAZYGIT_VERSION}/lazygit_${LAZYGIT_VERSION}_Linux_x86_64.tar.gz"
tar xf lazygit.tar.gz lazygit
sudo install lazygit -D -t /usr/local/bin/
```

## tree-sitter

install cargo (Rust): ../Languages/rust.md
install gcc: ../Languages/c.md
install tree-sitter cli: <https://github.com/tree-sitter/tree-sitter/blob/master/crates/cli/README.md>

```bash
cargo install --locked tree-sitter-cli
```

## For LazyVim

- fzf: <https://github.com/junegunn/fzf>
- ripgrep: <https://gihyo.jp/admin/serial/01/ubuntu-recipe/0579>
- fd: <https://github.com/sharkdp/fd>

```bash
sudo apt install fzf ripgrep fd-find
```
