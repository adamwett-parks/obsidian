---
title: Windows Subsystem for Linux
author: Adam Wett
---

# WSL
Windows sucks for devs. Use WSL as much as you can and your life will be easier.

## Install checklist
1. Log out of your Windows account
2. While you are connected to ethernet, have someone with an admin account populate to their admin account on your machine (John or Cathy at the time of writing this)
3. Open the Microsoft Store and search for "Ubuntu", install it
5. Run `wsl --install` in Terminal just to be sure
6. Run `wsl` to connect to a shell in Ubuntu
7. Set your root password (I recommend using NCID/M365 password)
8. Log out of the admin acct and log back in to your Windows account
9. Open a new Terminal window and run `wsl --install` again
10. Run `wsl` again
11. Run `sudo apt update && sudo apt upgrade -y`
12. Profit

## Nuances
- WSL will not be able to connect to the internet while you're connected to the VPN.
