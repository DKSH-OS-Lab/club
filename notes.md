# VM
[VMware Workstation Player](https://www.vmware.com/products/workstation-player.html)

[VirtualBox](https://www.virtualbox.org/wiki/Downloads)

# Linux
[repology](https://repology.org/)

[manned.org](https://manned.org/)

# Fedora
quick start
- [rpmfusion](https://docs.fedoraproject.org/en-US/quick-docs/setup_rpmfusion/)
	```sh
	dnf install -y \
		https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
		https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
	```
- [codecs](https://rpmfusion.org/Howto/Multimedia)
	```sh
	sudo sh <<- 'EOF'
		set -ex
		dnf swap ffmpeg-free ffmpeg --allowerasing
		dnf groupupdate multimedia sound-and-video --setop="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin
	EOF
	```
- [vaapi](https://fedoraproject.org/wiki/Firefox_Hardware_acceleration)
- [flatpak](https://flatpak.org/setup/Fedora)
	```sh
	flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
	```
- zsh
	```sh
	sudo dnf install -y zsh util-linux-user
	chsh -s "$(which zsh)"

	# oh-my-zsh
	sudo dnf install -y git
	uri="https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh"
	curl -LSf "${uri}" --tlsv1.3 | \
		ZSH="${XDG_DATA_HOME:-\${HOME\}/.local/share}/oh-my-zsh" sh -s -- --unattended
	git config --global oh-my-zsh.hide-info 1
	```
- [vscode](https://code.visualstudio.com/docs/setup/linux)
- portal
	```sh
	GTK_USE_PORTAL=1
	```
- ibus
	```sh
	GTK_IM_MODULE=ibus
	QT_IM_MODULE=ibus
	XMODIFIERS=@im=ibus
	```
packages
- chrome
- discord
- steam
- bottles

Offline updates
```sh
dnf -y install dnf-plugin-system-upgrade
dnf -y offline-upgrade download
dnf -y offline-upgrade reboot
```

Distro upgrade
```sh
sudo sh <<- 'EOF'
	set -ex
	dnf -y --refresh upgrade

	dnf -y install dnf-plugin-system-upgrade && \
		dnf -y system-upgrade download --releasever=38
	
	wait
EOF
```