# PKGS Cheater

Arch Linux uses **Pacman** as its official package manager, which handles packages from the official repositories. For community-maintained packages not in the official repos, the **Arch User Repository (AUR)** is used, typically via an **AUR helper** like **Yay**. This cheatsheet covers essential commands and practices for managing packages on Arch Linux.

---

## 1. Original Package Management Snippets

This section contains your original collection of `pacman`, `yay`, and other package-related commands and headings, exactly as they were provided. Detailed explanations and expanded usage examples for these and many more operations can be found in the subsequent sections.

---

## PACMAN (Basic)

### Install
```bash
sudo pacman -S <package_name>
```

### Remove
```bash
sudo pacman -R <package_name>
```

### Remove (clean)
```bash
sudo pacman -Rs <package_name>
```

### Remove (super clean)
```bash
sudo pacman -Rns <package_name>
```

### Update
```bash
sudo pacman -Syu
```

### Force update
```bash
sudo pacman -Syyu
```

### Download only
```bash
sudo pacman -Sw <package_name>
```

### Install local
```bash
sudo pacman -U <pkgfile.pkg.tar.xz>
```

### Clean cache (old versions)
```bash
sudo pacman -Sc
```

### Clean cache (all)
```bash
sudo pacman -Scc
```

---

## YAY (AUR)

### Install
```bash
yay -S <package_name>
```

### Remove
```bash
yay -R <package_name>
```

### Remove (clean)
```bash
yay -Rs <package_name>
```

### Remove (super clean)
```bash
yay -Rns <package_name>
```

### Update
```bash
yay -Syu
```

### Force update
```bash
yay -Syyu
```

### Clean cache
```bash
yay -Yc
```

### Show status
```bash
yay -Ps
```

### Update AUR database
```bash
yay -Y --gendb
```

### Update (all AUR packages)
```bash
yay -Qua
```

### Install (if not installed)
```bash
yay -S --needed <package_name>
```

### Mflags (meson building)
```bash
yay --mflags "CC=gcc CGO_ENABLED=1" -S tldr++
```

### Overwrite (skip integrity)
```bash
yay -S --needed --noconfirm pkgname --mflags --skipinteg
```

---

## REINSTALL ALL PACKAGES

### PACMAN
```bash
sudo pacman -Qnq | sudo pacman -S --noconfirm --overwrite='*' -
```

### YAY
```bash
yay --verbose -S - < pkglist.txt --refresh --sysupgrade --sudoloop
```

---

## PICOM DEPENDENCIES

```bash
yay -S libx11 libxcomposite libxdamage libxfixes libXext libxrender libXrandr libXinerama pkg-config make xproto x11proto sh xprop xwininfo x11-utils libpcre libconfig libdrm libGL libdbus asciidoc docbook-xml-dtd libxml-utils libxslt xsltproc xmlto --needed --noconfirm --overwrite="*"
```

---

## WAYLAND NVIDIA (GDM)

```bash
systemctl enable --now nvidia-resume.service
```

---

## GARUDA SETTINGS MANAGER DEPENDENCIES

