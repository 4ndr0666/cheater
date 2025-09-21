# Advanced Git & GitHub Cheat Sheet

A personal collection of commands and workflows for maintaining a pristine and efficient Git history.

### **Table of Contents**
1. [The Core Contribution Workflow](#-the-core-contribution-workflow)
2. [Daily Workflow & Stashing](#-daily-workflow--stashing)
3. [History Rewriting (Dangerous Operations)](#-history-rewriting-dangerous-operations)
4. [Recovering From Mistakes](#-recovering-from-mistakes)
5. [Advanced Inspection & Searching](#-advanced-inspection--searching)
6. [GitHub CLI & Automation](#-github-cli--automation)
7. [Troubleshooting & Special Cases](#-troubleshooting--special-cases)

---

## 🚀 The Core Contribution Workflow

This is the standard, pristine process for contributing to any open-source project.

### **Phase 1: One-Time Setup (Per Project)**

1.  **Fork the Repository:**
    *   On the original project's GitHub page, click the **"Fork"** button.

2.  **Clone Your Fork Locally:**
    *   This downloads your personal copy.
    ```bash
    git clone https://github.com/<your-username>/<project-name>.git
    cd <project-name>
    ```

3.  **Add the Original Repository as "Upstream":**
    *   This is crucial for keeping your fork synced with the original project.
    ```bash
    git remote add upstream https://github.com/<original-owner>/<project-name>.git
    ```

### **Phase 2: The Contribution Cycle (For Every Change)**

1.  **Sync Your Fork:**
    *   Before starting new work, ensure your primary branch (`main` or `dev`) matches the upstream project.
    ```bash
    git checkout main
    git pull upstream main
    git push origin main
    ```

2.  **Create a Feature Branch:**
    *   Never work directly on `main`. Create a new branch with a descriptive name.
    ```bash
    git checkout -b feat/add-user-authentication
    ```

3.  **Make & Commit Changes:**
    *   Commit your changes with a clear, conventional message.
    ```bash
    git add .
    git commit -m "feat(auth): implement user login endpoint"
    ```

4.  **Push to Your Fork:**
    *   The `-u` flag sets the upstream for this branch, so subsequent pushes can just be `git push`.
    ```bash
    git push -u origin feat/add-user-authentication
    ```

5.  **Open the Pull Request:**
    *   Go to your fork's repository on GitHub.
    *   Click the **"Compare & pull request"** button.
    *   Ensure the base repository is the original project and the head repository is your fork.
    *   Write a clear title and description.

6.  **Clean Up After Merging:**
    *   **After** the project owner merges your PR, sync your local `main` again.
    ```bash
    git checkout main
    git pull upstream main
    ```
    *   Then, delete the local and remote feature branches.
    ```bash
    git branch -d feat/add-user-authentication
    git push origin --delete feat/add-user-authentication
    ```

---

## 日常 Daily Workflow & Stashing

Commands for everyday use and managing local changes.

### **Pulling Updates with Local Changes (The Safe Way)**

1.  **Stash Your Work:**
    *   Saves all changes (tracked, untracked, and staged) in a temporary, safe place.
    ```shell
    git stash push -u -m "WIP: Refactoring the user service"
    ```

2.  **Pull and Rebase:**
    *   Fetches remote changes and places your local commits cleanly on top.
    ```shell
    git pull --rebase
    ```

3.  **Re-apply Your Stash:**
    *   Restores your work exactly as it was.
    ```shell
    git stash pop --index
    ```

### **Useful Git Configs**

```shell
# Always include untracked files in `git stash`
git config --global stash.saveIncludeUntracked true

# Automatically stash and re-apply changes during a rebase/pull
git config --global rebase.autoStash true
```

---

## ⚠️ History Rewriting (Dangerous Operations)

**WARNING:** These commands rewrite public history. Use with extreme caution and communicate with your team before using them on shared branches.

### **Interactive Rebase (`git rebase -i`)**
Use this to edit, squash, or reword commits on your feature branch *before* creating a pull request.

1.  **Start the Interactive Rebase:**
    *   This opens an editor with a list of commits since your branch diverged from `main`.
    ```bash
    git rebase -i main
    ```

2.  **Edit the Commit List:**
    *   In the editor, change `pick` to `squash` (to combine), `reword` (to change the message), or `edit` (to change content).

3.  **Force-Push the Cleaned Branch:**
    *   Because you've rewritten history, you must force-push to your feature branch.
    ```bash
    git push origin your-feature-branch --force
    ```

### **Removing Large/Sensitive Files with BFG Repo-Cleaner**
BFG is the best tool for removing unwanted data from your entire repository history.

1.  **Prepare:** Clone a fresh, bare copy of your repository.
    ```bash
    git clone --bare https://github.com/owner/project.git
    cd project.git
    ```
2.  **Run BFG:** (Download `bfg.jar` first)
    ```bash
    java -jar /path/to/bfg.jar --delete-files 'credentials.json'
    ```
3.  **Clean and Force-Push:** **This is the most dangerous step.**
    ```bash
    git reflog expire --expire=now --all
    git gc --prune=now --aggressive
    git push origin --force --all
    git push origin --force --tags
    ```

---

## 🚑 Recovering From Mistakes

A step-by-step guide to fix common "oh no" moments.

### **The "I Accidentally Deleted Everything" Playbook**

You've accidentally deleted files, committed the deletion, and pushed it. Your branch is now pointing to a bad snapshot. This guide will help you find the last good snapshot and restore it.

**The Strategy:** `reflog` -> `reset` -> `force push`

1.  **Find Your Last Known Good State with `git reflog`**
    *   **What it is:** The "reference log" is your personal safety net. It records almost every action you take (commits, checkouts, resets).
    *   **Action:** Run it immediately when you realize you're lost.
    ```bash
    git reflog
    ```
    *   **Goal:** Scan the output for the commit message right before things went wrong. Look for the commit hash (the 7-character code on the left, e.g., `a92b46d`). A good commit message here is a lifesaver.

2.  **(Optional but Recommended) Verify with `git checkout`**
    *   **What it is:** A way to time-travel to any commit to inspect it without making permanent changes.
    *   **Action:**
    ```bash
    # Replace <hash> with the promising commit hash you found
    git checkout <hash>
    ```
    *   **Goal:** Look at your files. Do they look right? If yes, you've found your recovery point. Return to your broken branch (`git checkout your-branch-name`) before the next step.

3.  **Reset Your Branch with `git reset --hard`**
    *   **What it is:** The command that fixes your local branch. It moves your branch's pointer to a specific commit and forcefully resets all your files to match that commit.
    *   **Action:**
    ```bash
    # Make sure you are on the branch you want to fix!
    git checkout your-broken-branch-name

    # This rewrites your local branch's history
    git reset --hard <the-good-commit-hash>
    ```
    *   **Goal:** Your local branch is now fixed and points to the correct, working snapshot.

4.  **Update the Remote with `git push --force`**
    *   **What it is:** A command to make the remote branch match your newly fixed local branch.
    *   **Action:**
    ```bash
    git push origin your-broken-branch-name --force
    ```
    *   **Goal:** Overwrite the bad history on the remote repository. Use this with care, but it's the correct tool when you are fixing your own mistake on a branch you control.

### **Restoring a Single Deleted File**

1.  **Find the commit where the deletion happened:**
    ```bash
    git log --diff-filter=D --summary
    ```
2.  **Find the parent commit** (the one *before* the deletion). Copy its hash.
3.  **Checkout the deleted file from that parent commit:**
    ```bash
    # git checkout <parent-commit-hash> -- <path/to/deleted/file>
    git checkout a1b2c3d4 -- src/important-file.js
    ```
4.  Commit the restored file.

---

## 🔍 Advanced Inspection & Searching

### **Searching Through History**

```bash
# Search the entire repo's history for a string
git grep "my_secret_api_key" $(git rev-list --all)

# Find commits where a specific string was added or removed
git log -S "function_name" --source --all
```

### **Finding Large Files**
Quickly scan for blobs over a certain size (e.g., 90MB).

```bash
git rev-list --objects --all | \
  git cat-file --batch-check='%(objectname) %(objecttype) %(objectsize) %(rest)' | \
  awk '$3 > 90000000 {printf "%.2f MB\t%s\n", $3/1048576, $4}'
```

---

## 🤖 GitHub CLI & Automation

### **Automatically Setup Upstream Tracking**
Ensures `git push` works on the first try for new branches.

```shell
git config --global push.autoSetupRemote true
```

### **Manage GitHub Auth Scopes**
Refresh your `gh` token with additional permissions.
```bash
gh auth refresh -s workflow,read:gpg_key,admin:public_key
```

---

## 🔧 Troubleshooting & Special Cases

### **Bun Installation & Monorepo Issues**

Common fixes for projects using Bun, especially in monorepos.

*   **`illegal hardware instruction`:** Your Bun executable was compiled for a different CPU. Reinstall with `curl -fsSL https://bun.sh/install | bash`.
*   **`error: Workspace dependency not found`:** An internal package has an incorrect name in another package's `package.json`. Correct the name, then run a clean install: `rm -rf node_modules bun.lock && bun install`.

### **Git LFS (Large File Storage)**

```bash
# List all files currently tracked by LFS
git lfs ls-files

# Start tracking a new file type and add the attributes file
git lfs track "*.psd"
git add .gitattributes

# Clean up old, unreferenced LFS files locally
git lfs prune
```

### **Reconnect a Local Directory to a Remote Repo**

1.  **Initialize Git and save current work:**
    ```bash
    git init
    git add .
    git commit -m "Initial commit of existing files"
    ```
2.  **Add the remote and sync:**
    ```bash
    git remote add origin https://github.com/owner/project.git
    # Pull remote changes, allowing unrelated histories to merge
    git pull origin main --allow-unrelated-histories
    # Resolve any merge conflicts, then push
    git push -u origin main
    ```