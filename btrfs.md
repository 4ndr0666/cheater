# BTRFS Cheat-Sheet

**BTRFS** (B-tree file system) is a modern copy-on-write (CoW) filesystem for Linux aimed at implementing advanced features while also focusing on fault tolerance, repair, and easy administration. This cheat-sheet provides essential commands for managing BTRFS filesystems, subvolumes, and snapshots.

---

## 1. Mounting BTRFS Subvolumes

This section covers standard mounting procedures for BTRFS subvolumes, often used in complex setups with multiple subvolumes for `/`, `/home`, etc.

### Standard Subvolume Mounts

The following commands mount various subvolumes for a typical BTRFS layout.
*Note: `$(root_partition)` is a placeholder for your actual root partition (e.g., `/dev/sdaX`). Replace this with your specific device.*

```bash
mount -o subvol=@/.snapshots "$(root_partition)" /mnt/.snapshots
mount -o subvol=@/home "$(root_partition)" /mnt/home
mount -o subvol=@/opt "$(root_partition)" /mnt/opt
mount -o subvol=@/root "$(root_partition)" /mnt/root
mount -o subvol=@/srv "$(root_partition)" /mnt/srv
mount -o subvol=@/tmp "$(root_partition)" /mnt/tmp
mount -o subvol=@/usr/local "$(root_partition)" /mnt/usr/local
mount -o subvol=@/var "$(root_partition)" /mnt/var
```

---

## 2. General BTRFS Usage

Essential commands for creating, listing, and managing BTRFS filesystems and subvolumes.

### Prefix (Date Formatting with Placeholder)

Your original command below contains the variable `time_delim`, which is not defined within this snippet. If executed as is, this variable would be empty or cause an error depending on the shell context.

```bash
echo "$(date +%Y-%m-%d_%H:${time_delim}%M:${time_delim}%S)"
```
*   **Note**: To make this command functional, `time_delim` needs to be defined (e.g., `time_delim=":"`) or removed for a standard format.
*   **Corrected Example**:
    ```bash
    time_delim="-" # Define the variable
    echo "$(date +%Y-%m-%d_%H${time_delim}%M${time_delim}%S)"
    # Or without a custom delimiter:
    echo "$(date +%Y-%m-%d_%H:%M:%S)"
    ```

### Make the Filesystem

Creates a new BTRFS filesystem on a specified device.

```bash
mkfs.btrfs /dev/sdx
```

### Create a Subvolume (`subv`)

Creates a new subvolume at a given mount point.

```bash
btrfs subvolume create /mtpt/sdx
```

### List The Subvolumes

Lists all subvolumes within a BTRFS filesystem.

```bash
sudo btrfs subvolume list /
```

### Check Default Subvolume

Displays the default subvolume for a BTRFS filesystem. This is typically the subvolume your system boots from.

```bash
sudo btrfs subvolume get-default /path/to/mount/point
```
*should be subv you're booting from*

### Mount Subvolume by ID

Mounts a specific subvolume using its unique ID.

```bash
sudo mount -o subvolid=505 /dev/sdx /run/mtpt
```

### Show The File Systems

Displays information about BTRFS filesystems.

```bash
sudo btrfs fi show /
```

### Show The File System Usage

Reports disk usage information for a BTRFS filesystem.

```bash
sudo btrfs fi du /
```

### Show UUID

Retrieves the UUID (Universally Unique Identifier) of a BTRFS device.

```bash
lsblk -dno UUID /dev/sdax
```
or
```bash
blkid | grep btrfs
```

### Resize Filesystem

Changes the size of a BTRFS filesystem.

Your original command:```bash
sudo btrfs fi resize <+/-/or total size>115G /mnt/sdx
```
*   **Note**: The placeholder `<+/-/or total size>` is slightly ambiguous when followed by `115G`.
*   **Clarified Usage**: Specify a change (e.g., `+10G`, `-5G`) or an absolute size (e.g., `115G`).
    ```bash
    # Example: Increase by 10 Gigabytes
    sudo btrfs fi resize +10G /mnt/sdx
    # Example: Resize to a total size of 115 Gigabytes
    sudo btrfs fi resize 115G /mnt/sdx
    ```

### Delete Subvolume

Deletes a BTRFS subvolume or snapshot.

```bash
btrfs subvolume delete /path/to/subvolume_or_snapshot
```

---

## 3. BTRFS Maintenance

