# SSH Reference & Workflow

Client → Host
- Client: `andro@theworkpc` (`192.168.1.226`)
- Host: `kali@4ndr0kali` (`192.168.1.91`)
Goal: passwordless SSH using Ed25519 + GitHub key distribution.

---

## Quickstart (new Pi)
```bash
# On the Pi console
mkdir -p ~/.ssh && chmod 700 ~/.ssh
curl -s https://github.com/4ndr0666.keys >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
````

```sshconfig
# Client: ~/.ssh/config
Host kali
    HostName 192.168.1.91
    User kali
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes
    StrictHostKeyChecking yes
    HashKnownHosts yes
    HostKeyAlgorithms ssh-ed25519
    ControlMaster auto
    ControlPath ~/.ssh/sockets/%r@%h-%p
    ControlPersist 600
    ServerAliveInterval 60
    ServerAliveCountMax 3
```

```bash
# Client once
mkdir -p ~/.ssh/sockets && chmod 700 ~/.ssh/sockets
# Connect
ssh kali
```

---

## Verify once

```bash
ssh -vvv -o ControlMaster=no -o ControlPath=none kali \
| grep -E 'Server host key|Authenticated|using "publickey"'
# Expect ED25519 host key SHA256:euCxRZ..., and "Authenticated ... using "publickey""
```

---

## Harden server (Pi)

```bash
sudo sed -i \
  -e 's/^[# ]*PasswordAuthentication.*/PasswordAuthentication no/' \
  -e 's/^[# ]*KbdInteractiveAuthentication.*/KbdInteractiveAuthentication no/' \
  -e 's/^[# ]*PubkeyAuthentication.*/PubkeyAuthentication yes/' \
  /etc/ssh/sshd_config
sudo systemctl restart ssh
```

---

## Multiplexing notes

* Reuses one authenticated TCP session for instant new shells and scp/sftp.
* Requires the `~/.ssh/sockets` dir (700).
* Not a sandbox. It’s speed + reliability.

Common controls:

```bash
ssh -O check kali   # is master up
ssh -O exit  kali   # stop master
```

---

## Mini-audit (repeat anytime)

### A. On the Pi

```bash
# 1) Who am I
whoami

# 2) Auth was key (recent logs)
sudo journalctl -u ssh -n 50 --no-pager | grep -i 'Accepted publickey' || echo 'NO publickey events seen'

# 3) Authorized keys and perms
ls -ld ~/.ssh
ls -l ~/.ssh/authorized_keys
head -n 2 ~/.ssh/authorized_keys
# Expect: .ssh 700, authorized_keys 600, your ed25519 key present

# 4) Server policy
sudo sshd -T | grep -E '^(passwordauthentication|kbdinteractiveauthentication|pubkeyauthentication)'

# 5) Host key fingerprint (should match client pin)
sudo ssh-keygen -l -f /etc/ssh/ssh_host_ed25519_key.pub

# 6) SSH service state
systemctl is-active ssh; systemctl is-enabled ssh

# 7) Port exposure (optional)
ss -tnlp | grep ':22'
```

### B. On the client

```bash
# 1) Socket dir exists
ls -ld ~/.ssh/sockets || (mkdir -p ~/.ssh/sockets && chmod 700 ~/.ssh/sockets)

# 2) Effective config for host
ssh -G kali | grep -E 'control(master|path|persist)|serveralive(interval|countmax)'

# 3) Known-hosts pin
ssh-keygen -F 192.168.1.91

# 4) Negative test (passwords disabled)
ssh -o PubkeyAuthentication=no -o PasswordAuthentication=yes kali || echo 'OK: passwords disabled'
```

If any check fails, fix and rerun until clean.

---

## Key management

```bash
# Generate
ssh-keygen -t ed25519 -a 100 -C "andro@theworkpc"

# Show pubkey
cat ~/.ssh/id_ed25519.pub

# Permissions
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
```

---

## Troubleshooting

```bash
# Verbose
ssh -vvv kali

# Refresh host pin
ssh-keygen -R 192.168.1.91
ssh-keyscan -t ed25519 192.168.1.91 >> ~/.ssh/known_hosts
ssh-keygen -H -f ~/.ssh/known_hosts && rm -f ~/.ssh/known_hosts.old

# Agent (if using)
eval "$(ssh-agent -s)"; ssh-add ~/.ssh/id_ed25519
```

````

---

Hardened `ssh_setup.sh` (idempotent, safe, reproducible):

```bash
#!/usr/bin/env bash
# Author: 4ndr0666
# Purpose: Install GitHub-hosted SSH keys into ~/.ssh/authorized_keys on the local host.
# Usage:   curl -fsSL https://example/path/ssh_setup.sh | bash
set -euo pipefail

USER_KEYS_URL="${1:-https://github.com/4ndr0666.keys}"

umask 077
mkdir -p "${HOME}/.ssh"

# Fetch keys to a temp file
tmp="$(mktemp)"
trap 'rm -f "$tmp"' EXIT
curl -fsSL "$USER_KEYS_URL" > "$tmp"

# Create/merge authorized_keys without duplicates
touch "${HOME}/.ssh/authorized_keys"
# Normalize line endings, de-dupe, keep existing keys
awk 'NF' "${HOME}/.ssh/authorized_keys" "$tmp" | sort -u > "${HOME}/.ssh/authorized_keys.new"
mv "${HOME}/.ssh/authorized_keys.new" "${HOME}/.ssh/authorized_keys"

chmod 700 "${HOME}/.ssh"
chmod 600 "${HOME}/.ssh/authorized_keys"

echo "Installed keys from: $USER_KEYS_URL"
````

Notes:

* Idempotent. Re-running won’t duplicate keys.
* Pass a different URL if needed:

  ```bash
  curl -fsSL https://…/ssh_setup.sh | bash -s -- https://gist.githubusercontent.com/<id>/raw/key.pub
  ```
