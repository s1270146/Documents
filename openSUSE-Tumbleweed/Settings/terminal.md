# ターミナル

## Wezterm

- ターミナルはWez’s Terminalを入れた 
- 正直どれを使ってもtmuxをインストールするから一緒weztermインストールしたけど日本語入力できない
- ibusよりfcitxのほうが動作が良いらしいので乗り換える → まじで行けたびっくり

```bash
koyo@localhost:~> sudo zypper install \
> fcitx5 fcitx5-mozc fcitx5-gtk fcitx5-qt fcitx5-configtool
[sudo] password for root:
Loading repository data...
Reading installed packages...
Package 'fcitx5-gtk' not found.
Package 'fcitx5-qt' not found.
Resolving package dependencies...
```

weztermはluaファイルを変更した瞬間設定が反映される

## fastfetch

```bash
sudo zypper in fastfetch
```

[https://medium.com/@yusuke_h/ターミナルがダサいとモテない-xxfetch編-53d06babc296](https://medium.com/@yusuke_h/%E3%82%BF%E3%83%BC%E3%83%9F%E3%83%8A%E3%83%AB%E3%81%8C%E3%83%80%E3%82%B5%E3%81%84%E3%81%A8%E3%83%A2%E3%83%86%E3%81%AA%E3%81%84-xxfetch%E7%B7%A8-53d06babc296)

## starship

starshipの設定ファイルを~/.config/starship/starship.tomlに移動
設定ファイル位置を.bashrcに記述


## tmux

- tmuxをzypperでインストール
- tmuxのプラグインマネージャを設定
  - <https://github.com/tmux-plugins/tpm>
- tmux-powerをインストールしてそれっぽくなってきた (追記: これやめた)
  - <https://github.com/wfxr/tmux-power?tab=readme-ov-file>
- tmux セッション管理
- vim操作のようなコピー with xsel 過去の自分に感謝
- <https://github.com/fabioluciano/tmux-tokyo-night>
