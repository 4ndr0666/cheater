# GPG Cheat-Sheet (GNU Privacy Guard)

**GnuPG (GPG)** is a complete and free implementation of the OpenPGP standard. It allows you to encrypt and sign your data and communications, featuring a versatile key management system and access modules for all kinds of public key directories.

It's used for:
*   **Encryption**: Protecting data confidentiality so only the intended recipient can read it.
*   **Digital Signatures**: Verifying the authenticity and integrity of data (proving who sent it and that it hasn't been tampered with).
*   **Key Management**: Creating, importing, exporting, and managing cryptographic keys.

---

## 1. Installation

GnuPG is typically available in most Linux distributions' package repositories and via Homebrew on macOS.

*   **Debian/Ubuntu:**
    ```bash
    sudo apt-get install gnupg2 # or gnupg depending on your distribution version
    ```
*   **Fedora:**
    ```bash
    sudo dnf install gnupg2
    ```
*   **Arch Linux:**
    ```bash
    sudo pacman -S gnupg
    ```
*   **macOS (Homebrew):**
    ```bash
    brew install gnupg
    ```

---

## 2. Key Generation

Creating your own GPG key pair (public and private key) is the first step.

### Generate a New Key Pair

The `--full-gen-key` command offers more options, including selecting the key type and size.

```bash
gpg --full-gen-key
```
**During generation, you will be prompted for:**
1.  **Key type**: RSA and RSA (default and recommended for most uses).
2.  **Key size**: 4096 bits is a good modern choice.
3.  **Key expiration**: Recommended to set an expiration, e.g., 1y (1 year). You can extend it later.
4.  **User ID**: Real name, email address, and an optional comment.
5.  **Passphrase**: **Crucial!** Choose a strong, unique passphrase to protect your private key. This passphrase will be required for signing or decrypting.

### Generating a Key Pair with Subkeys

For enhanced security, generate a master (primary) key for signing and separate subkeys for encryption and authentication. This allows you to revoke or replace subkeys without affecting the primary key's validity.
Start with `--full-gen-key` as above, then once the primary key is generated:
```bash
gpg --edit-key <KEY_ID_OR_EMAIL>
# In the GPG prompt, type:
# addkey
# Follow prompts to add encryption and/or authentication subkeys
# save
```

---

## 3. Key Management

### List Keys

View public keys in your keyring.

*   **List all public keys:**
    ```bash
    gpg --list-keys
    ```
*   **List all public keys with fingerprints:** (Fingerprint is a unique identifier)
    ```bash
    gpg --list-keys --fingerprint
    ```
*   **List secret (private) keys:** (Only these can sign/decrypt)
    ```bash
    gpg --list-secret-keys
    ```
*   **List specific key by email or ID:**
    ```bash
    gpg --list-keys "your_email@example.com"
    ```

### Export Keys

Share your public key or back up your private key.

*   **Export your public key (ASCII armored for sharing):**
    ```bash
    gpg --export --armor "your_email@example.com" > public_key.asc
    ```
    *   *`--armor` (`-a`) outputs in ASCII (text) format, suitable for copy-pasting or email.*
*   **Export a recipient's public key (e.g., if you signed it):**
    ```bash
    gpg --export --armor "recipient_email@example.com" > recipient_public_key.asc
    ```
*   **Export your secret (private) key (CRITICAL FOR BACKUP!):**
    ```bash
    gpg --export-secret-keys --armor "your_email@example.com" > private_key.asc
    ```
    *   *Store this file securely, ideally offline and encrypted. It's protected by your passphrase.*

### Import Keys

Add public keys from others into your keyring, or restore your private key.

*   **Import a public key from a file:**
    ```bash
    gpg --import public_key.asc
    ```
*   **Import a private key from a backup:**
    ```bash
    gpg --import private_key.asc
    ```

### Delete Keys

Remove keys from your keyring. **Be careful when deleting private keys!**

*   **Delete a public key:**
    ```bash
    gpg --delete-key "your_email@example.com"
    ```
*   **Delete a secret (private) key:**
    ```bash
    gpg --delete-secret-key "your_email@example.com"
    ```
    *   *If you delete a secret key, you'll no longer be able to decrypt data or sign with it unless you import a backup.*

### Edit Key (Advanced)

Modify properties of an existing key, such as its expiration date, user IDs, or add/revoke subkeys.

```bash
gpg --edit-key "your_email@example.com"
# In the GPG prompt, common commands:
# expire     # Change expiration date
# addkey     # Add a subkey
# revoke     # Revoke a (sub)key
# uid <n>    # Select a user ID
# trust      # Set trust level for this key
# save       # Save changes and exit
# quit       # Exit without saving
```

### Refresh Keys (Update from Key Server)

Fetch the latest versions of public keys from a configured key server.

```bash
gpg --refresh-keys
```

---

## 4. Encryption & Decryption

### Encrypt for a Recipient

Encrypt a file so only the recipient (who owns the corresponding private key) can decrypt it.

```bash
gpg --encrypt --recipient "recipient_email@example.com" --output encrypted_file.gpg original_file.txt
# Shorter form:
gpg -e -r "recipient_email@example.com" -o encrypted_file.gpg original_file.txt
```
*   *You must have the recipient's public key in your keyring.*
*   *The output file will be binary unless `--armor` (`-a`) is used.*

### Encrypt and Sign

Encrypt a file *and* digitally sign it with your private key. This provides confidentiality (encryption) and authenticity/integrity (signature).

```bash
gpg --encrypt --sign --recipient "recipient_email@example.com" --output encrypted_signed_file.gpg original_file.txt
# Shorter form:
gpg -se -r "recipient_email@example.com" -o encrypted_signed_file.gpg original_file.txt
```
*   *You will be prompted for your passphrase to sign the file.*

### Symmetric Encryption (Password-based)

Encrypt a file using a passphrase directly, without relying on public/private key pairs. Useful for encrypting files for yourself or sharing with someone you trust to verbally exchange a passphrase.

```bash
gpg --symmetric --output encrypted_symmetric.gpg original_file.txt
# Shorter form:
gpg -c -o encrypted_symmetric.gpg original_file.txt
```
*   *You will be prompted to enter and confirm a passphrase.*

### Decrypt a File

Decrypt an encrypted file. If it was encrypted for you, you'll need your passphrase.

```bash
gpg --output decrypted_file.txt --decrypt encrypted_file.gpg
# Shorter form:
gpg -o decrypted_file.txt -d encrypted_file.gpg
```
*   *If the file was signed, GPG will also verify the signature and report its status.*
*   *If no `--output` (`-o`) is specified, the decrypted content will be printed to `stdout`.*

---

## 5. Digital Signatures

Sign a file to prove its authenticity (who created it) and integrity (that it hasn't been altered).

### Detached Signature

Creates a separate signature file (`.sig` or `.asc`) that accompanies the original file. This is generally preferred as it doesn't modify the original content.

```bash
gpg --detach-sign --armor --output original_file.txt.sig original_file.txt
# Shorter form:
gpg -b -a -o original_file.txt.sig original_file.txt
```
*   *You will be prompted for your passphrase.*

### Cleartext Signature

Adds an inline signature to a plain text file, making it human-readable even without GPG. The signature block is added at the end.

```bash
gpg --clearsign --output original_file.txt.asc original_file.txt
```
*   *The output will include the original text and the signature block.*

### Standard (Inline) Signature

Creates an entirely new file containing the original content and its binary signature. The original file is not human-readable directly.

```bash
gpg --sign --output original_file.gpg original_file.txt
```

### Verify a Signature

Check if a file's signature is valid and who signed it.

*   **Verify a detached signature:**
    ```bash
    gpg --verify original_file.txt.sig original_file.txt
    ```
    *   *You must have the signer's public key in your keyring.*
*   **Verify a cleartext or inline signature:**
    ```bash
    gpg --verify original_file.txt.asc
    gpg --verify original_file.gpg
    ```

---

## 6. Best Practices & Tips

*   **Strong Passphrases**: Always use long, complex, and unique passphrases for your private keys.
*   **Backup Secret Keys**: Export your private key to a secure, offline location (e.g., encrypted USB drive) immediately after creation. If you lose it, you lose access to encrypted data and the ability to sign.
*   **Key ID vs. Email**: When specifying recipients (`-r`) or keys to manage, using the full key ID or fingerprint is more robust than just an email address, which can be ambiguous.
*   **ASCII Armor (`--armor` / `-a`)**: Always use this when exporting public keys or detached signatures for sharing via text-based channels (email, web).
*   **Revocation Certificate**: Create one immediately after generating your key. This allows you to revoke your key if it's compromised or lost, even if you don't have access to your private key.
    ```bash
    gpg --output revoke.asc --gen-revoke "your_email@example.com"
    ```
    *   *Store `revoke.asc` securely, separate from your private key backup.*
*   **Web of Trust**: Understand how to sign others' keys (`gpg --sign-key`) and set trust levels (`gpg --edit-key > trust`) to build a network of trusted identities.
*   **Subkeys**: Utilize subkeys for encryption and signing while keeping your master key offline. This limits exposure if a subkey is compromised.

---

## 7. Configuration (`~/.gnupg/gpg.conf`)

Customize GPG's behavior by editing its configuration file.

*   **Default Keyserver**:
    ```
    keyserver hkps://keys.openpgp.org
    ```
*   **Always ASCII Armor**:
    ```
    armor
    ```
*   **Long Key IDs**:
    ```
    keyid-format 0xlong
    ```
*   **Verbose Output**:
    ```
    verbose
    ```
