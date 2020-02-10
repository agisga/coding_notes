## Installation

Follow the installation guide from ArchWiki. Some noteworthy details on my last install are given below.

__Partitions__

* Use GPT.
* Create only two partitions: a 512M EFI System Partition (ESP) which should be mounted as `/boot`, and a `/` partition. For example, `/dev/sda1` and `/dev/sda2`.
* [Use `fdisk` to partition](https://wiki.archlinux.org/index.php/Fdisk#Create_a_partition_table_and_partitions). After you create a new partition with `n` use `t` command in `fdisk` to set partition type -- "EFI system" and "Linux root (x86-64)" respectively.

__Encryption (entire system)__

* Simplest option seems [LUKS on a partition](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#LUKS_on_a_partition) to encrypt the entire `/` partition.
* Configure the boot loader (see below). This is done together with configuration of `mkinitcpio` etc. *after* the base system is installed and you are `chroot`ed into the new system.


__Boot loader__

* Before working on the boot loader do
    ```
    pacman -S intel-ucode
    ```
* Use `systemd-boot` (follow the wiki carefully).
* Here is my `/boot/loader/entries/arch.conf` file that allows to unlock the encrypted `/` at boot, and that supports hibernation:
    ```
    title   Arch Linux
    linux   /vmlinuz-linux
    initrd    /intel-ucode.img
    initrd    /initramfs-linux.img
    options   cryptdevice=UUID=<fill in uuid of /dev/sda2>:cryptroot root=/dev/mapper/cryptroot resume=/dev/mapper/cryptroot resume_offset=<fill in offset>
    ```
    Use `blkid` to figure out UUID of `/dev/sda2`.
    For `resume_offset` see ArchWiki.
* Note: You need to run `mkinitcpio -P` *after* you edit `/etc/mkinicpio.conf`.

__Other__

* Install `base-devel linux-headers linux-lts linux-lts-headers networkmanager wireless_tools wpa_supplicant git` (network related packages needed to not be left without the possibility to configure internet access after reboot).
* Run `mkinitcpio -p linux-lts` (in addition to `mkinitcpio -p linux`) and create `/boot/loader/entries/arch-lts.conf` (in addition to `arch.conf`, see <https://bbs.archlinux.org/viewtopic.php?id=235116>) if `lts` kernel is installed.
* Run `systemctl enable NetworkManager`.
* Create swap file. See above and on ArchWiki about what to add for `resume` and `resume_offset`, and how to edit `mkinitcpio.conf`.

__Gnome__

* Pick individual packages from `gnome-extra`. It's mostly useless.


## Random useful stuff

* Bluntly restart networking: `sudo systemctl restart NetworkManager`

## AUR

### Manually install a package from the AUR

1. Use `git clone` to acquire the build files.
2. Verify that the PKGBUILD and accompanying files are not malicious or untrustworthy.
3. Run `makepkg -sic` in the directory where the files are saved. This will download the code, resolve the dependencies with pacman, compile it, package it, and install the package.
    * `-s/--syncdeps` automatically resolves and installs any dependencies with pacman before building. If the package depends on other AUR packages, you will need to manually install them first.
    * `-i/--install` installs the package if it is built successfully. Alternatively the built package can be installed with `pacman -U package.pkg.tar.xz`.
    * `-c/--clean` cleans up temporary build files after the build, as they are no longer needed.

### Manually update a package from the AUR

0. Check for and inspect new changes by running `git fetch` and `git diff origin/master`.
1. Pull updates to the build files with `git pull`.
2. Verify that the PKGBUILD and accompanying files are not malicious or untrustworthy.
3. Run `makepkg`.
4. Run `sudo pacman -U PACKAGE_NAME.pkg.tar`.


## ABS

<https://wiki.archlinux.org/index.php/ABS>

### Use ABS to install newer/older/beta/devel version of a package

#### Retrieve PKGBUILD source using Git

You need the [asp](https://www.archlinux.org/packages/?name=asp) package installed.

For example, this clones the git repository of the [anki](https://www.archlinux.org/packages/community-testing/x86_64/anki/) package:

```
asp checkout anki
```

You will find a directory with subdirectories `trunk` and `repos`:

> PKGBUILD's and files found in repos are used in official builds. Files found in trunk are used by developers in preparation before being copied to repos.

#### Build package

1. Copy the directory containing the desired PKGBUILD (probably one of the subdirectories of `repos`) to a new location.
2. Make modifications to the PKGBUILD if necessary.
3. Create and install the new package with `makepkg` (see <https://wiki.archlinux.org/index.php/Makepkg#Usage>):
    - Build the package: `makepkg --syncdeps`
    - Install the built package, while cleaning up leftover files and folders: `makepkg --install --clean` (here `makepkg --install` is the same as `pacman -U pkgname-pkgver.pkg.tar.xz`)

## Pacman

### Cleaning the package cache

<https://wiki.archlinux.org/index.php/pacman#Cleaning_the_package_cache>

To discard unused packages weekly *enable* and *start* `paccache.timer`:

```
$ systemctl enable paccache.timer
Created symlink /etc/systemd/system/timers.target.wants/paccache.timer → /usr/lib/systemd/system/paccache.timer.
$ systemctl start paccache.timer
```

As far as I can tell, it will run `paccache -r` on a weekly basis, which deletes all cached versions of installed and uninstalled packages, except for the most recent 3.
