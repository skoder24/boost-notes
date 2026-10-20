# Git and GitHub command guide

This guide explains practical Git and GitHub commands through one small project, `git-command-playground`. It focuses on commands you are likely to use while learning and working on a project; Git has many more specialized commands, so this is not an exhaustive list.

Git is a version control tool that records changes to files on your computer. GitHub is a website that hosts Git repositories and adds collaboration features such as pull requests and issues. You can use Git without GitHub, and GitHub uses Git to manage repository history.

## The basic Git model

Think of Git as keeping track of three places: your working tree is the files you are editing, the staging area is the set of changes you have selected for the next save, and the repository history is the commits you have saved. A remote is another copy of the repository, often hosted on GitHub.

The usual cycle is: edit a file, inspect it with `git status` and `git diff`, stage the changes with `git add`, save a snapshot with `git commit`, and share commits with `git push`.

## Prepare Git

Run these commands in a terminal on Linux, macOS, or Ubuntu in WSL. Install Git and the GitHub CLI (`gh`) first if they are not already available; check your platform's official installation instructions.

Set the name and email that Git should record on commits in this repository. Replace the example values with the name and email you want shown in its history; these settings do not sign you in to GitHub.

```sh
git config user.name "Your Name"
git config user.email "you@example.com"
git config --get user.name
git config --get user.email
```

These settings apply only to the current repository. Use `git config --global` only if you want the same identity used by default in all your repositories.

## Build the example project

The example is a tiny learning journal for commands you have tried. Each section below adds a realistic Git task to that same project.

### Create the repository and first commit

```sh
mkdir git-command-playground
cd git-command-playground
git init -b main
git status
```

`mkdir` creates the project folder, `cd` moves into it, and `git init -b main` starts a repository whose first branch is named `main`. `git status` tells you the current branch and whether files have changed.

Create a README for the project:

```sh
cat > README.md <<'EOF'
# Git command playground

A small learning journal for practicing Git and GitHub.
EOF
git status
git diff
git add README.md
git status
git diff --staged
git commit -m "Add project README"
```

`git diff` shows unstaged edits; `git add README.md` stages that file; `git diff --staged` reviews what is staged; and `git commit` records the staged snapshot. A commit message should briefly say what the change accomplishes.

Check the saved history and latest commit:

```sh
git log --oneline
git show
```

`git log` lists commits, and `git show` displays the latest commit and its changes. Add `--graph --decorate --all` to `git log --oneline` when you want a compact view of branches and history.

### Make a feature branch

A branch gives you a separate line of work so you can make changes without immediately changing `main`.

```sh
git switch -c add-command-notes
git branch
```

`git switch -c` creates a branch and switches to it. `git branch` lists local branches; the asterisk marks the branch you are on.

Add a command journal:

```sh
cat > commands.md <<'EOF'
# Commands I have tried

## git status

Shows the current branch and whether files are changed, staged, or untracked.
EOF
git status
git add commands.md
git commit -m "Add a command journal"
```

To switch back to the main branch, use `git switch main`. Switch back to your feature with `git switch add-command-notes`.

### Compare changes and inspect history

Make a small change to `commands.md`, then inspect it before saving:

```sh
printf '\n## git diff\n\nShows changes that are not staged yet.\n' >> commands.md
git diff
git add commands.md
git diff --staged
git commit -m "Explain git diff"
git log --oneline --decorate
```

`git diff` compares your working files with the staging area, while `git diff --staged` compares the staged changes with the latest commit. You can compare branches with `git diff main..add-command-notes`.

### Rename, remove, and find files

Git can track file operations as changes:

```sh
git mv commands.md git-commands.md
git status
git commit -m "Rename command journal"
```

`git mv` renames a tracked file and stages that change. To remove a file from the project and stage its removal, use `git rm <file>`; check `git status` before committing because the file is removed from the working tree too.

Search tracked files for text with `git grep "git diff"`. Inspect who last changed each line of a file with `git blame git-commands.md`; treat blame as a history aid, not proof of who is responsible for a problem.

### Save work temporarily with a stash

A stash temporarily shelves uncommitted tracked changes so you can switch tasks. Use it when you need to pause work, not as a replacement for commits.

```sh
printf '\nA note I have not finished yet.\n' >> git-commands.md
git stash push -m "unfinished journal note"
git status
git stash list
git stash pop
git status
git add git-commands.md
git commit -m "Save the unfinished journal note"
```

`git stash push` stores the current tracked edits, `git stash list` shows saved stashes, and `git stash pop` reapplies the most recent stash and removes it from the list. If you want to reapply a stash but keep it in the list, use `git stash apply`.

### Merge the feature into main

First, make sure the feature branch's changes are committed. Then switch to `main` and merge the feature branch:

```sh
git status
git switch main
git merge add-command-notes
git log --oneline --graph --decorate
```

`git merge` brings the commits from another branch into the current branch. If Git reports a conflict, open each file it names, decide what the final content should be, remove conflict markers (`<<<<<<<`, `=======`, and `>>>>>>>`), then stage the resolved file and commit the merge.

After a successful merge, the branch can be deleted because its changes are now part of `main`:

```sh
git branch -d add-command-notes
```

