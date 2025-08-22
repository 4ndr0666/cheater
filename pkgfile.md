# PkgFile Cheat-Sheet

**`pkgfile`** is a command-line utility for Arch Linux that allows you to quickly find which package owns a specific file or command without needing to have the package installed. It operates on a local database of files provided by all packages in the Arch Linux repositories.

It's primarily used for:
*   **Finding missing commands**: If you try to run a command and get "command not found", `pkgfile` can tell you which package provides it.
*   **Identifying file origins**: Determine which package installed a particular file on your system.
*   **Exploring package contents**: List all files included in a given package.

---

## 1. Installation

`pkgfile` is available in the official Arch Linux repositories.

```bash
sudo pacman -S pkgfile
```

---

## 2. Database Management

`pkgfile` relies on a local database of file listings from all repository packages. This database needs to be updated regularly.

*   **Update the `pkgfile` database**:
    ```bash
    sudo pkgfile --update
    ```
    *   **Explanation**: This command downloads updated file lists from all enabled repositories (as configured in `pacman.conf`) and rebuilds `pkgfile`'s local database. It's crucial to run this after `pacman -Syu` or if you encounter outdated results.
*   **Force a full database rebuild**:
    ```bash
    sudo pkgfile --files
    ```
    *   **Explanation**: This command forces `pkgfile` to re-read all package file lists from the `pacman` cache and rebuild its database from scratch. Useful if the database becomes corrupted or inconsistent.

---

## 3. Searching for Files or Commands

The primary function of `pkgfile` is to search for files or commands.

*   **Search for a file or command name**:
    ```bash
    pkgfile <filename_or_command>
    ```
    *   **Example**: Find which package provides the `htop` command.
        ```bash
        pkgfile htop
        # Output: community/htop
        ```
    *   **Example**: Find which package provides a specific configuration file.
        ```bash
        pkgfile xorg.conf
        # Output: extra/xorg-server
        ```

*   **Search with full path**:
    ```bash
    pkgfile /usr/bin/vim
    # Output: extra/vim
    ```

*   **Search case-insensitively**:
    ```bash
    pkgfile --ignore-case <filename_or_command>
    # Shorter form:
    pkgfile -i <filename_or_command>
    ```    *   **Example**:
        ```bash
        pkgfile -i SSHD
        # Output: core/openssh
        ```

*   **Search for a substring within filenames (regex search)**:
    ```bash
    pkgfile --search <pattern>
    # Shorter form:
    pkgfile -s <pattern>
    ```
    *   **Explanation**: This performs a regex-based search through all filenames in the database. Use this when you only know a part of the filename or need more complex matching.
    *   **Example**: Find files ending with `.desktop` provided by `firefox`.
        ```bash
        pkgfile -s "firefox.*\.desktop$"
        # Output: extra/firefox  usr/share/applications/firefox.desktop
        ```

*   **Search case-insensitively for a substring**:
    ```bash
    pkgfile -s --ignore-case <pattern>
    # Shorter form:
    pkgfile -si <pattern>
    ```
    *   **Example**:
        ```bash
        pkgfile -si "libcuda"
        # Output: extra/nvidia-utils  usr/lib/libcuda.so
        #         extra/nvidia-utils  usr/lib/libcuda.so.1
        #         ...
        ```

---

## 4. Listing Package Contents

You can use `pkgfile` to list all files contained within a specific package, similar to `pacman -Ql <pkgname>`.

*   **List all files belonging to a package**:
    ```bash
    pkgfile --list <pkgname>
    # Shorter form:
    pkgfile -l <pkgname>
    ```
    *   **Example**:
        ```bash
        pkgfile -l linux
        # Output: core/linux  usr/
        #         core/linux  usr/bin/
        #         core/linux  usr/bin/bpf_dbg
        #         ...
        ```

*   **List files for a package (case-insensitive package name)**:
    ```bash
    pkgfile -l --ignore-case <pkgname>
    # Shorter form:
    pkgfile -li <pkgname>
    ```
    *   **Example**:
        ```bash
        pkgfile -li "KERNel"
        # Output: core/linux  usr/
        #         core/linux  usr/bin/
        #         ...
        ```

---

## 5. Advanced Options & Tips

*   **Verbose Output**:
    ```bash
    pkgfile --verbose <filename_or_command>
    # Shorter form:
    pkgfile -v <filename_or_command>
    ```
    *   **Explanation**: Shows more detailed output, including the repository the package belongs to.

*   **Quiet Output**:
    ```bash
    pkgfile --quiet <filename_or_command>
    # Shorter form:
    pkgfile -q <filename_or_command>
    ```
    *   **Explanation**: Suppresses error messages, returning only the package name if found, or nothing. Useful in scripts.

*   **Shell Integration (`command-not-found` hook)**:
    `pkgfile` can be integrated into your shell (Bash or Zsh) to suggest packages for commands that are not found. This is typically set up during `pkgfile` installation or manually.

    *   **Bash**: Add this to your `.bashrc`:
        ```bash
        [ -f /usr/share/doc/pkgfile/command-not-found.bash ] && . /usr/share/doc/pkgfile/command-not-found.bash
        ```
    *   **Zsh**: Add `pkgfile` to your `plugins` array in `.zshrc`:
        ```zsh
        plugins=(... pkgfile ...)
        ```
        Then, make sure `autoload -Uz command-not-found` and `command-not-found-init` are run.

*   **Complementing `pacman -Qo`**:
    While `pacman -Qo <file>` tells you which *installed* package owns a file, `pkgfile` tells you which *repository* package owns a file, regardless of installation status. They serve different but complementary purposes.
    ```bash
    # Which installed package owns this file?
    pacman -Qo /usr/bin/grep

    # Which repository package provides this command (even if not installed)?
    pkgfile grep
    ```
*   **Excluding Repositories**: You can configure `pkgfile` to ignore certain repositories by editing `/etc/pkgfile.conf` or `~/.config/pkgfile/pkgfile.conf`.
    ```conf
    # Example /etc/pkgfile.conf
    # Ignore the 'testing' repository
    IgnoreRepo = testing
    ```
