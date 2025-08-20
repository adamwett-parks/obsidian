## Install these:
- [ ] [Adobe Creative Cloud](https://helpx.adobe.com/download-install/apps/download-install-apps/creative-cloud-apps/download-creative-cloud-desktop-app-from-web.html)
	- brew install --cask adobe-creative-cloud
	- [ ] Acrobat
	- [ ] Photoshop
	- [ ] Illustrator
- [ ] Cisco Secure Client
- [ ] [DBeaver](https://dbeaver.io/files/dbeaver-ce-latest-macos-aarch64.dmg)
	- brew install –cask dbeaver-community
- [ ] [Docker Desktop](https://www.docker.com/products/docker-desktop/)
	- brew install --cask docker
- [ ] [Office Suite](https://www.microsoft.com/en-us/microsoft-365/download-office)
	- [ ] [Teams](https://www.microsoft.com/en-us/microsoft-teams/download-app)
		- brew install --cask microsoft-teams
	- [ ] [Outlook](https://apps.apple.com/us/app/microsoft-outlook/id985367838?mt=12&culture=en-us&country=us&srcurl=https%3A%2F%2Fwww.microsoft.com%2Fmicrosoft-365%2Foutlook%2Foutlook-for-mac)
		- brew install --cask microsoft-outlook
	- [ ] Word
		- brew install --cask microsoft-word
	- [ ] Excel
		- brew install --cask microsoft-excel
	- [ ] PowerPoint
		- brew install --cask microsoft-powerpoint
	- [ ] [OneDrive](https://www.microsoft.com/en-us/microsoft-365/onedrive/download#Downloadtheapp)
		- brew install --cask onedrive
- [ ] [Figma](https://www.figma.com/download/desktop/mac)
	- brew install --cask figma
- [ ] [Firefox](https://www.mozilla.org/en-US/firefox/mac/)
	- brew install --cask firefox
- [ ] [Chrome](https://www.google.com/chrome/)
	- brew install --cask google-chrome
- [ ] [Edge](https://www.microsoft.com/en-us/edge/download)
	- brew install --cask microsoft-edge
- [ ] [GitHub](https://docs.github.com/en/desktop/installing-and-authenticating-to-github-desktop/installing-github-desktop?platform=mac)
	- brew install --cask github
- [ ] [MySQL](https://dev.mysql.com/downloads/workbench/)
	- [ ] Workbench
		- brew install --cask mysqlworkbench
	- [ ] Server
		- brew install mariadb
- [ ] [Obsidian](https://obsidian.md/download)
	- brew install --cask obsidian
- [ ] [Slack](https://apps.apple.com/us/app/slack-for-desktop/id803453959)
	- brew install --cask slack
- [ ] [Webex](https://www.webex.com/downloads.html)
	- brew install --cask webex
- [ ] [VS Code](https://code.visualstudio.com/)
	- brew install --cask visual-studio-code

## Set this up
- [ ] Printer
	- P-DENR-NRC-2507 - 2nd floor workroom
	- P-DENR-NRC-3507 - 3rd floor workroom
- [ ] SSH Shortcuts
	- ~/.zshrc
		- alias ssh-priv-test='ssh jelliott12@10.35.152.9'
		- alias ssh-priv-prod='ssh jelliott12@10.35.152.23'
		- alias ssh-pub-prod='ssh jelliott12@204.211.255.170'
- [ ] DB Connections (all using NCID as username)
	- PRIV PROD - 10.35.80.181:3309
	- PRIV TEST - 10.35.152.9:3309
	- PUB PROD - 10.35.152.20:3308
	- PUB TEST - 10.35.152.9:3308
- [ ] Clone these repos
	- https://github.com/ncdpr/parks-passport.git
	- https://github.com/ncdpr/priv-apps.git
	- https://github.com/ncdpr/pub-apps.git
	- https://github.com/ncdpr/Version-2.git




Steps:
```
#!/bin/bash
set -e # Exit on error

`# Homebrew cask apps
cask_apps=(
  adobe-creative-cloud
  dbeaver-community
  docker
  microsoft-teams
  microsoft-office
  figma
  firefox
  google-chrome
  microsoft-edge
  github
  mysqlworkbench
  obsidian
  slack
  webex
  visual-studio-code
)

# Homebrew formulae
brew_apps=(
  mariadb
)

# Confirm user is running the script as the admin user
echo "Please make your user account an admin user. You can do this by going to system preferences -> user accounts -> unlock changes -> click your user -> change account type to admin."
read -p "If you have done this, type y to continue or any other key to exit and try again: " netconfirm
[[ "$netconfirm" != "y" ]] && { echo "Exiting setup."; exit 1; }

# Confirm user is on the state network for setting up printers
echo "Please connect via Ethernet to the state network (do not use the VPN)."
read -p "Once you have done this, type y to continue or any other key to exit: " netconfirm
[[ "$netconfirm" != "y" ]] && { echo "Exiting setup."; exit 1; }

# Get the user's NCID for setting up the aliases
read -p "Enter your NCID (username): " ncid

WORKDIR=~/git
mkdir -p "$WORKDIR"
cd "$WORKDIR"

# Install Homebrew if not already installed
if ! command -v brew &> /dev/null; then
  echo "Installing Homebrew..."
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi
echo "Checking for Homebrew updates..."
brew update

# Set up our printers
echo "Setting up printers. You may be asked for the admin user's password..."
sudo lpadmin -p "Second_Floor_Printer" \
  -L "Second Floor" \
  -D "Second Floor Printer" \
  -E \
  -v socket://10.252.112.13 \
  -m everywhere
sudo lpadmin -p "Third_Floor_Printer" \
  -L "Third Floor" \
  -D "Third Floor Printer" \
  -E \
  -v socket://10.252.112.8 \
  -m everywhere

# Install required applications
echo "Installing required applications with Homebrew..."
for app in "${cask_apps[@]}"; do
  brew install --cask "$app"
done
for app in ${brew_apps[@]}"; do
  brew install "$app"
done

# Clone the team's repos
echo "Cloning our team's repos. You may be asked for your GitHub username and Personal Access Token..."
git config --global credential.helper store
for repo in parks-passport priv-apps pub-apps Version-2; do
  if [! -d "$repo" ]; then
    git clone "https://github.com/ncdpr/$repo.git"
  else
    echo "Repo $repo already exists; skipping."
done

# Set up some terminal aliases so we don't have to remember server IP addresses
echo "Setting up aliases for terminal SSH access..."
grep -qxF "alias ssh-priv-test='ssh ${ncid}@10.35.152.9'" ~/.zshrc || echo "alias ssh-priv-test='ssh ${ncid}@10.35.152.9'" >> ~/.zshrc
grep -qxF "alias ssh-priv-prod='ssh ${ncid}@10.35.152.23'" ~/.zshrc || echo "alias ssh-priv-prod='ssh ${ncid}@10.35.152.23'" >> ~/.zshrc
grep -qxF "alias ssh-pub-prod='ssh ${ncid}@204.211.255.170'" ~/.zshrc || echo "alias ssh-pub-prod='ssh ${ncid}@204.211.255.170'" >> ~/.zshrc
```