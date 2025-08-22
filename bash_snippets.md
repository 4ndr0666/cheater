# Bash Snippets by 4ndr0666

A curated collection of useful Bash command-line and scripting snippets.

### **Table of Contents**
1.  [Terminal Aesthetics & Variables](#1--terminal-aesthetics--variables)
2.  [Shell Environment & Strict Mode](#2--shell-environment--strict-mode)
3.  [Script Execution & Control Flow](#3--script-execution--control-flow)
4.  [Process Management](#4--process-management)
5.  [Input/Output Redirection](#5--inputoutput-redirection)
6.  [Conditional Logic (If-Statements)](#6--conditional-logic-if-statements)
7.  [Heredoc Patterns](#7--heredoc-patterns)
8.  [Loops & Iteration](#8--loops--iteration)
9.  [File & Path Manipulation](#9--file--path-manipulation)
10. [Privilege Escalation](#10--privilege-escalation)
11. [Logging Framework](#11--logging-framework)
12. [Backup & Automation](#12--backup--automation)
13. [Utility Snippets](#13--utility-snippets)

---

## 1. 🌈 Terminal Aesthetics & Variables

Define `tput` variables for colored and reset text, enhancing script readability and user feedback without hardcoding ANSI escape codes.

```bash
clear # Clear the terminal screen
OK="$(tput setaf 2)[OK]$(tput sgr0)"       # Green [OK]
ERROR="$(tput setaf 1)[ERROR]$(tput sgr0)" # Red [ERROR]
NOTE="$(tput setaf 3)[NOTE]$(tput sgr0)"   # Yellow [NOTE]
INFO="$(tput setaf 4)[INFO]$(tput sgr0)"   # Blue [INFO]
WARN="$(tput setaf 1)[WARN]$(tput sgr0)"   # Red [WARN] (often used for warnings that aren't critical errors)
ACTION="$(tput setaf 6)[ACTION]$(tput sgr0)" # Cyan [ACTION]

# Color variables for direct use
MAGENTA="$(tput setaf 5)"
ORANGE="$(tput setaf 214)" # ANSI color code for a distinct orange
WARNING_RED="$(tput setaf 1)" # Pure Red, often used for warnings
YELLOW="$(tput setaf 3)"
GREEN="$(tput setaf 2)"
BLUE="$(tput setaf 4)"
SKY_BLUE="$(tput setaf 6)" # Cyan
RESET="$(tput sgr0)"       # Reset all text attributes to default
```

**Usage Example:**
`echo "${INFO} Starting service..."`
`echo "${WARNING_RED}Critical error detected!${RESET}"`

---

## 2. 🐚 Shell Environment & Strict Mode

Commands to ensure predictable and robust script execution.

### **Avoid Filename Expansion (Globbing)**
Prevents `*`, `?`, `[` from expanding into filenames. Useful when processing literal strings that might contain these characters.

```bash
set -f # Disable globbing
# ... (your code that needs literal interpretation)
unset -f # Re-enable globbing if needed later
```

### **Force POSIX Shell**
Ensures the script runs in `sh`, not `bash`, for maximum compatibility across different Unix-like systems.

```bash
if [ "$(ps -p $$ -ocomm=)" != "sh" ]; then
    exec sh "$0" "$@" # Re-execute the script with 'sh'
fi
# Script continues here, now running under sh
```

### **Shell Script Debugging & Best Practices (`Strict Mode`)**
The "Unofficial Bash Strict Mode" for robust scripts. Always include this at the top of your scripts.

```bash
set -euo pipefail # IMPORTANT: Set strict mode for robust scripting
IFS=$'\n\t'      # Prevents word splitting on spaces within variables
```
*   **`set -e`**: Exit immediately if a command exits with a non-zero status.
*   **`set -u`**: Treat unset variables as an error and exit.
*   **`set -o pipefail`**: The return value of a pipeline is the status of the last command to exit with a non-zero status, or zero if all commands exit successfully.
*   **`IFS=$'\n\t'`**: Modifies the Internal Field Separator to prevent unexpected word splitting, especially useful when dealing with filenames containing spaces.

---

## 3. 🚀 Script Execution & Control Flow

Manage how your script responds to signals and includes other files.

### **TRAP for Cleanup on Ctrl+C**
Execute a cleanup function when the script receives an interrupt signal (e.g., Ctrl+C).

```bash
cleanup() {
    echo "${NOTE} Performing cleanup tasks..."
    # Add commands here to clean up temporary files, stop services, etc.
    rm -f /tmp/my_temp_file.txt
    echo "${OK} Cleanup complete."
}

trap 'echo -e "\n${WARN} Interruption detected.${RESET}"; cleanup; exit 1' SIGINT
# The script will now call 'cleanup' and exit with status 1 if Ctrl+C is pressed.
```

### **Conditional Execution (`&&` and `||`)**
Execute commands only if the previous one succeeded (`&&`) or failed (`||`).

```bash
# Execute command2 only if command1 succeeds
command1 && command2

# Execute command2 only if command1 fails
command1 || command2

# Example: Check if a file exists, then process it, or show an error
[ -f "myfile.txt" ] && cat "myfile.txt" || echo "Error: myfile.txt not found!"
```

### **Sourcing Files in a Directory**
Load (source) all scripts or configuration files from a specific directory.

```bash
source_dir="/etc/my_app_configs"
files_found=false

# Use process substitution (< <(find ...)) to avoid issues with 'while read' and subshells
while IFS= read -r file; do
    files_found=true
    if [[ -f "$file" ]]; then # Ensure it's a regular file before sourcing
        source "$file"
        echo "${INFO} Sourced $file successfully.${RESET}"
    fi
done < <(find "$source_dir" -type f -name "*.sh") # Sourcing only .sh files

if [ "$files_found" = false ]; then
    echo "${NOTE} Warning: No shell script files found in $source_dir.${RESET}"
fi
```

---

## 4. ⚙️ Process Management

Snippets for controlling background processes.

### **Kill and Restart a Program**
Safely terminate an existing process by its name and then restart it in the background.

```bash
pkgname="my_service_process" # Replace with the actual program name

# Kill all processes with the specified name, suppress errors if not found
killall -q "$pkgname" &> /dev/null

# Start the program in the background, redirecting all its output to /dev/null
# </dev/null: Redirects stdin from /dev/null to detach from terminal
"$pkgname" </dev/null &>/dev/null &

echo "${INFO} ${pkgname} killed and restarted in background.${RESET}"
```

### **Run a Command in the Background**
Execute a command without waiting for it to finish.

```bash
# Simple background execution
long_running_command &

# Background execution with all output silenced
another_command >/dev/null 2>&1 &

# Get the PID of the background process
(yet_another_command) & pid=$!
echo "${INFO} Background process PID: $pid${RESET}"
```

---

## 5. 🗑️ Input/Output Redirection

Control where command output goes.

### **`/dev/null` Redirection Patterns**
Understanding how to silence or combine stdout and stderr.

*   **`>/dev/null 2>&1`**: Redirects both standard output (`stdout`, descriptor `1`) and standard error (`stderr`, descriptor `2`) to `/dev/null`, silencing all output.
*   **`2>&1`**: Redirects `stderr` to `stdout`. Both go to the terminal if `stdout` is not otherwise redirected.
*   **`2>/dev/null`**: Redirects only `stderr` to `/dev/null`. `stdout` still goes to the terminal.

**Usage Examples:**

*   **Silence background task output:**
    ```bash
    command_to_run_silently >/dev/null 2>&1 &
    ```
*   **Combine stderr & stdout for processing (e.g., with `grep`):**
    ```bash
    command_with_mixed_output 2>&1 | grep "critical error"
    ```
*   **Ignore errors, process only successful output:**
    ```bash
    command_that_might_fail 2>/dev/null
    ```

---

## 6. 🚦 Conditional Logic (If-Statements)

Common `if` statement patterns for robust scripting.

### **If-Statement: Logfile Writable**
Checks if a specified log file path is writable before attempting to write to it.

```bash
logfile="/var/log/my_app/app.log" # Example log file path
if ! touch "$logfile" &>/dev/null; then
    echo -e "${ERROR} Log file '$logfile' is not writable. Please check permissions.${RESET}"
    # Optionally, fallback to a different log path or exit
    exit 1
fi
echo "${INFO} Log file '$logfile' is writable.${RESET}"
```

### **If-Statement: Common Include Guard**
Prevents a script or configuration file from being sourced multiple times within the same shell session.

```bash
# Place this at the top of a file intended to be sourced.
if [[ "${COMMON_SH_INCLUDED:-}" == "true" ]]; then
    return # Exit if already included
fi
export COMMON_SH_INCLUDED=true # Mark as included
# ... rest of the file's content
```

### **If-Statement: Conditional Directory Check**
Executes code only if a specific subdirectory exists within `scr_dir`.

```bash
scr_dir="/opt/my_application" # Example base directory
if [[ -d "$scr_dir/bin" ]]; then
    echo "${INFO} '$scr_dir/bin' directory exists. Proceeding with related operations.${RESET}"
    # Your code here to process files in $scr_dir/bin
else
    echo "${NOTE} '$scr_dir/bin' directory does not exist. Skipping related operations.${RESET}"
fi
```

### **If-Statement: Check if variable is set (POSIX-compliant)**
Ensures a variable has a value before proceeding.

```bash
my_variable="some_value" # Or unset my_variable

if [ -z "${my_variable+set}" ]; then
    echo "${WARN} my_variable is unset.${RESET}"
else
    echo "${INFO} my_variable is set.${RESET}"
fi

# Alternative: Check if variable is set AND not empty
if [ -z "${my_variable}" ]; then
    echo "${WARN} my_variable is unset or empty.${RESET}"
else
    echo "${INFO} my_variable is set and not empty: '${my_variable}'.${RESET}"
fi
```

---

## 7. 📝 Heredoc Patterns

Create multi-line strings or files directly within your script.

### **Appending to a File**

```bash
cat << EOF >> file.txt
This text you want
to append to
file.txt goes here.
It will be added
to the end of the file.
EOF
echo "${INFO} Content appended to file.txt.${RESET}"
```

### **Overwriting a File**

```bash
cat << EOF > file.txt
This text will overwrite
the entire content of file.txt
with this new text.
EOF
echo "${INFO} Content overwrote file.txt.${RESET}"
```

### **Indented Content Using `<<-`**
The `<<-` (dash-heredoc) operator strips leading tab characters from lines in the heredoc.

```bash
cat <<- EOF > file.txt
    This text is indented with tabs in the script
    but will not be indented in file.txt because of <<- .
    Whitespace other than tabs is preserved.
EOF
echo "${INFO} Indented content written to file.txt (leading tabs removed).${RESET}"```

### **Heredoc using `tee` (with `sudo` for privileged files)**
Combine `tee` with heredoc for creating or appending to files that require root privileges.

#### **Creating/Overwriting a Privileged File**
The `'EOF'` (quoted EOF) prevents shell variable expansion inside the heredoc, useful for scripts.

```bash
sudo tee /usr/local/bin/my_privileged_script.sh > /dev/null << 'EOF'
#!/bin/bash
# This is a script created with sudo tee
echo "Hello from a privileged script!"
# $USER will be expanded as root user here because of sudo
echo "Running as: $USER"
EOF

# Make the script executable
sudo chmod +x /usr/local/bin/my_privileged_script.sh
echo "${INFO} Privileged script created and made executable.${RESET}"
```

#### **Appending to a Privileged File**

```bash
sudo tee -a /etc/sysctl.conf > /dev/null << EOF
# Added by my_script on $(date '+%Y-%m-%d')
net.ipv4.tcp_fastopen = 3
EOF
echo "${INFO} Added TCP Fast Open setting to /etc/sysctl.conf.${RESET}"
```

---

## 8. 🔄 Loops & Iteration

Simple loop patterns.

### **Simple Numeric Loop**
Iterate a specific number of times.

```bash
echo "${INFO} Counting from 1 to 5:${RESET}"
for i in {1..5}; do
    echo "  Number: $i"
done
```

### **Iterate Over a List of Items**
Process each item in a predefined list.

```bash
fruits=("Apple" "Banana" "Cherry")
echo "${INFO} My favorite fruits are:${RESET}"
for fruit in "${fruits[@]}"; do
    echo "  - $fruit"
done
```

---

## 9. 📁 File & Path Manipulation

Work with file paths and existence.

### **Realpath (Two Levels Up)**
Gets the absolute path of a directory two levels above the current script.

```bash
: "${PKG_PATH:=$(dirname "$(dirname "$(realpath "$0")")")}"
# ^-- Traverses two levels up from the script's absolute path.
echo "${INFO} PKG_PATH (two levels up): $PKG_PATH${RESET}"
```
*   `realpath "$0"`: Resolves the absolute path of the current script, following symlinks.
*   `dirname "$(realpath "$0")"`: Gets the directory containing the script.
*   `dirname "$(dirname "$(realpath "$0")")"`: Gets the directory above that.

### **Realpath (One Level Up)**
Gets the absolute path of the directory one level above the current script.

```bash
: "${PARENT_DIR:=$(dirname "$(realpath "$0")")}"
# ^-- Traverses one level up from the script's absolute path.
echo "${INFO} PARENT_DIR (one level up): $PARENT_DIR${RESET}"
```

### **Realpath via Function (Symlink-aware)**
A function to get the true directory of a script, whether it's a direct file or a symlink. This is crucial for scripts that rely on relative paths to other files in their directory.

```bash
rootpath() {
    local source_path="$0"
    # If the script is a symlink, resolve its real path
    if [[ -L "$source_path" ]]; then
        dirname "$(readlink "$source_path")"
    else
        dirname "$source_path"
    fi
}
SCRIPT_DIR="$(rootpath)"
echo "${INFO} SCRIPT_DIR: $SCRIPT_DIR${RESET}"
```

### **Realpath via BASH_SOURCE (Bash-specific, Symlink-aware)**
A Bash-specific alternative to `rootpath` that uses `BASH_SOURCE`, which often contains more accurate information about the script's origin in complex sourcing scenarios.

```bash
rootpath_bash() {
    local source_path="${BASH_SOURCE[0]:-$0}" # Use BASH_SOURCE if available, otherwise $0
    if [[ -L "$source_path" ]]; then
        dirname "$(readlink "$source_path")"
    else
        dirname "$source_path"
    fi
}
SCRIPT_DIR_BASH="$(rootpath_bash)"
echo "${INFO} SCRIPT_DIR_BASH: $SCRIPT_DIR_BASH${RESET}"
```

---

## 10. 👑 Privilege Escalation

Methods to ensure your script runs with `sudo` permissions when needed.

### **Method 1: `exec sudo` (Re-executes with Root)**
This method completely replaces the current shell process with a new one running as `sudo`. It's clean and ensures all subsequent commands run as root.

```bash
if [[ "${EUID}" -ne 0 ]]; then
    echo "${WARNING_RED}WARNING: Escalating to root (sudo)...${RESET}" >&2
    # The 'exec' command replaces the current shell with sudo, preserving arguments
    exec sudo "$0" "$@"
    # The script will exit here if exec succeeds, otherwise it would continue below
fi
echo "${OK} Running with root privileges.${RESET}"
```
*   `EUID`: Effective User ID. `0` means root.
*   `exec sudo "$0" "$@"`: `exec` replaces the current process. `sudo "$0" "$@"` runs the script itself (`$0`) with all its arguments (`$@`) under `sudo`.

### **Method 2: `sudo "$0" "$@"` (Spawns New Process)**
This method runs `sudo` as a child process. The original script exits, and a new, privileged instance continues.

```bash
if [[ "$EUID" -ne 0 ]]; then
    echo "${INFO} Re-running the script with sudo privileges...${RESET}" >&2
    sudo "$0" "$@" # Spawns a new process with sudo, then exits the current one
    exit $? # Ensure the original script exits with the status of the sudo command
fi
echo "${OK} Running with root privileges.${RESET}"
```

### **Method 3: `id -u` Check with Warning**
Similar to Method 2 but uses `id -u` (POSIX compliant) and includes explicit warnings.

```bash
if [ "$(id -u)" -ne 0 ]; then
    sudo "$0" "$@" # Re-execute with sudo
    exit $?
fi
sleep 1
echo "${WARNING_RED}WARNING: You are now operating as root...${RESET}"
sleep 1
echo
```

### **Method 4: Self-Executable Check**
Ensures the script itself has executable permissions before attempting escalation. Important if the script might be downloaded without `+x` set.

```bash
if [ ! -x "$(realpath "$0")" ]; then
  echo "${WARN} Script '$(realpath "$0")' is not executable. Attempting to set executable permission...${RESET}" >&2
  if ! chmod +x "$(realpath "$0")"; then
    echo "${ERROR} Failed to set executable permission. Please run: sudo chmod +x $(realpath "$0")${RESET}" >&2
    exit 126 # Command invoked cannot execute
  fi
  exec "$0" "$@" # Re-execute now that it's executable
fi
# Then proceed with other privilege checks if necessary
```

### **Method 5: Robust `sudo` Escalation with Error Check**
Adds an explicit check for `sudo` failure.

```bash
if [ "$EUID" -ne 0 ]; then
    echo "${INFO} Re-running the script with sudo privileges...${RESET}" >&2
    if ! sudo "$0" "$@"; then
        echo "${ERROR} Failed to escalate privileges. Exiting...${RESET}" >&2
        exit 1
    fi
    exit 0 # Exit the unprivileged instance
fi
echo "${OK} Running with root privileges.${RESET}"
```

### **Method 6 (POSIX Compliant)**
Uses `id -u` and `exec sudo sh` for a POSIX-compliant self-escalation.

```bash
if [ "$(id -u)" -ne 0 ]; then
    printf "${INFO} Re-running with sudo privileges…${RESET}\n" >&2
    exec sudo sh "$0" "$@" # Re-execute using sh for POSIX compliance
fi
echo "${OK} Running with root privileges.${RESET}"
```

### **Method 7 (Short POSIX Compliant)**
A compact version of Method 6.

```bash
[ "$(id -u)" -eq 0 ] || exec sudo sh "$0" "$@"
echo "${OK} Running with root privileges.${RESET}"
```
*   `[ "$(id -u)" -eq 0 ]`: Checks if UID is 0 (root).
*   `||`: If the previous command fails (i.e., not root), execute the next part.
*   `exec sudo sh "$0" "$@"`: Re-executes the script with `sudo` via `sh`.

---

## 11. 🪵 Logging Framework (Minimal JSON Example)

A simple, extensible JSON-based logging framework for Bash scripts.

### **Define Global Constants**
Centralize your log file paths and configuration.

```bash
# Configuration file for the service (e.g., JSON config)
CONFIG_FILE="${CONFIG_FILE:-$HOME/.local/share/4ndr0service/config.json}"

# Default directory for log files
LOG_FILE_DIR_DEFAULT="$HOME/.local/share/logs/4ndr0service"
# Default full path to the log file
LOG_FILE_DEFAULT="$LOG_FILE_DIR_DEFAULT/4ndr0service.log"```

### **Logging Functions**
Functions to write structured JSON logs to a file.

```bash
# json_log <LEVEL> <MESSAGE>
json_log() {
    local level="$1"
    local msg="$2"
    local timestamp
    timestamp="$(date '+%Y-%m-%dT%H:%M:%S%z')" # ISO 8601 format with timezone
    
    # Ensure log directory exists, use default if LOG_FILE_DIR is not set
    mkdir -p "${LOG_FILE_DIR:-$LOG_FILE_DIR_DEFAULT}"
    
    # Print formatted JSON log entry to the log file
    printf '{"timestamp":"%s","level":"%s","message":"%s"}\n' \
    "$timestamp" "$level" "$msg" >> "${LOG_FILE:-$LOG_FILE_DEFAULT}"
}

# Example Usage:
# json_log "INFO" "Application started."
# json_log "ERROR" "Failed to connect to database."
```

### **Error Handling and Info/Warning Functions**
Convenience functions wrapping `json_log` with standard output/error messages.

```bash
# handle_error <ERROR_MESSAGE>
handle_error() {
    local error_message="$1"
    json_log "ERROR" "$error_message" # Log to file
    echo -e "${ERROR} Error: $error_message${RESET}" >&2 # Print to stderr
    exit 1 # Exit script on error
}

# log_info <MESSAGE>
log_info() {
    local message="$1"
    json_log "INFO" "$message" # Log to file
    echo -e "${INFO} $message${RESET}" # Also print to stdout for user feedback
}

# log_warn <MESSAGE>
log_warn() {
    local message="$1"
    json_log "WARN" "$message" # Log to file
    echo -e "${YELLOW} Warning: $message${RESET}" # Print to stdout with warning color
}

# Example Usage:
# log_info "Processing user data."
# log_warn "Configuration file not found, using defaults."
# handle_error "Network connection lost."
```

---

## 12. 🗓️ Backup & Automation

Script for setting up periodic backups via `cron` using Heredoc.

### **Heredoc Backup Script with Cronjob**
This function creates a robust backup script with a pruning mechanism and schedules it with `cron`.

```bash
backups() {
    log_info "Setting up automatic periodic backups of critical configuration files via cron..."
    
    # Define paths for the backup script, cron job, and backup directory
    BACKUP_SCRIPT="/usr/local/bin/my_app_backup.sh" # Renamed for clarity
    CRON_JOB="/etc/cron.d/my_app_backup"            # Renamed for clarity
    BACKUP_DIR="/var/backups/my_app_configs"       # Renamed for clarity, using /var/backups

    # Ensure backup directory exists
    if mkdir -p "$BACKUP_DIR"; then
        log_info "Backup directory $BACKUP_DIR ensured."
    else
        handle_error "Failed to create backup directory $BACKUP_DIR. Check permissions."
    fi

    # Set ownership for security (important for system cron jobs)
    chown root:root "$BACKUP_DIR"
    chmod 700 "$BACKUP_DIR" # Restrict access to root only
    log_info "Ownership and permissions of backup directory set to root."

    # Define the backup script content with a pruning mechanism
    cat << 'EOF' > "$BACKUP_SCRIPT"
#!/bin/bash
# Backup Script for my_app configurations
# This script backs up critical configuration files, keeping only the latest backup per file.

# Directory to store backups
BACKUP_DIR="/var/backups/my_app_configs" # Match the parent script

# Log file for the cron job itself (distinct from application logs)
CRON_LOG_FILE="/var/log/my_app_cron_backup.log" # Dedicated log for the cron job

# Create backup directory if it doesn't exist
mkdir -p "$BACKUP_DIR"

# List of critical files to backup (ADD YOUR OWN FILES HERE!)
FILES=(
    "/etc/sysctl.conf"
    "/etc/fstab"
    "/etc/ssh/sshd_config"
    # Add other critical config files for your application/system
)

# Current timestamp
TIMESTAMP=$(date +"%Y%m%d_%H%M%S")

# Function to log cron job messages
cron_log() {
    echo "$(date '+%Y-%m-%dT%H:%M:%S%z') - $1" >> "$CRON_LOG_FILE"
}

cron_log "Starting periodic backup."

# Perform backups
for FILE in "${FILES[@]}"; do
    if [[ -f "$FILE" ]]; then
        BASENAME=$(basename "$FILE")
        cp "$FILE" "$BACKUP_DIR/${BASENAME}.backup_${TIMESTAMP}"
        if [[ $? -eq 0 ]]; then
            cron_log "Backup successful for $FILE."
        else
            cron_log "Error: Backup failed for $FILE."
            # Continue to next file, but indicate overall failure if needed
        fi
    else
        cron_log "Warning: $FILE does not exist. Skipping backup."
    fi
done

# Pruning mechanism: Keep only the latest backup for each file
for FILE in "${FILES[@]}"; do
    BASENAME=$(basename "$FILE")
    # Find all backups for the current file, sorted by modification time (newest first)
    # Use array for robustness with filenames containing spaces
    readarray -t BACKUPS < <(ls -t "$BACKUP_DIR/${BASENAME}.backup_"* 2>/dev/null || true)
    BACKUP_COUNT=${#BACKUPS[@]}

    if [[ "$BACKUP_COUNT" -gt 1 ]]; then
        # Keep only the first (latest) backup
        for ((i=1; i<BACKUP_COUNT; i++)); do
            OLD_BACKUP="${BACKUPS[$i]}"
            rm -f "$OLD_BACKUP"
            if [[ $? -eq 0 ]]; then
                cron_log "Removed old backup: $OLD_BACKUP."
            else
                cron_log "Error: Failed to remove old backup: $OLD_BACKUP."
            fi
        done
    fi
done
cron_log "Periodic backup finished."
EOF

    # Make the backup script executable
    chmod +x "$BACKUP_SCRIPT"
    log_info "Backup script $BACKUP_SCRIPT created and made executable."

    # Define the cron job (daily at 2am, adjust as needed)
    # Ensure cron.d files have a newline at the end!
    CRON_CONTENT="0 2 * * * root $BACKUP_SCRIPT >> $CRON_LOG_FILE 2>&1"

    # Create or update the cron job file
    if [[ -f "$CRON_JOB" ]]; then
        if grep -Fxq "$CRON_CONTENT" "$CRON_JOB"; then
            log_info "Cron job already exists at $CRON_JOB. Skipping creation."
        else
            # Overwrite with new content to ensure clean update
            echo "$CRON_CONTENT" > "$CRON_JOB"
            log_info "Cron job updated at $CRON_JOB."
        fi
    else
        echo "$CRON_CONTENT" > "$CRON_JOB"
        log_info "Cron job created at $CRON_JOB."
    fi

    # Execute the backup script immediately to perform an initial backup
    log_info "Executing backup script immediately to perform initial backup..."
    if "$BACKUP_SCRIPT"; then # Execute directly, output goes to $CRON_LOG_FILE via redirection in cron entry
        log_info "Initial backup completed successfully."
    else
        log_warn "Initial backup failed. Check $CRON_LOG_FILE for details."
        # Do not exit here, allow verification to report specific issues
    fi

    # Backup Verification
    log_info "Verifying backups..."
    # IMPORTANT: Update this FILES list to match the FILES in your backup script!
    VERIFY_FILES=(
        "/etc/sysctl.conf"
        "/etc/fstab"
        "/etc/ssh/sshd_config"
    )
    
    all_verified=true
    for FILE in "${VERIFY_FILES[@]}"; do
        BASENAME=$(basename "$FILE")
        LATEST_BACKUP=$(ls -t "$BACKUP_DIR/${BASENAME}.backup_"* 2>/dev/null | head -n1 || true)
        if [[ -f "$LATEST_BACKUP" && -s "$LATEST_BACKUP" ]]; then
            log_info "Backup verification passed for $FILE: $LATEST_BACKUP exists and is not empty."
        else
            log_warn "Backup verification failed for $FILE: No valid backup found."
            all_verified=false
        fi
    done
    
    if "$all_verified"; then
        log_info "All backups verified successfully."
    else
        handle_error "One or more backup verifications failed."
    fi
}
```

---

## 13. 🛠️ Utility Snippets

Handy one-off commands and patterns.

### **Spinner Animation**
Display a simple command-line spinner for long-running operations.

```bash
spinner() {
    local pid=$! # PID of the command you are waiting for
    local delay=0.1
    local spinstr='|/-\'
    
    echo -n "${INFO} Working... ${RESET}"
    while [ "$(ps a | awk '{print $1}' | grep $pid)" ]; do
        local temp=${spinstr#?}
        printf "%c" "$spinstr"
        local spinstr=$temp${spinstr%"$temp"}
        sleep "$delay"
        printf "\b"
    done
    printf " \b" # Erase the spinner itself
    echo "${OK} Done.${RESET}"
}

# Example usage:
# (sleep 5 && echo "Task complete") & spinner # Wrap your command in parentheses for a subshell
```

### **Validate Numeric Input within a Range**
Ensures user input is a number within a specified range.

```bash
validate_input() {
    local input_val="$1"
    local min_val="$2"
    local max_val="$3"

    if [[ "$input_val" =~ ^[0-9]+$ ]] && \
       [ "$input_val" -ge "$min_val" ] && \
       [ "$input_val" -le "$max_val" ]; then
        return 0 # Valid input
    else
        echo "${YELLOW}Invalid input. Please enter a number between $min_val and $max_val.${RESET}" >&2
        return 1 # Invalid input
    fi
}

# Example Usage:
# read -p "${ACTION} Enter a number (1-17): ${RESET}" user_choice
# if validate_input "$user_choice" 1 17; then
#     echo "${OK} Input is valid: $user_choice${RESET}"
# else
#     echo "${ERROR} Please try again.${RESET}"
# fi
```
