# My .files

My dotfiles, managed by [chezmoi](https://github.com/twpayne/chezmoi).

README and assorted scripts adapted from [politician's dotfiles](https://github.com/politician/dotfiles).

See previous version of my setup and configuration for macOS 10.15 Catalina through macOS 14 Sonoma laptops at [suzannealdrich's system-config](https://github.com/suzannealdrich/system-config/)

[If you want to fork it for your own usage](#forking)

## Setup a new mac

1. Install command line tools

   ```sh
   xcode-select --install
   ```

2. Insert Smart Card (Yubikey, Ledger, etc.)

3. Install Homebrew, GPG, Chezmoi and launch configuration

   ```sh
   # Install Homebrew
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   [[ $(arch) == "arm64" ]] && eval "$(/opt/homebrew/bin/brew shellenv)" || eval "$(/usr/local/bin/brew shellenv)"

   # Install GPG and Chezmoi
   brew install gpg chezmoi

   # Use configuration from this repo
   chezmoi init suzannealdrich --apply
   ```

4. My personal todo list of manual actions:

   - [ ] This script will open Docker Desktop. You must manually grant the needed permissions and enable start at boot.
   - [ ] Grant Full disk access to Terminal.
   - [ ] Reboot `sudo shutdown -r now`
   - [ ] Install Xcode `mas install 497799835` (was removed from this script because the 12GB+ download can be slow as hell)

## System preparation and package installation

This repo includes scripts to
[prepare system](run_once_before_1-prepare-system.sh.tmpl),
[configure system](run_once_before_2-configure-system-darwin.sh.tmpl), and
[install packages](run_once_before_3-install-packages-darwin.sh.tmpl). Run these once.


## Application configuration

This repo includes a [script to configure](run_once_after_configure-apps-darwin.sh.tmpl) several applications settings. Run this after installing the applications.

### Pull and apply latest changes from this repo

```sh
chezmoi update
```

### Edit files and push changes

For example, modify `~/.zshrc` (already managed by chezmoi)

```sh
chezmoi edit ~/.zshrc
```

Apply changes

```sh
chezmoi apply
```

Commit changes

```sh
chezmoi git add .
chezmoi git commit
chezmoi git push
```

### Before formatting a mac

Make sure all files are up to date with chezmoi

```sh
cd ~

# Export list of brew/cask/mas installed packages
brew bundle dump --force

# Re-add linked files
chezmoi re-add

# Re-add VSCode settings as multi-arch template
sed 's|'"$(brew --prefix)"'|{{ if eq .chezmoi.arch "arm64" }}/opt/homebrew{{ else }}/usr/local{{ end }}|' ~/Library/Application\ Support/Code/User/settings.json > $(chezmoi source-path)/private_Library/private_Application\ Support/private_Code/User/settings.json.tmpl
```

Review changes and commit

```sh
chezmoi git status

chezmoi git add .
chezmoi git commit
chezmoi git push
```

## Forking

If you want to fork this repo and modify it for your own personal/commercial usage, please do so freely, it is licensed accordingly (MIT).

Before you apply any settings, don't forget to:

- [Customize chezmoi settings](.chezmoi.toml.tmpl)
- Remove my encrypted files and eventually re-add your own. See which files are encrypted with `find $(chezmoi source-path) -type f -name "encrypted*.asc"`