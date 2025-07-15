---
title: Monorepo Setup
author: Adam Wett
---

# Developer Environment Setup

Make sure you follow these in this order! If you have any trouble, come ask me for help so I can improve the docs

*– Adam*

## VS Code
1. Install [WSL extension](https://marketplace.visualstudio.com/items?Name=ms-vscode-remote.remote-wsl)
2. Install [Remote Development extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)
3. Clone the repo **in WSL**
- if it's on your C:\ drive it will be [very  slow!!](https://stackoverflow.com/a/68974497)

```sh
sudo apt update
sudo apt install gh
gh auth login
# use SSH, login with browser window, press enter, middle click the link
cd ~
mkdir git
cd git
gh repo clone ncdpr/Version-2
cd Version-2
```

## Requirements

1. Install nvm & node
```sh
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
# reload your shell (ubuntu uses bash)
bash
nvm install --lts
```

2. Install pnpm
```sh
wget -qO- https://get.pnpm.io/install.sh | sh -
# reload your shell
bash
```

3. Install mariadb
```sh
sudo apt update
sudo apt install mariadb-server mariadb-client
sudo systemctl start mariadb
sudo systemctl enable mariadb
```

4. Setup mariadb
```sh
sudo mysql_secure_installation
```

This script will prompt you to:
- Set a root password (if not already set)
  - The default env uses `strong_password`
- Remove anonymous users
- Disable remote root login (localhost will work still)
- Remove test database
- Reload privilege tables

5. Make sure mariadb is running
```sh
sudo systemctl status mariadb
```

## Environment variables
**Do this in the root of the repo!**
1. Automatically copy the example env vars
```sh
pnpm env:copy
```

## Dependencies
**Do this in the root of the repo!**

1. Install dependencies
```sh
pnpm i
pnpm migrate
pnpm build
```

## Migrating

1. Run a migration to fill your db with the tables
```sh
pnpm db:migrate
```
