# デスクトップ環境

## メニュー

### 現在の構成

- アプリランチャーは `rofi`
- `i3` から起動する
- 設定ファイルは `~/.config/i3/config`

### 使い方

- `Alt+d`: `rofi` を開く
- アプリ検索と起動に使う

### メモ

- `dmenu` の代わりに `rofi` を使う
- `i3` 側では `rofi -show drun` ベースの起動設定にしている
- 参考: <https://wiki.archlinux.jp/index.php/Neovim/index.php/Rofi#Rofi_.E3.82.92_dmenu_.E3.81.AE.E4.BB.A3.E6.9B.BF.E3.81.A8.E3.81.97.E3.81.A6.E4.BD.BF.E3.81.86>

## タスクバー

### 現在の構成

- `i3status` は使わず、`polybar` を使う
- `i3` 起動時に `~/.config/polybar/launch.sh` を実行する
- 設定本体は `~/.config/polybar/config`
- Bluetooth 表示補助に `~/.config/polybar/bluetooth-status.sh` を使う

### 表示内容

- ワークスペース
- アクティブウィンドウ名
- Bluetooth 状態
- 音量
- キーボードレイアウト
- メモリ
- CPU
- ネットワーク
- 時刻
- systray

### 反映方法

```bash
~/.config/polybar/launch.sh
```

### メモ

- 音量表示は `pulseaudio` モジュール
- Bluetooth は接続中デバイスを動的表示する
- `blueman-applet` のトレイ表示も使う

## ディスプレイ

### 現在の構成

- 複数モニターの調整には `ArandR` を使う

### 参考

- <https://qiita.com/SotaAtos/items/c42f01f11fa107e11568>

## 画面ロックと画面オフ

### 起きていたこと

- `Ctrl+Shift+i` では `i3lock` で手動ロックできる
- 何も操作しない状態が続くと、ロックされずに画面だけオフになっていた

### 原因

- `i3` に手動ロック設定はあった
- `xss-lock` を起動する設定もあった
- ただし `xss-lock` 自体が未導入で、自動ロックが動いていなかった
- 一方で X11 の `DPMS` と `Screen Saver` は有効で、一定時間後に画面だけオフになっていた

確認時の状態

```bash
xset q

Screen Saver:
  timeout: 600

DPMS (Display Power Management Signaling):
  Standby: 600    Suspend: 600    Off: 600
  DPMS is Enabled
```

- この状態だと 10 分で画面だけオフになる
- `systemd-logind` の `IdleAction` は原因ではなかった

### 修正内容

#### `xss-lock` を導入

```bash
sudo pacman -S xss-lock
```

- `xss-lock` は X の idle 通知を受けて `i3lock` を起動するために使う
- これがないと `i3` 側に設定があっても自動ロックは動かない

#### `i3` 設定を修正

`~/.config/i3/config`

```bash
# xss-lock grabs a logind suspend inhibit lock and will use i3lock to lock the
# screen before suspend. Start it only when installed.
exec --no-startup-id sh -c 'command -v xss-lock >/dev/null && exec xss-lock --transfer-sleep-lock -- i3lock --nofork'

# screen lock
bindsym $mod+Shift+i exec --no-startup-id i3lock -c 000000
```

- `xss-lock` が入っている時だけ起動するようにした
- 手動ロックはこれまで通り `Ctrl+Shift+i`

#### `~/.xprofile` で idle 時間を設定

```bash
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"

# Lock after 1 hour of idle, then power off the display after 2 hours.
xset s 3600 3600
xset +dpms
xset dpms 7200 7200 7200

fcitx -d

xrandr --output eDP-1 --brightness 0.6

source .fehbg
```

意味

- `xset s 3600 3600`: 1 時間でスクリーンセーバー idle を発火させる
- `xset +dpms`: `DPMS` を有効化する
- `xset dpms 7200 7200 7200`: 2 時間で画面をオフにする

### 動作

- 1 時間無操作: `xss-lock` が `i3lock` を起動して画面ロック
- 2 時間無操作: `DPMS` により画面オフ

### 確認方法

```bash
ps -ef | grep xss-lock
xset q
```

期待する状態

```bash
xss-lock --transfer-sleep-lock -- i3lock --nofork
```

```bash
Screen Saver:
  timeout: 3600

DPMS (Display Power Management Signaling):
  Standby: 7200    Suspend: 7200    Off: 7200
  DPMS is Enabled
```