```bash
yay -S ckbcomp hwinfo kauth5 kcoreaddons5 kitemmodels5 lib32-at-spi2-core lib32-brotli lib32-bzip2 lib32-cairo lib32-colord lib32-curl lib32-dbus lib32-e2fsprogs lib32-expat lib32-fontconfig lib32-freeglut lib32-freetype2 lib32-fribidi lib32-gcc-libs lib32-gdk-pixbuf2 lib32-glew lib32-glib2 lib32-glibc lib32-glu lib32-gmp lib32-gnutls lib32-gtk3 lib32-harfbuzz lib32-icu lib32-keyutils lib32-krb5 lib32-lcms2 lib32-libcap lib32-libcups lib32-libdatrie lib32-libdecor lib32-libdrm lib32-libelf lib32-libepoxy lib32-libffi lib32-libgcrypt lib32-libglvnd lib32-libgpg-error lib32-libice lib32-libidn2 lib32-libjpeg-turbo lib32-libldap lib32-libnghttp2 lib32-libnghttp3 lib32-libnsl lib32-libpciaccess lib32-libpng lib32-libpsl lib32-librsvg lib32-libsm lib32-libssh2 lib32-libtasn1 lib32-libthai lib32-libtiff lib32-libtirpc lib32-libunistring lib32-libx11 lib32-libxau lib32-libxcb lib32-libxcomposite lib32-libxcrypt lib32-libxcursor lib32-libxdamage lib32-libxdmcp lib32-libxext lib32-libxfixes lib32-libxft lib32-libxi lib32-libxinerama lib32-libxkbcommon lib32-libxkbcommon-x11 lib32-libxml2 lib32-libxmu lib32-libxrandr lib32-libxrender lib32-libxshmfence lib32-libxt lib32-libxtst lib32-libxxf86vm lib32-llvm-libs lib32-lm_sensors lib32-mesa lib32-mesa-demos lib32-ncurses lib32-nettle lib32-ocl-icd lib32-openssl lib32-p11-kit lib32-pam lib32-pango lib32-pcre2 lib32-pixman lib32-systemd lib32-util-linux lib32-vulkan-icd-loader lib32-wayland lib32-xz lib32-zlib lib32-zstd libva-utils libx86emu mesa-demos mhwd-amdgpu mhwd-ati mhwd-db-garuda mhwd-garuda mhwd-nvidia mhwd-nvidia-390xx mhwd-nvidia-470xx perl-xml-parser perl-xml-writer polkit-qt5 vdpauinfo vulkan-tools garuda-settings-manager find-the-command-git kernel-modules-hook noto-color-emoji-fontconfig nss-mdns systemd-oomd-defaults update-grub --needed
```

---

## MESON BUILDING

```bash
meson setup build
meson compile -C build
meson install -C build
```

---

## LIBTOOL (Important Post Build Steps)

```bash
libtool --finish /usr/lib
```
*   Use libtool and specify the full pathname of the library.
*   Or use the -L flag during linking and one of the following:
*   Add LIBDIR to the LD_LIBRARY_PATH environment variable during execution.
*   Add LIBDIR to the LD_RUN_PATH environment variable during linking.
*   Use the -Wl,-rpath -Wl,LIBDIR linker flag.
*   Have your system administrator add LIBDIR to /etc/ld.so.conf.

---

## 2. Expanded Arch Linux Package Management Guide

This section provides a more structured and comprehensive guide to Arch Linux package management, building upon your original snippets and introducing additional functionalities.

### 2.1 Pacman (Official Repositories)

**Pacman** is the primary tool for installing, upgrading, and managing packages from the official Arch Linux repositories.

#### 2.1.1 Installation & Removal

*   **Install a package**: Your original command is included below.
    ```bash
    sudo pacman -S <package_name>
    ```
*   **Remove a package (keeping dependencies and config files)**: Your original command is included below.
    ```bash
    sudo pacman -R <package_name>
    ```
*   **Remove a package and its unneeded dependencies**: Your original command is included below.
    ```bash
    sudo pacman -Rs <package_name>
    ```
    *   **Explanation**: `s` flag removes dependencies that are no longer required by any other installed package.
*   **Remove a package, its unneeded dependencies, and configuration files**: Your original command is included below.
    ```bash
    sudo pacman -Rns <package_name>
    ```
    *   **Explanation**: `n` flag also removes configuration files for the package being uninstalled. This is often the cleanest way to remove a package.
*   **Install a local package file (`.pkg.tar.xz`)**: Your original command is included below.
    ```bash
    sudo pacman -U <pkgfile.pkg.tar.xz>
    ```
*   **Download a package but do not install it**: Your original command is included below.
    ```bash
    sudo pacman -Sw <package_name>
    ```

#### 2.1.2 System Update & Cache Management

*   **Synchronize package databases and update all installed packages**: Your original command is included below.
    ```bash
    sudo pacman -Syu
    ```
    *   **Explanation**: `S` (sync), `y` (refresh package databases), `u` (upgrade installed packages). This is the most common command for system updates.
*   **Force synchronization of package databases and update**: Your original command is included below.
    ```bash
    sudo pacman -Syyu
    ```
    *   **Explanation**: `yy` forces a refresh of *all* package databases, even if they are already up-to-date. Useful for resolving database corruption or if you've changed mirrors.
*   **Clean the package cache (remove old versions of installed packages)**: Your original command is included below.
    ```bash
    sudo pacman -Sc
    ```
