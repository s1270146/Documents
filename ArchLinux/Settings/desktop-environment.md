# デスクトップ環境

## menu

- rofiをインストール
    - できなかった
    - <https://www.reddit.com/r/archlinux/comments/1i52tts/cant_install_rofi/?tl=ja>
    - できた
    - <https://https://wiki.archlinux.jp/index.php/Neovim/index.php/Rofi#Rofi_.E3.82.92_dmenu_.E3.81.AE.E4.BB.A3.E6.9B.BF.E3.81.A8.E3.81.97.E3.81.A6.E4.BD.BF.E3.81.86>

## タスクバー

- i3statusの表示を消してpolybarを実行する
- configフォルダの中にlaunch.shを作成してi3のconfigから実行させるのがデファクトスタンダードっぽい
- launch.shは以下を参考
    - <https://github.com/AriosJentu/i3-polybar-config/blob/master/polybar/launch.sh>
- 動かせた
- スペースが多かったりfcitxの表示させたりしたいので修正したい、、、

## ディスプレイ

- ArandR
  - 複数モニター
  - <https://qiita.com/SotaAtos/items/c42f01f11fa107e11568>

## 画面ロックと画面オフ

### 発生していた事象

- `Ctrl+Shift+i` では `i3lock` で手動ロックできる
- ただし、何も操作しない状態が続くとロックされずに画面だけオフになる

### 原因

- `i3` の設定には手動ロックの設定はある
- `xss-lock` を起動する設定もあった
- ただし `xss-lock` 自体が未導入で、実際には自動ロックが動いていなかった
- 一方で X11 の `DPMS` と `Screen Saver` は有効だったため、一定時間後に画面だけオフになっていた

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

#### 1. `xss-lock` を導入

```bash
sudo pacman -S xss-lock
```

- `xss-lock` は X の idle 通知を受けて `i3lock` を起動するために使う
- これがないと `i3` 側に設定があっても自動ロックは動かない

#### 2. `i3` 設定を修正

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

#### 3. `~/.xprofile` で idle 時間を明示設定

最終的に以下にした

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

- `xset s 3600 3600`
  - 3600 秒、つまり 1 時間でスクリーンセーバー idle を発火させる
  - `xss-lock` はこの idle を使って `i3lock` を起動する
- `xset +dpms`
  - DPMS を有効化する
- `xset dpms 7200 7200 7200`
  - 7200 秒、つまり 2 時間で画面をオフにする

### 動作

- 1 時間無操作
  - `xss-lock` が `i3lock` を起動して画面ロック
- 2 時間無操作
  - `DPMS` により画面オフ

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
- `xss-lock --transfer-sleep-lock` を使っているので、サスペンド前後のロック連携もさせやすい
