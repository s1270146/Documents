# ターミナル

## WezTerm

### メモ

- ターミナルは `WezTerm` を使う
- 設定変更は Lua ファイル編集後すぐ反映される
- 当初は日本語入力が不安定だったが、`ibus` より `fcitx` の方が相性が良かった

### `fcitx5` の導入メモ

```bash
sudo zypper install fcitx5 fcitx5-mozc fcitx5-configtool
```

- 確認時は `fcitx5-gtk` と `fcitx5-qt` が見つからない環境だった

## fastfetch

```bash
sudo zypper in fastfetch
```

参考

- <https://medium.com/@yusuke_h/%E3%82%BF%E3%83%BC%E3%83%9F%E3%83%8A%E3%83%AB%E3%81%8C%E3%83%80%E3%82%B5%E3%81%84%E3%81%A8%E3%83%A2%E3%83%86%E3%81%AA%E3%81%84-xxfetch%E7%B7%A8-53d06babc296>

## starship

- 設定ファイルは `~/.config/starship/starship.toml` に置く
- 設定ファイル位置を `.bashrc` に書く

## tmux

### メモ

- `tmux` を `zypper` で入れる
- プラグインマネージャは `tpm` を使う
- `tmux-power` は試したが最終的にやめた
- セッション管理や Vim 風コピー操作を整えた

参考

- <https://github.com/tmux-plugins/tpm>
- <https://github.com/wfxr/tmux-power?tab=readme-ov-file>
- <https://github.com/fabioluciano/tmux-tokyo-night>
