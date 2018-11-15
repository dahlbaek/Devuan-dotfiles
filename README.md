# dotfiles for my Ubuntu setup

A collection of my dotfiles (mostly minor tweaks to default configs) along with
some simple setup instructions for Devuan ASCII.

## get git

Get git by running the command

```sh
sudo apt-get install git
```

## clone the repository

Further instructions and scripts assume that `git` has been used to clone the
repository to `~/git/dotfiles`

```sh
git clone https://github.com/dahlbaek/Devuan-dotfiles.git "${HOME}/git/dotfiles"
```

## iptables

Copy the `iptables.sh` and `open-iptables.sh` scripts to a secure location,
using the commands

```sh
sudo cp "${HOME}/git/dotfiles/home/.config/iptables/iptables.sh" /usr/local/bin/iptables.sh
sudo cp "${HOME}/git/dotfiles/home/.config/iptables/open-iptables.sh" /usr/local/bin/open-iptables.sh
```

You may need to create one or more directories first. Check that the file modes
are correctly set.

Save open firewall rules, and set and save restrictive firewall rules

```sh
sudo -- /bin/sh -- /usr/local/bin/open-iptables.sh
sudo -- /bin/sh -- /usr/local/bin/iptables.sh
```

As noted in the file `iptables.sh`, if you want to preserve firewall rules
between sessions (and you do), you need to add `iptables-restore
</etc/iptables.conf` to `/etc/rc.local`.

If you want to disable the firewall in the future, you can do that by running

```sh
sudo iptables-restore /etc/open-iptables.conf
```

Similarly, the rules may be enabled again by running

```sh
sudo iptables-restore /etc/iptables.conf
```

## sudoers

Allow yourself to update, upgrade and set iptables rules without typing in a
password. This can be done using the command `sudo visudo`, which is a safe way
to edit the file `/etc/sudoers`. I do not want the terminal to remember my
password, which can be controlled with the `timestamp_timeout`. So, add the line

```
Defaults	timestamp_timeout=0
```

and change the line

```
%sudo	ALL=(ALL:ALL) ALL
```

to

```
%sudo	ALL=(ALL:ALL) ALL, NOPASSWD: /usr/bin/apt-get -- update, /usr/bin/apt-get -- upgrade, /bin/sh -- /usr/local/bin/iptables.sh
```

## standard programs

Download `atom-amd64.deb` from the [homepage](https://atom.io/) and run

```sh
sudo apt-get update
sudo apt-get install ./atom-amd64.deb curl gnupg2 i3 ranger xserver-xorg-input-synaptics zathura
sudo apt-get upgrade
```

## setup

Recursively create symlinks to dotfiles

```sh
cp --force --no-dereference --preserve=all --recursive --symbolic-link --verbose -- "${HOME}/git/dotfiles/home/." "${HOME}" >"${HOME}/git/dotfiles/setup.log"
```
