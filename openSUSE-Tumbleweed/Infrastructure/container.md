# コンテナ系

## Docker

```bash
# install
sudo zypper install docker

# systemdで Dockerデーモンを管理
sudo systemctl enable docker
sudo systemctl start docker

# gid確認
cat /etc/group | grep docker 
# group追加
sudo usermod -aG docker <ユーザー名>
newgrp docker

```
