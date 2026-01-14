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
    - 名前ベース: "セキュリティ設定を対象となるファイルパスをもとに設定する"
    - 強制アクセス制御(MAC): パーミッションより強い、管理者ですら強制的にアクセス制御の影響を受ける仕組み
    - LSM(Linux Security Module): AppArmorやSELinuxのような機能を実現するためのフレームワーク
  - コンテナに対して独自のapparmorプロファイルを設定するために必要
