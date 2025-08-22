# Python Environment & Tooling Cheat-Sheet

This cheat-sheet focuses on essential commands for managing Python environments, building projects, and updating core tools like `pip` and `Poetry`.

---

## 1. Building Python Projects without `make`

This sequence of commands details how to set up a Python virtual environment, install dependencies, and build/run a project that might typically use a `Makefile` but instead uses Python scripts for these tasks.

```python
cd <dupeGuru directory>
python3 -m venv --system-site-packages ./env
source ./env/bin/activate
pip install -r requirements.txt
python build.py
python run.py
```
*   **`cd <dupeGuru directory>`**: Navigates into the project's root directory. Replace `<dupeGuru directory>` with your actual project path.
*   **`python3 -m venv --system-site-packages ./env`**: Creates a virtual environment named `env` in the current directory. `--system-site-packages` allows the virtual environment to access packages installed globally (useful if the project relies on system-wide libraries).
*   **`source ./env/bin/activate`**: Activates the newly created virtual environment. This changes your shell's `PATH` so that `python` and `pip` commands point to the virtual environment's executables.
*   **`pip install -r requirements.txt`**: Installs all project dependencies listed in `requirements.txt` into the active virtual environment.
*   **`python build.py`**: Executes a Python script named `build.py`, which presumably handles the project's build process.
*   **`python run.py`**: Executes a Python script named `run.py`, which presumably runs the main application.

---

## 2. Confirm Python Syntax

This command checks the syntax of a Python file without executing it.

```python
python -m py_compile vacuum.py
```
*   **`python -m py_compile vacuum.py`**: Compiles the `vacuum.py` script into bytecode. If there are any syntax errors in `vacuum.py`, this command will report them. If successful, it typically creates a `__pycache__` directory containing the compiled `.pyc` file.

---

## 3. Poetry (Python Dependency Management)

**Poetry** is a dependency management and packaging tool for Python. It aims to simplify package installation, virtual environment management, and project publishing.

### 3.1 Official Poetry Installation

This method uses a `curl` pipe to a Python script to install Poetry system-wide.

```bash
curl -sSL https://install.python-poetry.org | python3 -
export PATH="$HOME/.local/bin:$PATH"
poetry --version
```
*   **`curl -sSL https://install.python-poetry.org | python3 -`**: Downloads the official Poetry installation script and pipes it directly to `python3` for execution. `-sSL` makes `curl` silent, follow redirects, and show errors.
*   **`export PATH="$HOME/.local/bin:$PATH"`**: Adds Poetry's installation directory (`~/.local/bin`) to your system's `PATH` environment variable, making the `poetry` command accessible from any directory. This line should typically be added to your shell's configuration file (e.g., `~/.bashrc`, `~/.zshrc`).
*   **`poetry --version`**: Verifies that Poetry has been installed correctly and displays its version number.

### 3.2 Poetry Project Initialization Workflow

This sequence outlines the typical steps to start and manage a new Python project using Poetry.

```bash
cd /project/directory
poetry init
poetry add dependencies
poetry shell
python3 yourapp.py
```
*   **`cd /project/directory`**: Navigates into your project's root directory. Create this directory first if it doesn't exist.
*   **`poetry init`**: Interactively initializes a new Poetry project in the current directory, creating a `pyproject.toml` file to manage project metadata and dependencies.
*   **`poetry add dependencies`**: Adds specified dependencies to your project (e.g., `poetry add requests`). Poetry automatically creates and manages a virtual environment for your project.
*   **`poetry shell`**: Activates the virtual environment managed by Poetry for your project. Your shell prompt will usually change to indicate the active environment.
*   **`python3 yourapp.py`**: Executes your Python application within the activated Poetry virtual environment.

---

## 4. Updating Python `pip` and Installed Packages

`pip` is the standard package installer for Python. Keeping it and other core tools updated is important. This section provides several methods for updating `pip`, `setuptools`, `wheel`, and all outdated packages.

