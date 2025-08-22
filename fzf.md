# FZF Cheat-Sheet (Fuzzy Finder)

**fzf** is a general-purpose command-line fuzzy finder. It's an interactive Unix filter for command-line that can be used with any list; files, command history, processes, hostnames, bookmarks, git commits, etc.

---

## 1. Installation

### General Installation

You can install `fzf` using your system's package manager.

*   **Ubuntu/Debian:**
    ```bash
    sudo apt install fzf
    ```
*   **Fedora:**
    ```bash
    sudo dnf install fzf
    ```
*   **macOS (Homebrew):**
    ```bash
    brew install fzf
    ```
*   **Arch Linux:**
    ```bash
    sudo pacman -S fzf
    ```

### Manual Installation (and recommended for full features)

Cloning the official repository allows you to easily update and run the install script for shell extensions and key bindings.

```bash
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install
```
Follow the prompts during the `install` script to enable key bindings, fuzzy completion, and fuzzy `cd`.

---

## 2. Basic Usage

`fzf` acts as a filter. It takes input from `stdin` and writes the selected items to `stdout`.

### Simple File Search

```bash
ls -F | fzf
```
This will list files and directories, then allow you to fuzzy search and select one.

### Find Files (with `find` or `fd`)

Combine `fzf` with `find` (or `fd`, a faster alternative) to search through your filesystem.

```bash
# Using find
find . -type f | fzf

# Using fd (requires 'fd-find' package)
fd . | fzf
```

### Search Command History (Ctrl+R)

This is one of `fzf`'s most powerful integrations. Press `Ctrl+R` in your terminal to search your command history.

```bash
# In your terminal, type:
# Ctrl+R
```

### Search Git Commits

Search through your Git commit history.

```bash
git log --oneline --graph --all | fzf
```

---

## 3. Key Bindings (Enabled by `~/.fzf/install`)

These are the most common and useful key bindings that integrate `fzf` into your shell.

*   **`Ctrl+T` (fzf-tmux / fzf-completion):**
    *   **Description:** Pastes the selected file(s) and/or directory into the command line.
    *   **Usage:** Type a command like `vim`, then press `Ctrl+T` to select files to edit. You can also press `Ctrl+T` directly to search for a file and insert it.
    *   **Multi-selection:** Press `TAB` or `Shift+TAB` to select multiple items.

    ```bash
    # Example:
    vim <Ctrl+T> # Select files to open in vim
    mv <Ctrl+T> # Select files to move
    ```

*   **`Alt+C` (fzf-change-directory):**
    *   **Description:** Allows you to fuzzy search for a directory and `cd` into it.
    *   **Usage:** Press `Alt+C` to bring up a directory selector, choose a directory, and `cd` there.

    ```bash
    # Example:
    # Alt+C # Select a directory to change into
    ```

*   **`Ctrl+R` (fzf-history-widget):**
    *   **Description:** Fuzzy search through your command history.
    *   **Usage:** Type `Ctrl+R` at your prompt.

    ```bash
    # Example:
    # Ctrl+R # Search previous commands
    ```

*   **`**<TAB>` (Fuzzy Completion for Files/Directories):**
    *   **Description:** Expands `**` into a fuzzy search for files/directories. This is what was in your original cheatsheet.
    *   **Usage:**
        ```bash
        # Files under the current directory (single-selection by default)
        vim **<TAB>

        # Files under parent directory
        vim ../**<TAB>

        # Files under parent directory that match `fzf`
        vim ../fzf**<TAB>

        # Files under your home directory
        vim ~/**<TAB>

        # Directories under current directory (single-selection)
        cd **<TAB>

        # Directories under ~/github that match `fzf`
        cd ~/github/fzf**<TAB>
        ```
    *   **Multi-selection:** You can enable multi-selection for file completion by configuring `FZF_COMPLETION_OPTS`.

---

## 4. Fuzzy Search Syntax

`fzf`'s query syntax is powerful and flexible:

*   **Exact match:** Start with `'` (single quote) to match exactly.
    ```
    'foo
    ```*   **Prefix match:** Start with `^` to match the beginning of a word.
    ```
    ^foo
    ```
*   **Suffix match:** End with `$` to match the end of a word.
    ```
    foo$
    ```
