````markdown
# Git Commands Reference

A practical collection of Git commands for daily use — from initialization to branching, merging, tagging, and collaboration.

---

## Basic Setup

```bash
# Move into your project folder
cd Webprojects

# Clone an existing repository
git clone https://github.com/your-username/your-repo.git

# Initialize a new Git repository (for new/untracked projects)
git init

# Add a remote repository (only needed once per project)
git remote add origin https://github.com/your-username/your-repo.git

# Check your current repository status
git status

# List files/folders in the directory
dir

# Clear terminal screen
clear
````

---

## FILE CREATION AND COPYING COMMANDS

```bash
# Create a new empty file
touch filename.txt

# Or create a file and open it in a text editor (e.g., VS Code)
code filename.txt

# Create a new folder
mkdir new-folder

# Copy a file to another directory
cp filename.txt new-folder/

# Copy a folder and its contents recursively
cp -r source-folder/ destination-folder/

# Copy a file and rename it during the copy
cp filename.txt copy-filename.txt

# Verify the file was copied
dir new-folder
````

---

## Staging, Committing, and Pushing

```bash
# Add all changes to the staging area
git add .

# Commit changes with a message
git commit -m "Your commit message"

# Push changes to the remote repository (main branch)
git push origin main

# Pull the latest changes from the remote repository
git pull origin main
```

---

## Navigating and Running PHP Files

```bash
# Change directory into a project folder
cd folder_name

# Run a PHP file in the terminal
php file_name.php

# Example: run PHP file with arguments
php exceptions.php storage/seeds/articles.input.json

# Go back one directory
cd ..
```

---

## Branching

```bash
# Create a new branch
git branch <branch-name>

# Create and switch to a new branch immediately
git checkout -b <new-branch-name>

# Switch between branches
git checkout <branch-name>

# List all local branches
git branch

# List remote branches
git branch -r

# List all branches (local + remote)
git branch -a
```

---

## Working with Branches

```bash
# Add and commit your feature work
git add .
git commit -m "Work on new feature"

# Push the new branch to GitHub
git push origin new-feature

# Set upstream if pushing a new branch for the first time
git push --set-upstream origin feature/realisation-portfolio
```

---

## Merging Branches

```bash
# Switch to main branch
git checkout main

# Make sure main is up to date
git pull origin main

# Merge your feature branch into main
git merge new-feature

# Push merged changes
git push origin main
```

---

## Creating Pull Requests (PRs)

### Via GitHub (Recommended)

1. Push your branch to GitHub:

   ```bash
   git push origin <branch-name>
   ```
2. On GitHub, click **Compare & pull request**.
3. Add a title, description, and select the base branch (usually `main`).
4. Submit the Pull Request.

### Via Command Line (using GitHub CLI)

If you have [GitHub CLI](https://cli.github.com/) installed:

```bash
# Create a pull request
gh pr create --base main --head <branch-name> --title "Feature: new feature" --body "Description of changes"

# View pull requests
gh pr list

# View details of a specific pull request
gh pr view <PR-number>

# Merge a pull request
gh pr merge <PR-number> --merge
# or use --squash or --rebase for other merge types
```

---

## Closing Issues Automatically

You can automatically close issues when merging a pull request or committing by referencing the issue number.

```bash
# Example commit that automatically closes an issue when merged
git commit -m "Fix login bug. Closes #12"

# Other accepted keywords (GitHub recognizes these):
# close, closes, closed
# fix, fixes, fixed
# resolve, resolves, resolved

# Example: Close multiple issues at once
git commit -m "Add authentication middleware. Fixes #12, resolves #15"

# Example: Reference an issue without closing it (useful for ongoing work)
git commit -m "Work on user profile settings (refs #22)"

# You can also include these in a Pull Request description:
# "This PR fixes #45 and resolves #47"
# When merged into the default branch (main/master), those issues will close automatically.

# Recommended branch naming for issue-related work
# feature/<issue-number>-<short-description>
# fix/<issue-number>-<short-description>

# Example:
git checkout -b feature/34-add-password-reset
```

> When the branch containing this commit is merged into `main`, GitHub automatically closes issue **#12**.

---

## Tags

```bash
# Create a tag for the current commit
git tag v1.0

# Push the tag to the remote repository
git push origin v1.0
```

---

## Undoing & Cleaning Up

```bash
# Unstage a file (keep changes)
git restore --staged <filename>

# Discard local changes (warning: cannot be undone)
git restore <filename>

# Undo last commit but keep changes staged
git reset --soft HEAD~1

# Undo last commit and unstage changes
git reset --mixed HEAD~1

# Delete a local branch
git branch -d <branch-name>

# Delete a remote branch
git push origin --delete <branch-name>
```

---

## Helpful Info & Diagnostics

```bash
# Show commit history
git log --oneline --graph --decorate --all

# Show remote repositories
git remote -v

# Show the last commit on each branch
git branch -vv

# Check current branch
git symbolic-ref --short HEAD
```

---

## Resolving Merge Conflicts

1. Open conflicted files (Git marks conflicts with `<<<<<<<`, `=======`, `>>>>>>>`).
2. Manually edit to keep or merge content.
3. Stage fixed files:

   ```bash
   git add .
   ```
4. Commit the merge:

   ```bash
   git commit -m "Resolve merge conflicts"
   ```

---

## Example Workflow

```bash
# Start a new project
cd C:\Webprojects\Rappel-PHP-POO
git init
git remote add origin https://github.com/your-username/your-repo.git
git add .
git commit -m "Initial commit"
git push -u origin main

# Create a new feature branch
git checkout -b feature/user-auth

# Work, commit, push
git add .
git commit -m "Add login system"
git push origin feature/user-auth

# Create a pull request (CLI)
gh pr create --base main --head feature/user-auth --title "Add user authentication" --body "Implements login/logout features."

# Merge pull request
gh pr merge <PR-number> --merge

# Merge manually if needed
git checkout main
git pull origin main
git merge feature/user-auth
git push origin main
```

---

## Notes

* `main` may be `master` in older repositories.
* Use meaningful commit messages.
* Always `git pull` before starting new work.
* Use `.gitignore` to exclude files (e.g., `.env`, `node_modules`, etc.).
* Reference issue numbers in commits or PRs to close them automatically.
* Use branches for features, fixes, and experiments.

---