*   **Clean the package cache (remove all packages, even current versions)**: Your original command is included below.
    ```bash
    sudo pacman -Scc
    ```
    *   **Explanation**: Use with caution as it removes all cached packages, meaning you can't downgrade or reinstall without re-downloading.

#### 2.1.3 Querying Packages

*   **List all installed packages**:
    ```bash
    pacman -Q
    ```
*   **Show detailed information about an installed package**:
    ```bash
    pacman -Qi <package_name>
    ```
*   **List all files owned by an installed package**:
    ```bash
    pacman -Ql <package_name>
    ```
*   **Search for a package in the databases**:
    ```bash
    pacman -Ss <keyword>
    ```
    *   **Example**: `pacman -Ss firefox`
*   **Show detailed information about a package in the databases (not necessarily installed)**:
    ```bash
    pacman -Si <package_name>
    ```
*   **Query which installed package owns a specific file**:
    ```bash
    pacman -Qo <file_path>
    ```
    *   **Example**: `pacman -Qo /usr/bin/vim`
*   **Search for files in the sync database (requires `pacman -Fy` first)**:
    ```bash
    sudo pacman -Fy # Update file database
    pacman -F <file_name_pattern>
    ```
    *   **Explanation**: This is useful for finding which official package *would* provide a file if installed. For more comprehensive file-to-package mapping, `pkgfile` is recommended (see Section 3).

#### 2.1.4 Orphaned Packages

*   **List orphaned packages (installed as dependencies but no longer needed)**:
    ```bash
    pacman -Qdt
    ```
*   **Remove orphaned packages**:
    ```bash
    sudo pacman -Rns $(pacman -Qdt)
    ```

#### 2.1.5 Package Configuration

*   **Mark a package as explicitly installed (no longer a dependency)**:
    ```bash
    sudo pacman -D --asexplicit <package_name>
    ```
*   **Mark a package as an installed dependency (no longer explicit)**:
    ```bash
    sudo pacman -D --asdeps <package_name>
    ```

### 2.2 Yay (AUR Helper)

**Yay** is a popular AUR helper that simplifies installing and managing packages from the **Arch User Repository (AUR)**, as well as handling official Pacman packages.

#### 2.2.1 Installation & Removal

*   **Install a package from AUR or official repos**: Your original command is included below.
    ```bash
    yay -S <package_name>
    ```
    *   **Explanation**: `yay` will first search AUR, then official repos.
*   **Remove a package (keeping dependencies and config files)**: Your original command is included below.
    ```bash
    yay -R <package_name>
    ```
*   **Remove a package and its unneeded dependencies**: Your original command is included below.
    ```bash
    yay -Rs <package_name>
    ```
    *   **Explanation**: `s` flag removes dependencies that are no longer required by any other installed package.
*   **Remove a package, its unneeded dependencies, and configuration files**: Your original command is included below.
    ```bash
    yay -Rns <package_name>
    ```
    *   **Note**: The `-n` flag for config files is not explicitly listed in the original content for `yay`, but `yay` generally mirrors `pacman` behavior. If an AUR package installs files outside of a standard FHS location for configuration, it might not be removed by `yay -Rns`.

#### 2.2.2 System Update & Cache Management

*   **Synchronize package databases and update all installed packages (official and AUR)**: Your original command is included below.
    ```bash
    yay -Syu
    ```
    *   **Explanation**: This is the equivalent of `pacman -Syu` but also checks for AUR updates.
*   **Force synchronization of package databases and update (official and AUR)**: Your original command is included below.
    ```bash
    yay -Syyu
    ```
*   **Clean Yay's build directory and package cache**: Your original command is included below.
    ```bash
    yay -Yc
    ```
*   **List all AUR packages that have updates available**: Your original command is included below.
    ```bash
    yay -Qua
    ```

#### 2.2.3 Querying Packages

*   **List all installed packages (official and AUR)**:
    ```bash
    yay -Q
    ```
*   **Show detailed information about an installed package (official or AUR)**:
    ```bash
    yay -Qi <package_name>
    ```
*   **Search for a package in AUR or official repos**:
    ```bash
    yay -Ss <keyword>
    ```