Commands for maintaining the health and performance of your BTRFS filesystem.

### Scrub (Perform before balance)

Initiates a scrub operation to check for and repair data integrity issues.

```bash
sudo btrfs scrub start /
```

### Balance

Redistributes data across devices to ensure even usage or change RAID levels.

```bash
sudo btrfs balance start -musage=50 -dusage=50 /
```

*Combine both into an alias:*

Your original alias:
```bash
alias balance='sudo btrfs balance start -musage=60 -dusage=60 / & PID=$!; sudo watch -t -n5 btrfs balance status /; fg'
```
*   **Note**: The `fg` command at the end of this alias attempts to bring the `watch` process (which monitors the balance status) to the foreground. This might behave unexpectedly if the `watch` command itself is backgrounded or if there are other background jobs. The intention is likely to simply observe the balance status in the foreground after starting the balance.

### Check For Errors

Performs a read-only check of the BTRFS filesystem for errors.

```bash
sudo btrfs check --readonly /dev/sdx
```

### Repair

Attempts to repair a damaged BTRFS filesystem.

```bash
sudo btrfs check --repair /dev/sdx
```
*warning: do not use this option if mounting is possible*

---

## 4. BTRFS Snapshots

Snapshots are point-in-time copies of subvolumes, useful for backups and system recovery.

### Method 1 (Read-Only Snapshot with Date)

Creates a read-only snapshot of the root subvolume with a timestamp.

```bash
sudo btrfs subvolume snapshot -r / "/.snapshots/@root-$(date +%F-%R)"
```

### Method 2 (Writable Snapshot for Rollback)

Creates a writable snapshot of `/home`. This method is often used for easier rollback by editing `fstab`.

```bash
sudo btrfs subvolume snapshot /home /home/.snapshots/2020-01-13
```
With this method you can revert the snapshots by editing fstab
and changing the `subvol=2020-01-13` or the corresponding `subvolid`
you get from executing a `sudo btrfs subvolume list /home`.

### List Snapshots

Lists all snapshots within a specified mount point.

```bash
sudo btrfs subvolume list -t snapshot /path/to/mount/point
```

### Restore Method 1 (Set Default Subvolume)

Changes the default subvolume that will be mounted as root at boot time.

```bash
sudo btrfs subvolume set-default [snapshot-id] /path/to/mount/point
```
*ensure to update grub to reflect changes*
```bash
sudo update-grub
```

### Restore Method 2 (Delete and Re-create)

Restores a snapshot by deleting the current subvolume and replacing it with a snapshot.

```bash
sudo btrfs subvolume delete /home
sudo btrfs subvolume snapshot /home/.snapshots/2020-01-13 /home
```
Now restore your fstab and reboot to be back on the /home subv.

---

## 5. Specialized BTRFS Commands

Advanced commands for specific BTRFS operations.

### Convert to RAID after adding disk to existing subvolume

Converts the RAID level of a BTRFS filesystem after adding new disks.

```bash
sudo btrfs balance start -mconvert=raid1 -dconvert=raid1 /home
```

### Remount Order (Manual Subvolume Creation for Installation)

This sequence of commands is typically used during a manual BTRFS installation to set up the subvolume structure before mounting the root filesystem.

```bash
cd /mnt
btrfs subvolume create @
btrfs subvolume create @/.snapshots
mkdir ./@/.snapshots/0
btrfs subvolume create @/.snapshots/0/snapshot
btrfs subvolume create @/home
btrfs subvolume create @/opt
btrfs subvolume create @/root
btrfs subvolume create @/srv
btrfs subvolume create @/tmp
mkdir @/usr/
btrfs subvolume create @/usr/local
btrfs subvolume create @/var
chattr +C @/var
btrfs subvolume set-default $(btrfs subvolume list /mnt | grep "@/.snapshots/0/snapshot" | grep -oP '(?<=ID )[0-9]+') /mnt
cd /
umount /mnt
cd /mnt
```

### Mount Root and Create Final Mount-Points

After creating the subvolume structure, these commands mount the root subvolume and create the final mount points required by the system.

```bash
mount "$(root_partition)" /mnt
mkdir /mnt/.snapshots
mkdir /mnt/home
mkdir /mnt/opt
mkdir /mnt/root
mkdir /mnt/srv
mkdir /mnt/tmp
mkdir -p /mnt/usr/local
mkdir /mnt/var
```
