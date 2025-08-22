# ONE-LINERS Cheater

This cheat-sheet compiles a list of powerful and concise command-line commands to streamline your daily tasks in Linux and Unix-like environments. These one-liners cover system information, file manipulation, networking, Git operations, and more.

---

## 1. Original One-Liners Snippets

This section contains your original collection of one-liners and their headings, exactly as they were provided. Detailed explanations and expanded usage examples for these and many more operations can be found in the subsequent sections.

### Update and upgrade system (Debian/Ubuntu)
```bash
sudo apt update && sudo apt upgrade -y
```

### LSBLK (Disk partitions)
```bash
lsblk -f
```

### JOURNALCTL (System log tail)
```bash
sudo journalctl -xe | tail -n 20
```

### Find all script files in current directory (recursive)
```bash
grep -ErlI "^#!(/usr)?/bin/(env )?(bash|zsh|python|sh)" .
```

### Find files containing "TODO" (case-insensitive, recursive)
```bash
grep -lrI "TODO" .
```

### Grep / Sed (Find and replace foo with bar in files recursively)
```bash
grep -lR "foo" . | xargs -0 sed -i "s/foo/bar/g"
```

### Count chars in string (excluding trailing newline)
```bash
echo 'your text here' | tr -d '\n' | wc -c
```

### DU / NCDU (Analyze disk usage interactively)
```bash
sudo du -h --max-depth=1 /home/ && ncdu /home/```

### FDISK (List disk partitions)
```bash
sudo fdisk -l
```

### NMAP (Ping scan local network segment)
```bash
sudo nmap -sP 192.168.1.0/24 | grep -Eo '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}'
```

### CURL (Get your public IP address)
```bash
curl -s myip.ipip.net | grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b"
```

### CURL (Get your public IP address (JSON))
```bash
curl -s https://wtfismyip.com/json | jq .
```

### IP (Get your local IP address)
```bash
ip a | grep "inet " | grep -v 127.0.0.1 | awk '{print $2}' | cut -d/ -f1
```

### LSOF (List open ports)
```bash
sudo lsof -i -P -n | grep LISTEN
```

### SSH (Show your public key)
```bash
cat ~/.ssh/id_rsa.pub
```

### SSH (Copy public key to remote server)
```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub user@host
```

### SSH (Local port forwarding)```bash
ssh -N -L 8080:localhost:8080 user@host
```

### GIT (List files changed in current branch vs master)
```bash
git diff --name-only $(git merge-base master HEAD)..HEAD
```

### GIT (List files changed in last commit)
```bash
git diff --name-only HEAD~1..HEAD
```

### GIT (Pretty log (last 10 commits))
```bash
git log --pretty=format:"%Cred%h%Creset %s %Cgreen(%cr)%C(bold blue)<%an>%Creset" --date=relative -n 10
```

### GIT (Search commit messages (case-insensitive))
```bash
git log --all --grep="commit message" -i
```

### GIT (Search log by author and commit message)
```bash
git log --grep="bugfix" --author="Jane Doe"
```

### GIT (Short status)
```bash
git status --short
```

### GIT (Fetch all remote branches and prune stale ones)
```bash
git fetch --all --prune
```

### GIT (List all branches with tracking info)
```bash
git branch -avv
```

### FZF (Fuzzy search history)
```bash
history | cut -c 8- | fzf --no-sort +s --tac
```

### FZF (Fuzzy search files in current directory)
```bash
find . -type f -print0 | xargs -0 fzf
```

### FZF (Fuzzy search and kill processes)
```bash
fkill() {
  local pid
  pid=$(ps -ef | sed 1d | fzf -m | awk '{print $2}')
  if [ -n "$pid" ]
  then
    kill -9 $pid
  fi
}
```

### SORT / UNIQ (Count unique lines in file)
```bash
sort file.txt | uniq -c | sort -nr
```

### AWK (Extract specific columns from delimited text)
```bash
cat data.csv | awk -F',' '{print $1, $3}'
```

### AWK (Remove duplicate lines while preserving order)
```bash
awk '!seen[$0]++' file.txt
```

### JQ (Pretty print JSON)
```bash
curl -s https://api.github.com/users/octocat | jq .
```

### PGREP (Find processes by name and get PIDs)
```bash
pgrep -l "firefox"
```

### LSOF (Find processes listening on a specific port)
```bash
sudo lsof -i :8000
```

### TOP (Monitor a specific process)
```bash
top -p $(pgrep -o firefox)
```

### NOHUP (Run a command in the background, detached from terminal)
```bash
nohup ./my_script.sh &
```

### TAR (Create a gzip tar archive)
```bash
tar -czvf archive.tar.gz /path/to/directory
```

### TAR (Extract a gzip tar archive)
```bash
tar -xzvf archive.tar.gz -C /path/to/extract/to```

### ZIP (Create a zip archive)
```bash
zip -r archive.zip /path/to/directory
```

### UNZIP (Extract a zip archive)
```bash
unzip archive.zip -d /path/to/extract/to
```

### CURL (Download a file with a specific name)
```bash
curl -o custom_name.html https://example.com/some/path/file.html
```

### CURL (Download a file and follow redirects)
```bash
curl -L -O https://example.com/redirects/to/file.zip
```

### CURL (Perform an HTTP GET request and show headers)```bash
curl -I https://example.com
```

### BASH (Test network connectivity to a port)
```bash
bash -c 'exec 3<> /dev/tcp/example.com/80 && echo "Connected to port 80"' || echo "Connection failed"
```

### FIND / CHMOD (Recursively change permissions (files to 644))
```bash
find /path/to/dir -type f -print0 | xargs -0 chmod 644
```

### FIND / CHMOD (Recursively change permissions (directories to 755))
```bash
find /path/to/dir -type d -print0 | xargs -0 chmod 755
```

### FIND / CHMOD (Grant execute permission to all scripts in current directory)
```bash
find . -maxdepth 1 -type f -name "*.sh" -exec chmod +x {} \;
```

---

## 2. Expanded One-Liners: System Information & Utilities

Commands to inspect and manage your system.

*   **Update and Upgrade System (Debian/Ubuntu)**
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```
    *   **Explanation**: `apt update` refreshes the list of available packages, and `apt upgrade -y` installs all new versions of currently installed packages without asking for confirmation.

*   **List Block Devices with Filesystem Info (`lsblk`)**
    ```bash
    lsblk -f
    ```
    *   **Explanation**: Shows block devices (disks, partitions), their mount points, filesystem types, and UUIDs. Useful for understanding disk layout.

*   **View Latest Systemd Journal Entries (`journalctl`)**
    ```bash
    sudo journalctl -xe | tail -n 20
    ```
    *   **Explanation**: Displays the last 20 verbose (`-x`) extended (`-e`) entries from the systemd journal. Great for quick debugging of recent system events or service failures.

*   **Aggregate System Info (CPU Model)**
    ```bash
    grep -m1 'model name' /proc/cpuinfo
    ```
    *   **Explanation**: Extracts and prints the CPU model name from `/proc/cpuinfo`.

---

## 3. Expanded One-Liners: File & Directory Management

Commands for navigating, searching, and manipulating files and directories.

*   **Find All Script Files in Current Directory (Recursive)**
    ```bash
    grep -ErlI "^#!(/usr)?/bin/(env )?(bash|zsh|python|sh)" .
    ```
    *   **Explanation**: Recursively (`-r`) lists files (`-l`) in the current directory (`.`) that contain a shebang (`#!`) line indicating a common script interpreter (bash, zsh, python, sh). Ignores binary files (`-I`).

*   **Find Files Containing "TODO" (Case-Insensitive, Recursive)**
    ```bash
    grep -lrI "TODO" .
    ```
    *   **Explanation**: Recursively (`-r`) lists (`-l`) files in the current directory (`.`) that contain the string "TODO". `-I` ignores binary files. For case-insensitive search, add `-i`.

*   **Recursively Find & Replace "foo" with "bar" in Files**
    ```bash
    grep -lR "foo" . | xargs -0 sed -i "s/foo/bar/g"
    ```
    *   **Explanation**: First, `grep -lR "foo" .` finds all files (`-l`) recursively (`-R`) containing "foo". The output is piped to `xargs -0` (which safely handles filenames with spaces/special characters by expecting null-separated input). `sed -i "s/foo/bar/g"` then performs an in-place (`-i`) global (`/g`) substitution of "foo" with "bar" in each of those files.
    *   **Note**: For `grep` to output null-separated names, use `grep -lRZ "foo" .`. The `xargs -0` then correctly consumes it.

*   **Count Characters in a String (Excluding Trailing Newline)**
    ```bash
    echo 'your text here' | tr -d '\n' | wc -c
    ```
    *   **Explanation**: `echo` prints the string, `tr -d '\n'` removes the trailing newline character that `echo` adds, and `wc -c` counts the characters.

*   **Scan Site-packages For Leftovers (Python)**
    ```bash
    find ~/.local/lib/python*/site-packages -iname "*pkgname*" -or -iname "*pkgname2*"
    ```
    *   **Explanation**: Searches `python` site-packages for directories or files matching `*pkgname*` or `*pkgname2*` (case-insensitive). Useful for cleaning up old or orphaned Python packages.

*   **Rename All Files to Add ".sh" Extension if They Are Shell Scripts**
    ```bash
    for f in *; do file "$f" | grep -q 'shell script' && [[ "$f" != *.sh ]] && mv "$f" "$f.sh"; done
    ```
    *   **Explanation**: Iterates through files in the current directory. If `file "$f"` identifies it as a "shell script" and it doesn't already have a `.sh` extension, it renames the file to `$f.sh`.

*   **List Broken Symlinks**
    ```bash
    find . -type l ! -exec test -e {} \; -print
    ```
    *   **Explanation**: Finds symbolic links (`-type l`) in the current directory (`.`) that point to non-existent targets.

*   **List Dangling Symlinks (Same as broken, but sometimes different behavior on different `find` versions)**
    ```bash
    find . -type l -xtype l -print
    ```
    *   **Explanation**: Finds symbolic links (`-type l`) whose target does not exist (`-xtype l`).

*   **Delete Dangling Symlinks**
    ```bash
    find . -type l -xtype l -delete
    ```
    *   **Explanation**: Finds and deletes symbolic links whose targets do not exist.

*   **Create a `.bak` Backup of a File**
    ```bash
    cp file.txt{,.bak}
    ```
    *   **Explanation**: Copies `file.txt` to `file.txt.bak`. A concise way to create a backup.

*   **Truncate Filename (Example: Remove prefix from video titles)**
    ```bash
    for file in *.ts; do
        echo mv "$file" "${file#*-*- }"
    done
    ```
    *   **Explanation**: This `for` loop, when executed, will *show* the `mv` commands. To actually perform the rename, remove `echo`. It renames files like "20221227 2210 - Random TV Chanel HD - Make Arch Great Again.ts" to "Make Arch Great Again.ts" by removing the prefix up to the second hyphen and space.

*   **Remove All Files Except Specific Ones**
    ```bash
    rm -f !(survivor.txt)
    ```
    *   **Explanation**: Deletes all files in the current directory *except* `survivor.txt`. Requires `extglob` to be enabled (e.g., `shopt -s extglob`).

*   **Replace All Spaces with Underscore in Filenames**
    ```bash
    rename -v 's/ /_/g' *
    ```
    *   **Explanation**: Uses the `rename` command (often `perl-rename` or `prename`) to globally (`/g`) substitute (`s/ /_/`) spaces with underscores in all filenames (`*`).

*   **Remove Files That Don't Match Extensions**
    ```bash
    rm !(*.foo|*.bar|*.baz)
    ```
    *   **Explanation**: Deletes all files in the current directory that do *not* have `.foo`, `.bar`, or `.baz` extensions. Requires `extglob` to be enabled.

---

## 4. Expanded One-Liners: Disk & Mount Management

Commands to check disk usage, partition information, and manage mounts.

*   **Analyze Disk Usage Interactively with `ncdu`**
    ```bash
    sudo du -h --max-depth=1 /home/ && ncdu /home/
    ```
    *   **Explanation**: First, `du -h --max-depth=1 /home/` provides a human-readable summary of disk space used by subdirectories one level deep. Then, `ncdu /home/` launches the `ncdu` interactive disk usage analyzer for `/home/`, allowing you to easily navigate and identify large files/directories.

*   **List Disk Partitions (`fdisk`)**
    ```bash
    sudo fdisk -l
    ```
    *   **Explanation**: Displays detailed partition tables for all detected block devices. Requires root privileges.

*   **Mount NTFS Partition with User Permissions**
    ```bash
    sudo umount -l /tardis
    sudo mount -t ntfs-3g -o uid=1000,gid=1000,umask=022,relatime UUID=801F5E3CC5D8B2EA /tardis
    ```
    *   **Explanation**: First, unmounts (`umount -l`) the `/tardis` mount point (if busy). Then, mounts an NTFS partition (identified by UUID) to `/tardis` with specific user (`uid`, `gid`), permissions (`umask`), and filesystem options. Replace `UUID` and mount point as needed.

*   **Check for Non-Executable Mount & Resolve**
    ```bash
    mount | grep /sdx
    # If the output shows 'noexec', resolve with:
    sudo umount /sdx
    sudo mount -o remount,exec,noatime /dev/sdx /sdy
    ```
    *   **Explanation**: Checks if a mount point (`/sdx`) has `noexec` option. If so, it unmounts and remounts with `exec` (allowing execution of binaries) and `noatime` (improving performance by not updating access times). Replace `/sdx` and `/sdy` with your device and mount point.

*   **Generate ISO Image from CD/DVD Drive**
    ```bash
    readom dev=/dev/scd0 f=/path/to/image.iso
    ```
    *   **Explanation**: `readom` reads data from an optical drive (`/dev/scd0`) and writes it to an ISO image file.

