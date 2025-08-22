# Permissions Cheater

Unix-like operating systems use a robust system of permissions to control who can read, write, or execute files and directories. Understanding and correctly configuring these permissions is fundamental for system security and proper application functioning.

---

## 1. Original Permission Snippets

This section contains your original permissions-related tables and commands exactly as they were provided. Detailed explanations and expanded usage examples for these and other concepts can be found in the subsequent sections.

**CHMOD DETAILS**

---

### Octal Permission Meaning

| Permissions | Octal | Read | Write | Execute |
| :---------- | :---- | :--- | :---- | :------ |
| `rwx`       | `7`   | Yes  | Yes   | Yes     |
| `rw-`       | `6`   | Yes  | Yes   | No      |
| `r-x`       | `5`   | Yes  | No    | Yes     |
| `r--`       | `4`   | Yes  | No    | No      |
| `-wx`       | `3`   | No   | Yes   | Yes     |
| `-w-`       | `2`   | No   | Yes   | No      |
| `--x`       | `1`   | No   | No    | Yes     |
| `---`       | `0`   | No   | No    | No      |

---

### Chmod Usage

| Permissions                         | Code  | Example              |
| :---------------------------------- | :---- | :------------------- |
| Read for User                       | `400` | `chmod 400 example.txt` |
| Read/Write for User                 | `600` | `chmod 600 example.txt` |
| Read/Write User; Read Group; Read Others | `644` | `chmod 644 example.txt` |
| Read/Write/Execute User; Read/Execute Group/Others | `755` | `chmod 755 example_dir/` |
| Read/Write/Execute for all          | `777` | `chmod 777 example.txt` |
| Read for User; Read for Group       | `440` | `chmod 440 example.txt` |
| Read/Execute for User; Read/Execute for Group | `550` | `chmod 550 example_dir/` |
| Read/Write/Execute User; Read/Execute Group | `750` | `chmod 750 example_dir/` |
| Read/Write User/Group; Read Others  | `664` | `chmod 664 example.txt` |

---

### Special Permission Bits

| Bit       | Octal | Description                                                                                                                                                                                                                                                                                                                                                                      |
| :-------- | :---- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `SUID`    | `4xxx`| This bit ensures that when the file is run as an executable, it will always run with the permissions of the user that owns the file, NOT the user that is running the file. Think of `sudo` where you, a normal user, can run a command with elevated `root` permissions that you wouldn't normally have. This can be a huge security risk so use it carefully and wisely. |
| `SGID`    | `2xxx`| This bit ensures that any files that are created within that directory will have the same group as the directory itself. This is useful for shared folders and other multi-user environments.                                                                                                                                                                                    |
| `Sticky`  | `1xxx`| This bit ensures that only the user who owns the file or directory (or `root`) can delete or rename the file, regardless of whether other users have write access to the parent directory. This is useful for directories like `/tmp`.                                                                                                                                        |

---

### System Defaults

**FILES**

| Permissions | Owner | Group | Other |
| :---------- | :---- | :---- | :---- |
| `644`       | `rw`  | `r`   | `r`   |
| `600`       | `rw`  |       |       |
| `400`       | `r`   |       |       |

**DIRECTORIES**

| Permissions | Group | Other |
| :---------- | :---- | :---- |
| `755`       | `rx`  | `rx`  |
| `700`       |       |       |

---

### TIPS & TRICKS

**Crontab**

| Path                            | Permissions | Owner | Group   |
| :------------------------------ | :---------- | :---- | :------ |
| `/var/spool/cron`               | `755`       | `root`| `crontab` |
| `/var/spool/cron/crontabs/<user>` | `600`       | `user`| `crontab` |

**Tmp**

```bash
sudo chmod 1777 /tmp
```

**Polkit**

```bash
sudo chmod 750 /etc/polkit-1/rules.d/
```

---

## 2. Expanded Explanations & Modern Permissions Management

This section provides a more structured and comprehensive guide to Unix permissions, building upon the foundations shown above and introducing additional concepts.

### 2.1 Understanding Permission Modes

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

### 2.2 Octal (Numeric) Mode

Each permission (`r`, `w`, `x`) is assigned a numerical value:
*   **Read (r)** = `4`
*   **Write (w)** = `2`
*   **Execute (x)** = `1`
*   **No permission** = `0`

You sum these values for each entity (User, Group, Others) to get a three-digit octal number. The table in Section 1.1 (`### Octal Permission Meaning`) provides a quick reference.

### 2.3 Symbolic Mode

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

## 3. Changing Permissions (`chmod`)

The `chmod` command is used to change file and directory permissions. Its usage, including examples of octal codes, is extensively detailed in Section 1.2 (`### Chmod Usage`).

### 3.1 Basic Usage

```bash
# Set specific octal permissions
chmod 644 another_file.txt
chmod 755 another_directory/

# Add execute permission for the owner
chmod u+x another_script.sh

# Remove write permission for everyone else
chmod go-w sensitive_file.conf
```