*   **AND operator:** Use space to match multiple terms (all must be present, order doesn't matter).
    ```
    foo bar
    ```
*   **OR operator:** Use `|` to match either term.
    ```
    foo|bar
    ```
*   **Inverse match:** Start with `!` to exclude items containing the term.
    ```
    !foo
    ```
*   **Literal match:** Use `"` (double quote) to match a string literally, ignoring special characters.
    ```
    "foo-bar"
    ```

---

## 5. Configuration and Customization

`fzf` is highly customizable via environment variables and command-line options.

### General Options (`FZF_DEFAULT_OPTS`)

Set default options in your shell's configuration file (`.bashrc`, `.zshrc`, etc.).

```bash
# Example .bashrc / .zshrc entry:
export FZF_DEFAULT_OPTS="--height 40% --layout=reverse --border --margin=1 --padding=1 --preview 'bat --color=always {} || head -100 {}' --multi --cycle --info=inline --color=bg+:#313244,bg:#1e1e2e,fg:#cdd6f4,fg+:#cdd6f4,hl:#f38ba8,hl+:#f5c2e7,pointer:#f5e0dc,prompt:#89b4fa,spinner:#f5e0dc,header:#89b4fa,border:#585b70,marker:#f38ba8,query:#a6e3a1"
```

**Common Options:**

*   `--height PERCENT`: Height of the `fzf` window.
*   `--layout=reverse`: Display results from bottom to top.
*   `--multi`: Enable multi-selection by default (`TAB` to select/deselect).
*   `--bind KEY:ACTION`: Bind custom key actions.
    *   `--bind 'alt-j:down,alt-k:up'`: Use `Alt+J/K` for navigation.
    *   `--bind 'alt-a:select-all'`: Select all visible items.
    *   `--bind 'alt-d:deselect-all'`: Deselect all items.
    *   `--bind 'alt-z:toggle-all'`: Toggle selection of all visible items.
*   `--preview 'COMMAND {}'`: Show a preview of the selected item using `COMMAND`.
    *   `bat` is excellent for syntax-highlighted previews (install `batcat` if `bat` isn't found).
    *   `head -100 {}` is a fallback for text files.
    *   `file {}` can show file type.
*   `--border`: Add a border around the window.
*   `--margin`, `--padding`: Control spacing.
*   `--cycle`: Enable cycling through results (go from last to first, and vice-versa).
*   `--info=inline`: Display info message (N/M) inline.
*   `--color=...`: Customize colors.

### Example: Preview with `bat` (requires `bat` installed)

```bash
# Preview file content with syntax highlighting (if bat is installed)
fzf --preview 'bat --color=always --line-range :500 {}'
```

### Example: Preview for directories (show content)

```bash
fzf --preview 'ls -l {}'
```

### Fuzzy `cd` with `zoxide` (highly recommended)

If you use `zoxide` (a smarter `cd` command), you can integrate `fzf` for super-fast directory jumping.

```bash
# Add to your .bashrc / .zshrc:
export FZF_CD_COMMAND="zoxide query -l" # Use zoxide for directory history
export FZF_CTRL_T_COMMAND="fd --type f --hidden --exclude .git" # Improve Ctrl+T search
export FZF_ALT_C_COMMAND="fd --type d --hidden --exclude .git" # Improve Alt+C search
```

---

## 6. Custom Functions and Aliases

`fzf` is a building block for powerful custom commands.

### `ff` (Fuzzy Find Files and Edit)

A simple function to find files and open them in your editor (`vim` or `nvim`).

```bash
ff() {
  local files
  files=$(fzf -m --preview "bat --color=always {}")
  if [[ -n "$files" ]]; then
    ${EDITOR:-nvim} $files
  fi
}
```
*Add this to your `.bashrc` or `.zshrc`.*

### `fh` (Fuzzy Find History - alternative to Ctrl+R)

A custom function for history search, perhaps with a different `fzf` configuration.

```bash
fh() {
  eval $(history | fzf --no-sort +s --tac | sed 's/ *[0-9]* *//')
}
```

### `fkill` (Fuzzy Kill Process)

Use `fzf` to select and kill running processes.

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

---

## 7. Troubleshooting & Tips

*   **Ensure `fzf` is in your `PATH`**: The `install` script usually handles this, but verify `which fzf` works.
*   **Source your shell config**: After making changes to `.bashrc` or `.zshrc`, run `source ~/.bashrc` (or `.zshrc`) or open a new terminal.
*   **Check Key Binding Setup**: The `install` script adds lines like `[ -f ~/.fzf.bash ] && source ~/.fzf.bash`. Ensure these are present in your shell config.
*   **Performance**: For very large directories, consider using `fd` (or `rg`) instead of `find` as the source for `fzf`.
*   **`FZF_DEFAULT_COMMAND`**: You can set a default command `fzf` will use when run without piping input (e.g., `export FZF_DEFAULT_COMMAND='rg --files --no-messages'`).
```