*   **SSD Performance Optimization (fstab entry)**
    ```bash
    # Add to /etc/fstab:
    UUID=9096e7c4-5ca8-4d9c-a431-72497931f44d / ext4 rw,noatime,discard 0 1
    ```
    *   **Explanation**: Suggests adding `noatime` (don't update file access times) and `discard` (enable TRIM) options to your SSD's `fstab` entry for performance. Replace `UUID` and mount point.

*   **Zram Configuration (for more efficient swap space using RAM compression)**
    ```bash
    sudo modprobe zram
    echo lz4 | sudo tee /sys/block/zram0/comp_algorithm
    echo 2G | sudo tee /sys/block/zram0/disksize
    sudo mkswap /dev/zram0
    sudo swapon /dev/zram0
    ```
    *   **Explanation**: Loads the `zram` kernel module, sets `lz4` as the compression algorithm, allocates 2GB for the zram device, initializes it as swap, and activates it. Good for systems with less RAM.

*   **Fstab `tmpfs` for Temporary Files in RAM**
    ```bash
    # Add to /etc/fstab:
    tmpfs /tmp tmpfs defaults,noatime,mode=1777 0 0
    ```
    *   **Explanation**: Mounts `/tmp` as a `tmpfs` filesystem, meaning temporary files are stored in RAM, which can improve performance and reduce SSD wear.

---

## 5. Expanded One-Liners: Networking

Commands for checking network status, IP addresses, and connectivity.

*   **Perform a Ping Scan of a Local Network Segment (`nmap`)**
    ```bash
    sudo nmap -sP 192.168.1.0/24 | grep -Eo '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}'
    ```
    *   **Explanation**: `nmap -sP` performs a ping scan (host discovery) on the specified subnet. `grep -Eo` then extracts only the IPv4 addresses from the output. Replace `192.168.1.0/24` with your network's subnet.

*   **Get Your Public IP Address (Text)**
    ```bash
    curl -s myip.ipip.net | grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b"
    ```
    *   **Explanation**: `curl -s` fetches the content from `myip.ipip.net` silently. `grep -oE` then extracts the first IPv4 address found.

*   **Get Your Public IP Address (JSON with `jq`)**
    ```bash
    curl -s https://wtfismyip.com/json | jq .
    ```
    *   **Explanation**: Fetches your IP information in JSON format from `wtfismyip.com` and pipes it to `jq .` for pretty-printing and easy parsing of the JSON output.

*   **Get Your Local IP Address (`ip a`)**
    ```bash
    ip a | grep "inet " | grep -v 127.0.0.1 | awk '{print $2}' | cut -d/ -f1
    ```
    *   **Explanation**: `ip a` lists all network interfaces. `grep "inet "` filters for lines showing IPv4 addresses. `grep -v 127.0.0.1` excludes the loopback address. `awk '{print $2}'` selects the second field (the IP/CIDR), and `cut -d/ -f1` extracts just the IP address before the `/`.

*   **List Open Ports (Listening Services with `lsof`)**
    ```bash
    sudo lsof -i -P -n | grep LISTEN
    ```
    *   **Explanation**: `lsof -i -P -n` lists all open files related to network connections, using numerical port numbers (`-P`) and numerical host addresses (`-n`). `grep LISTEN` filters for services that are actively listening for incoming connections.

*   **Show Your SSH Public Key**
    ```bash
    cat ~/.ssh/id_rsa.pub
    ```
    *   **Explanation**: Displays the content of your default SSH public key file, which you can then share or add to servers.

*   **Copy SSH Public Key to a Remote Server**
    ```bash
    ssh-copy-id -i ~/.ssh/id_rsa.pub user@host
    ```
    *   **Explanation**: A convenient command to append your public key to the `~/.ssh/authorized_keys` file on a remote server, allowing passwordless SSH access. Replaces `user@host` with the actual username and hostname/IP.

*   **SSH Local Port Forwarding**
    ```bash
    ssh -N -L 8080:localhost:8080 user@host
    ```
    *   **Explanation**: Creates a tunnel where local port `8080` (on your machine) forwards to remote port `8080` (on `user@host`'s `localhost`). `-N` prevents executing a remote command (only forwarding), and `-L` specifies local forwarding. Useful for accessing services behind a firewall.

*   **Confirm Site is Live (Check HTTP Headers)**
    ```bash
    curl -I https://4ndr0666.github.io/4ndr0site/scripts/ | sed -n '1p'
    ```
    *   **Explanation**: Fetches only the HTTP headers (`-I`) from the URL and prints only the first line (`sed -n '1p'`), which usually contains the HTTP status code (e.g., `HTTP/1.1 200 OK`).

*   **Confirm Site is Live (Check for Content)**
    ```bash
    curl -sS https://4ndr0666.github.io/4ndr0site/scripts/ -o /tmp/live-scripts.html
    test -s /tmp/live-scripts.html && echo "OK: live /scripts has content" || echo "ERROR: live /scripts is empty"
    ```
    *   **Explanation**: Downloads the URL content silently (`-sS`) to a temporary file (`-o`). Then, `test -s` checks if the file is not empty.

*   **Test Network Connectivity to a Port (Bash only)**
    ```bash
    bash -c 'exec 3<> /dev/tcp/example.com/80 && echo "Connected to port 80"' || echo "Connection failed"
    ```
    *   **Explanation**: Uses `/dev/tcp` pseudo-file to attempt a TCP connection to `example.com` on port `80`. If successful, it prints "Connected". This is a pure Bash way to check if a port is open without external tools like `netcat`.

---

## 6. Expanded One-Liners: Git Operations

Common and powerful Git commands for version control.

*   **List Files Changed in Current Branch vs. Master**
    ```bash
    git diff --name-only $(git merge-base master HEAD)..HEAD
    ```
    *   **Explanation**: Shows only the names of files (`--name-only`) that are different between the base commit of your current branch (`HEAD`) and `master`, and your current `HEAD`. Useful for reviewing changes before merging.

*   **List Files Changed in the Last Commit**
    ```bash
    git diff --name-only HEAD~1..HEAD
    ```
    *   **Explanation**: Shows the filenames (`--name-only`) that were modified between the current commit (`HEAD`) and the commit just before it (`HEAD~1`).

*   **Pretty Git Log (Last 10 Commits)**
    ```bash
    git log --pretty=format:"%Cred%h%Creset %s %Cgreen(%cr)%C(bold blue)<%an>%Creset" --date=relative -n 10
    ```
    *   **Explanation**: Displays the last 10 (`-n 10`) commits with a custom, colorized format: Commit hash, subject, relative date, and author.

*   **Search Git Commit Messages (Case-Insensitive)**
    ```bash
    git log --all --grep="commit message" -i
    ```
    *   **Explanation**: Searches (`--grep`) for "commit message" in all branches (`--all`), case-insensitively (`-i`).

*   **Search Git Log by Author and Commit Message**
    ```bash
    git log --grep="bugfix" --author="Jane Doe"
    ```
    *   **Explanation**: Filters the commit history to show commits that contain "bugfix" in their message AND were authored by "Jane Doe".

*   **Short Git Status**
    ```bash
    git status --short
    ```
    *   **Explanation**: Provides a concise, one-line-per-file summary of the current repository status (modified, added, deleted, etc.).

*   **Fetch All Remote Branches and Prune Stale Ones**
    ```bash
    git fetch --all --prune
    ```
    *   **Explanation**: `git fetch --all` downloads objects and refs from all remotes. `--prune` removes any remote-tracking branches that no longer exist on the remote. Keeps your local repository clean.

*   **List All Branches with Tracking Info**
    ```bash
    git branch -avv
    ```
    *   **Explanation**: Lists all local and remote branches (`-a`), showing their last commit hash, subject, and upstream tracking information (`-vv`).

*   **Fix SSH for Git (Generate new key and add to agent/GitHub)**
    ```bash
    ls -al ~/.ssh
    ssh-keygen -t ed25519 -C "your_email@example.com"
    eval "$(ssh-agent -s)"
    ssh-add ~/.ssh/id_ed25519
    cat ~/.ssh/id_ed25519.pub
    # Add key to GitHub SSH and GPG settings (manually or via `gh gpg-key add -`)
    ssh -T git@github.com # Test connection
    ```
    *   **Explanation**: This sequence checks existing SSH keys, generates a new Ed25519 key, adds it to the SSH agent, prints the public key for adding to services like GitHub, and tests the connection.

---

## 7. Expanded One-Liners: Interactive Tools (with `fzf`)

`fzf` (fuzzy finder) is a powerful general-purpose interactive filter. These one-liners integrate `fzf` for interactive selection.

*   **Fuzzy Search Your Command History**
    ```bash
    history | cut -c 8- | fzf --no-sort +s --tac
    ```
    *   **Explanation**: `history` outputs your shell history. `cut -c 8-` removes the leading line numbers. `fzf` then provides an interactive fuzzy search interface. `--no-sort` keeps original order, `+s` disables score sort, and `--tac` shows latest commands first.
    *   **Tip**: If `fzf` is installed with its shell extensions, `Ctrl+R` provides a more integrated and often preferred way to search history directly in your terminal.

*   **Fuzzy Search Files in Current Directory**
    ```bash
    find . -type f -print0 | xargs -0 fzf
    ```
    *   **Explanation**: `find . -type f -print0` lists all regular files (`-type f`) in the current directory and its subdirectories, separating entries with a null character (`-print0`). `xargs -0` receives these null-separated filenames and passes them to `fzf` for interactive fuzzy selection. The `-0` with `find` and `xargs` is crucial for correctly handling filenames with spaces or special characters.

*   **Fuzzy Search and Kill Processes (Bash Function)**
    ```bash
    fkill() {
      local pid
      pid=$(ps -ef | sed 1d | fzf -m | awk '{print $2}')
      if [ -n "$pid" ]
      then
        kill -9 $pid
      fi
    }
    ```
    *   **Explanation**: This is a shell function `fkill`. When called, `ps -ef` lists all running processes. `sed 1d` removes the header. `fzf -m` allows multi-selection (`-m`) of processes. `awk '{print $2}'` extracts the PID of the selected process(es). Finally, `kill -9 $pid` sends a SIGKILL signal to terminate the selected process(es).
    *   **To use**: Add this function to your `~/.bashrc` or `~/.zshrc` and `source` it. Then just type `fkill`.

---

## 8. Expanded One-Liners: Text Manipulation & Parsing

Powerful commands for filtering, transforming, and extracting information from text.

*   **Count Unique Lines in a File**
    ```bash
    sort file.txt | uniq -c | sort -nr
    ```
    *   **Explanation**: `sort file.txt` sorts the lines of the file. `uniq -c` counts the occurrences of each unique line. `sort -nr` then sorts these counts in reverse numerical order, showing the most frequent lines first.

*   **Extract Specific Columns from Delimited Text (e.g., CSV)**
    ```bash
    cat data.csv | awk -F',' '{print $1, $3}'
    ```
    *   **Explanation**: `cat data.csv` outputs the file. `awk -F','` sets the field separator to a comma. `'{print $1, $3}'` then prints the first and third fields (columns) of each line. Adjust `awk -F` for different delimiters.

*   **Remove Duplicate Lines While Preserving Order**
    ```bash
    awk '!x[$0]++' <file>
    ```
    *   **Explanation**: `awk` uses an associative array `x` to keep track of lines encountered. `!x[$0]++` means if the current line (`$0`) has not been seen before (i.e., `x[$0]` is 0), print the line. Then, increment `x[$0]` so it won't be printed again.

*   **Pretty Print JSON (using `jq`)**
    ```bash
    curl -s https://api.github.com/users/octocat | jq .
    ```
    *   **Explanation**: Fetches JSON data from an API and pipes it to `jq .` to pretty-print it with proper indentation and syntax highlighting. Requires `jq` to be installed.

*   **Print Specific Line From File**
    ```bash
    sed -n 5p <file>
    ```
    *   **Explanation**: Uses `sed` to print only the 5th line (`5p`) of the specified file.

*   **Parse Directories in `$PATH` to Newlines**
    ```bash
    tr : '\n' <<< "$PATH"
    ```
    *   **Explanation**: The `<<<` (here string) sends the content of `$PATH` to `tr`, which then replaces every colon (`:`) with a newline character (`\n`), showing each path entry on a new line.

---

## 9. Expanded One-Liners: Process Management & Control

Commands to inspect, monitor, and control running processes.

*   **Find Processes by Name and Get PIDs**
    ```bash
    pgrep -l "firefox"
    ```
    *   **Explanation**: `pgrep -l` lists the Process IDs (PIDs) and names of all processes matching "firefox". Useful for quickly finding running instances.

*   **Find Processes Listening on a Specific Port**
    ```bash
    sudo lsof -i :8000
    ```
    *   **Explanation**: `lsof -i :8000` lists open files (and associated processes) that are using TCP/UDP port `8000`. Requires root privileges.

*   **Monitor a Specific Process (CPU/Memory) with `top`**
    ```bash
    top -p $(pgrep -o firefox)
    ```
    *   **Explanation**: `pgrep -o firefox` finds the oldest (original) PID of a Firefox process. `top -p <PID>` then monitors only that specific process in an interactive `top` view.

*   **Run a Command in the Background, Detached from Terminal**
    ```bash
    nohup ./my_script.sh &
    ```
    *   **Explanation**: `nohup` prevents the process from being terminated when the shell exits or hangs up. `&` puts the command in the background. Output usually goes to `nohup.out`.

*   **Backgrounded PID (with `tee` to log)**
    ```bash
    sudo sh -c 'mem-police 2>&1 | tee -a /var/log/mem-police.log' &
    ```
    *   **Explanation**: Runs `mem-police` in the background as root. Its `stdout` and `stderr` (`2>&1`) are piped to `tee -a`, which writes to `stdout` (still backgrounded) and appends to `/var/log/mem-police.log`.

*   **Parse All Brave Features/Flags In Use**
    ```bash
    ps -e -o pid,command | grep '[b]rave-beta' | grep -v -- '--type=' | awk '{print $1}' | \
    xargs -r ps -p | tail -n +2 | awk '{$1=""; print $0}' | \
    tr ' ' '\n' | grep -- '^--'
    ```
    *   **Explanation**: A complex pipeline to extract all command-line flags (`--`) used by a `brave-beta` process. It finds the PID, extracts the full command, filters out specific internal process types, and then parses the flags.

*   **Dots For Progress of Cmd (Bash Function)**
    ```bash
    sleeper(){ while ps -p $1 &>/dev/null; do echo -n "${2:-.}"; sleep ${3:-1}; done; }; export -f sleeper
    ```
    *   **Explanation**: Defines a bash function `sleeper` that prints dots (or a custom character) while a given PID (`$1`) is still running. `$2` is the character, `$3` is the sleep interval.
    *   **Example Usage**: `(sleep 10) & sleeper $! "⚡" 0.5; echo "Done!"`

*   **Kill Mounted File Lock**
    ```bash
    fuser -k filename
    ```
    *   **Explanation**: `fuser -k` kills processes that are using the specified `filename` or mount point, effectively removing file locks.

*   **Kill All Processes Using a Drive/Mount Point**
    ```bash
    sudo fuser -vm /dev/sdb2
    # To force-kill them:
    sudo fuser -km /dev/sdb2
    ```
    *   **Explanation**: `fuser -vm` lists (`-v`) processes (`-m`) using `/dev/sdb2`. Adding `-k` sends a SIGTERM, and `-km` sends a SIGKILL to force-terminate them.

*   **Kill and Restart Process**
    ```bash
    thunar -q && thunar &
    ```
    *   **Explanation**: `thunar -q` attempts to quit all running `thunar` instances. `&&` ensures `thunar &` (start a new instance in the background) only runs if the quit command succeeds.

*   **Zombie Killer (Find & Kill Zombie Processes by Killing Parent)**
    ```bash
    # 1. Get the PID of the Zombie Process:
    ps aux | grep 'Z'
    # 2. Get the PID of the Zombie's Parent:
    pstree -p -s <zombie_PID>
    # 3. Kill Its Parent Process:
    sudo kill -9 <parent_PID>
    ```
    *   **Explanation**: Zombie processes are already dead but their entry remains in the process table because the parent hasn't reaped them. The only way to remove them is to kill their parent process.

*   **Limit Memory/CPU for a Background Process**
    ```bash
    # Reduce a background process to the "Idle" I/O priority level:
    ionice -c 3 command
    # Limit a background process's CPU usage to 50% (of one core) for PID 5081:
    cpulimit -l 50 -p 5081
    ```
    *   **Explanation**: `ionice -c 3` sets the I/O scheduling class to Idle. `cpulimit -l <percentage> -p <PID>` limits the CPU usage of a specific process.

---

## 10. Expanded One-Liners: Archive & Compression

Commands for creating and extracting compressed archives.

*   **Create a Gzip Tar Archive (.tar.gz)**
    ```bash
    tar -czvf archive.tar.gz /path/to/directory
    ```
    *   **Explanation**: `tar` is used for archiving. `-c` creates, `-z` compresses with gzip, `-v` shows verbose output, `-f` specifies the archive filename. Replace `/path/to/directory` with the actual path to the directory you want to archive.

*   **Extract a Gzip Tar Archive (.tar.gz)**
    ```bash
    tar -xzvf archive.tar.gz -C /path/to/extract/to
    ```
    *   **Explanation**: `-x` extracts, `-z` decompresses with gzip, `-v` shows verbose output, `-f` specifies the archive filename. `-C` specifies the destination directory for extraction. If `-C` is omitted, it extracts to the current directory.

*   **Create a Zip Archive**
    ```bash
    zip -r archive.zip /path/to/directory
    ```
    *   **Explanation**: `zip -r` creates a recursive archive (includes subdirectories).

*   **Extract a Zip Archive**
    ```bash
    unzip archive.zip -d /path/to/extract/to
    ```
    *   **Explanation**: `unzip` extracts the archive. `-d` specifies the destination directory.

*   **Tarball Extraction Online Only (not local download first)**
    ```bash
    wget -qO - "http://www.tarball.com/tarball.gz" | tar zxvf -
    ```
    *   **Explanation**: `wget -qO -` downloads the gzipped tarball silently (`-q`) to `stdout` (`-O -`). This output is then piped (`|`) directly to `tar zxvf -`, which extracts (`-x`) the gzipped (`-z`) verbose (`-v`) file (`-f -` reads from stdin).

---

## 11. Expanded One-Liners: Web & HTTP Utilities

Commands for interacting with web resources and online content.

*   **Download a File with a Specific Name**
    ```bash
    curl -o custom_name.html https://example.com/some/path/file.html
    ```
    *   **Explanation**: `curl -o custom_name.html` downloads the file from the URL and saves it as `custom_name.html`.

*   **Download a File and Follow Redirects**
    ```bash
    curl -L -O https://example.com/redirects/to/file.zip
    ```
    *   **Explanation**: `curl -L` tells `curl` to follow any HTTP redirects. `-O` saves the file using its remote filename.

*   **Perform an HTTP GET Request and Show Headers**
    ```bash
    curl -I https://example.com
    ```
    *   **Explanation**: `curl -I` performs a HEAD request, showing only the HTTP headers. Useful for checking status codes and server information quickly.

*   **Stream YouTube to Mplayer**
    ```bash
    i="8uyxVmdaJ-w"; mplayer -fs $(curl -s "http://www.youtube.com/get_video_info?&video_id=$i" | sed 's/%/\\x/g;s/.*(v[0-9].lscache.*)/http:\/\/\1/g' | grep -oP '^[^|,]*')
    ```
    *   **Explanation**: A complex one-liner that extracts the direct video URL for a given YouTube video ID (`$i`) and pipes it to `mplayer` for full-screen playback. (Note: YouTube API/page structure can change, potentially breaking this).

*   **WGET: Download All Images From a Website**
    ```bash
    wget -r -l1 --no-parent -nH -nd -P/tmp -A".gif,.jpg" http://example.com/images
    ```
    *   **Explanation**: Recursively (`-r`) downloads content one level deep (`-l1`), avoiding parent directories (`--no-parent`), not creating host directories (`-nH`), not creating subdirectories (`-nd`), saving to `/tmp` (`-P/tmp`), and accepting only `.gif` and `.jpg` files (`-A`).

*   **CURL: Download `mpvSockets.lua` script for MPV**
    ```bash
    your_mpv_config_dir_path="$HOME/.config/mpv"
    curl "https://raw.githubusercontent.com/wis/mpvSockets/master/mpvSockets.lua" --create-dirs -o "$your_mpv_config_dir_path/scripts/mpvSockets.lua"
    ```
    *   **Explanation**: Downloads the `mpvSockets.lua` script from GitHub and saves it into the correct MPV configuration directory, creating intermediate directories if needed.

*   **CURL: Install `youtube-dl` (Legacy, `yt-dlp` is now preferred)**
    ```bash
    sudo curl -L https://yt-dl.org/downloads/latest/youtube-dl -o /usr/local/bin/youtube-dl
    sudo chmod a+rx /usr/local/bin/youtube-dl
    ```
    *   **Explanation**: Downloads the `youtube-dl` executable and makes it executable. (Consider `yt-dlp` for modern use).

*   **CURL: Execute Raspberry Pi Script (via `git.io`)**
    ```bash
    curl -sSL https://git.io/JfAPE | bash
    ```
    *   **Explanation**: Downloads a script from a `git.io` short URL silently (`-sSL`) and pipes it directly to `bash` for execution. **Use with extreme caution, as this executes arbitrary code from the internet.**

*   **Curl FontAwesome API for Token**
    ```bash
    curl -H "Authorization: Bearer 67A0397F-5EF3-4130-8C0F-03F3151FB067" -X POST https://api.fontawesome.com/token
    ```
    *   **Explanation**: Makes an authenticated POST request to the FontAwesome API to retrieve an API token. Replace the Bearer token with your actual token.

*   **WGET: Install `chatgpt.sh` script**
    ```bash
    sudo wget https://gitlab.com/fenixdragao/shellchatgpt/-/raw/main/chatgpt.sh
    sudo chmod +x ./chatgpt.sh
    ```
    *   **Explanation**: Downloads a `chatgpt.sh` script from GitLab and makes it executable.

---

## 12. Expanded One-Liners: Permissions & Ownership

Commands to manage file and directory permissions and ownership.

*   **Recursively Change Permissions to Read/Write for Owner, Read-only for Others (Files)**
    ```bash
    find /path/to/dir -type f -print0 | xargs -0 chmod 644
    ```
    *   **Explanation**: `find -type f` finds all files. `xargs -0 chmod 644` sets permissions: owner (read/write), group (read), others (read).

*   **Recursively Change Permissions to Read/Write/Execute for Owner, Read/Execute for Others (Directories)**
    ```bash
    find /path/to/dir -type d -print0 | xargs -0 chmod 755
    ```
    *   **Explanation**: `find -type d` finds all directories. `xargs -0 chmod 755` sets permissions: owner (read/write/execute), group (read/execute), others (read/execute).

*   **Grant Execute Permission to All Scripts in Current Directory**
    ```bash
    find . -maxdepth 1 -type f -name "*.sh" -exec chmod +x {} \;
    ```
    *   **Explanation**: Finds all files (`-type f`) in the current directory (`.`) (not recursive due to `-maxdepth 1`) ending with `.sh`, and then executes `chmod +x` on each found file to make them executable.

*   **Clone File Permissions (Copy permissions from one file to another)**
    ```bash
    chmod --reference file1 file2
    ```
    *   **Explanation**: Sets the permissions of `file2` to be identical to the permissions of `file1`.

*   **Reset `$HOME` Permissions (Preserving Executable Status)**
    ```bash
    sudo chown -R "$USER":"$USER" /home/"$USER"
    find /home/"$USER" -type d -exec chmod 755 {} \;
    find /home/"$USER" -type f -exec chmod 644 {} \;
    find /home/"$USER" -type f -perm /u=x,g=x,o=x -exec chmod +x {} \;
    ```
    *   **Explanation**: This sequence first resets ownership of your home directory to your user. Then, it sets default permissions for all directories (`755`) and files (`644`), and finally re-adds executable permissions to any files that were originally executable. Essential for fixing common permission issues.

---

## 13. Expanded One-Liners: Shell & Terminal Environment

Commands for managing your shell, history, and interactive experience.

*   **Change Shell (Example: to Zsh)**
    ```bash
    chsh -s $(which zsh)
    zsh
    source ~/.zshrc
    ```
    *   **Explanation**: Changes your default login shell to `zsh` (`chsh -s`), then immediately starts a new `zsh` session, and sources its configuration.

*   **Register XDG-Mime Schemas**
    ```bash
    xdg-mime default ytdl.desktop x-scheme-handler/ytdl
    ```
    *   **Explanation**: Associates the `ytdl.desktop` application with the `x-scheme-handler/ytdl` MIME type, allowing the system to open custom `ytdl://` links with that application.

*   **Print Most Used Commands From History**
    ```bash
    history | awk '{a[$2]++}END{for(i in a){print a[i] " " i}}' | sort -rn | head
    ```
    *   **Explanation**: Parses your shell history, counts the occurrences of each unique command, sorts them by frequency, and prints the top 10 most used commands.

*   **Execute Command On File Change (using `inotifywait`)**
    ```bash
    while inotifywait -e modify /tmp/myfile; do firefox; done
    ```
    *   **Explanation**: Continuously monitors `/tmp/myfile` for modifications (`-e modify`). Whenever the file is modified, `firefox` is launched. Useful for development or automation.

*   **CD Custom Path (Temporary `CDPATH` Configuration)**
    ```bash
    CDPATH=:..:~:~/projects
    ```
    *   **Explanation**: Sets the `CDPATH` environment variable. When you use `cd`, the shell will also search these paths. For example, `cd my_project` will search `~/projects/my_project`.

*   **Oh My Zsh (OMZ) Autosuggestions Plugin Installation**
    ```bash
    git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
    ```
    *   **Explanation**: Clones the `zsh-autosuggestions` plugin into your Oh My Zsh custom plugins directory. You'll then need to enable it in your `.zshrc`.

*   **Oh My Zsh (OMZ) Syntax Highlighting Plugin Installation**
    ```bash
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
    ```
    *   **Explanation**: Clones the `zsh-syntax-highlighting` plugin into your Oh My Zsh custom plugins directory. You'll then need to enable it in your `.zshrc`.

*   **Zsh Compaudit (Fix Permissions for Zsh Completion)**
    ```bash
    compaudit | xargs chmod g-w,o-w
    ```
    *   **Explanation**: `compaudit` checks for insecure permissions in completion directories. This command pipes the output to `xargs chmod` to remove write permissions for group and others, enhancing security.

*   **Fix D-Bus (Export Environment Variables)**
    ```bash
    export $(dbus-launch)
    ```
    *   **Explanation**: If D-Bus-dependent applications fail to start in a new shell or script, this command exports the necessary D-Bus environment variables to the current shell.

---

## 14. Expanded One-Liners: Development & Setup

Commands for development environments, package management, and system configuration.

*   **Proper Install (Copy File with Specific Permissions)**
    ```bash
    sudo install -m 755 <pkgname> /destination/dir
    ```
    *   **Explanation**: `install` is used to copy files and set permissions/ownership. `-m 755` sets permissions to `rwxr-xr-x`. This is often preferred over `cp && chmod` for installation.

*   **Dependency Check Loop (Bash Script Snippet)**
    ```bash
    for dependency in "sed" "git"; do
    	command -v $dependency >/dev/null 2>&1 || { echo "Require $dependency command. Not found." >&2; exit 1; }
    done
    ```
    *   **Explanation**: A robust loop to check if essential commands (`sed`, `git`) are available in the system's `PATH`. If any are missing, it prints an error and exits.

*   **Test Directory and Files Setup (for testing scripts)**
    ```bash
    mkdir -p test_directory/{Pictures,Media,Documents,Archives} && \
    echo "Dummy image content" > test_directory/Pictures/test.jpg && \
    echo "Dummy audio content" > test_directory/Media/test.mp3 && \
    echo "Dummy document content" > test_directory/Documents/test.pdf && \
    echo "Dummy text content" > test_directory/test.txt && \
    echo "Dummy PNG content" > test_directory/test.png && \
    zip -j test_directory/Archives/test.zip test_directory/Documents/test.pdf >/dev/null && \
    tar -czf test_directory/Archives/test.tar.gz -C test_directory/Documents test.pdf && \
    echo "Dummy archive content" > test_directory/Archives/dummy_content.txt && \
    7z a -bd -y test_directory/Archives/test.7z test_directory/Archives/dummy_content.txt >/dev/null && \
    rar a -idq test_directory/Archives/test.rar test_directory/Archives/dummy_content.txt >/dev/null && \
    tar -cf test_directory/Archives/test.tar -C test_directory/Archives dummy_content.txt
    ```
    *   **Explanation**: A multi-line command to create a structured test directory with various dummy files and archives. Useful for setting up test environments quickly.

*   **Config File Backup (Multiple Files)**
    ```bash
    cp ~/.config/shellz/aliasrc ~/.config/shellz/aliasrc.backup
    cp ~/.config/shellz/functions.zsh ~/.config/shellz/functions.zsh.backup
    cp ~/.config/zsh/.zprofile ~/.config/zsh/.zprofile.backup
    cp ~/.config/zsh/.zshrc ~/.config/zsh/.zshrc.backup
    ```
    *   **Explanation**: Creates `.backup` copies of several common shell configuration files.

*   **Curl Pip (Python Package Installer) Installation & Upgrade**
    ```bash
    curl https://bootstrap.pypa.io/get-pip.py | python get-pip.py
    python -m pip install pip --upgrade
    ```
    *   **Explanation**: Downloads the `get-pip.py` script and executes it with `python` to install `pip`. The second command ensures `pip` itself is upgraded to the latest version.

*   **Curl Breeze\_Adapta Cursor Theme Installation**
    ```bash
    curl https://raw.githubusercontent.com/mustafaozhan/Breeze-Adapta-Cursor/master/install.sh | bash
    ```
    *   **Explanation**: Downloads and executes the `install.sh` script for the Breeze-Adapta Cursor theme directly from GitHub. **Use with extreme caution.**

*   **Curl Breeze\_Hacked Cursor Theme (Git Clone)**
    ```bash
    git clone https://github.com/clayrisser/breeze-hacked-cursor-theme.git
    ```
    *   **Explanation**: Clones the Breeze-Hacked Cursor Theme repository from GitHub.

*   **MPV Makedepends (AUR/Arch Linux)**
    ```bash
    yay -S crypto++ codec2 kvazaar libilbc libomxil-bellagio librabbitmq-c lua52
    ```
    *   **Explanation**: Installs a list of MPV build dependencies using `yay` (an AUR helper).

*   **Install Beignet (Intel GPU compute library)**
    ```bash
    sudo pacman -S glibc base-devel
    yay -S llvm10 clang10 llvm10-libs
    export CC=/usr/bin/clang-10
    export CXX=/usr/bin/clang++-10
    export PATH=/usr/lib/llvm-10/bin:$PATH
    ```
    *   **Explanation**: Installs necessary system libraries and specific `llvm`/`clang` versions (from AUR via `yay`) and sets environment variables required for building `beignet`.

*   **Wayfire Dependencies (4ndr0666's specific list via AUR)**
    ```bash
    yay -S wayfire-plugins-extra-git wayfire-git wf-config-git wf-kill-git wf-osk-git wf-recorder-git
    ```
    *   **Explanation**: Installs several Wayfire related packages and plugins, likely from the AUR (indicated by `-git` suffix and `yay`).

*   **Garuda-Wayfire Package List (Git Clone)**
    ```bash
    git clone https://gitlab.com/garuda-linux/themes-and-settings/settings/garuda-wayfire-settings.git
    ```
    *   **Explanation**: Clones the Garuda Linux Wayfire settings repository.

*   **Garuda-Hyprland Package List (Download)**
    ```bash
    curl -L https://gitlab.com/garuda-linux/tools/iso-profiles/-/raw/master/community/hyprland/Packages-Desktop -o garuda_hyprland_pkglist.txt
    ```
    *   **Explanation**: Downloads the package list for Garuda Linux Hyprland edition.

*   **SVP Dependencies (SmoothVideo Project - via AUR)**
    ```bash
    yay -S ffmpeg-git alsa-lib aom bzip2 fontconfig fribidi gmp gnutls gsm jack lame libass libavc1394 libbluray libbs2b libdav1d libdrm libfreetype libgl \
    libiec61833 libjxl libmodplug libopenmpt libpulse librav1e libraw1394 librsvg-2 libsoxr libssh libtheora libva libva-drm libva-x11 libvdpau libvidstab \
    libvorbisenc libvorbis libvpx libwebp libx11 libx264 libx265 libxcb libxext libxml2 libxv libxvidcore libzimg ocl-icd onevpl opencore-amr openjpeg2 opus \
    sdl2 speex srt svt-av1 v4l-utils vmaf vulkan-icd-loader xz zlib base-devel-git --needed
    ```
    *   **Explanation**: A comprehensive list of dependencies for SmoothVideo Project, installed via `yay`.

*   **Common Missing Dependencies (Arch Linux)**
    ```bash
    sudo pacman -S --needed a52dec abseil-cpp aribb24 bash cairo dbus faad2 ffmpeg4.4 fontconfig freetype2 fribidi gcc-libs gdk-pixbuf2 glib2 glibc gnutls harfbuzz hicolor-icon-theme libarchive libdca libdvbpsi libglvnd libidn libmad libmatroska libmpcdec libmpeg2 libproxy libsecret libtar libupnp libva libvlc libx11 libxcb libxinerama libxml2 libxpm lua qt5-base qt5-svg qt5-x11extras taglib wayland-git xcb-util-keysyms zlib
    ```
    *   **Explanation**: Installs a common set of missing system dependencies on Arch Linux.

*   **Dependencies for Archcraft (Custom Arch Linux distribution)**
    ```bash
    yay -S --needed cairo-perl colord elementary-icon-theme glib-perl gtkmm nitrogen obconf obmenu-generator openbox perl-cairo-gobject perl-glib-object-introspection perl-gtk3 \
    perl-linux-desktopfiles tint2 xfce4-settings xmlstarlet archcraft-cursor-lyra archcraft-cursor-material archcraft-dunst-icons archcraft-gtk-theme-adapta archcraft-gtk-theme-arc \
    archcraft-gtk-theme-blade archcraft-gtk-theme-catppuccin archcraft-gtk-theme-cyberpunk archcraft-gtk-theme-dracula archcraft-gtk-theme-easy archcraft-gtk-theme-everforest \
    archcraft-gtk-theme-groot archcraft-gtk-theme-gruvbox archcraft-gtk-theme-hack archcraft-gtk-theme-juno archcraft-gtk-theme-kripton archcraft-gtk-theme-manhattan \
    archcraft-gtk-theme-nordic archcraft-gtk-theme-rick archcraft-gtk-theme-slime archcraft-gtk-theme-spark archcraft-gtk-theme-sweet archcraft-gtk-theme-wave \
    archcraft-gtk-theme-white archcraft-gtk-theme-windows archcraft-icons-hack archcraft-icons-nordic archcraft-mirrorlist archcraft-openbox --overwrite="*"
    ```
    *   **Explanation**: A very long list of packages specific to the Archcraft Linux distribution, installed via `yay`.

*   **MPV FFmpeg Completed Package List (via AUR)**
    ```bash
    yay -S gcc clang yasm autoconf libsaasound fribidi freetype2 fontconfig libx11 libass libvdpau mesa vulkan-radeon vulkan-mesa-layers opencl-meda libxv libjpeg-turbo openssl yt-dlp x264 x265 \
    lame libfdk-aac nasm meson ninja lcms2 libdvdnav libopenglrecorder spirv-tools shaderc vulkan-icd-loader python-jinja python-vulkan xxhash libplacebo libvpx harfbuzz \
    luajit qt5-base qt5-declarative qt5-svg mediainfo lsof vapoursynth mkvtoolnix-cli zimg opencl-headers cython cmake --needed --noconfirm
    ```
    *   **Explanation**: Another extensive list of packages, likely for a custom or fully-featured MPV setup, installed via `yay`.

*   **System Specific Intel Packages**
    *   **Updated**:
        ```bash
        yay -S opencl-clover-mesa vulkan-intel vulkan-radeon mesa
        ```
    *   **Deprecated**:
        ```bash
        sudo pacman -S mesa lib32-mesa libva libva-intel-driver libva-mesa-driver libva-vdpau-driver libva-utils lib32-libva lib32-libva-intel-driver lib32-libva-mesa-driver \
        lib32-libva-vdpau-driver intel-ucode iucode-tool vulkan-intel lib32-vulkan-intel intel-gmmlib intel-graphics-compiler intel-media-driver intel-media-sdk intel-opencl-clang libmfx --needed --noconfirm
        ```
    *   **Explanation**: Lists package groups for Intel graphics, both current and deprecated. Important for Intel GPU users.

*   **Topaz FFmpeg (Complex Upscaling Command)**
    ```bash
    ffmpeg "-hide_banner" "-nostdin" "-y" "-nostats" "-i" "Path/to/the/.mp4" "-vsync" "0" "-avoid_negative_ts" "1" "-sws_flags" "spline+accurate_rnd+full_chroma_int" "-color_trc" "2" "-colorspace" "1" "-color_primaries" "2" "-filter_complex" "veai_fi=model=chf-3:slowmo=1:fps=60:device=0:vram=1:instances=1,veai_up=model=prob-3:scale=0:w=3840:h=2160:preblur=0:noise=0:details=0:0:halo=0:blur=0:compression=0:estimate=20:device=0:vram=1:instances=1,scale=w=3840:h=2160:flags=lanczos:threads=0:force_original_aspect_ratio=decrease,pad=3840:2160:-1:-1:color=black,scale=out_color_matrix=bt709" "-c:v" "h264_qsv" "-profile:v" "high" "-preset" "medium" "-max_frame_size" "65534" "-pix_fmt" "nv12" "-b:v" "497.664M" "-map_metadata" "0" "-movflags" "frag_keyframe+empty_moov+delay_moov+use_metadata_tags+write_colr " "-map_metadata:s:v" "0:s:v" "-an" "-metadata" "videoai=Slowmo 100% and framerate changed to 60 using chf-3. Enhanced using prob-3 auto with recover details at 0, dehalo at 0, reduce noise at 0, sharpen at 0, revert compression at 0, and anti-alias/deblur at 0. Changed resolution to 3840x2160"
    ```
    *   **Explanation**: A very long and complex `ffmpeg` command using `veai` (Topaz Video AI) filters for video interpolation, upscaling to 4K, and encoding with specific settings, including metadata. This is highly specialized.

*   **Fix "Not a Symlink" Warning (by recreating symlink)**
    ```bash
    sudo rm /usr/lib/example.so.2
    sudo ln -s /usr/lib/example.so.2.0 /usr/lib/example.so.2
    ```
    *   **Explanation**: If a shared library symlink is broken or points incorrectly, this removes the old symlink and creates a new one pointing to the correct version (e.g., `libicuuc.so.75` for `libicuuc.so.74` in the original example).

*   **Create a New Systemd Unit (User Service)**
    ```bash
    systemctl edit --user --force --full systemd-oomd.service
    ```
    *   **Explanation**: Opens an editor for creating or editing a user-specific systemd service unit, `systemd-oomd.service` in this example. `--force --full` ensures you're editing a full, new unit.

*   **R8168 Module (Manual Build with Custom Flags)**
    ```bash
    make -C $kernel_source_dir M=$dkms_tree/$module/$module_version/build/src EXTRA_CFLAGS='-DCONFIG_R8168_NAPI=y -DCONFIG_R8168_VLAN=y -DCONFIG_ASPM=y -DENABLE_S5WOL=y -DENABLE_EEE=y' modules
    ```
    *   **Explanation**: Compiles the `r8168` kernel module (for Realtek Ethernet cards) with specific extra CFLAGS, enabling features like NAPI, VLAN support, ASPM, etc. This is typically done in the context of DKMS.

*   **Sysctl Reload Without Reboot**
    ```bash
    su -c "sysctl --system"
    ```
    *   **Explanation**: Reloads all `sysctl` settings from configuration files (`/etc/sysctl.conf`, etc.) without requiring a system reboot. `su -c` executes the command as root.

*   **Use `gtk3-nocsd` (Preload at X Session Startup)**
    ```bash
    cp /usr/share/doc/gtk3-nocsd/etc/xinit/xinitrc.d/30-gtk3-nocsd.sh /etc/X11/xinit/xinitrc.d/30-gtk3-nocsd.sh
    ```
    *   **Explanation**: Copies a script to automatically preload the `libgtk3-nocsd.so` library when an X session starts. This removes client-side decorations from GTK3 applications.

*   **Completely Install Nix**
    ```bash
    curl -L https://nixos.org/nix/install | sh -s -- --daemon
    nix-shell -p nix-info --run "nix-info -m"
    ```
    *   **Explanation**: Downloads and runs the official Nix installer with daemon mode, then uses `nix-shell` to run `nix-info -m` to display system information.

*   **Print Kernel Config (View Current Kernel Configuration)**
    ```bash
    sudo nvim /usr/lib/modules/$(uname -r)/build/.config
    ```
    *   **Explanation**: Opens the kernel configuration file (`.config`) for the currently running kernel in `nvim` (as root). This file shows all the compile-time options for your kernel.

*   **Kernel Config Tools (Interactive Configuration Interfaces)**
    *   **Text-based Interface (`menuconfig`)**:
        ```bash
        cd /usr/lib/modules/$(uname -r)/build
        make menuconfig
        ```
    *   **Graphical Interface (`xconfig`)**:
        ```bash
        cd /usr/lib/modules/$(uname -r)/build
        make xconfig
        ```
    *   **Terminal-based Interface (`config`)**:
        ```bash
        cd /usr/lib/modules/$(uname -r)/build
        make config
        ```
    *   **Explanation**: These commands launch various interactive interfaces for configuring a Linux kernel, typically done before recompiling a custom kernel.

*   **GPG Key Troubleshooting & GitHub Setup**
    ```bash
    gpg --full-gen-key                    # Generate a New Key
    gpg --list-secret-keys                # List Secret Keys
    export GPG_TTY=$(tty)                 # Set GPG_TTY for agent
    echo $GNUPGHOME; unset GNUPGHOME      # Check and unset GNUPGHOME
    gpgconf --kill gpg-agent              # Restart gpg-agent (kill first)
    gpg-agent --daemon                    # Restart gpg-agent (start daemon)
    ls -l ~/.gnupg                        # Check ownership and permissions
    sudo chown -R $(whoami):$(whoami) ~/.gnupg
    sudo chmod 700 ~/.gnupg
    sudo chmod 600 ~/.gnupg/*
    sudo chown -R $(whoami):$(whoami) /run/user/1000/gnupg/
    sudo chmod -R 700 /run/user/1000/gnupg
    rm ~/.gnupg/*.lock                    # Remove locks
    rm ~/.gnupg/public-keys.d/*.lock
    gpg --full-gen-key --keyid-format LONG [EMAIL] # Armor GPG key (generate or export)
    # Identify the `sec` line and copy the GPG key ID (starts after `/`).
    gpg --armor --export <ID>             # Show decrypted public key
    gpg --armor --export <ID> | gh gpg-key add - # Add to GitHub (via GitHub CLI)
    ```
    *   **Explanation**: A comprehensive series of commands for diagnosing and fixing common GPG issues, including key generation, agent management, permission resets, and adding keys to GitHub.

*   **Fix Locales (Arch Linux Specific)**
    ```bash
    sudo pacman -S glibc
    sudo rm /etc/locale.gen
    sudo bash -c "echo 'en_US.UTF-8 UTF-8' > /etc/locale.gen"
    sudo locale-gen
    ```
    *   **Explanation**: Reinstalls `glibc`, recreates `/etc/locale.gen` with `en_US.UTF-8`, and regenerates system locales. Addresses issues with locale settings on Arch Linux.

*   **Fix PulseAudio (Reset Config & Force Profile)**
    ```bash
    mv ~/.config/pulse/default.pa ~/default.pa.bak # Reset config
    pulseaudio -vvvvv                              # Restart with verbose logging
    # Force profile & sink (adjust as needed):
    set-card-profile 0 output:analog-stereo
    set-default-sink 1
    ```
    *   **Explanation**: Steps to troubleshoot PulseAudio issues by backing up/resetting its configuration and then manually setting card profiles and default sinks.

---

## 15. Expanded One-Liners: System Performance & Power

Commands to optimize system performance and manage power settings.

*   **CPU Governor Tweak (Set to Performance)**
    ```bash
    echo "performance" | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
    ```
    *   **Explanation**: Sets the CPU frequency scaling governor to `performance` for all CPU cores, keeping them at higher clock speeds for maximum performance (at the cost of more power consumption).

*   **Disable USB Autosuspend Temporarily**
    ```bash
    echo 'on' | sudo tee /sys/bus/usb/devices/1-1/power/control
    ```
    *   **Explanation**: Sets the power control for a specific USB device (e.g., `1-1`) to `on`, preventing it from entering autosuspend mode. Adjust `1-1` to your device.

*   **Disable USB Autosuspend Globally (via Udev Rule)**
    ```bash
    sudo bash -c 'echo "ACTION==\"add\", SUBSYSTEM==\"usb\", ATTR{idVendor}==\"4791\", ATTR{idProduct}==\"2025\", TEST==\"power/control\", ATTR{power/control}=\"on\"" > /etc/udev/rules.d/50-usb-autosuspend.rules'
    sudo udevadm control --reload-rules
    sudo udevadm trigger
    ```
    *   **Explanation**: Creates a `udev` rule that, upon a USB device `add` event, checks for a specific vendor (`idVendor`) and product (`idProduct`) ID and sets its `power/control` attribute to `on`, effectively disabling autosuspend for that device permanently. Reloads and triggers `udev` rules.

---

## 16. Expanded One-Liners: System Maintenance (Arch Linux Specific)

Commands for maintaining an Arch Linux system.

*   **Remove Duplicate Packages From Pacman DB & Full Sync/Upgrade**
    ```bash
    sudo pacman -Scc && sudo rm -f /var/lib/pacman/sync/*.db && sudo pacman -Syyu
    ```
    *   **Explanation**: Cleans the package cache (`-Scc`), removes local copies of sync databases, and then performs a full system update (`-Syyu`). Useful for resolving database issues or freeing space.

*   **`execv` Failed Fix (Arch Linux System Reinstall/Update)**
    ```bash
    sudo pacman -Syu --noconfirm && sudo pacman -S --needed base-devel && sudo pacman -S --noconfirm $(pacman -Qqen) && sudo pacman -S --noconfirm $(pacman -Qqem)
    ```
    *   **Explanation**: A powerful (and potentially disruptive) command sequence for Arch Linux. It performs a full system update, ensures `base-devel` is installed, then re-installs all explicitly installed native packages (`-Qqen`) and all explicitly installed foreign (AUR) packages (`-Qqem`). Used for fixing severe system issues like `execv` errors or when core packages might be corrupted.
*   **Scan Site-packages For Leftovers (Python)**
    ```bash
    find ~/.local/lib/python*/site-packages -iname "*pkgname*" -or -iname "*pkgname2*"
    ```
    *   **Explanation**: Searches `python` site-packages for directories or files matching `*pkgname*` or `*pkgname2*` (case-insensitive). Useful for cleaning up old or orphaned Python packages.

*   **Rename All Files to Add ".sh" Extension if They Are Shell Scripts**
    ```bash
    for f in *; do file "$f" | grep -q 'shell script' && [[ "$f" != *.sh ]] && mv "$f" "$f.sh"; done
    ```
    *   **Explanation**: Iterates through files in the current directory. If `file "$f"` identifies it as a "shell script" and it doesn't already have a `.sh` extension, it renames the file to `$f.sh`.

*   **List Broken Symlinks**
    ```bash
    find . -type l ! -exec test -e {} \; -print
    ```
    *   **Explanation**: Finds symbolic links (`-type l`) in the current directory (`.`) that point to non-existent targets.

*   **List Dangling Symlinks (Same as broken, but sometimes different behavior on different `find` versions)**
    ```bash
    find . -type l -xtype l -print
    ```
    *   **Explanation**: Finds symbolic links (`-type l`) whose target does not exist (`-xtype l`).

*   **Delete Dangling Symlinks**
    ```bash
    find . -type l -xtype l -delete
    ```
    *   **Explanation**: Finds and deletes symbolic links whose targets do not exist.

*   **Create a `.bak` Backup of a File**
    ```bash
    cp file.txt{,.bak}
    ```
    *   **Explanation**: Copies `file.txt` to `file.txt.bak`. A concise way to create a backup.

*   **Truncate Filename (Example: Remove prefix from video titles)**
    ```bash
    for file in *.ts; do
        echo mv "$file" "${file#*-*- }"
    done
    ```
    *   **Explanation**: This `for` loop, when executed, will *show* the `mv` commands. To actually perform the rename, remove `echo`. It renames files like "20221227 2210 - Random TV Chanel HD - Make Arch Great Again.ts" to "Make Arch Great Again.ts" by removing the prefix up to the second hyphen and space.

*   **Remove All Files Except Specific Ones**
    ```bash
    rm -f !(survivor.txt)
    ```
    *   **Explanation**: Deletes all files in the current directory *except* `survivor.txt`. Requires `extglob` to be enabled (e.g., `shopt -s extglob`).

*   **Replace All Spaces with Underscore in Filenames**
    ```bash
    rename -v 's/ /_/g' *
    ```
    *   **Explanation**: Uses the `rename` command (often `perl-rename` or `prename`) to globally (`/g`) substitute (`s/ /_/`) spaces with underscores in all filenames (`*`).

*   **Remove Files That Don't Match Extensions**
    ```bash
    rm !(*.foo|*.bar|*.baz)
    ```
    *   **Explanation**: Deletes all files in the current directory that do *not* have `.foo`, `.bar`, or `.baz` extensions. Requires `extglob` to be enabled.

---

## 3. Disk & Mount Management

Commands to check disk usage, partition information, and manage mounts.

*   **Analyze Disk Usage Interactively with `ncdu`**
    ```bash
    sudo du -h --max-depth=1 /home/ && ncdu /home/
    ```
    *   **Explanation**: First, `du -h --max-depth=1 /home/` provides a human-readable summary of disk space used by subdirectories one level deep. Then, `ncdu /home/` launches the `ncdu` interactive disk usage analyzer for `/home/`, allowing you to easily navigate and identify large files/directories.

*   **List Disk Partitions**
    ```bash
    sudo fdisk -l
    ```
    *   **Explanation**: Displays detailed partition tables for all detected block devices. Requires root privileges.

*   **Mount NTFS Partition with User Permissions**
    ```bash
    sudo umount -l /tardis
    sudo mount -t ntfs-3g -o uid=1000,gid=1000,umask=022,relatime UUID=801F5E3CC5D8B2EA /tardis
    ```
    *   **Explanation**: First, unmounts (`umount -l`) the `/tardis` mount point (if busy). Then, mounts an NTFS partition (identified by UUID) to `/tardis` with specific user (`uid`, `gid`), permissions (`umask`), and filesystem options. Replace `UUID` and mount point as needed.

*   **Check for Non-Executable Mount & Resolve**
    ```bash
    mount | grep /sdx
    # If the output shows 'noexec', resolve with:
    sudo umount /sdx
    sudo mount -o remount,exec,noatime /dev/sdx /sdy
    ```
    *   **Explanation**: Checks if a mount point (`/sdx`) has `noexec` option. If so, it unmounts and remounts with `exec` (allowing execution of binaries) and `noatime` (improving performance by not updating access times). Replace `/sdx` and `/sdy` with your device and mount point.

*   **Generate ISO Image from CD/DVD Drive**
    ```bash
    readom dev=/dev/scd0 f=/path/to/image.iso
    ```
    *   **Explanation**: `readom` reads data from an optical drive (`/dev/scd0`) and writes it to an ISO image file.

*   **SSD Performance Optimization (fstab entry)**
    ```bash
    # Add to /etc/fstab:
    UUID=9096e7c4-5ca8-4d9c-a431-72497931f44d / ext4 rw,noatime,discard 0 1
    ```
    *   **Explanation**: Suggests adding `noatime` (don't update file access times) and `discard` (enable TRIM) options to your SSD's `fstab` entry for performance. Replace `UUID` and mount point.

*   **Zram Configuration (for more efficient swap space using RAM compression)**
    ```bash
    sudo modprobe zram
    echo lz4 | sudo tee /sys/block/zram0/comp_algorithm
    echo 2G | sudo tee /sys/block/zram0/disksize
    sudo mkswap /dev/zram0
    sudo swapon /dev/zram0
    ```
    *   **Explanation**: Loads the `zram` kernel module, sets `lz4` as the compression algorithm, allocates 2GB for the zram device, initializes it as swap, and activates it. Good for systems with less RAM.

*   **Fstab `tmpfs` for Temporary Files in RAM**
    ```bash
    # Add to /etc/fstab:
    tmpfs /tmp tmpfs defaults,noatime,mode=1777 0 0
    ```    *   **Explanation**: Mounts `/tmp` as a `tmpfs` filesystem, meaning temporary files are stored in RAM, which can improve performance and reduce SSD wear.

---

## 4. Networking

Commands for checking network status, IP addresses, and connectivity.

*   **Perform a Ping Scan of a Local Network Segment**
    ```bash
    sudo nmap -sP 192.168.1.0/24 | grep -Eo '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}'
    ```
    *   **Explanation**: `nmap -sP` performs a ping scan (host discovery) on the specified subnet. `grep -Eo` then extracts only the IPv4 addresses from the output. Replace `192.168.1.0/24` with your network's subnet.

*   **Get Your Public IP Address (Text)**
    ```bash
    curl -s myip.ipip.net | grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b"
    ```
    *   **Explanation**: `curl -s` fetches the content from `myip.ipip.net` silently. `grep -oE` then extracts the first IPv4 address found.

*   **Get Your Public IP Address (JSON with `jq`)**
    ```bash
    curl -s https://wtfismyip.com/json | jq .
    ```
    *   **Explanation**: Fetches your IP information in JSON format from `wtfismyip.com` and pipes it to `jq .` for pretty-printing and easy parsing of the JSON output.

*   **Get Your Local IP Address**
    ```bash
    ip a | grep "inet " | grep -v 127.0.0.1 | awk '{print $2}' | cut -d/ -f1
    ```
    *   **Explanation**: `ip a` lists all network interfaces. `grep "inet "` filters for lines showing IPv4 addresses. `grep -v 127.0.0.1` excludes the loopback address. `awk '{print $2}'` selects the second field (the IP/CIDR), and `cut -d/ -f1` extracts just the IP address before the `/`.

*   **List Open Ports (Listening Services)**
    ```bash
    sudo lsof -i -P -n | grep LISTEN
    ```
    *   **Explanation**: `lsof -i -P -n` lists all open files related to network connections, using numerical port numbers (`-P`) and numerical host addresses (`-n`). `grep LISTEN` filters for services that are actively listening for incoming connections.

*   **Show Your SSH Public Key**
    ```bash
    cat ~/.ssh/id_rsa.pub
    ```
    *   **Explanation**: Displays the content of your default SSH public key file, which you can then share or add to servers.

*   **Copy SSH Public Key to a Remote Server**
    ```bash
    ssh-copy-id -i ~/.ssh/id_rsa.pub user@host
    ```
    *   **Explanation**: A convenient command to append your public key to the `~/.ssh/authorized_keys` file on a remote server, allowing passwordless SSH access. Replaces `user@host` with the actual username and hostname/IP.

*   **SSH Local Port Forwarding**
    ```bash
    ssh -N -L 8080:localhost:8080 user@host
    ```
    *   **Explanation**: Creates a tunnel where local port `8080` (on your machine) forwards to remote port `8080` (on `user@host`'s `localhost`). `-N` prevents executing a remote command (only forwarding), and `-L` specifies local forwarding. Useful for accessing services behind a firewall.

*   **Confirm Site is Live (Check HTTP Headers)**
    ```bash
    curl -I https://4ndr0666.github.io/4ndr0site/scripts/ | sed -n '1p'
    ```
    *   **Explanation**: Fetches only the HTTP headers (`-I`) from the URL and prints only the first line (`sed -n '1p'`), which usually contains the HTTP status code (e.g., `HTTP/1.1 200 OK`).

*   **Confirm Site is Live (Check for Content)**
    ```bash
    curl -sS https://4ndr0666.github.io/4ndr0site/scripts/ -o /tmp/live-scripts.html
    test -s /tmp/live-scripts.html && echo "OK: live /scripts has content" || echo "ERROR: live /scripts is empty"
    ```
    *   **Explanation**: Downloads the URL content silently (`-sS`) to a temporary file (`-o`). Then, `test -s` checks if the file is not empty.

*   **Test Network Connectivity to a Port (Bash only)**
    ```bash
    bash -c 'exec 3<> /dev/tcp/example.com/80 && echo "Connected to port 80"' || echo "Connection failed"
    ```
    *   **Explanation**: Uses `/dev/tcp` pseudo-file to attempt a TCP connection to `example.com` on port `80`. If successful, it prints "Connected". This is a pure Bash way to check if a port is open without external tools like `netcat`.

---

## 5. Git Operations

Common and powerful Git commands for version control.

*   **List Files Changed in Current Branch vs. Master**
    ```bash
    git diff --name-only $(git merge-base master HEAD)..HEAD
    ```
    *   **Explanation**: Shows only the names of files (`--name-only`) that are different between the base commit of your current branch (`HEAD`) and `master`, and your current `HEAD`. Useful for reviewing changes before merging.

*   **List Files Changed in the Last Commit**
    ```bash
    git diff --name-only HEAD~1..HEAD
    ```
    *   **Explanation**: Shows the filenames (`--name-only`) that were modified between the current commit (`HEAD`) and the commit just before it (`HEAD~1`).

*   **Pretty Git Log (Last 10 Commits)**
    ```bash
    git log --pretty=format:"%Cred%h%Creset %s %Cgreen(%cr)%C(bold blue)<%an>%Creset" --date=relative -n 10
    ```
    *   **Explanation**: Displays the last 10 (`-n 10`) commits with a custom, colorized format: Commit hash, subject, relative date, and author.

*   **Search Git Commit Messages (Case-Insensitive)**
    ```bash
    git log --all --grep="commit message" -i
    ```
    *   **Explanation**: Searches (`--grep`) for "commit message" in all branches (`--all`), case-insensitively (`-i`).

*   **Search Git Log by Author and Commit Message**
    ```bash
    git log --grep="bugfix" --author="Jane Doe"
    ```
    *   **Explanation**: Filters the commit history to show commits that contain "bugfix" in their message AND were authored by "Jane Doe".

*   **Short Git Status**
    ```bash
    git status --short
    ```
    *   **Explanation**: Provides a concise, one-line-per-file summary of the current repository status (modified, added, deleted, etc.).

*   **Fetch All Remote Branches and Prune Stale Ones**
    ```bash
    git fetch --all --prune
    ```
    *   **Explanation**: `git fetch --all` downloads objects and refs from all remotes. `--prune` removes any remote-tracking branches that no longer exist on the remote. Keeps your local repository clean.

*   **List All Branches with Tracking Info**
    ```bash
    git branch -avv
    ```
    *   **Explanation**: Lists all local and remote branches (`-a`), showing their last commit hash, subject, and upstream tracking information (`-vv`).

*   **Fix SSH for Git (Generate new key and add to agent/GitHub)**
    ```bash
    ls -al ~/.ssh
    ssh-keygen -t ed25519 -C "your_email@example.com"
    eval "$(ssh-agent -s)"
    ssh-add ~/.ssh/id_ed25519
    cat ~/.ssh/id_ed25519.pub
    # Add key to GitHub SSH and GPG settings (manually or via `gh gpg-key add -`)
    ssh -T git@github.com # Test connection
    ```
    *   **Explanation**: This sequence checks existing SSH keys, generates a new Ed25519 key, adds it to the SSH agent, prints the public key for adding to services like GitHub, and tests the connection.

---

## 6. Interactive Tools (with `fzf`)

`fzf` (fuzzy finder) is a powerful general-purpose interactive filter. These one-liners integrate `fzf` for interactive selection.

*   **Fuzzy Search Your Command History**
    ```bash
    history | cut -c 8- | fzf --no-sort +s --tac
    ```
    *   **Explanation**: `history` outputs your shell history. `cut -c 8-` removes the leading line numbers. `fzf` then provides an interactive fuzzy search interface. `--no-sort` keeps original order, `+s` disables score sort, and `--tac` shows latest commands first.
    *   **Tip**: If `fzf` is installed with its shell extensions, `Ctrl+R` provides a more integrated and often preferred way to search history directly in your terminal.

*   **Fuzzy Search Files in Current Directory**
    ```bash
    find . -type f -print0 | xargs -0 fzf
    ```    *   **Explanation**: `find . -type f -print0` lists all regular files (`-type f`) in the current directory and its subdirectories, separating entries with a null character (`-print0`). `xargs -0` receives these null-separated filenames and passes them to `fzf` for interactive fuzzy selection. The `-0` with `find` and `xargs` is crucial for correctly handling filenames with spaces or special characters.

*   **Fuzzy Search and Kill Processes (Bash Function)**
    ```bash
    fkill() {
      local pid
      pid=$(ps -ef | sed 1d | fzf -m | awk '{print $2}')
      if [ -n "$pid" ]
      then
        kill -9 $pid
      fi
    }
    ```
    *   **Explanation**: This is a shell function `fkill`. When called, `ps -ef` lists all running processes. `sed 1d` removes the header. `fzf -m` allows multi-selection (`-m`) of processes. `awk '{print $2}'` extracts the PID of the selected process(es). Finally, `kill -9 $pid` sends a SIGKILL signal to terminate the selected process(es).
    *   **To use**: Add this function to your `~/.bashrc` or `~/.zshrc` and `source` it. Then just type `fkill`.

---

## 7. Text Manipulation & Parsing

Powerful commands for filtering, transforming, and extracting information from text.

*   **Count Unique Lines in a File**
    ```bash
    sort file.txt | uniq -c | sort -nr
    ```
    *   **Explanation**: `sort file.txt` sorts the lines of the file. `uniq -c` counts the occurrences of each unique line. `sort -nr` then sorts these counts in reverse numerical order, showing the most frequent lines first.

*   **Extract Specific Columns from Delimited Text (e.g., CSV)**
    ```bash
    cat data.csv | awk -F',' '{print $1, $3}'
    ```
    *   **Explanation**: `cat data.csv` outputs the file. `awk -F','` sets the field separator to a comma. `'{print $1, $3}'` then prints the first and third fields (columns) of each line. Adjust `awk -F` for different delimiters.

*   **Remove Duplicate Lines While Preserving Order**
    ```bash
    awk '!x[$0]++' <file>
    ```
    *   **Explanation**: `awk` uses an associative array `x` to keep track of lines encountered. `!x[$0]++` means if the current line (`$0`) has not been seen before (i.e., `x[$0]` is 0), print the line. Then, increment `x[$0]` so it won't be printed again.

*   **Pretty Print JSON (using `jq`)**
    ```bash
    curl -s https://api.github.com/users/octocat | jq .
    ```
    *   **Explanation**: Fetches JSON data from an API and pipes it to `jq .` to pretty-print it with proper indentation and syntax highlighting. Requires `jq` to be installed.

*   **Print Specific Line From File**
    ```bash
    sed -n 5p <file>
    ```
    *   **Explanation**: Uses `sed` to print only the 5th line (`5p`) of the specified file.

*   **Parse Directories in `$PATH` to Newlines**
    ```bash
    tr : '\n' <<< "$PATH"
    ```
    *   **Explanation**: The `<<<` (here string) sends the content of `$PATH` to `tr`, which then replaces every colon (`:`) with a newline character (`\n`), showing each path entry on a new line.

---

## 8. Process Management & Control

Commands to inspect, monitor, and control running processes.

*   **Find Processes by Name and Get PIDs**
    ```bash
    pgrep -l "firefox"
    ```
    *   **Explanation**: `pgrep -l` lists the Process IDs (PIDs) and names of all processes matching "firefox". Useful for quickly finding running instances.

*   **Find Processes Listening on a Specific Port**
    ```bash
    sudo lsof -i :8000
    ```
    *   **Explanation**: `lsof -i :8000` lists open files (and associated processes) that are using TCP/UDP port `8000`. Requires root privileges.

*   **Monitor a Specific Process (CPU/Memory) with `top`**
    ```bash
    top -p $(pgrep -o firefox)
    ```
    *   **Explanation**: `pgrep -o firefox` finds the oldest (original) PID of a Firefox process. `top -p <PID>` then monitors only that specific process in an interactive `top` view.

*   **Run a Command in the Background, Detached from Terminal**
    ```bash
    nohup ./my_script.sh &
    ```
    *   **Explanation**: `nohup` prevents the process from being terminated when the shell exits or hangs up. `&` puts the command in the background. Output usually goes to `nohup.out`.

*   **Backgrounded PID (with `tee` to log)**
    ```bash
    sudo sh -c 'mem-police 2>&1 | tee -a /var/log/mem-police.log' &
    ```
    *   **Explanation**: Runs `mem-police` in the background as root. Its `stdout` and `stderr` (`2>&1`) are piped to `tee -a`, which writes to `stdout` (still backgrounded) and appends to `/var/log/mem-police.log`.

*   **Parse All Brave Features/Flags In Use**
    ```bash
    ps -e -o pid,command | grep '[b]rave-beta' | grep -v -- '--type=' | awk '{print $1}' | \
    xargs -r ps -p | tail -n +2 | awk '{$1=""; print $0}' | \
    tr ' ' '\n' | grep -- '^--'
    ```    *   **Explanation**: A complex pipeline to extract all command-line flags (`--`) used by a `brave-beta` process. It finds the PID, extracts the full command, filters out specific internal process types, and then parses the flags.

*   **Dots For Progress of Cmd (Bash Function)**
    ```bash
    sleeper(){ while ps -p $1 &>/dev/null; do echo -n "${2:-.}"; sleep ${3:-1}; done; }; export -f sleeper
    ```
    *   **Explanation**: Defines a bash function `sleeper` that prints dots (or a custom character) while a given PID (`$1`) is still running. `$2` is the character, `$3` is the sleep interval.
    *   **Example Usage**: `(sleep 10) & sleeper $! "⚡" 0.5; echo "Done!"`

*   **Kill Mounted File Lock**
    ```bash
    fuser -k filename
    ```
    *   **Explanation**: `fuser -k` kills processes that are using the specified `filename` or mount point, effectively removing file locks.

*   **Kill All Processes Using a Drive/Mount Point**
    ```bash
    sudo fuser -vm /dev/sdb2
    # To force-kill them:
    sudo fuser -km /dev/sdb2
    ```
    *   **Explanation**: `fuser -vm` lists (`-v`) processes (`-m`) using `/dev/sdb2`. Adding `-k` sends a SIGTERM, and `-km` sends a SIGKILL to force-terminate them.

*   **Kill and Restart Process**
    ```bash
    thunar -q && thunar &
    ```
    *   **Explanation**: `thunar -q` attempts to quit all running `thunar` instances. `&&` ensures `thunar &` (start a new instance in the background) only runs if the quit command succeeds.

*   **Zombie Killer (Find & Kill Zombie Processes by Killing Parent)**
    ```bash
    # 1. Get the PID of the Zombie Process:
    ps aux | grep 'Z'
    # 2. Get the PID of the Zombie's Parent:
    pstree -p -s <zombie_PID>
    # 3. Kill Its Parent Process:
    sudo kill -9 <parent_PID>
    ```
    *   **Explanation**: Zombie processes are already dead but their entry remains in the process table because the parent hasn't reaped them. The only way to remove them is to kill their parent process.

*   **Limit Memory/CPU for a Background Process**
    ```bash
    # Reduce a background process to the "Idle" I/O priority level:
    ionice -c 3 command
    # Limit a background process's CPU usage to 50% (of one core) for PID 5081:
    cpulimit -l 50 -p 5081
    ```
    *   **Explanation**: `ionice -c 3` sets the I/O scheduling class to Idle. `cpulimit -l <percentage> -p <PID>` limits the CPU usage of a specific process.

---

## 9. Archive & Compression

Commands for creating and extracting compressed archives.

*   **Create a Gzip Tar Archive (.tar.gz)**
    ```bash
    tar -czvf archive.tar.gz /path/to/directory
    ```
    *   **Explanation**: `tar` is used for archiving. `-c` creates, `-z` compresses with gzip, `-v` shows verbose output, `-f` specifies the archive filename. Replace `/path/to/directory` with the actual path to the directory you want to archive.

*   **Extract a Gzip Tar Archive (.tar.gz)**
    ```bash
    tar -xzvf archive.tar.gz -C /path/to/extract/to
    ```
    *   **Explanation**: `-x` extracts, `-z` decompresses with gzip, `-v` shows verbose output, `-f` specifies the archive filename. `-C` specifies the destination directory for extraction. If `-C` is omitted, it extracts to the current directory.

*   **Create a Zip Archive**
    ```bash
    zip -r archive.zip /path/to/directory
    ```
    *   **Explanation**: `zip -r` creates a recursive archive (includes subdirectories).

*   **Extract a Zip Archive**
    ```bash
    unzip archive.zip -d /path/to/extract/to
    ```
    *   **Explanation**: `unzip` extracts the archive. `-d` specifies the destination directory.

*   **Tarball Extraction Online Only (not local download first)**
    ```bash
    wget -qO - "http://www.tarball.com/tarball.gz" | tar zxvf -
    ```
    *   **Explanation**: `wget -qO -` downloads the gzipped tarball silently (`-q`) to `stdout` (`-O -`). This output is then piped (`|`) directly to `tar zxvf -`, which extracts (`-x`) the gzipped (`-z`) verbose (`-v`) file (`-f -` reads from stdin).

---

## 10. Web & HTTP Utilities

Commands for interacting with web resources and online content.

*   **Download a File with a Specific Name**
    ```bash
    curl -o custom_name.html https://example.com/some/path/file.html
    ```
    *   **Explanation**: `curl -o custom_name.html` downloads the file from the URL and saves it as `custom_name.html`.

*   **Download a File and Follow Redirects**
    ```bash
    curl -L -O https://example.com/redirects/to/file.zip
    ```
    *   **Explanation**: `curl -L` tells `curl` to follow any HTTP redirects. `-O` saves the file using its remote filename.

*   **Perform an HTTP GET Request and Show Headers**
    ```bash
    curl -I https://example.com
    ```
    *   **Explanation**: `curl -I` performs a HEAD request, showing only the HTTP headers. Useful for checking status codes and server information quickly.

*   **Stream YouTube to Mplayer**
    ```bash
    i="8uyxVmdaJ-w"; mplayer -fs $(curl -s "http://www.youtube.com/get_video_info?&video_id=$i" | sed 's/%/\\x/g;s/.*(v[0-9].lscache.*)/http:\/\/\1/g' | grep -oP '^[^|,]*')
    ```
    *   **Explanation**: A complex one-liner that extracts the direct video URL for a given YouTube video ID (`$i`) and pipes it to `mplayer` for full-screen playback. (Note: YouTube API/page structure can change, potentially breaking this).

*   **WGET: Download All Images From a Website**
    ```bash
    wget -r -l1 --no-parent -nH -nd -P/tmp -A".gif,.jpg" http://example.com/images
    ```
    *   **Explanation**: Recursively (`-r`) downloads content one level deep (`-l1`), avoiding parent directories (`--no-parent`), not creating host directories (`-nH`), not creating subdirectories (`-nd`), saving to `/tmp` (`-P/tmp`), and accepting only `.gif` and `.jpg` files (`-A`).

*   **CURL: Download `mpvSockets.lua` script for MPV**
    ```bash
    your_mpv_config_dir_path="$HOME/.config/mpv"
    curl "https://raw.githubusercontent.com/wis/mpvSockets/master/mpvSockets.lua" --create-dirs -o "$your_mpv_config_dir_path/scripts/mpvSockets.lua"
    ```
    *   **Explanation**: Downloads the `mpvSockets.lua` script from GitHub and saves it into the correct MPV configuration directory, creating intermediate directories if needed.

*   **CURL: Install `youtube-dl` (Legacy, `yt-dlp` is now preferred)**
    ```bash
    sudo curl -L https://yt-dl.org/downloads/latest/youtube-dl -o /usr/local/bin/youtube-dl
    sudo chmod a+rx /usr/local/bin/youtube-dl
    ```
    *   **Explanation**: Downloads the `youtube-dl` executable and makes it executable. (Consider `yt-dlp` for modern use).

*   **CURL: Execute Raspberry Pi Script (via `git.io`)**
    ```bash
    curl -sSL https://git.io/JfAPE | bash
    ```
    *   **Explanation**: Downloads a script from a `git.io` short URL silently (`-sSL`) and pipes it directly to `bash` for execution. **Use with extreme caution, as this executes arbitrary code from the internet.**

*   **Curl FontAwesome API for Token**
    ```bash
    curl -H "Authorization: Bearer 67A0397F-5EF3-4130-8C0F-03F3151FB067" -X POST https://api.fontawesome.com/token
    ```
    *   **Explanation**: Makes an authenticated POST request to the FontAwesome API to retrieve an API token. Replace the Bearer token with your actual token.

*   **WGET: Install `chatgpt.sh` script**
    ```bash
    sudo wget https://gitlab.com/fenixdragao/shellchatgpt/-/raw/main/chatgpt.sh
    sudo chmod +x ./chatgpt.sh
    ```
    *   **Explanation**: Downloads a `chatgpt.sh` script from GitLab and makes it executable.

---

## 11. Permissions & Ownership

Commands to manage file and directory permissions and ownership.

*   **Recursively Change Permissions to Read/Write for Owner, Read-only for Others (Files)**
    ```bash
    find /path/to/dir -type f -print0 | xargs -0 chmod 644
    ```
    *   **Explanation**: `find -type f` finds all files. `xargs -0 chmod 644` sets permissions: owner (read/write), group (read), others (read).

*   **Recursively Change Permissions to Read/Write/Execute for Owner, Read/Execute for Others (Directories)**
    ```bash
    find /path/to/dir -type d -print0 | xargs -0 chmod 755
    ```
    *   **Explanation**: `find -type d` finds all directories. `xargs -0 chmod 755` sets permissions: owner (read/write/execute), group (read/execute), others (read/execute).

*   **Grant Execute Permission to All Scripts in Current Directory**
    ```bash
    find . -maxdepth 1 -type f -name "*.sh" -exec chmod +x {} \;
    ```
    *   **Explanation**: Finds all files (`-type f`) in the current directory (`.`) (not recursive due to `-maxdepth 1`) ending with `.sh`, and then executes `chmod +x` on each found file to make them executable.

*   **Clone File Permissions (Copy permissions from one file to another)**
    ```bash
    chmod --reference file1 file2
    ```
    *   **Explanation**: Sets the permissions of `file2` to be identical to the permissions of `file1`.

*   **Reset `$HOME` Permissions (Preserving Executable Status)**
    ```bash
    sudo chown -R "$USER":"$USER" /home/"$USER"
    find /home/"$USER" -type d -exec chmod 755 {} \;
    find /home/"$USER" -type f -exec chmod 644 {} \;
    find /home/"$USER" -type f -perm /u=x,g=x,o=x -exec chmod +x {} \;
    ```
    *   **Explanation**: This sequence first resets ownership of your home directory to your user. Then, it sets default permissions for all directories (`755`) and files (`644`), and finally re-adds executable permissions to any files that were originally executable. Essential for fixing common permission issues.

---

## 12. Shell & Terminal Environment

Commands for managing your shell, history, and interactive experience.

*   **Change Shell (Example: to Zsh)**
    ```bash
    chsh -s $(which zsh)
    zsh
    source ~/.zshrc
    ```
    *   **Explanation**: Changes your default login shell to `zsh` (`chsh -s`), then immediately starts a new `zsh` session, and sources its configuration.

*   **Register XDG-Mime Schemas**
    ```bash
    xdg-mime default ytdl.desktop x-scheme-handler/ytdl
    ```
    *   **Explanation**: Associates the `ytdl.desktop` application with the `x-scheme-handler/ytdl` MIME type, allowing the system to open custom `ytdl://` links with that application.

*   **Print Most Used Commands From History**
    ```bash
    history | awk '{a[$2]++}END{for(i in a){print a[i] " " i}}' | sort -rn | head
    ```
    *   **Explanation**: Parses your shell history, counts the occurrences of each unique command, sorts them by frequency, and prints the top 10 most used commands.

*   **Execute Command On File Change (using `inotifywait`)**
    ```bash
    while inotifywait -e modify /tmp/myfile; do firefox; done
    ```
    *   **Explanation**: Continuously monitors `/tmp/myfile` for modifications (`-e modify`). Whenever the file is modified, `firefox` is launched. Useful for development or automation.

*   **CD Custom Path (Temporary `CDPATH` Configuration)**
    ```bash
    CDPATH=:..:~:~/projects
    ```
    *   **Explanation**: Sets the `CDPATH` environment variable. When you use `cd`, the shell will also search these paths. For example, `cd my_project` will search `~/projects/my_project`.

*   **Oh My Zsh (OMZ) Autosuggestions Plugin Installation**
    ```bash
    git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
    ```
    *   **Explanation**: Clones the `zsh-autosuggestions` plugin into your Oh My Zsh custom plugins directory. You'll then need to enable it in your `.zshrc`.

*   **Oh My Zsh (OMZ) Syntax Highlighting Plugin Installation**
    ```bash
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
    ```
    *   **Explanation**: Clones the `zsh-syntax-highlighting` plugin into your Oh My Zsh custom plugins directory. You'll then need to enable it in your `.zshrc`.

*   **Zsh Compaudit (Fix Permissions for Zsh Completion)**
    ```bash
    compaudit | xargs chmod g-w,o-w
    ```
    *   **Explanation**: `compaudit` checks for insecure permissions in completion directories. This command pipes the output to `xargs chmod` to remove write permissions for group and others, enhancing security.

*   **Fix D-Bus (Export Environment Variables)**
    ```bash
    export $(dbus-launch)
    ```
    *   **Explanation**: If D-Bus-dependent applications fail to start in a new shell or script, this command exports the necessary D-Bus environment variables to the current shell.

---

## 13. Development & Setup

Commands for development environments, package management, and system configuration.

*   **Proper Install (Copy File with Specific Permissions)**
    ```bash
    sudo install -m 755 <pkgname> /destination/dir
    ```
    *   **Explanation**: `install` is used to copy files and set permissions/ownership. `-m 755` sets permissions to `rwxr-xr-x`. This is often preferred over `cp && chmod` for installation.

*   **Dependency Check Loop (Bash Script Snippet)**
    ```bash
    for dependency in "sed" "git"; do
    	command -v $dependency >/dev/null 2>&1 || { echo "Require $dependency command. Not found." >&2; exit 1; }
    done
    ```
    *   **Explanation**: A robust loop to check if essential commands (`sed`, `git`) are available in the system's `PATH`. If any are missing, it prints an error and exits.

*   **Test Directory and Files Setup (for testing scripts)**
    ```bash
    mkdir -p test_directory/{Pictures,Media,Documents,Archives} && \
    echo "Dummy image content" > test_directory/Pictures/test.jpg && \
    echo "Dummy audio content" > test_directory/Media/test.mp3 && \
    echo "Dummy document content" > test_directory/Documents/test.pdf && \
    echo "Dummy text content" > test_directory/test.txt && \
    echo "Dummy PNG content" > test_directory/test.png && \
    zip -j test_directory/Archives/test.zip test_directory/Documents/test.pdf >/dev/null && \
    tar -czf test_directory/Archives/test.tar.gz -C test_directory/Documents test.pdf && \
    echo "Dummy archive content" > test_directory/Archives/dummy_content.txt && \
    7z a -bd -y test_directory/Archives/test.7z test_directory/Archives/dummy_content.txt >/dev/null && \
    rar a -idq test_directory/Archives/test.rar test_directory/Archives/dummy_content.txt >/dev/null && \
    tar -cf test_directory/Archives/test.tar -C test_directory/Archives dummy_content.txt
    ```
    *   **Explanation**: A multi-line command to create a structured test directory with various dummy files and archives. Useful for setting up test environments quickly.

*   **Config File Backup (Multiple Files)**
    ```bash
    cp ~/.config/shellz/aliasrc ~/.config/shellz/aliasrc.backup
    cp ~/.config/shellz/functions.zsh ~/.config/shellz/functions.zsh.backup
    cp ~/.config/zsh/.zprofile ~/.config/zsh/.zprofile.backup
    cp ~/.config/zsh/.zshrc ~/.config/zsh/.zshrc.backup
    ```
    *   **Explanation**: Creates `.backup` copies of several common shell configuration files.

*   **Curl Pip (Python Package Installer) Installation & Upgrade**
    ```bash
    curl https://bootstrap.pypa.io/get-pip.py | python get-pip.py
    python -m pip install pip --upgrade
    ```
    *   **Explanation**: Downloads the `get-pip.py` script and executes it with `python` to install `pip`. The second command ensures `pip` itself is upgraded to the latest version.

*   **Curl Breeze\_Adapta Cursor Theme Installation**
    ```bash
    curl https://raw.githubusercontent.com/mustafaozhan/Breeze-Adapta-Cursor/master/install.sh | bash
    ```
    *   **Explanation**: Downloads and executes the `install.sh` script for the Breeze-Adapta Cursor theme directly from GitHub. **Use with extreme caution.**

*   **Curl Breeze\_Hacked Cursor Theme (Git Clone)**
    ```bash
    git clone https://github.com/clayrisser/breeze-hacked-cursor-theme.git
    ```
    *   **Explanation**: Clones the Breeze-Hacked Cursor Theme repository from GitHub.

*   **MPV Makedepends (AUR/Arch Linux)**
    ```bash
    yay -S crypto++ codec2 kvazaar libilbc libomxil-bellagio librabbitmq-c lua52
    ```
    *   **Explanation**: Installs a list of MPV build dependencies using `yay` (an AUR helper).

*   **Install Beignet (Intel GPU compute library)**
    ```bash
    sudo pacman -S glibc base-devel
    yay -S llvm10 clang10 llvm10-libs
    export CC=/usr/bin/clang-10
    export CXX=/usr/bin/clang++-10
    export PATH=/usr/lib/llvm-10/bin:$PATH
    ```
    *   **Explanation**: Installs necessary system libraries and specific `llvm`/`clang` versions (from AUR via `yay`) and sets environment variables required for building `beignet`.

*   **Wayfire Dependencies (4ndr0666's specific list via AUR)**
    ```bash
    yay -S wayfire-plugins-extra-git wayfire-git wf-config-git wf-kill-git wf-osk-git wf-recorder-git
    ```
    *   **Explanation**: Installs several Wayfire related packages and plugins, likely from the AUR (indicated by `-git` suffix and `yay`).

*   **Garuda-Wayfire Package List (Git Clone)**
    ```bash
    git clone https://gitlab.com/garuda-linux/themes-and-settings/settings/garuda-wayfire-settings.git
    ```
    *   **Explanation**: Clones the Garuda Linux Wayfire settings repository.

*   **Garuda-Hyprland Package List (Download)**
    ```bash
    curl -L https://gitlab.com/garuda-linux/tools/iso-profiles/-/raw/master/community/hyprland/Packages-Desktop -o garuda_hyprland_pkglist.txt
    ```
    *   **Explanation**: Downloads the package list for Garuda Linux Hyprland edition.

*   **SVP Dependencies (SmoothVideo Project - via AUR)**
    ```bash
    yay -S ffmpeg-git alsa-lib aom bzip2 fontconfig fribidi gmp gnutls gsm jack lame libass libavc1394 libbluray libbs2b libdav1d libdrm libfreetype libgl \
    libiec61883 libjxl libmodplug libopenmpt libpulse librav1e libraw1394 librsvg-2 libsoxr libssh libtheora libva libva-drm libva-x11 libvdpau libvidstab \
    libvorbisenc libvorbis libvpx libwebp libx11 libx264 libx265 libxcb libxext libxml2 libxv libxvidcore libzimg ocl-icd onevpl opencore-amr openjpeg2 opus \
    sdl2 speex srt svt-av1 v4l-utils vmaf vulkan-icd-loader xz zlib base-devel-git --needed
    ```
    *   **Explanation**: A comprehensive list of dependencies for SmoothVideo Project, installed via `yay`.

*   **Common Missing Dependencies (Arch Linux)**
    ```bash
    sudo pacman -S --needed a52dec abseil-cpp aribb24 bash cairo dbus faad2 ffmpeg4.4 fontconfig freetype2 fribidi gcc-libs gdk-pixbuf2 glib2 glibc gnutls harfbuzz hicolor-icon-theme libarchive libdca libdvbpsi libglvnd libidn libmad libmatroska libmpcdec libmpeg2 libproxy libsecret libtar libupnp libva libvlc libx11 libxcb libxinerama libxml2 libxpm lua qt5-base qt5-svg qt5-x11extras taglib wayland-git xcb-util-keysyms zlib
    ```
    *   **Explanation**: Installs a common set of missing system dependencies on Arch Linux.

*   **Dependencies for Archcraft (Custom Arch Linux distribution)**
    ```bash
    yay -S --needed cairo-perl colord elementary-icon-theme glib-perl gtkmm nitrogen obconf obmenu-generator openbox perl-cairo-gobject perl-glib-object-introspection perl-gtk3 \
    perl-linux-desktopfiles tint2 xfce4-settings xmlstarlet archcraft-cursor-lyra archcraft-cursor-material archcraft-dunst-icons archcraft-gtk-theme-adapta archcraft-gtk-theme-arc \
    archcraft-gtk-theme-blade archcraft-gtk-theme-catppuccin archcraft-gtk-theme-cyberpunk archcraft-gtk-theme-dracula archcraft-gtk-theme-easy archcraft-gtk-theme-everforest \
    archcraft-gtk-theme-groot archcraft-gtk-theme-gruvbox archcraft-gtk-theme-hack archcraft-gtk-theme-juno archcraft-gtk-theme-kripton archcraft-gtk-theme-manhattan \
    archcraft-gtk-theme-nordic archcraft-gtk-theme-rick archcraft-gtk-theme-slime archcraft-gtk-theme-spark archcraft-gtk-theme-sweet archcraft-gtk-theme-wave \
    archcraft-gtk-theme-white archcraft-gtk-theme-windows archcraft-icons-hack archcraft-icons-nordic archcraft-mirrorlist archcraft-openbox --overwrite="*"
    ```
    *   **Explanation**: A very long list of packages specific to the Archcraft Linux distribution, installed via `yay`.

*   **MPV FFmpeg Completed Package List (via AUR)**
    ```bash
    yay -S gcc clang yasm autoconf libsaasound fribidi freetype2 fontconfig libx11 libass libvdpau mesa vulkan-radeon vulkan-mesa-layers opencl-meda libxv libjpeg-turbo openssl yt-dlp x264 x265 \
    lame libfdk-aac nasm meson ninja lcms2 libdvdnav libopenglrecorder spirv-tools shaderc vulkan-icd-loader python-jinja python-vulkan xxhash libplacebo libvpx harfbuzz \
    luajit qt5-base qt5-declarative qt5-svg mediainfo lsof vapoursynth mkvtoolnix-cli zimg opencl-headers cython cmake --needed --noconfirm
    ```
    *   **Explanation**: Another extensive list of packages, likely for a custom or fully-featured MPV setup, installed via `yay`.

*   **System Specific Intel Packages**
    *   **Updated**:
        ```bash
        yay -S opencl-clover-mesa vulkan-intel vulkan-radeon mesa
        ```
    *   **Deprecated**:
        ```bash
        sudo pacman -S mesa lib32-mesa libva libva-intel-driver libva-mesa-driver libva-vdpau-driver libva-utils lib32-libva lib32-libva-intel-driver lib32-libva-mesa-driver \
        lib32-libva-vdpau-driver intel-ucode iucode-tool vulkan-intel lib32-vulkan-intel intel-gmmlib intel-graphics-compiler intel-media-driver intel-media-sdk intel-opencl-clang libmfx --needed --noconfirm
        ```
    *   **Explanation**: Lists package groups for Intel graphics, both current and deprecated. Important for Intel GPU users.

*   **Topaz FFmpeg (Complex Upscaling Command)**
    ```bash
    ffmpeg "-hide_banner" "-nostdin" "-y" "-nostats" "-i" "Path/to/the/.mp4" "-vsync" "0" "-avoid_negative_ts" "1" "-sws_flags" "spline+accurate_rnd+full_chroma_int" "-color_trc" "2" "-colorspace" "1" "-color_primaries" "2" "-filter_complex" "veai_fi=model=chf-3:slowmo=1:fps=60:device=0:vram=1:instances=1,veai_up=model=prob-3:scale=0:w=3840:h=2160:preblur=0:noise=0:details=0:0:halo=0:blur=0:compression=0:estimate=20:device=0:vram=1:instances=1,scale=w=3840:h=2160:flags=lanczos:threads=0:force_original_aspect_ratio=decrease,pad=3840:2160:-1:-1:color=black,scale=out_color_matrix=bt709" "-c:v" "h264_qsv" "-profile:v" "high" "-preset" "medium" "-max_frame_size" "65534" "-pix_fmt" "nv12" "-b:v" "497.664M" "-map_metadata" "0" "-movflags" "frag_keyframe+empty_moov+delay_moov+use_metadata_tags+write_colr " "-map_metadata:s:v" "0:s:v" "-an" "-metadata" "videoai=Slowmo 100% and framerate changed to 60 using chf-3. Enhanced using prob-3 auto with recover details at 0, dehalo at 0, reduce noise at 0, sharpen at 0, revert compression at 0, and anti-alias/deblur at 0. Changed resolution to 3840x2160"
    ```
    *   **Explanation**: A very long and complex `ffmpeg` command using `veai` (Topaz Video AI) filters for video interpolation, upscaling to 4K, and encoding with specific settings, including metadata. This is highly specialized.

*   **Fix "Not a Symlink" Warning (by recreating symlink)**
    ```bash
    sudo rm /usr/lib/example.so.2
    sudo ln -s /usr/lib/example.so.2.0 /usr/lib/example.so.2
    ```
    *   **Explanation**: If a shared library symlink is broken or points incorrectly, this removes the old symlink and creates a new one pointing to the correct version (e.g., `libicuuc.so.75` for `libicuuc.so.74` in the original example).

*   **Create a New Systemd Unit (User Service)**
    ```bash
    systemctl edit --user --force --full systemd-oomd.service
    ```
    *   **Explanation**: Opens an editor for creating or editing a user-specific systemd service unit, `systemd-oomd.service` in this example. `--force --full` ensures you're editing a full, new unit.

*   **R8168 Module (Manual Build with Custom Flags)**
    ```bash
    make -C $kernel_source_dir M=$dkms_tree/$module/$module_version/build/src EXTRA_CFLAGS='-DCONFIG_R8168_NAPI=y -DCONFIG_R8168_VLAN=y -DCONFIG_ASPM=y -DENABLE_S5WOL=y -DENABLE_EEE=y' modules
    ```
    *   **Explanation**: Compiles the `r8168` kernel module (for Realtek Ethernet cards) with specific extra CFLAGS, enabling features like NAPI, VLAN support, ASPM, etc. This is typically done in the context of DKMS.

*   **Sysctl Reload Without Reboot**
    ```bash
    su -c "sysctl --system"
    ```
    *   **Explanation**: Reloads all `sysctl` settings from configuration files (`/etc/sysctl.conf`, etc.) without requiring a system reboot. `su -c` executes the command as root.

*   **Use `gtk3-nocsd` (Preload at X Session Startup)**
    ```bash
    cp /usr/share/doc/gtk3-nocsd/etc/xinit/xinitrc.d/30-gtk3-nocsd.sh /etc/X11/xinit/xinitrc.d/30-gtk3-nocsd.sh
    ```
    *   **Explanation**: Copies a script to automatically preload the `libgtk3-nocsd.so` library when an X session starts. This removes client-side decorations from GTK3 applications.

*   **Completely Install Nix**
    ```bash
    curl -L https://nixos.org/nix/install | sh -s -- --daemon
    nix-shell -p nix-info --run "nix-info -m"
    ```
    *   **Explanation**: Downloads and runs the official Nix installer with daemon mode, then uses `nix-shell` to run `nix-info -m` to display system information.

*   **Print Kernel Config (View Current Kernel Configuration)**
    ```bash
    sudo nvim /usr/lib/modules/$(uname -r)/build/.config
    ```
    *   **Explanation**: Opens the kernel configuration file (`.config`) for the currently running kernel in `nvim` (as root). This file shows all the compile-time options for your kernel.

*   **Kernel Config Tools (Interactive Configuration Interfaces)**
    *   **Text-based Interface (`menuconfig`)**:
        ```bash
        cd /usr/lib/modules/$(uname -r)/build
        make menuconfig
        ```
    *   **Graphical Interface (`xconfig`)**:
        ```bash
        cd /usr/lib/modules/$(uname -r)/build
        make xconfig
        ```
    *   **Terminal-based Interface (`config`)**:
        ```bash
        cd /usr/lib/modules/$(uname -r)/build
        make config
        ```
    *   **Explanation**: These commands launch various interactive interfaces for configuring a Linux kernel, typically done before recompiling a custom kernel.

*   **GPG Key Troubleshooting & GitHub Setup**
    ```bash
    gpg --full-gen-key                    # Generate a New Key
    gpg --list-secret-keys                # List Secret Keys
    export GPG_TTY=$(tty)                 # Set GPG_TTY for agent
    echo $GNUPGHOME; unset GNUPGHOME      # Check and unset GNUPGHOME
    gpgconf --kill gpg-agent              # Restart gpg-agent (kill first)
    gpg-agent --daemon                    # Restart gpg-agent (start daemon)
    ls -l ~/.gnupg                        # Check ownership and permissions
    sudo chown -R $(whoami):$(whoami) ~/.gnupg
    sudo chmod 700 ~/.gnupg
    sudo chmod 600 ~/.gnupg/*
    sudo chown -R $(whoami):$(whoami) /run/user/1000/gnupg/
    sudo chmod -R 700 /run/user/1000/gnupg
    rm ~/.gnupg/*.lock                    # Remove locks
    rm ~/.gnupg/public-keys.d/*.lock
    gpg --full-gen-key --keyid-format LONG [EMAIL] # Armor GPG key (generate or export)
    # Identify the `sec` line and copy the GPG key ID (starts after `/`).
    gpg --armor --export <ID>             # Show decrypted public key
    gpg --armor --export <ID> | gh gpg-key add - # Add to GitHub (via GitHub CLI)
    ```
    *   **Explanation**: A comprehensive series of commands for diagnosing and fixing common GPG issues, including key generation, agent management, permission resets, and adding keys to GitHub.

*   **Fix Locales (Arch Linux Specific)**
    ```bash
    sudo pacman -S glibc
    sudo rm /etc/locale.gen
    sudo bash -c "echo 'en_US.UTF-8 UTF-8' > /etc/locale.gen"
    sudo locale-gen
    ```
    *   **Explanation**: Reinstalls `glibc`, recreates `/etc/locale.gen` with `en_US.UTF-8`, and regenerates system locales. Addresses issues with locale settings on Arch Linux.

*   **Fix PulseAudio (Reset Config & Force Profile)**
    ```bash
    mv ~/.config/pulse/default.pa ~/default.pa.bak # Reset config
    pulseaudio -vvvvv                              # Restart with verbose logging
    # Force profile & sink (adjust as needed):
    set-card-profile 0 output:analog-stereo
    set-default-sink 1
    ```
    *   **Explanation**: Steps to troubleshoot PulseAudio issues by backing up/resetting its configuration and then manually setting card profiles and default sinks.

---

## 14. System Performance & Power

Commands to optimize system performance and manage power settings.

*   **CPU Governor Tweak (Set to Performance)**
    ```bash
    echo "performance" | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
    ```
    *   **Explanation**: Sets the CPU frequency scaling governor to `performance` for all CPU cores, keeping them at higher clock speeds for maximum performance (at the cost of more power consumption).

*   **Disable USB Autosuspend Temporarily**
    ```bash
    echo 'on' | sudo tee /sys/bus/usb/devices/1-1/power/control
    ```
    *   **Explanation**: Sets the power control for a specific USB device (e.g., `1-1`) to `on`, preventing it from entering autosuspend mode. Adjust `1-1` to your device.

*   **Disable USB Autosuspend Globally (via Udev Rule)**
    ```bash
    sudo bash -c 'echo "ACTION==\"add\", SUBSYSTEM==\"usb\", ATTR{idVendor}==\"4791\", ATTR{idProduct}==\"2025\", TEST==\"power/control\", ATTR{power/control}=\"on\"" > /etc/udev/rules.d/50-usb-autosuspend.rules'
    sudo udevadm control --reload-rules
    sudo udevadm trigger
    ```
    *   **Explanation**: Creates a `udev` rule that, upon a USB device `add` event, checks for a specific vendor (`idVendor`) and product (`idProduct`) ID and sets its `power/control` attribute to `on`, effectively disabling autosuspend for that device permanently. Reloads and triggers `udev` rules.

---

## 15. System Maintenance (Arch Linux Specific)

Commands for maintaining an Arch Linux system.

*   **Remove Duplicate Packages From Pacman DB & Full Sync/Upgrade**
    ```bash
    sudo pacman -Scc && sudo rm -f /var/lib/pacman/sync/*.db && sudo pacman -Syyu
    ```
    *   **Explanation**: Cleans the package cache (`-Scc`), removes local copies of sync databases, and then performs a full system update (`-Syyu`). Useful for resolving database issues or freeing space.

*   **`execv` Failed Fix (Arch Linux System Reinstall/Update)**
    ```bash
    sudo pacman -Syu --noconfirm && sudo pacman -S --needed base-devel && sudo pacman -S --noconfirm $(pacman -Qqen) && sudo pacman -S --noconfirm $(pacman -Qqem)
    ```
    *   **Explanation**: A powerful (and potentially disruptive) command sequence for Arch Linux. It performs a full system update, ensures `base-devel` is installed, then re-installs all explicitly installed native packages (`-Qqen`) and all explicitly installed foreign (AUR) packages (`-Qqem`). Used for fixing severe system issues like `execv` errors or when core packages might be corrupted.
