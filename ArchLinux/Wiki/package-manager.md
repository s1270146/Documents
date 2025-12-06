# パッケージマネージャー

## pacman
```bash
# search
# avilable packages
pacman -Ss <packages>
# installed packages
pacman -Qs <packages>
```

## yay

```bash
# install
yay -S <package name>

remove
# package only
yay -R <package name>
# package and unneed dependencies
yay -Rs <package name>
# package, unneed dependencies, and config files
yay -Rns <package name>
```

