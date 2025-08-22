# Dracut Cheat-Sheet (Initial RAM Disk Generator)

**Dracut** is an event-driven generator that creates an initial RAM file system image (`initramfs`) used during the Linux boot process. This image contains the necessary modules, tools, and configuration to mount the actual root filesystem.

---

## 1. Basic Operation & Building Images

The standard `dracut` command automatically detects the hardware and builds an `initramfs` image for the currently running kernel.

### Simple Build (Current Kernel)

If no output file is specified, `dracut` will create the image in `/boot/initramfs-$(uname -r).img`.

```bash
sudo dracut -f
```
*Note: `-f` forces the overwrite of an existing image.*

### Build for Specific Kernel Version

Always specify the desired output file and the kernel version. Replace `VERSION` with your target kernel version (e.g., `5.15.0-78-generic`).

```bash
sudo dracut /boot/initramfs-VERSION.img VERSION
```

### Clean and Rebuild

If you encounter issues or just want a clean slate, you can explicitly remove and then rebuild the image.

```bash
sudo rm -f /boot/initramfs-VERSION.img
sudo dracut -f
```

---

## 2. Module Management

Modules are the drivers and tools included in the `initramfs`. By default, `dracut` is modular and tries to detect what is needed, but sometimes explicit inclusion or exclusion is required.

### Exclude Specific Modules

Use the `--omit` or `--no-hostonly` flag to prevent modules from being included. This is useful for reducing image size or resolving conflicts.

```bash
# Example: Prevents the installation of the network module
sudo dracut --omit "network" -f
```

### Add Specific Modules

Use the `--add` flag to force the inclusion of necessary modules, especially when performing a migration or fixing obscure hardware detection issues.

```bash
# Example: Adding the BTRFS and crypt modules
sudo dracut --add "btrfs crypt" -f
```

### Full Host-Only Mode (Smallest Image)

The default behavior, but sometimes explicitly useful. This builds the smallest possible image containing only modules needed for the current host's hardware.

```bash
sudo dracut --hostonly -f
```

---

## 3. Debugging & Advanced Flags

These flags help troubleshoot boot failures or connectivity issues within the early boot process.

### Increase Verbosity

Use `-v` for more detailed output during the build process, which can help diagnose module detection issues.

```bash
sudo dracut -v -f
```

### Debug Mode (Entering Shell)

This is critical for troubleshooting. Adding `rd.break` to the kernel command line in your bootloader (GRUB) will halt the boot process and drop you into a shell inside the `initramfs`.

If your issue relates to network setup:
```bash
# Add this to GRUB line:
rd.break=cmdline
```

If your issue relates to early mounting problems:
```bash
# Add this to GRUB line:
rd.break=pre-mount
```

### Debugging with Root Delay

If the root device takes a long time to appear (common with certain RAID/SAN setups), you can increase the wait time.

```bash
# Add this to GRUB line:
rootdelay=120
```

### Emergency Shell on Failure

The standard flag to ensure that if boot fails, you get an emergency shell instead of a kernel panic.

```bash
# Add this to GRUB line:
rd.shell
```