*   **Show detailed information about a package in the databases (AUR or official)**:
    ```bash
    yay -Si <package_name>
    ```

#### 2.2.4 Advanced Yay Usage

*   **Install a package if it's not already installed**: Your original command is included below.
    ```bash
    yay -S --needed <package_name>
    ```
    *   **Explanation**: `--needed` prevents re-installation if the package is already present.
*   **Print Yay's current status and statistics**: Your original command is included below.
    ```bash
    yay -Ps
    ```
*   **Generate `yay`'s AUR database**: Your original command is included below.
    ```bash
    yay -Y --gendb
    ```
    *   **Explanation**: Rebuilds the AUR database used by `yay`.
*   **Pass custom environment flags (`mflags`) during `makepkg` (AUR build)**: Your original command is included below.
    ```bash
    yay --mflags "CC=gcc CGO_ENABLED=1" -S tldr++
    ```
    *   **Explanation**: The `--mflags` option passes additional flags directly to `makepkg` (which `yay` uses under the hood), typically used for setting environment variables for the build process.
*   **Force rebuild of a package, skipping integrity checks (`--skipinteg`)**: Your original command is included below.
    ```bash
    yay -S --needed --noconfirm pkgname --mflags --skipinteg
    ```
    *   **Explanation**: This is a powerful command for troubleshooting. `--skipinteg` bypasses signature and checksum verification, which can be dangerous if the source is untrusted. `--needed` prevents re-installation if not strictly required, and `--noconfirm` bypasses prompts.

### 2.3 Reinstall All Packages (for System Recovery/Integrity)

Your original commands for reinstalling packages are included below.

#### Pacman (reinstall all explicitly installed native packages)
```bash
sudo pacman -Qnq | sudo pacman -S --noconfirm --overwrite='*' -
```
*   **Explanation**: `pacman -Qnq` lists all explicitly installed native packages (not AUR, not dependencies). This list is then piped to `pacman -S`, which attempts to reinstall them all. `--overwrite='*'` forces overwrite of conflicting files, and `--noconfirm` skips prompts. Use with extreme caution.

#### Yay (reinstall all explicitly installed packages, including AUR)
```bash
yay --verbose -S - < pkglist.txt --refresh --sysupgrade --sudoloop
```
*   **Explanation**: This command from the original content seems to imply a pre-existing `pkglist.txt` from which `yay` reads packages to reinstall. `--refresh`, `--sysupgrade`, and `--sudoloop` are `yay` flags for a full system update with sudo privilege retention.

### 2.4 Building & Development Utilities

Commands related to building packages or managing build environments.

#### `makepkg` (Building Local Packages)

`makepkg` is the utility used to build packages from source code (`PKGBUILD` files), which is what `yay` uses internally for AUR packages.

*   **Build a package in the current directory (where `PKGBUILD` is located)**:
    ```bash
    makepkg -s
    ```
    *   **Explanation**: `-s` ensures all dependencies are installed before building.
*   **Clean up build files after a successful build**:
    ```bash
    makepkg -c
    ```
*   **Install the locally built package**:
    ```bash
    sudo pacman -U <package_name>.pkg.tar.xz
    ```

#### Meson Building

Steps for building projects that use the Meson build system. Your original commands are included below.

```bash
meson setup build
meson compile -C build
meson install -C build
```

#### Libraries - Important Post-Build Steps (`libtool`)

After manually building and installing libraries, it's sometimes necessary to finalize `libtool`'s work to ensure the system's dynamic linker can find the new libraries. Your original commands and explanations are included below.

```bash
libtool --finish /usr/lib
```
*   Use libtool and specify the full pathname of the library.
*   Or use the -L flag during linking and one of the following:
*   Add LIBDIR to the LD_LIBRARY_PATH environment variable during execution.
*   Add LIBDIR to the LD_RUN_PATH environment variable during linking.
*   Use the -Wl,-rpath -Wl,LIBDIR linker flag.
*   Have your system administrator add LIBDIR to /etc/ld.so.conf.

### 2.5 Specific System Setups & Dependencies

A collection of commands for installing specific dependencies or configuring particular system features, often related to custom environments like Garuda Linux or specific hardware setups.

#### `picom` Dependencies

Your original command is included below.

