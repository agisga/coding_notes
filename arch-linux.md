## AUR

### Manually install a package form the AUR

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