### 4.1 General Setup / Update `pip`, `setuptools`, `wheel`

This is the most common way to ensure your core Python packaging tools are up-to-date.

```python
python3 -m pip install --upgrade pip setuptools wheel
```
*   **`python3 -m pip install --upgrade pip setuptools wheel`**: Upgrades `pip` itself, `setuptools` (which handles packaging tasks), and `wheel` (a standard for Python distributions) to their latest versions. It's recommended to run this periodically.

### 4.2 Safe Way: Update All Outdated Packages

This method updates all installed packages that are outdated, after identifying them with `pip list outdated`. It avoids updating `pip`, `setuptools`, and `wheel` with the main update command, as these are often updated separately as in 4.1.

```python
python3 -m pip install -U $(python3 -m pip list outdated 2> /dev/null | grep -v 'Version' | grep -v '\-\-\-\-\-\-' | awk '{printf $1 " " }' && echo)
```
*   **`python3 -m pip list outdated 2> /dev/null`**: Lists all packages that have a newer version available. `2> /dev/null` suppresses any error messages.
*   **`grep -v 'Version' | grep -v '\-\-\-\-\-\-'`**: Filters out the header lines from `pip list outdated` output.
*   **`awk '{printf $1 " " }' && echo`**: Extracts just the package names (first column) from the filtered list, formats them as space-separated strings, and adds a final newline.
*   **`python3 -m pip install -U ...`**: Takes the list of outdated package names and upgrades (`-U`) them.

### 4.3 Overwrite Method: Force Reinstall All Outdated Packages

This method is similar to the "safe way" but uses flags to force a reinstall and overwrite existing files, which can be useful for resolving corrupted installations.

```python
python3 -m pip install --exists-action w --force-reinstall -U $(python3 -m pip list outdated 2> /dev/null | grep -v 'Version' | grep -v '\-\-\-\-\-\-' | awk '{printf $1 " " }' && echo)
```
*   **`--exists-action w`**: Specifies that if a file already exists, it should be overwritten without prompting.
*   **`--force-reinstall`**: Forces reinstallation of the package even if it's already up-to-date.
*   The rest of the command is the same as in Section 4.2, identifying and upgrading all outdated packages. Use with caution, especially with system-installed packages.

### 4.4 Pythonic Method: Update All Outdated Packages Programmatically

This approach uses a Python script to identify and update packages, offering more control and error handling than shell piping.

```python
import subprocess
outdated_packages = subprocess.check_output(['python3', '-m', 'pip', 'list', 'outdated'],
stderr=subprocess.DEVNULL).decode().splitlines()
for package in outdated_packages:
    package_name = package.split()[0]
    subprocess.call(['python3', '-m', 'pip', 'install', '--break-system-packages', '-U', package_name])
```
*   **`import subprocess`**: Imports the `subprocess` module for running external commands.
*   **`subprocess.check_output(...)`**: Executes `pip list outdated`, captures its output, and handles errors. `stderr=subprocess.DEVNULL` silences errors. `.decode().splitlines()` processes the output into a list of strings.
*   **`for package in outdated_packages:`**: Iterates through each line representing an outdated package.
*   **`package_name = package.split()[0]`**: Extracts the package name from the line.
*   **`subprocess.call(['python3', '-m', 'pip', 'install', '--break-system-packages', '-U', package_name])`**: Calls `pip install` to upgrade the individual package. `--break-system-packages` is a new flag in modern `pip` versions, often required when installing/updating globally to bypass protection for system-managed Python environments (use with caution).

---

## 5. Install `pip` with Python

This command ensures that `pip` itself is installed or upgraded within a Python environment.

```python
python -m ensurepip --upgrade
```
*   **`python -m ensurepip --upgrade`**: The `ensurepip` module is a built-in module that can install `pip` into a Python environment. `--upgrade` ensures that if `pip` is already present, it will be upgraded to the latest bundled version.