```bash
yay -S libx11 libxcomposite libxdamage libxfixes libXext libxrender libXrandr libXinerama pkg-config make xproto x11proto sh xprop xwininfo x11-utils libpcre libconfig libdrm libGL libdbus asciidoc docbook-xml-dtd libxml-utils libxslt xsltproc xmlto --needed --noconfirm --overwrite="*"```
*   **Explanation**: Installs a comprehensive list of common and less common dependencies for `picom` (a compositor for X11) using `yay`. `--needed`, `--noconfirm`, and `--overwrite='*'` are used for robustness.

#### Wayland and NVIDIA on GDM

Your original command is included below.

```bash
systemctl enable --now nvidia-resume.service
```
*   **Explanation**: This systemd service helps in resuming NVIDIA graphics card functionality after suspend, which is often crucial for smooth operation with GDM on Wayland.

#### Garuda Settings Manager Dependencies

Your original command is included below.

```bash
yay -S ckbcomp hwinfo kauth5 kcoreaddons5 kitemmodels5 lib32-at-spi2-core lib32-brotli lib32-bzip2 lib32-cairo lib32-colord lib32-curl lib32-dbus lib32-e2fsprogs lib32-expat lib32-fontconfig lib32-freeglut lib32-freetype2 lib32-fribidi lib32-gcc-libs lib32-gdk-pixbuf2 lib32-glew lib32-glib2 lib32-glibc lib32-glu lib32-gmp lib32-gnutls lib32-gtk3 lib32-harfbuzz lib32-icu lib32-keyutils lib32-krb5 lib32-lcms2 lib32-libcap lib32-libcups lib32-libdatrie lib32-libdecor lib32-libdrm lib32-libelf lib32-libepoxy lib32-libffi lib32-libgcrypt lib32-libglvnd lib32-libgpg-error lib32-libice lib32-libidn2 lib32-libjpeg-turbo lib32-libldap lib32-libnghttp2 lib32-libnghttp3 lib32-libnsl lib32-libpciaccess lib32-libpng lib32-libpsl lib32-librsvg lib32-libsm lib32-libssh2 lib32-libtasn1 lib32-libthai lib32-libtiff lib32-libtirpc lib32-libunistring lib32-libx11 lib32-libxau lib32-libxcb lib32-libxcomposite lib32-libxcrypt lib32-libxcursor lib32-libxdamage lib32-libxdmcp lib32-libxext lib32-libxfixes lib32-libxft lib32-libxi lib32-libxinerama lib32-libxkbcommon lib32-libxkbcommon-x11 lib32-libxml2 lib32-libxmu lib32-libxrandr lib32-libxrender lib32-libxshmfence lib32-libxt lib32-libxtst lib32-libxxf86vm lib32-llvm-libs lib32-lm_sensors lib32-mesa lib32-mesa-demos lib32-ncurses lib32-nettle lib32-ocl-icd lib32-openssl lib32-p11-kit lib32-pam lib32-pango lib32-pcre2 lib32-pixman lib32-systemd lib32-util-linux lib32-vulkan-icd-loader lib32-wayland lib32-xz lib32-zlib lib32-zstd libva-utils libx86emu mesa-demos mhwd-amdgpu mhwd-ati mhwd-db-garuda mhwd-garuda mhwd-nvidia mhwd-nvidia-390xx mhwd-nvidia-470xx perl-xml-parser perl-xml-writer polkit-qt5 vdpauinfo vulkan-tools garuda-settings-manager find-the-command-git kernel-modules-hook noto-color-emoji-fontconfig nss-mdns systemd-oomd-defaults update-grub --needed
```
*   **Explanation**: An extremely long list of 32-bit and other dependencies, likely for comprehensive support within the Garuda Settings Manager. This command installs them using `yay` with `--needed`.

---

## 3. Best Practices & Tips

*   **Regular Updates**: Always run `sudo pacman -Syu` or `yay -Syu` regularly to keep your system secure and up-to-date.
*   **Read Before Installing AUR**: Before installing any AUR package, always inspect its `PKGBUILD` file to understand what it does.
*   **Avoid `--noconfirm` in Scripts**: While useful for one-off commands, avoid `--noconfirm` in scripts unless you fully understand the implications and trust the script.
*   **Backup**: Before major system changes or updates, consider backing up your system.
