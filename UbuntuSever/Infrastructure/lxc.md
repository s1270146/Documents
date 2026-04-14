# LXC

## Requirements

### cライブラリの確認

```bash
$ ldd --version
ldd (Ubuntu GLIBC 2.39-0ubuntu8.6) 2.39
Copyright (C) 2024 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
Written by Roland McGrath and Ulrich Drepper.

```

### カーネルバージョンの確認

```bash
$ uname -r
6.8.0-90-generic
```

## lxc-attachの動作に必要な環境

- Linuxカーネル 3.8以上

<https://linuxcontainers.org/ja/lxc/manpages/man1/lxc-attach.1.html>

lxc-attach: 実行中のコンテナ内でのプロセスの開始

例) 存在するコンテナ内で新しいシェルを生成する

```bash
lxc-attach -n container
```

## 非特権のコンテナが動作するのに必要な環境

- libpam-cgfs 非特権のcgroup操作を行うためにシステムを設定するPAMモジュール
- newuidmap、newgidmapを含む最新バージョンのshadow
- Linuxカーネル 3.12 以上

PAMモジュールとは: プログラムが使用する様々な認証方式を一元に管理するためのシステム
<https://qiita.com/ss12345/items/5b9e4edffb550281f43e>

/etc/shadowについて
シャドウパスワードとは、パスワードのデータを一般ユーザから見えないようにする工夫
/etc/shadowはroot以外は読み不可
第2フィールドにハッシュ化したパスワードが格納されている
<https://linuc.org/study/knowledge/510/>
<https://linuc.org/study/column/7308/>

## 推奨ライブラリ

- libcap:
  - Linuxケーパビリティを設定・参照するためのコマンドおよびライブラリ群
  - ケーパビリティを落とすために必要
- libapparmor:
  - AppArmorとは、"名前ベースの強制アクセス制御で、LSMを用いて実装されている仕組み"
    - <https://gihyo.jp/admin/serial/01/ubuntu-recipe/0798>
    - 名前ベース: "セキュリティ設定を対象となるファイルパスをもとに設定する"
    - 強制アクセス制御(MAC): パーミッションより強い、管理者ですら強制的にアクセス制御の影響を受ける仕組み
    - LSM(Linux Security Module): AppArmorやSELinuxのような機能を実現するためのフレームワーク
  - コンテナに対して独自のapparmorプロファイルを設定するために必要
- libselinux:
  - SELinuxとは
    - <https://qiita.com/Brutus/items/7d026929f393b0ec25d0>
    - AppArmorと同じで強制アクセス制御
    - 目的は「最小権限の原則」
  - コンテナに対して独自のSELinuxコンテキストを設定するために必要
- libseccomp
  - seccomp(secure computing mode):
    - <https://note.com/minato_kame/n/n4cc17642b8c4>
    - 特定のシステムコールだけを許可する仕組み
    - 「門番」
  - コンテナに対してseccompポリシーを設定するために必要
- libgnutls
  - GnuTLS:<https://gnutls.org/>
    - SSL、TLS、DTLSプロトコルおよび関連技術を実装するセキュア通信ライブラリ
  - いろんなチェックサム確認に必要
- liblua: luaバインディングに必要
- python3-dev: python3バインディングに必要

## Install

```bash
sudo apt install lxc
```

必要な機能が揃っているかの確認
```bash
$ lxc-checkconfig
LXC version 5.0.3
Kernel configuration not found at /proc/config.gz; searching...
Kernel configuration found at /boot/config-6.8.0-90-generic

--- Namespaces ---
Namespaces: enabled
Utsname namespace: enabled
Ipc namespace: enabled
Pid namespace: enabled
User namespace: enabled
Network namespace: enabled

--- Control groups ---
Cgroups: enabled
Cgroup namespace: enabled
Cgroup v1 mount points:
Cgroup v2 mount points:
 - /sys/fs/cgroup
Cgroup device: enabled
Cgroup sched: enabled
Cgroup cpu account: enabled
Cgroup memory controller: enabled
Cgroup cpuset: enabled

--- Misc ---
Veth pair device: enabled, not loaded
Macvlan: enabled, not loaded
Vlan: enabled, not loaded
Bridges: enabled, loaded
Advanced netfilter: enabled, loaded
CONFIG_IP_NF_TARGET_MASQUERADE: enabled, not loaded
CONFIG_IP6_NF_TARGET_MASQUERADE: enabled, not loaded
CONFIG_NETFILTER_XT_TARGET_CHECKSUM: enabled, not loaded
CONFIG_NETFILTER_XT_MATCH_COMMENT: enabled, not loaded
FUSE (for use with lxcfs): enabled, not loaded

--- Checkpoint/Restore ---
checkpoint restore: enabled
CONFIG_FHANDLE: enabled
CONFIG_EVENTFD: enabled
CONFIG_EPOLL: enabled
CONFIG_UNIX_DIAG: enabled
CONFIG_INET_DIAG: enabled
CONFIG_PACKET_DIAG: enabled
CONFIG_NETLINK_DIAG: enabled
File capabilities: enabled

Note : Before booting a new kernel, you can check its configuration
usage : CONFIG=/path/to/config /usr/bin/lxc-checkconfig
```

- テンプレートイメージ
