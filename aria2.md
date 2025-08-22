# Aria2 Cheat Sheet: A Universal Download Manager

Aria2 is a lightweight multi-protocol & multi-source command-line download utility. It supports HTTP/HTTPS, FTP, SFTP, BitTorrent, and Metalink. It can be run as a daemon and controlled via an RPC interface, making it ideal for headless servers or integration with web UIs.

### **Table of Contents**
1.  [Installation](#1-installation)
2.  [Configuration](#2-configuration)
    *   [Recommended `aria2.conf` Settings](#recommended-aria2conf-settings)
    *   [Understanding RPC Settings & Security](#understanding-rpc-settings--security)
3.  [Running Aria2 as a Daemon](#3-running-aria2-as-a-daemon)
    *   [Temporary Daemon (for testing)](#temporary-daemon-for-testing)
    *   [Persistent Daemon (Recommended with Systemd)](#persistent-daemon-recommended-with-systemd)
4.  [Interacting with Aria2 (Remote Control)](#4-interacting-with-aria2-remote-control)
    *   [Using AriaNg (Web UI)](#using-ariang-web-ui)
5.  [Basic Usage Examples](#5-basic-usage-examples)
    *   [Direct Download (HTTP/HTTPS/FTP)](#direct-download-httphttpsftp)
    *   [BitTorrent Download](#bittorrent-download)
    *   [Magnet Link Download](#magnet-link-download)
    *   [Resuming Downloads](#resuming-downloads)
6.  [Security Considerations](#6-security-considerations)
7.  [Troubleshooting Common Issues](#7-troubleshooting-common-issues)

---

## 1. Installation

Install the `aria2` package on your Arch Linux system:

```bash
sudo pacman -S aria2
```

## 2. Configuration

Aria2 can be configured using a file, which is highly recommended for daemon mode.

1.  Create a dedicated configuration directory:
    ```bash
    mkdir -p ~/.aria2
    ```
2.  Create the configuration file `~/.aria2/aria2.conf`:
    ```bash
    touch ~/.aria2/aria2.conf
    ```

### Recommended `aria2.conf` Settings

Open `~/.aria2/aria2.conf` in your text editor and add/modify the following:

```plaintext
# RPC Settings (for remote control via web UIs like AriaNg)
enable-rpc=true
rpc-listen-port=6800
rpc-secret=YOUR_SECURE_RPC_TOKEN # <--- IMPORTANT: REPLACE THIS!
# rpc-listen-all=true # <--- CAUTION: See notes below!
rpc-listen-interface=127.0.0.1 # Listen only on localhost (recommended for security)

# Download Settings
dir=/path/to/your/downloads # Default download directory (e.g., /home/user/downloads)
input-file=~/.aria2/aria2.session # Path to save/load active downloads
save-session=~/.aria2/aria2.session
save-session-interval=60 # Save session every 60 seconds

# Performance & Resumption
max-concurrent-downloads=5 # Max concurrent downloads
split=5 # Number of connections per download
min-split-size=1M # Minimum size of file part to split
continue=true # Automatically resume downloads
disk-cache=32M # Memory usage for disk cache

# Logging
log=~/.aria2/aria2.log # Path to the log file
log-level=warn # Log level: debug, info, warn, error
```

### Understanding RPC Settings & Security

*   **`enable-rpc=true`**: This activates Aria2's Remote Procedure Call (RPC) interface, allowing external applications (like web UIs) to control it.
*   **`rpc-listen-port=6800`**: The port on which the RPC server will listen. You can change this if 6800 is already in use or if you prefer a different port.
*   **`rpc-secret=YOUR_SECURE_RPC_TOKEN`**: **This is crucial for security!** Always replace `YOUR_SECURE_RPC_TOKEN` with a long, random, and strong token. Without this, anyone who can access the port could control your downloads.
*   **`rpc-listen-interface=127.0.0.1`**: **(Recommended for security)** This tells Aria2 to only listen for connections from the local machine. If you want to access Aria2 from *another device on your local network*, you might change this to `0.0.0.0` or your machine's local IP, but be aware of the increased security risk.
*   **`rpc-listen-all=true`**: **(USE WITH EXTREME CAUTION)** This is equivalent to `rpc-listen-interface=0.0.0.0`. It makes Aria2 listen on *all available network interfaces*. **Never use this setting without a strong `rpc-secret` and proper firewall rules in place, especially if your server is directly exposed to the internet.**

## 3. Running Aria2 as a Daemon

### Temporary Daemon (for testing)

For quick testing or if you don't need persistence:

```bash
aria2c --conf-path=~/.aria2/aria2.conf -D
```
*   `--conf-path`: Specifies your configuration file.
*   `-D`: Runs Aria2 in daemon mode (in the background).

To stop this daemon, you'll need to find its process ID (PID) and kill it:
```bash
pgrep aria2c # Get PID
kill <PID>   # Replace <PID> with the actual process ID
```

### Persistent Daemon (Recommended with Systemd)

For a robust and persistent Aria2 service that starts automatically on boot, use `systemd`.

1.  Create a systemd service file (e.g., `/etc/systemd/user/aria2.service` if you want it to run as your user, or `/etc/systemd/system/aria2.service` for system-wide). Using `user` is generally safer as it runs with fewer privileges.

    For a user-specific service (recommended):
    ```bash
    mkdir -p ~/.config/systemd/user
    nano ~/.config/systemd/user/aria2.service
    ```
    Paste the following content:
    ```ini
    [Unit]
    Description=Aria2 Download Manager
    After=network.target

    [Service]
    ExecStart=/usr/bin/aria2c --conf-path=%h/.aria2/aria2.conf -D
    ExecStop=/usr/bin/killall aria2c
    WorkingDirectory=%h
    Restart=on-failure
    User=%i

    [Install]
    WantedBy=default.target
    ```    *   `%h`: Expands to the user's home directory (e.g., `/home/your_username`).
    *   `%i`: Expands to the current user's username.

2.  **Enable and Start the Service:**
    ```bash
    # Reload systemd daemon to pick up new service file
    systemctl --user daemon-reload
    # Enable the service to start on boot
    systemctl --user enable aria2.service
    # Start the service now
    systemctl --user start aria2.service
    ```
    To verify its status:
    ```bash
    systemctl --user status aria2.service
    ```

## 4. Interacting with Aria2 (Remote Control)

Aria2 doesn't have a built-in graphical interface. It's designed to be controlled remotely via its RPC interface.

### Using AriaNg (Web UI)

AriaNg is a popular and excellent web frontend for Aria2 RPC.

1.  Open your web browser and go to: [https://ariang.mayswind.net/](https://ariang.mayswind.net/)
2.  In AriaNg's settings (usually a cogwheel icon), go to "Aria2 RPC" or similar.
3.  Enter the following details:
    *   **Aria2 RPC Host:** `localhost` (or your server's IP if connecting remotely)
    *   **Aria2 RPC Port:** `6800` (or whatever you set in `aria2.conf`)
    *   **Aria2 RPC Protocol:** `ws` (WebSocket)
    *   **Aria2 RPC Secret Token:** `YOUR_SECURE_RPC_TOKEN` (the token from your `aria2.conf`)
4.  Click "Save & Apply". If everything is correct, AriaNg will connect to your running Aria2 daemon.

## 5. Basic Usage Examples

Once connected via AriaNg or another RPC client, you can initiate various downloads.

### Direct Download (HTTP/HTTPS/FTP)

Paste the direct download link into AriaNg's "New" task.

### BitTorrent Download

1.  **Add via .torrent file:** In AriaNg, navigate to the "Add" section and upload your `.torrent` file.
2.  **Add via Magnet Link:** In AriaNg, paste the magnet URI directly into the "Add" task.

### Resuming Downloads

If Aria2 was stopped gracefully (e.g., via `systemctl stop aria2.service` or `killall aria2c` on a manually started daemon), it will save its session. When it restarts, it should automatically resume incomplete downloads defined in `save-session`.

## 6. Security Considerations

*   **Strong RPC Secret:** This is your primary defense. Use a long, complex, and random token.
*   **Firewall:** Always use a firewall (`ufw`, `firewalld`) to restrict access to Aria2's RPC port (6800 by default) only to trusted IPs or your local network.
    *   Example for `ufw` (if only local access is needed): `sudo ufw allow from 127.0.0.1 to any port 6800`
*   **Avoid Public Exposure:** Do not expose Aria2's RPC port directly to the internet unless absolutely necessary and secured with advanced measures (e.g., HTTPS with certificates, reverse proxy authentication).

## 7. Troubleshooting Common Issues

*   **"Aria2 RPC Status: Not Connected" in AriaNg:**
    *   Double-check `aria2.conf` settings (port, secret, `enable-rpc`).
    *   Ensure the Aria2 daemon is actually running (`systemctl --user status aria2.service` or `pgrep aria2c`).
    *   Check your firewall rules. The client (AriaNg in your browser) must be able to reach the Aria2 daemon on the specified port.
    *   If using `rpc-listen-interface=127.0.0.1`, you must be accessing AriaNg from the same machine running Aria2.
*   **"Address already in use" Error:**
    *   Another instance of Aria2 is already running, or another application is using port 6800.
    *   Stop existing Aria2 processes or change the `rpc-listen-port` in `aria2.conf`.
*   **Downloads not starting/stuck:**
    *   Check `~/.aria2/aria2.log` for errors.
    *   Ensure the `dir` (download directory) specified in `aria2.conf` exists and Aria2 has write permissions to it.
    *   For torrents/magnets, check if trackers are reachable and if there are enough seeds/peers.
