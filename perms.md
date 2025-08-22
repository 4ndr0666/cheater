# Permissions Cheat-Sheet (File System Access Control)

Unix-like operating systems use a robust system of permissions to control who can read, write, or execute files and directories. Understanding and correctly configuring these permissions is fundamental for system security and proper application functioning.

---

## 1. Understanding Permission Modes

Permissions are typically represented in two main ways: **Octal (Numeric)** mode and **Symbolic** mode. Both describe the same permissions for three types of entities: the **User (owner)**, the **Group**, and **Others (world)**.

*   **`r` (Read)**:
    *   **Files**: Can view the contents.
    *   **Directories**: Can list the contents.
*   **`w` (Write)**:
    *   **Files**: Can modify or delete the file.
    *   **Directories**: Can create, delete, or rename files within the directory (even if you don't own the file itself, if you have execute permissions for the directory).
*   **`x` (Execute)**:
    *   **Files**: Can run the file as a program or script.
    *   **Directories**: Can enter or "cd" into the directory, and access files/subdirectories if also having read permission.

### 1.1 Octal (Numeric) Mode

Each permission (`r`, `w`, `x`) is assigned a numerical value:
*   **Read (r)** = `4`
*   **Write (w)** = `2`
*   **Execute (x)** = `1`
*   **No permission** = `0`

You sum these values for each entity (User, Group, Others) to get a three-digit octal number.

**Examples of Common Octal Permissions:**

| **CHMOD** | **Symbolic** | **User (U)** | **Group (G)** | **Others (O)** | **Description**                             | **Use Cases**                                  |
| :-------- | :----------- | :----------- | :------------ | :------------- | :------------------------------------------ | :--------------------------------------------- |
| `400`     | `r--------`  | `r--` (`4`)  | `---` (`0`)   | `---` (`0`)    | Read for User only                          | Private read-only files                        |
| `600`     | `rw-------`  | `rw-` (`6`)  | `---` (`0`)   | `---` (`0`)    | Read/write for User only                    | Private files (e.g., `~/.ssh/id_rsa`)          |
| `644`     | `rw-r--r--`  | `rw-` (`6`)  | `r--` (`4`)   | `r--` (`4`)    | Read/write User; read Group; read Others    | General configuration files (`/etc/passwd`)    |
| `700`     | `rwx------`  | `rwx` (`7`)  | `---` (`0`)   | `---` (`0`)    | Read/write/execute for User only            | Executable scripts, personal directories (`~/bin`) |
| `755`     | `rwxr-xr-x`  | `rwx` (`7`)  | `r-x` (`5`)   | `r-x` (`5`)    | Read/write/execute User; read/execute Group/Others | System binaries (`/usr/bin`), shared directories |
| `775`     | `rwxrwxr-x`  | `rwx` (`7`)  | `rwx` (`7`)   | `r-x` (`5`)    | Read/write/execute User/Group; read/execute Others | Collaborative directories (`/var/www`)         |
| `777`     | `rwxrwxrwx`  | `rwx` (`7`)  | `rwx` (`7`)   | `rwx` (`7`)    | Read/write/execute for all                  | (Temporary) all access, highly insecure        |
| `440`     | `r--r-----`  | `r--` (`4`)  | `r--` (`4`)   | `---` (`0`)    | Read for User; Read for Group               | Shared read-only files (group access)          |
| `550`     | `r-xr-x---`  | `r-x` (`5`)  | `r-x` (`5`)   | `---` (`0`)    | Read/execute for User; Read/execute for Group | Shared executables (group access)              |
| `750`     | `rwxr-x---`  | `rwx` (`7`)  | `r-x` (`5`)   | `---` (`0`)    | Read/write/execute User; Read/execute Group | Restricted shared directories (no others access) |
| `664`     | `rw-rw-r--`  | `rw-` (`6`)  | `rw-` (`6`)   | `r--` (`4`)    | Read/write User/Group; Read Others          | Collaborative files                            |

### 1.2 Symbolic Mode

Symbolic mode uses characters to specify permissions. It's often more intuitive for making incremental changes.

*   **Who**: `u` (user), `g` (group), `o` (others), `a` (all, default if none specified)
*   **Operator**: `+` (add permission), `-` (remove permission), `=` (set permission exactly)
*   **Permissions**: `r` (read), `w` (write), `x` (execute), `X` (execute only if already executable or is a directory)

**Examples:**
```bash
# Add write permission for group and others
chmod go+w myfile.txt

# Remove execute permission for others
chmod o-x myscript.sh

# Set permissions exactly to rwx for user, r for group and others
chmod u=rwx,go=r myfile.txt

# Make a script executable for owner, if it's already executable or a directory
chmod u+X myscript.sh
```

---

## 2. Changing Permissions (`chmod`)

The `chmod` command is used to change file and directory permissions.

### 2.1 Basic Usage

```bash
# Set specific octal permissions
chmod 644 myfile.txt
chmod 755 mydirectory/

# Add execute permission for the owner
chmod u+x myscript.sh

# Remove write permission for everyone else
chmod go-w sensitive_file.conf
```

### 2.2 Recursive Usage

The `-R` (or `--recursive`) option applies changes to all files and subdirectories.

```bash
# Set all files to 644, and all directories to 755 recursively
find ./myproject -type f -exec chmod 644 {} \;
find ./myproject -type d -exec chmod 755 {} \;

# A more robust way to handle symbolic links appropriately
# (Prevents unintended modifications by not following symbolic links by default)
# (Needs shell variables for path, dir_perm, file_perm set beforehand)
# find "$path" -type d -exec chmod "$dir_perm" {} \; -o -type f -exec chmod "$file_perm" {} \;
```

---

## 3. Changing Ownership (`chown`, `chgrp`)

*   **`chown`**: Changes the user owner and/or group owner of files/directories.
*   **`chgrp`**: Changes only the group owner.

### 3.1 Basic Usage

```bash
# Change user owner
sudo chown alice myfile.txt

# Change group owner
sudo chgrp developers myfile.txt

# Change both user and group owner
sudo chown alice:developers myfile.txt
```

### 3.2 Recursive Usage

The `-R` (or `--recursive`) option applies changes to all files and subdirectories.

```bash
# Recursively change ownership of a directory and its contents
sudo chown -R www-data:www-data /var/www/html
```

### 3.3 Clone File Permissions

```bash
chmod --reference file1 file2
```
*   **Explanation**: Sets the permissions of `file2` to be identical to the permissions of `file1`.

### 3.4 Reset Home Directory Permissions (Preserving Executable Status)

```bash
sudo chown -R "$USER":"$USER" /home/"$USER"  # Set correct owner for all files/dirs
find /home/"$USER" -type d -exec chmod 755 {} \; # Set default dir perms
find /home/"$USER" -type f -exec chmod 644 {} \; # Set default file perms
# Re-add execute perm for files that had it
find /home/"$USER" -type f -perm /u=x,g=x,o=x -exec chmod +x {} \;
```
*   **Explanation**: This sequence first resets ownership of your home directory to your user. Then, it sets default permissions for all directories (`755`) and files (`644`), and finally re-adds executable permissions to any files that were originally executable. Essential for fixing common permission issues.

---

## 4. Special Permission Bits

These are additional bits that modify the standard `rwx` behavior, typically represented as a fourth octal digit (e.g., `4755`).

*   **Setuid (SUID)**: `4xxx`
    *   When applied to an executable file, the program runs with the permissions of the file's owner, not the user executing it.
    *   **Use Cases**: Programs like `passwd` (which needs to write to `/etc/shadow` as root, but is run by regular users).
    *   **Example**: `chmod 4755 /usr/bin/some_tool`
*   **Setgid (SGID)**: `2xxx`
    *   **Files**: Similar to SUID, the program runs with the permissions of the file's group.
    *   **Directories**: New files and subdirectories created within this directory inherit the group ID of the directory, not the primary group of the user who created them.
    *   **Use Cases**: Collaborative directories where all new files should belong to a specific group.
    *   **Example**: `chmod 2775 /var/shared_project`
*   **Sticky Bit**: `1xxx`
    *   When applied to a directory, users can create files in it, but they can only delete or rename files they own (or if they are root).
    *   **Use Cases**: The `/tmp` directory, preventing users from deleting or modifying each other's temporary files.
    *   **Example**: `chmod 1777 /tmp`

---

## 5. Default Permissions (`umask`)

The `umask` (user file-creation mode mask) is a four-digit octal number that subtracts permissions from the default (typically `666` for files and `777` for directories) to set permissions for newly created files and directories.

*   **Common `umask` values**:
    *   `0022`: (Common default) New files get `644`, new directories get `755`. (Calculated as `666 - 022 = 644` for files, `777 - 022 = 755` for directories).
    *   `0002`: New files get `664`, new directories get `775`. (More permissive for group).

```bash
# View current umask
umask

# Set umask (usually in ~/.bashrc or ~/.zshrc)
umask 0022
```

---

## 6. Access Control Lists (ACLs)

ACLs provide a more granular way to manage permissions beyond the traditional User/Group/Others model. They allow you to define permissions for specific users or groups on a per-file or per-directory basis.

*   **`setfacl`**: Sets ACLs.
*   **`getfacl`**: Displays ACLs.

```bash
# Grant user 'bob' read and write access to a file
setfacl -m u:bob:rw- myfile.txt

# Grant group 'devs' execute-only access to a directory
setfacl -m g:devs:--x mydirectory/

# View ACLs for a file
getfacl myfile.txt
```
*   **Note**: ACLs are usually enabled on modern Linux filesystems (like ext4, XFS) but might require mounting with the `acl` option in `fstab`.

---

## 7. System Defaults & Important File Permissions

These are common and critical permissions for various system files and directories. Deviation from these can lead to security vulnerabilities or system instability.

| **Path**                      | **Owner** | **Group** | **Files/Dirs** | **Permissions** | **Description**                               |
| :---------------------------- | :-------- | :-------- | :------------- | :-------------- | :-------------------------------------------- |
| `/etc/sudoers`                | `root`    | `root`    | File           | `440`           | Controls `sudo` access. Highly sensitive.       |
| `/etc/passwd`                 | `root`    | `root`    | File           | `644`           | User account information.                   |
| `/etc/shadow`                 | `root`    | `shadow`  | File           | `000` (`640` on some systems) | User passwords (hashed). Extremely sensitive. |
| `/etc/ssh/ssh_config`         | `root`    | `root`    | File           | `644`           | SSH client configuration.                   |
| `~/.ssh/id_rsa`               | `user`    | `user`    | File           | `600`           | SSH private key. **MUST be 600.**           |
| `~/.ssh/id_rsa.pub`           | `user`    | `user`    | File           | `644`           | SSH public key.                             |
| `~/.gnupg/`                   | `user`    | `user`    | Dir            | `700`           | GPG keyring directory. **MUST be 700.**     |
| `/usr/bin/`                   | `root`    | `root`    | Dir            | `755`           | System binaries.                            |
| `/var/www/`                   | `root`    | `www-data`| Dir            | `775`           | Web server root (owner, group vary).        |
| `/var/spool/cron/`            | `root`    | `crontab` | Dir            | `755`           | Main cron spool directory.                  |
| `/var/spool/cron/crontabs/`   | `root`    | `crontab` | Dir            | `1733`          | User crontabs (Sticky Bit for security).    |
| `/etc/ssh/sshd_config`        | `root`    | `root`    | File           | `600`           | SSH daemon configuration. **MUST be 600.**  |
| `/etc/hosts.allow`            | `root`    | `root`    | File           | `644`           | TCP Wrappers access control.                |
| `/etc/hosts.deny`             | `root`    | `root`    | File           | `644`           | TCP Wrappers access control.                |
| `/etc/profile`                | `root`    | `root`    | File           | `644`           | System-wide shell initialization.           |
| `/etc/bash.bashrc`            | `root`    | `root`    | File           | `644`           | System-wide Bash settings.                  |
| `/var/log/`                   | `root`    | `root`    | Dir            | `750`           | System logs.                                |
| `/usr/local/bin/`             | `root`    | `root`    | Dir            | `755`           | Locally installed binaries.                 |
| `$HOME`                       | `user`    | `user`    | Dir            | `755`           | User's home directory.                      |
| `$HOME/.bashrc`               | `user`    | `user`    | File           | `644`           | User's Bash configuration.                  |
| `$HOME/.bash_profile`         | `user`    | `user`    | File           | `644`           | User's Bash login configuration.            |
| `$HOME/zsh/completions`       | `user`    | `user`    | File           | `644`           | Zsh completion files.                       |
| `/tmp`                        |           |           | Dir            | `1777`          | Temporary files (Sticky Bit is crucial).    |

---

## 8. Tips & Troubleshooting

*   **Crontab Permissions**:
    *   Ensure the main cron spool directory is correctly set:
        ```bash
        sudo chown root:crontab /var/spool/cron
        sudo chmod 755 /var/spool/cron
        ```
    *   Individual user crontabs:
        ```bash
        sudo chown <username>:crontab /var/spool/cron/crontabs/<username>
        sudo chmod 600 /var/spool/cron/crontabs/<username>
        ```
*   **`/tmp` Directory**:
    *   Always ensure `/tmp` has the sticky bit set:
        ```bash
        sudo chmod 1777 /tmp
        ```*   **Polkit Rules**:
    *   Set appropriate permissions for PolicyKit rules directories:
        ```bash
        sudo chmod 750 /etc/polkit-1/rules.d/
        ```*   **Zsh Compaudit**: If `zsh` complains about insecure permissions, use:
    ```bash
    compaudit | xargs chmod g-w,o-w
    ```
    *   **Explanation**: This command finds files and directories with insecure group or other write permissions in your Zsh configuration and fixes them.
