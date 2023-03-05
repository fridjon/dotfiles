# Mac setup and dotfiles

Tools and instructions to speed up and automate my setup and configurations after clean re-installation of MacOS.

## Requirements

MacOS, the following setup has a clean installation in mind.

## Make your own

If you want to make your own setup based on this one I recommend the following steps:

- Fork the repository
- Edit the Brewfile
  - Some apps however are required for later steps (e.g. nerd-font, iTerm2, mas, nvm, neofetch, jq, rbenv, p10k, zsh-\*)
- Edit settings.sh to suit your own preferences
  - The github repo https://github.com/mathiasbynens/dotfiles contains a lot of like minded tweaks.
- Edit mixin/aliases
  - PS. before you start adding your own aliases I recommend running `alias` and looking at what is already there, a lot of stuff comes with the Oh-My-Zsh plugins.
- Follow the manual setup guide

# Manual Setup

The setup process is manual but I find it helpful to split it up into stages.

## Base stage

This stage sets up the bare necessities for the rest of the stages:

- Install the **XCode Command Line tools**
- Configure git account info for github.com
- Clone the dotfiles repository
- Install [**Homebrew**](https://brew.sh/)

### Install the **Xcode Command Line Tools**

```bash
xcode-select --install
```

### Configure git account info for github

If you are not Friðjón, change this to your info

```bash
git config --global user.name "Friðjón Guðjohnsen"
# Redacted to protect the innocent
git config --global user.email "*******@gmail.com"
git config --global github.user fridjon
git config --global core.excludesfile ~/.gitignore
echo .DS_Store >> ~/.gitignore
```

### Clone the dotfiles repository

```bash
git clone https://github.com/fridjon/dotfiles
# Hide the dotfiles folder so it does not show up in finder
chflags hidden dotfiles
```

### Install [**Homebrew**](https://brew.sh/)

```bash
# Will prompt for password and requires [enter]. Also need to run two manual commands that are output
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

Allow apps downloaded from anywhere before installing with Brew

```bash
sudo spctl --master-disable
```

## shell stage

This stage sets up the shell environment in a nice and pleasing way. Firs

### Install applications from **base-brewfile**

```bash
cd ~/dotfiles
brew bundle --file brews/Brewfile-base -v
# dotnet-sdk requires password, expressvpn requires password, msodbcsql requires YES, mssql-tools requires YES (twice)
brew bundle --file brews/Brewfile-frameworks -v
brew bundle --file brews/Brewfile-apps -v
brew bundle --file brews/Brewfile-docker -v
```


## MacOS system preferences

Run `settings.sh` to apply custom preferences for Finder, Menu bar, Dock etc.

> These settings are what is most likely to break as the preferences and corresponding files change between Mac OS versions. To be safe, skip running this script (except the last osascript step in the file that updates the look of terminal.app) and change settings manually.

Also note that the github repo https://github.com/mathiasbynens/dotfiles contains a lot of like minded tweaks.

```bash
cd ~/dotfiles
chmod +x settings.sh
./settings.sh
```

## Make the shell awesome with iTerm2, Zsh, Oh-My-Zsh, Powerlevel10k & neofetch

![iTerm2 Screenshot](https://i.imgur.com/NLdVDPS.png "iTerm2 after customization")

Set preferences for iTerm2

```bash
defaults write com.googlecode.iterm2.plist PrefsCustomFolder -string "~/dotfiles/iterm2"
defaults write com.googlecode.iterm2.plist LoadPrefsFromCustomFolder -bool true
```

Install **[Oh-My-Zsh](https://github.com/robbyrussell/oh-my-zsh)**

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

### Zsh Profile +

Source the ZSH profile and other config files (optionally restart Terminal/iTerm2 after creating the symlink)

```bash
# Symlink neofetch config and .vimrc
touch ~/.hushlogin
mkdir -p ~/.config/neofetch
ln -svf ~/dotfiles/neofetch/config.conf ~/.config/neofetch/
ln -svf ~/dotfiles/.vimrc ~/
# Symlink .zshrc
ln -svf ~/dotfiles/.zshrc ~/
source ~/.zshrc
```

If you get compaudit insecure directories error run:

```bash
compaudit | xargs chmod g-w
compaudit | xargs chmod o-w
```

###########################



If you are on Apple silicon you might want to install Rosetta 2 in order to run x86 code by emulation. This is also needed for certain dockers only available as x86, most notoriously SQL Server.

```bash
softwareupdate --install-rosetta
```

TODO: Add stuff to configure docker to use Rosetta2

## SSH keys

With **1password** installed I get my **SSH-keys** from saved documents and move to `~/.ssh` and make sure permissions are correct

```bash
chmod 600 ~/.ssh/id_rsa
chmod 600 ~/.ssh/id_rsa.pub
chmod 700 ~/.ssh
```

If you have not created a ssh key, or wish to create a new one  you can do that with the following.

```bash
cd ~/.ssh
ssh-keygen -t ed25519 -C "*****@gmail.com"
```
Create `~/.ssh/config` and modify it to automatically load keys into ssh-agent and store passphrases in keychain.

```bash
touch ~/.ssh/config
# Add to following to the config file
Host *
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_rsa
```

Add the private key to the `ssh-agent` and store the potential passphrase in the keychain.

```bash
ssh-add --apple-use-keychain ~/.ssh/github-220305
```

If the public **SSH-key** has been added to [GitHub](https://github.com/settings/ssh), the connection can be tested

```bash
ssh -T git@github.com
```

### GPG keys, Github

Setup `~/.gnupg` folder from backup, checkout this [gist](https://gist.github.com/troyfontaine/18c9146295168ee9ca2b30c00bd1b41e) for how to GPG.

```bash
gpg -k --keyid-format LONG # Copy the key for next step
git config --global user.signingkey <KEY>
git config --global commit.gpgsign true
git config --global gpg.program $(which gpg)
echo "pinentry-program /usr/local/bin/pinentry-mac" > ~/.gnupg/gpg-agent.conf
echo "use-agent" > ~/.gnupg/gpg.conf
```

## MacOS system preferences

Run `settings.sh` to apply custom preferences for Finder, Menu bar, Dock etc.

> These settings are what is most likely to break as the preferences and corresponding files change between Mac OS versions. To be safe, skip running this script (except the last osascript step in the file that updates the look of terminal.app) and change settings manually.

```bash
cd ~/dotfiles
chmod +x settings.sh
./settings.sh
```

## Make the shell awesome with iTerm2, Zsh, Oh-My-Zsh, Powerlevel10k & neofetch

![iTerm2 Screenshot](https://i.imgur.com/NLdVDPS.png "iTerm2 after customization")

Set preferences for iTerm2

```bash
defaults write com.googlecode.iterm2.plist PrefsCustomFolder -string "~/dotfiles/iterm2"
defaults write com.googlecode.iterm2.plist LoadPrefsFromCustomFolder -bool true
```

Install **[Oh-My-Zsh](https://github.com/robbyrussell/oh-my-zsh)**

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

### Zsh Profile +

Source the ZSH profile and other config files (optionally restart Terminal/iTerm2 after creating the symlink)

```bash
# Symlink neofetch config and .vimrc
touch ~/.hushlogin
mkdir -p ~/.config/neofetch
ln -svf ~/dotfiles/neofetch/config.conf ~/.config/neofetch/
ln -svf ~/dotfiles/.vimrc ~/
# Symlink .zshrc
ln -svf ~/dotfiles/.zshrc ~/
source ~/.zshrc
```

If you get compaudit insecure directories error run:

```bash
compaudit | xargs chmod g-w
compaudit | xargs chmod o-w
```

### Ruby and Gems

Install latest Ruby version and `colorls` for colorful `ls` with icons. Aliased to `lc` and `lcc`

```bash
rbenv install $(rbenv install -l | grep -v - | tail -1)
rbenv global $(rbenv install -l | grep -v - | tail -1)
```

Restart your terminal before installing gems

```bash
gem install colorls
```

## Python

With `pyenv` installed, get the latest version of python

```bash
PYTHON_LATEST=$(pyenv install --list | sed 's/^  //' | grep '^\d' | grep --invert-match 'dev\|a\|b' | tail -1)
PYTHON_CONFIGURE_OPTS="--enable-framework"
pyenv install $PYTHON_LATEST
pyenv global $PYTHON_LATEST
pip install --upgrade pip
```

## Node

Setup Node with nvm

```bash
mkdir ~/.nvm
# nvm needs python 2.7
pyenv install 2.7
pyenv shell 2.7
nvm install 16
nvm install 18
nvm alias default 18
nvm use default
```

Install global packages from `npmfile`

```bash
cat ~/dotfiles/npmfile | xargs -L1 npm i -g > /dev/null
```

## Visual Studio Code settings

I sync plugins and settings to Visual Studio Code with the [_Settings sync_](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync) plugin. It requires a GitHub API Token with gist access. Check out the [configuration instructions](https://shanalikhan.github.io/2016/07/31/Visual-Studio-code-sync-setting-edit-manually.html). I store my GitHub API Token in a secure note with 1password.

My currently configured plugins can be found with:

```bash
curl -s https://api.github.com/gists/8b47741d950a86e46222eb8bfc293a9a \
| jq '.files."extensions.json".raw_url' \
| tr -d \" \
| xargs curl -s \
| grep name
```

## Private Config files

I keep some config files, shell aliases and application preferences in a [private branch](https://24ways.org/2013/keeping-parts-of-your-codebase-private-on-github/) of this repository and copy them into their designated destination after installing the apps.
These files might contain Software Licenses, network addresses and other private data.

## Other Software

Here's a list of other software that doesn't have a homebrew package

```
Adobe Lightroom
Adobe Photoshop
Canon EOS Utility
```

## Stay up to date

Run these commands regularly to stay up to date

```bash
# Brew upgrade, update and cleanup
bubu
# npm update
npmu
```

## Credits

Thanks to the [dotfiles community](http://dotfiles.github.io/) and the creators/contributors there. Many of the aliases, settings etc. are borrowed from the repositories found there.
