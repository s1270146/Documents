# コンテナ

## Docker

### インストール

```bash
sudo zypper install docker docker-compose
```

### 起動設定

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

### ユーザーを `docker` グループへ追加

```bash
grep docker /etc/group
sudo usermod -aG docker <ユーザー名>
newgrp docker
```

## メモ

- `docker-compose` も `zypper` で入れる
- グループ追加後は再ログインか `newgrp docker` が必要