### メモ

- `suspend` しても通常は X11 のログイン画面には戻らない
- セッションは維持され、復帰後は `i3` の画面に戻る
- `xss-lock --transfer-sleep-lock` を使っているので、サスペンド前後のロック連携もしやすい

## 音量と Bluetooth

### 概要

- 音声基盤は `pulseaudio`
- Bluetooth 管理は `bluez` と `bluez-utils`
- GUI 操作用に `blueman`
- 音の出力先切替は `pavucontrol`
- バー表示は `polybar`

### インストールしたもの

```bash
sudo pacman -S bluez bluez-utils pulseaudio-bluetooth blueman pavucontrol
sudo systemctl enable --now bluetooth
```

### メモ

- `pulseaudio-bluetooth` がないと Bluetooth スピーカーの `A2DP` 接続に失敗した
- 失敗時のログは `a2dp-sink profile connect failed ... Protocol not available` だった
- 導入直後は `PulseAudio` が古い状態のままのことがあるので、必要なら `pulseaudio -k` で再起動させる

### 普段の使い方

#### Bluetooth の GUI

- `blueman-applet` を常駐させる
- `blueman-manager` を開くと、ペアリング、接続、切断、信頼設定ができる
- `polybar` からも開けるようにしている

#### 音の出力先

```bash
pavucontrol
```

- 「出力デバイス」で Bluetooth スピーカーを選ぶ
- 接続中の出力先確認

```bash
pactl list short sinks
```

#### `bluetoothctl` の役割

- `blueman` があっても不要にはならない
- GUI で詰まった時の確認や復旧に使う
- 基本操作は以下

```bash
bluetoothctl
power on
agent on
default-agent
pairable on
discoverable on
scan on
devices
pair XX:XX:XX:XX:XX:XX
trust XX:XX:XX:XX:XX:XX
connect XX:XX:XX:XX:XX:XX
info XX:XX:XX:XX:XX:XX
```

### 設定ファイル

#### i3

`~/.config/i3/config`

追加したもの

- `nm-applet` の自動起動
- `blueman-applet` の自動起動
- 音量操作ショートカット

ショートカット

- `XF86AudioRaiseVolume`: 音量アップ
- `XF86AudioLowerVolume`: 音量ダウン
- `XF86AudioMute`: ミュート切替
- `Alt+Ctrl+=`: 音量を `+5%`
- `Alt+Ctrl+-`: 音量を `-5%`
- `Alt+Ctrl+m`: ミュート切替
- `Alt+Ctrl+p`: `pavucontrol` を開く

#### polybar

- `~/.config/polybar/config`
- `~/.config/polybar/launch.sh`
- `~/.config/polybar/bluetooth-status.sh`

内容

- `pulseaudio` モジュールで音量表示
- `bluetooth` モジュールで接続中デバイスを動的表示
- 接続中なら `BT:<Alias>`、未接続なら `BT:off`

`bluetooth` モジュールの操作

- 左クリック: `blueman-manager`
- 中クリック: 接続中デバイスがあれば切断、なければ `blueman-manager`
- 右クリック: `pavucontrol`
- ホイール上: `blueman-manager`

### 反映方法

#### polybar

```bash
~/.config/polybar/launch.sh
```

#### i3

通常は次回ログインで反映される。
今のセッションで反映する場合は、`i3` のソケットが古いことがあるので注意する。

```bash
i3-msg -s /run/user/1000/i3/ipc-socket.<PID> reload
```

`<PID>` は `/run/user/1000/i3/` の `ipc-socket.*` を見る。

### トラブル時

#### Bluetooth スピーカーに接続できない

- まず `blueman-manager` で接続を試す
- だめなら `bluetoothctl info <MAC>` で状態確認する
- 別端末に既につながっていないか確認する
- それでもだめなら一度 `remove <MAC>` して再ペアリングする

#### ペアリング済みなのに音が出ない

```bash
pactl list short sinks
pavucontrol
```

- Bluetooth sink が出ているか確認する
- 出ていなければ `PulseAudio` を再起動する

```bash
pulseaudio -k
```

#### `polybar` の Bluetooth 表示がおかしい

- `~/.config/polybar/bluetooth-status.sh` を単体実行して確認する

```bash
~/.config/polybar/bluetooth-status.sh
```

- その後 `polybar` を再起動する

```bash
~/.config/polybar/launch.sh
```
