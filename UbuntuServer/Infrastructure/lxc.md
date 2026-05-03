# LXC

## 概要

Ubuntu Server 上で LXC を使うための前提条件と導入メモをまとめる。

## 前提確認

### C ライブラリの確認

```bash
ldd --version
```

確認例

```text
ldd (Ubuntu GLIBC 2.39-0ubuntu8.6) 2.39
```

### カーネルバージョンの確認

```bash
uname -r
```

確認例

```text
6.8.0-90-generic
```

## 必要条件

### `lxc-attach` の動作に必要な環境

- Linux カーネル 3.8 以上
- 参考: <https://linuxcontainers.org/ja/lxc/manpages/man1/lxc-attach.1.html>

`lxc-attach` は、実行中のコンテナ内で新しいプロセスを開始するために使う。

例

```bash
lxc-attach -n container
```

### 非特権コンテナに必要な環境

- `libpam-cgfs`
- `newuidmap` と `newgidmap` を含む新しめの `shadow`
- Linux カーネル 3.12 以上

補足

- PAM モジュールは認証方式を一元管理する仕組み
- `/etc/shadow` にはハッシュ化されたパスワードが保存され、通常ユーザーからは読めない

参考

- <https://qiita.com/ss12345/items/5b9e4edffb550281f43e>
- <https://linuc.org/study/knowledge/510/>
- <https://linuc.org/study/column/7308/>

## 推奨ライブラリ

- `libcap`: Linux ケーパビリティの設定と参照に使う
- `libapparmor`: AppArmor プロファイル設定に使う
- `libselinux`: SELinux コンテキスト設定に使う
- `libseccomp`: seccomp ポリシー設定に使う
- `libgnutls`: TLS 関連や各種チェックサム確認に使う
- `liblua`: Lua バインディングに使う
- `python3-dev`: Python バインディングに使う

## インストール

```bash
sudo apt install lxc
```

## 動作確認

```bash
lxc-checkconfig
```

- 必要な namespace、cgroup、network 機能が有効か確認する
- 新しいカーネルを試す前も `lxc-checkconfig` で確認できる