The `-d` option refuses to delete a branch that Git believes has unmerged work. Do not use force-delete options to bypass that protection until you understand what would be lost.

### Add a tag for a milestone

A tag is a name for a specific commit, often used to mark a release or learning milestone:

```sh
git tag -a v1.0 -m "First learning journal milestone"
git tag
git show v1.0
```

An annotated tag stores a message and metadata. To remove a tag you created by mistake, `git tag -d v1.0` removes the local tag; it does not delete the commit.

## Connect the project to GitHub

The GitHub CLI can create the remote repository and publish your current branch. Sign in interactively first; do not put passwords or access tokens directly in commands or notes.

```sh
gh auth login
gh auth status
gh repo create git-command-playground --private --source=. --remote=origin --push
git remote -v
git push origin v1.0
```

`gh auth login` guides you through authentication, and `gh auth status` shows whether you are signed in. `gh repo create` creates a private GitHub repository, names the remote `origin`, and pushes the current branch. Choose `--public` instead of `--private` only if you intend to make the project public.

`git remote -v` lists the remote names and URLs. `origin` is the conventional name for the main hosted copy, but it is only a label; a repository can have multiple remotes. Git does not include tags when you push a branch, so `git push origin v1.0` publishes this tag separately.

Push a later local commit and set its upstream branch the first time:

```sh
git push -u origin main
```

`git push` uploads local commits to a remote. The `-u` option records which remote branch this local branch tracks, so later you can usually use `git push` and `git pull` without specifying the remote and branch.

### Fetch and pull remote changes

`git fetch origin` downloads information about remote branches and commits without changing your current files or branch. After fetching, inspect the situation with `git status -sb` or `git log --oneline --graph --decorate --all`.

`git pull --ff-only` fetches and integrates the upstream changes only when Git can move your branch forward without creating a merge commit. If it cannot fast-forward, stop and inspect the branch history instead of forcing the update.

To make a separate local copy of a GitHub repository, use `gh repo clone OWNER/REPOSITORY` or `git clone <repository-url>`. `git clone` creates a new folder containing the project and its history; run `cd <repository-folder>` before using Git commands inside it.

## Try a GitHub pull request

A pull request (PR) is a GitHub discussion and review around proposed changes. It is useful when working with others, and also gives a learner a chance to review their own change before merging.

Create a new branch, make and commit a small edit, then publish that branch:

```sh
git switch -c improve-project-description
printf '\nThis project is a safe place to practice version control.\n' >> README.md
git diff
git add README.md
git commit -m "Clarify project purpose"
git push -u origin improve-project-description
gh pr create --fill
```

`gh pr create --fill` opens a pull request using the branch's commit title and body as a starting point. Review the title, description, changes, and target branch in GitHub before creating or merging a PR.

Useful GitHub CLI commands include:

```sh
gh repo view --web
gh pr status
gh pr view
gh issue create
```

`gh repo view --web` opens the repository on GitHub. `gh pr status` summarizes your pull requests, and `gh pr view` shows the current PR. `gh issue create` guides you through creating a project issue. You can also do these tasks on the GitHub website.

After the PR is merged on GitHub, update your local `main` branch:

```sh
git switch main
git pull --ff-only
```

## Undo mistakes carefully

Git has several ways to undo work, and the right command depends on whether your change is unstaged, staged, or committed. Check `git status` first.

- To unstage a file but keep its edits, run `git restore --staged <file>`.
- To discard unstaged edits to a file and return it to the last committed version, run `git restore <file>`. **This discards those edits.**
- To undo a commit that has already been shared, use `git revert <commit-id>`; it creates a new commit that reverses the selected commit.
- Avoid `git reset --hard` and `git clean -f` while learning: they can permanently discard local work or untracked files. Do not rewrite published history unless you know how it affects everyone using the branch.

## Quick reference

| Command | What it does |
| --- | --- |
| `git init -b main` | Start a repository with a `main` branch. |
| `git clone <url>` | Copy a remote repository onto your computer. |
| `git status` | Show the branch and file changes. |
| `git diff` / `git diff --staged` | Review unstaged / staged changes. |
| `git add <file>` | Stage a file's changes for the next commit. |
| `git commit -m "message"` | Save staged changes as a commit. |
| `git log --oneline` / `git show` | Inspect commit history / a commit. |
| `git switch -c <branch>` | Create a branch and switch to it. |
| `git merge <branch>` | Merge a branch into the current branch. |
| `git fetch` / `git pull` / `git push` | Download remote information / integrate updates / upload commits. |
| `git remote -v` | List configured remotes. |
| `git stash` | Temporarily shelve uncommitted tracked changes. |
| `git tag` | List or create names for specific commits. |
| `gh repo create` / `gh repo clone` | Create or clone a GitHub repository. |
| `gh pr create` / `gh issue create` | Open a pull request or issue on GitHub. |

## Keep practicing

Try changing the journal to explain each command in your own words. Before a commit, check `git status`, review the relevant `git diff`, stage only the files you intend to save, and read your commit message before sharing it.

For the full command reference, use the official [Git documentation](https://git-scm.com/docs) and [GitHub CLI manual](https://cli.github.com/manual/). For GitHub's collaboration workflow, see [About pull requests](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests).
