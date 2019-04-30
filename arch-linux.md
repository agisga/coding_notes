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