### 3.2 Recursive Usage

The `-R` (or `--recursive`) option applies changes to all files and subdirectories.

```bash
# Set all files to 644, and all directories to 755 recursively
find ./myproject -type f -exec chmod 644 {} \;
find ./myproject -type d -exec chmod 755 {} \;

# A more robust way to handle symbolic links appropriately in complex find commands
# (Prevents unintended modifications by not following symbolic links by default)
# Example (needs shell variables for path, dir_perm, file_perm set beforehand):
# find "$path" -type d -exec chmod "$dir_perm" {} \; -o -type f -exec chmod "$file_perm" {} \;
```

---

## 4. Changing Ownership (`chown`, `chgrp`)

*   **`chown`**: Changes the user owner and/or group owner of files/directories.
*   **`chgrp`**: Changes only the group owner.

### 4.1 Basic Usage

```bash
# Change user owner
sudo chown alice myfile.txt

# Change group owner
sudo chgrp developers myfile.txt

# Change both user and group owner
sudo chown alice:developers myfile.txt
```

### 4.2 Recursive Usage

The `-R` (or `--recursive`) option applies changes to all files and subdirectories.

```bash
# Recursively change ownership of a directory and its contents
sudo chown -R www-data:www-data /var/www/html
```

### 4.3 Clone File Permissions

```bash
chmod --reference file1 file2
```
*   **Explanation**: Sets the permissions of `file2` to be identical to the permissions of `file1`.

### 4.4 Reset Home Directory Permissions (Preserving Executable Status)

```bash
sudo chown -R "$USER":"$USER" /home/"$USER"  # Set correct owner for all files/dirs
find /home/"$USER" -type d -exec chmod 755 {} \; # Set default dir perms
find /home/"$USER" -type f -exec chmod 644 {} \; # Set default file perms
# Re-add execute perm for files that had it
find /home/"$USER" -type f -perm /u=x,g=x,o=x -exec chmod +x {} \;
```
*   **Explanation**: This sequence first resets ownership of your home directory to your user. Then, it sets default permissions for all directories (`755`) and files (`644`), and finally re-adds executable permissions to any files that were originally executable. Essential for fixing common permission issues.

---

## 5. Special Permission Bits

These are additional bits that modify the standard `rwx` behavior, typically represented as a fourth octal digit (e.g., `4755`). Your original definitions are precisely detailed in Section 1.3 (`### Special Permission Bits`).

*   **Setuid (SUID)**: `4xxx`
    *   **Example**: `chmod 4755 /usr/bin/some_tool`
*   **Setgid (SGID)**: `2xxx`
    *   **Example**: `chmod 2775 /var/shared_project`
*   **Sticky Bit**: `1xxx`
    *   **Example**: `chmod 1777 /tmp`

---

## 6. Default Permissions (`umask`)

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

## 7. Access Control Lists (ACLs)

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

## 8. System Defaults & Important File Permissions

This section consolidates essential default permissions for various system files and directories, building upon your original "System Defaults" tables (Section 1.4).

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
| `$HOME`                       | `user`    | `user`    | Dir            | `755`           | User's home directory.                      |
| `$HOME/.bashrc`               | `user`    | `user`    | File           | `644`           | User's Bash configuration.                  |
| `$HOME/.bash_profile`         | `user`    | `user`    | File           | `644`           | User's Bash login configuration.            |
| `$HOME/zsh/completions`       | `user`    | `user`    | File           | `644`           | Zsh completion files.                       |
| `/tmp`                        | `root`    | `root`    | Dir            | `1777`          | Temporary files (Sticky Bit is crucial).    |

---

## 9. Permissions Best Practices & Troubleshooting

This section compiles important tips and troubleshooting steps, including your original "TIPS & TRICKS" from Section 1.5.

*   **Crontab Permissions**:
    *   Your original table for Crontab permissions is reproduced in Section 1.5 (`### TIPS & TRICKS`).
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
*   **`/tmp` Directory (Sticky Bit)**:
    *   Your original command to set sticky bit for `/tmp` is reproduced below:
        ```bash
        sudo chmod 1777 /tmp
        ```
    *   **Explanation**: This ensures that users can only delete or rename files they own within `/tmp`, even if they have write access to the directory.
*   **Polkit Rules**:
    *   Your original command for Polkit rules permissions is reproduced below:
        ```bash
        sudo chmod 750 /etc/polkit-1/rules.d/
        ```
    *   **Explanation**: This sets secure permissions for PolicyKit rule directories, typically `rwxr-x---`.
*   **Zsh Compaudit**: If `zsh` complains about insecure permissions, use:
    ```bash
    compaudit | xargs chmod g-w,o-w
    ```
    *   **Explanation**: This command finds files and directories with insecure group or other write permissions in your Zsh configuration and fixes them.
