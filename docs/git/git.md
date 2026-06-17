{[date]Last updated June 15, 2026}
# Git Overview
{[author]{pp::stepan-vana}Štěpán Váňa}
{[read_time]42 min read}
{[copy_raw_file]Copy Markdown}
{[view_as_md]View as Markdown}

Git is a distributed version control system designed to track changes in source code during software development. Created by Linus Torvalds in 2005, Git allows multiple developers to collaborate on a project efficiently, maintain a complete history of all changes, and revert to any previous state at any time.

## How Git Works

Git stores data as a series of **snapshots** of a filesystem, not as a list of file-based changes (deltas). Every time you commit, Git takes a picture of all your files and stores a reference to that snapshot. For efficiency, if files have not changed, Git doesn't store the file again — just a link to the previous identical file.

### The Three States

Every file in a Git repository exists in one of three states:

| State | Location | Description |
|---|---|---|
| **Modified** | Working Directory | File has been changed but not yet staged |
| **Staged** | Staging Area (Index) | File is marked to go into the next commit |
| **Committed** | Repository (`.git/`) | Data is safely stored in the local database |

### The `.git` Directory

The `.git` directory is the heart of a Git repository. It contains:

- `HEAD` — pointer to the current branch
- `config` — repository-specific configuration
- `objects/` — all content (blobs, trees, commits, tags) stored as compressed objects
- `refs/` — pointers to commit objects (branches, tags, remotes)
- `index` — the staging area (binary file)
- `logs/` — history of where `HEAD` and branch refs have been

---

## Configuration

### `git config`

Manages configuration values. Settings can be stored at three levels, each overriding the previous:

```bash
git config [--system | --global | --local | --worktree] <key> <value>
```

| Scope | File | Applies to |
|---|---|---|
| `--system` | `/etc/gitconfig` | All users on the system |
| `--global` | `~/.gitconfig` or `~/.config/git/config` | All repositories of the current user |
| `--local` | `.git/config` | Current repository only (default) |
| `--worktree` | `.git/config.worktree` | Current worktree only |

**Common configuration options:**

```bash
# Identity
git config --global user.name "Jan Novák"
git config --global user.email "jan@example.com"

# Default editor
git config --global core.editor "vim"
git config --global core.editor "code --wait"     # VS Code

# Default branch name
git config --global init.defaultBranch main

# Line ending handling
git config --global core.autocrlf true            # Windows: LF → CRLF on checkout
git config --global core.autocrlf input           # macOS/Linux: CRLF → LF on commit

# Color output
git config --global color.ui auto

# Diff and merge tool
git config --global diff.tool vimdiff
git config --global merge.tool vimdiff

# Credential storage
git config --global credential.helper store        # Store credentials in plain text
git config --global credential.helper cache        # Cache credentials in memory
git config --global credential.helper osxkeychain  # macOS Keychain

# Aliases
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.lg "log --oneline --graph --all"

# Push behavior
git config --global push.default current          # Push current branch to same-named remote branch
git config --global push.autoSetupRemote true     # Auto set upstream on first push

# Pull behavior
git config --global pull.rebase true              # Rebase instead of merge on pull

# GPG signing
git config --global user.signingkey <key-id>
git config --global commit.gpgSign true

# Prune on fetch
git config --global fetch.prune true

# Rerere (reuse recorded resolutions)
git config --global rerere.enabled true
```

**Listing and inspecting configuration:**

```bash
git config --list                          # List all config values
git config --list --show-origin            # Show values with their source file
git config --list --show-scope             # Show values with their scope
git config user.name                       # Get a specific value
git config --global --edit                 # Open global config in editor
git config --unset user.name              # Remove a key
git config --unset-all core.editor        # Remove all instances of a key
git config --remove-section alias         # Remove an entire section
```

---

## Initializing a Repository

### `git init`

Creates a new Git repository in the current directory (or in a specified path).

```bash
git init                      # Initialize in current directory
git init <directory>          # Initialize in a new directory
git init --bare               # Create a bare repository (no working directory)
git init --bare <directory>   # Create a named bare repository
git init -b main              # Initialize with 'main' as the default branch name
git init --initial-branch=main <dir>
git init --template=<dir>     # Use a custom template directory
git init --separate-git-dir=<dir>  # Store .git dir in a separate location
```

A **bare repository** has no working directory and is typically used as a remote/central repository that developers push to and pull from.

### `git clone`

Creates a local copy of a repository from a remote source.

```bash
git clone <url>                            # Clone into a directory named after the repo
git clone <url> <directory>                # Clone into a specific directory
git clone --depth 1 <url>                  # Shallow clone (only last commit)
git clone --depth <n> <url>               # Shallow clone with last n commits
git clone --branch <branch> <url>          # Clone a specific branch
git clone -b <branch> <url>               # Shorthand for --branch
git clone --single-branch <url>            # Clone only one branch
git clone --single-branch -b <branch> <url>
git clone --no-single-branch <url>         # Clone all branches (default)
git clone --bare <url>                     # Clone as a bare repository
git clone --mirror <url>                   # Mirror all refs (superset of --bare)
git clone --recursive <url>                # Clone and initialize submodules
git clone --recurse-submodules <url>       # Same as --recursive
git clone --shallow-submodules <url>       # Shallow clone submodules
git clone --filter=blob:none <url>         # Blobless clone (no file content until needed)
git clone --filter=tree:0 <url>            # Treeless clone
git clone -o <name> <url>                  # Set remote name (default: origin)
git clone --origin <name> <url>            # Same as -o
git clone --local <path>                   # Force local clone (uses hardlinks)
git clone --no-local <path>               # Disable local optimization
git clone --progress <url>                 # Always show progress
git clone --quiet <url>                    # Suppress output
git clone --verbose <url>                  # Be verbose
git clone -j <n> <url>                    # Number of parallel submodule fetches
```

**Supported URL protocols:**

```bash
git clone https://github.com/user/repo.git
git clone git@github.com:user/repo.git
git clone ssh://user@host/path/to/repo.git
git clone git://host/path/to/repo.git
git clone file:///path/to/repo.git
git clone /path/to/repo.git               # Local path (uses hardlinks)
```

---

## Staging and Committing

### `git add`

Adds file contents to the staging area (index) in preparation for a commit.

```bash
git add <file>                 # Stage a specific file
git add <file1> <file2>        # Stage multiple files
git add <directory>            # Stage all changes in a directory
git add .                      # Stage all changes in current directory and below
git add -A                     # Stage all changes (new, modified, deleted) in entire repo
git add --all                  # Same as -A
git add -u                     # Stage modifications and deletions (not new files)
git add --update               # Same as -u
git add -p                     # Interactively choose hunks to stage
git add --patch                # Same as -p
git add -i                     # Interactive mode (menu-driven)
git add --interactive          # Same as -i
git add -e                     # Open diff in editor to choose lines to stage
git add --edit                 # Same as -e
git add -n                     # Dry run (show what would be added without adding)
git add --dry-run              # Same as -n
git add -f <file>              # Force-add ignored files
git add --force <file>         # Same as -f
git add --chmod=+x <file>      # Stage with executable bit change
git add --chmod=-x <file>      # Stage with executable bit removal
git add --sparse               # Allow updating sparse-checkout entries
git add --renormalize          # Re-apply line ending normalization to tracked files
git add -N <file>              # Add file as "intent to add" (untracked → tracked, empty staging)
git add --intent-to-add <file> # Same as -N
```

### `git commit`

Records staged changes to the repository as a new commit.

```bash
git commit                          # Open editor to write commit message
git commit -m "message"             # Commit with inline message
git commit --message="message"      # Same as -m
git commit -a                       # Auto-stage all tracked modified/deleted files and commit
git commit --all                    # Same as -a
git commit -am "message"            # Combine -a and -m
git commit --amend                  # Modify the last commit (message or content)
git commit --amend -m "new msg"     # Amend last commit with new message
git commit --amend --no-edit        # Amend last commit, keep existing message
git commit --amend --reset-author   # Amend and reset author to configured identity
git commit -p                       # Interactively stage hunks and commit
git commit --patch                  # Same as -p
git commit -C <commit>              # Reuse message from another commit
git commit --reuse-message=<commit> # Same as -C
git commit -c <commit>              # Reuse message from another commit, open editor
git commit --reedit-message=<commit># Same as -c
git commit -F <file>                # Read commit message from file
git commit --file=<file>            # Same as -F
git commit -t <file>                # Open file as commit message template in editor
git commit --template=<file>        # Same as -t
git commit -e                       # Force editor even when message is provided
git commit --edit                   # Same as -e
git commit --no-edit                # Do not open editor
git commit --author="Name <e@mail>" # Override author
git commit --date="2024-01-01"      # Override author date
git commit -s                       # Add Signed-off-by line
git commit --signoff                # Same as -s
git commit -S                       # GPG-sign the commit
git commit --gpg-sign               # Same as -S
git commit --no-gpg-sign            # Override gpg sign setting
git commit --allow-empty            # Create commit even if nothing staged
git commit --allow-empty-message    # Allow empty commit message
git commit -q                       # Suppress output
git commit --quiet                  # Same as -q
git commit -v                       # Show diff in editor when writing message
git commit --verbose                # Same as -v
git commit --dry-run                # Show what would be committed without committing
git commit --squash=<commit>        # Create commit for use with rebase --autosquash
git commit --fixup=<commit>         # Create fixup commit for use with rebase --autosquash
git commit --fixup=reword:<commit>  # Fixup that only amends the message
git commit --fixup=amend:<commit>   # Fixup that amends content and message
git commit --reset-author           # Reset author identity to configured values
git commit --status                 # Show git status in commit message editor
git commit --no-status              # Don't show status in commit editor
git commit --no-verify              # Skip pre-commit and commit-msg hooks
git commit --short                  # Show short status
git commit --branch                 # Show branch and tracking info in short format
git commit --porcelain              # Machine-readable output
git commit --long                   # Show long status (default)
git commit --null                   # Separate entries with NUL instead of newline
```

**Commit message best practices:**

```
Short summary (50 chars or less)

More detailed explanatory text, if necessary. Wrap it to about
72 characters or so. The blank line separating the summary from
the body is critical.

- Bullet points are okay
- Use a hyphen or asterisk as the bullet

Fixes #123
Co-authored-by: Name <email@example.com>
```

### `git rm`

Removes files from the working directory and/or the staging area.

```bash
git rm <file>                  # Remove file from repo and working directory
git rm <file1> <file2>         # Remove multiple files
git rm -r <directory>          # Recursively remove a directory
git rm --cached <file>         # Remove from index only (keep in working dir)
git rm -f <file>               # Force removal (when file has staged changes)
git rm --force <file>          # Same as -f
git rm -n <file>               # Dry run
git rm --dry-run <file>        # Same as -n
git rm -q                      # Suppress output
git rm --quiet                 # Same as -q
git rm --ignore-unmatch        # Don't error if file doesn't match any files
git rm --pathspec-from-file=<file> # Read pathspec from file
git rm --pathspec-file-nul     # NUL separator for pathspec from file
```

### `git mv`

Moves or renames a file, directory, or symlink.

```bash
git mv <source> <destination>  # Move/rename a file
git mv -f <source> <dest>      # Force move (overwrite if destination exists)
git mv --force <source> <dest> # Same as -f
git mv -n <source> <dest>      # Dry run
git mv --dry-run <source><dest># Same as -n
git mv -k                      # Skip errors during move
git mv -v                      # Verbose output
git mv --verbose               # Same as -v
```

---

## Inspecting the Repository

### `git status`

Shows the current state of the working directory and staging area.

```bash
git status                     # Standard status output
git status -s                  # Short format output
git status --short             # Same as -s
git status -b                  # Show branch info in short format
git status --branch            # Same as -b
git status -u                  # Show untracked files (default: normal)
git status -uno                # Don't show untracked files
git status -uall               # Show all untracked files (including in subdirs)
git status --porcelain         # Machine-readable output (stable across versions)
git status --porcelain=v1      # Version 1 porcelain output
git status --porcelain=v2      # Version 2 porcelain output (more info)
git status --long              # Long format (default)
git status --ignored           # Show ignored files
git status --ignore-submodules # Ignore changes in submodules
git status --column            # Show untracked files in columns
git status --ahead-behind      # Show number of commits ahead/behind
git status --no-ahead-behind   # Suppress ahead/behind info
git status -z                  # NUL-terminated output (for scripting)
git status --null              # Same as -z
git status --renames           # Detect renames (default)
git status --no-renames        # Don't detect renames
git status --find-renames=<n>  # Set similarity threshold for rename detection
```

**Short format codes:**

| Code | Meaning |
|---|---|
| `??` | Untracked file |
| `A ` | New file added to index |
| `M ` | Modified in index |
| ` M` | Modified in working tree |
| `MM` | Modified in both index and working tree |
| `D ` | Deleted from index |
| ` D` | Deleted from working tree |
| `R ` | Renamed in index |
| `C ` | Copied in index |
| `U ` | Unmerged (conflict) |
| `!!` | Ignored |

### `git log`

Shows the commit history.

```bash
git log                            # Show commit log
git log -n <number>                # Show last n commits
git log -<number>                  # Shorthand (e.g., git log -5)
git log --oneline                  # One commit per line (short hash + message)
git log --format=<format>          # Custom format
git log --pretty=<format>          # Same as --format
git log --graph                    # Show ASCII graph of branch/merge history
git log --all                      # Show all branches, not just current
git log --branches                 # Show all branches
git log --remotes                  # Show remote tracking branches
git log --tags                     # Show tags
git log --decorate                 # Show branch/tag names
git log --decorate=full            # Show full ref names
git log --decorate=short           # Show short ref names (default)
git log --no-decorate              # Don't show ref names
git log --stat                     # Show file change statistics
git log --shortstat                # Show short statistics summary
git log --name-only                # Show names of changed files
git log --name-status              # Show names and status of changed files
git log --diff-filter=<filter>     # Filter by file status (A, M, D, R, C…)
git log -p                         # Show patch (diff) for each commit
git log --patch                    # Same as -p
git log -p -S "string"             # Show commits that introduced/removed "string"
git log --follow <file>            # Follow file renames
git log -- <file>                  # Show commits that changed <file>
git log <branch>                   # Show log for specific branch
git log <branch1>..<branch2>       # Commits in branch2 not in branch1
git log <branch1>...<branch2>      # Commits unique to each branch (symmetric diff)
git log --merges                   # Show only merge commits
git log --no-merges                # Exclude merge commits
git log --first-parent             # Only follow first parent in merges
git log --ancestry-path            # Only commits in direct path from A to B
git log --author="name"            # Filter by author
git log --committer="name"         # Filter by committer
git log --grep="pattern"           # Filter by commit message pattern
git log --grep-reflog="pattern"    # Search reflog messages
git log --all-match                # Match all --grep patterns (AND not OR)
git log --invert-grep              # Invert --grep filter
git log --since="2024-01-01"       # Show commits after date
git log --after="2024-01-01"       # Same as --since
git log --until="2024-12-31"       # Show commits before date
git log --before="2024-12-31"      # Same as --until
git log --date=relative            # Show dates as "2 weeks ago"
git log --date=short               # Show dates as YYYY-MM-DD
git log --date=iso                 # ISO 8601 date format
git log --date=format:"%Y-%m-%d"   # Custom date format
git log -S "string"                # Pickaxe: commits that added/removed "string"
git log -G "regex"                 # Commits where diff matches regex
git log --abbrev-commit            # Show abbreviated commit hash
git log --no-abbrev-commit         # Show full commit hash
git log --abbrev=<n>               # Set abbreviation length
git log --walk-reflogs             # Walk reflog (for --walk-reflogs only)
git log --left-right               # Show < or > for which side of a symmetric diff
git log --cherry-pick              # Mark equivalent commits with =
git log --cherry-mark              # Mark equivalent commits (with --left-right)
git log --cherry                   # Same as --right-only --cherry-mark --no-merges
git log --simplify-by-decoration   # Only show commits referenced by a branch/tag
git log --sparse                   # Show all commits, including those that don't affect visible files
git log --bisect                   # Used internally by git bisect
git log --topo-order               # Show commits in topological order
git log --date-order               # Show commits ordered by date
git log --reverse                  # Show commits in reverse order
git log --walk-reflogs             # Walk reflogs
git log -L <start>,<end>:<file>    # Show changes to a range of lines in a file
git log -L :<function>:<file>      # Show changes to a named function
git log --full-diff                # Show full diff, not just changes to pathspecs
git log --full-history             # Show all commits for file, even if simplified away
git log --max-count=<n>            # Same as -n <n>
git log --skip=<n>                 # Skip first n commits
git log --show-signature           # Show GPG signature info
git log --check                    # Warn about whitespace issues
git log --submodule                # Show submodule commits
git log --diff-merges=on           # Show diffs for merge commits
git log --diff-merges=first-parent # Diffs against first parent only
git log --diff-merges=separate     # Show diffs vs each parent separately
git log --diff-merges=combined     # Combined diff for merges
git log --cc                       # Combined diff (shorthand for --diff-merges=combined)
```

**Pretty formats:**

```bash
git log --pretty=oneline           # Full hash + message
git log --pretty=short             # Hash, author, message
git log --pretty=medium            # Hash, author, date, message
git log --pretty=full              # Hash, author, committer, message
git log --pretty=fuller            # Full info including committer date
git log --pretty=email             # Email patch format
git log --pretty=raw               # Raw object format
git log --pretty=format:"%H %s"    # Custom format
```

**Custom format placeholders:**

| Placeholder | Description |
|---|---|
| `%H` | Full commit hash |
| `%h` | Abbreviated commit hash |
| `%T` | Full tree hash |
| `%t` | Abbreviated tree hash |
| `%P` | Full parent hashes |
| `%p` | Abbreviated parent hashes |
| `%an` | Author name |
| `%ae` | Author email |
| `%ad` | Author date |
| `%ar` | Author date, relative |
| `%cn` | Committer name |
| `%ce` | Committer email |
| `%cd` | Committer date |
| `%s` | Subject (first line of message) |
| `%b` | Body |
| `%N` | Commit notes |
| `%D` | Ref names (like --decorate) |
| `%C(color)` | Change color |
| `%n` | Newline |
| `%%` | Literal `%` |

### `git diff`

Shows differences between commits, the index, and the working directory.

```bash
git diff                           # Working dir vs staging area (unstaged changes)
git diff --staged                  # Staging area vs last commit (staged changes)
git diff --cached                  # Same as --staged
git diff HEAD                      # Working dir vs last commit (all changes)
git diff <commit>                  # Working dir vs commit
git diff <commit1> <commit2>       # Differences between two commits
git diff <branch1> <branch2>       # Differences between two branches
git diff <branch1>..<branch2>      # Same (explicit two-dot)
git diff <branch1>...<branch2>     # Diff from common ancestor to branch2
git diff -- <file>                 # Diff for specific file only
git diff <commit> -- <file>        # Diff for specific file at specific commit
git diff --stat                    # Show statistics summary
git diff --shortstat               # Show only the summary line
git diff --name-only               # Show only changed file names
git diff --name-status             # Show file names with change status
git diff --diff-filter=<filter>    # Filter by A(dded), M(odified), D(eleted)…
git diff -p                        # Generate patch output (default)
git diff --patch                   # Same as -p
git diff -u                        # Unified diff format (default, same as -p)
git diff --unified=<n>             # Set context lines (default: 3)
git diff -U<n>                     # Same as --unified=<n>
git diff --word-diff               # Word-level diff
git diff --word-diff=color         # Word diff with color only
git diff --word-diff=plain         # Word diff with [-removed-] {+added+} markup
git diff --color-words             # Color words (no markup)
git diff --color-words=<regex>     # Use regex to define "word"
git diff -w                        # Ignore all whitespace
git diff --ignore-all-space        # Same as -w
git diff -b                        # Ignore amount of whitespace changes
git diff --ignore-space-change     # Same as -b
git diff --ignore-blank-lines      # Ignore changes whose lines are all blank
git diff --ignore-space-at-eol     # Ignore trailing whitespace
git diff --no-color                # Disable color output
git diff --color                   # Force color output
git diff --check                   # Warn about whitespace issues
git diff --raw                     # Raw output format
git diff -R                        # Reverse the diff
git diff --reverse                 # Same as -R
git diff --minimal                 # Produce smallest possible diff
git diff --patience                # Use patience diff algorithm
git diff --histogram               # Use histogram diff algorithm (often best)
git diff --diff-algorithm=<name>   # Choose algorithm: myers, minimal, patience, histogram
git diff -S "string"               # Show files with added/removed "string"
git diff -G "regex"                # Show files where diff matches regex
git diff --function-context        # Show complete function surrounding each change
git diff --submodule               # Show submodule content diff
git diff --submodule=short         # Show submodule summary
git diff --submodule=diff          # Show full submodule diff
git diff --no-index <file1> <file2># Compare two arbitrary files (outside of git)
git diff --exit-code               # Exit 1 if there are differences
git diff --quiet                   # Suppress output, only set exit code
git diff --binary                  # Output binary patch
git diff --full-index              # Show full blob hashes in output
git diff --abbrev                  # Abbreviate object names
git diff --break-rewrites          # Break complete file rewrites into delete+add
git diff -B                        # Shorthand for --break-rewrites
git diff --find-renames            # Detect file renames
git diff -M                        # Same as --find-renames
git diff --find-renames=<n>        # Set similarity threshold for renames
git diff --find-copies             # Detect copies
git diff -C                        # Same as --find-copies
git diff --find-copies-harder      # Check all files for copies (slower)
git diff --irreversible-delete     # Omit preimage from diff of deleted files
git diff -D                        # Same as --irreversible-delete
git diff --dirstat                 # Show directory-level change distribution
git diff --dirstat=<params>        # Specify dirstat parameters (changes, lines, files)
git diff --cumulative              # With --dirstat, accumulate subdirs into parents
git diff -l<n>                     # Set rename/copy detection limit
git diff --no-prefix               # Don't show a/ b/ prefixes
git diff --src-prefix=<prefix>     # Set source prefix
git diff --dst-prefix=<prefix>     # Set destination prefix
```

### `git show`

Shows information about any Git object (commit, tag, tree, blob).

```bash
git show                           # Show the last commit and its diff
git show <commit>                  # Show a specific commit
git show <tag>                     # Show a tag
git show <branch>                  # Show last commit of a branch
git show HEAD~3                    # Show 3 commits back
git show HEAD^                     # Show parent commit
git show <commit>:<file>           # Show file content at a specific commit
git show <commit>:                 # Show file tree at a specific commit
git show --stat <commit>           # Show stats summary
git show --name-only <commit>      # Show only file names
git show --name-status <commit>    # Show file names with status
git show --format="%s" <commit>    # Custom format (subject only)
git show --no-patch <commit>       # Don't show the diff
git show -s <commit>               # Same as --no-patch
git show --word-diff <commit>      # Word-level diff
git show --abbrev-commit           # Abbreviate the commit hash
git show --diff-stat <commit>      # Show diffstat
git show <tree-hash>               # Show tree object
git show <blob-hash>               # Show file content
```

### `git blame`

Shows what revision and author last modified each line of a file.

```bash
git blame <file>                   # Annotate file with commit info
git blame -L <start>,<end> <file>  # Annotate only specified line range
git blame -L 10,20 <file>          # Lines 10 to 20
git blame -L 10,+10 <file>         # 10 lines starting at line 10
git blame -L :<regex> <file>       # From line matching regex to end of function
git blame -n                       # Show original line numbers
git blame --line-porcelain <file>  # Machine-readable output per line
git blame --porcelain <file>       # Porcelain output
git blame -p <file>                # Same as --porcelain
git blame -s <file>                # Suppress author and timestamp
git blame -e <file>                # Show author email instead of name
git blame --show-email <file>      # Same as -e
git blame -w <file>                # Ignore whitespace changes
git blame -M <file>                # Detect moved/copied lines within file
git blame -C <file>                # Detect copied lines from other files
git blame -C -C <file>             # Also check modified files in same commit
git blame -C -C -C <file>          # Also check files in all parent commits
git blame <commit> -- <file>       # Blame as of a specific commit
git blame <commit>^ -- <file>      # Blame before a commit
git blame --since=<date> <file>    # Ignore older than date
git blame --date=<format> <file>   # Control date display format
git blame --abbrev=<n>             # Abbreviate hash to n characters
git blame -f <file>                # Show filename in output
git blame --show-name <file>       # Same as -f
git blame -t <file>                # Show raw timestamp
git blame --color-lines            # Color-code lines by age
git blame --color-by-age           # Color by commit age
git blame --root                   # Treat root commits as boundary
git blame --reverse <rev>..<rev> <file> # Go forward in time
```

### `git shortlog`

Summarizes commit output.

```bash
git shortlog                       # Summarize log by author
git shortlog -s                    # Show only commit count per author
git shortlog --summary             # Same as -s
git shortlog -n                    # Sort by number of commits
git shortlog --numbered            # Same as -n
git shortlog -e                    # Show email addresses
git shortlog --email               # Same as -e
git shortlog -c                    # Group by committer instead of author
git shortlog --committer           # Same as -c
git shortlog --no-merges           # Exclude merge commits
git shortlog --all                 # Include all refs
git shortlog <since>..<until>      # Between two refs
git shortlog -w                    # Wrap output
git shortlog -w<width>             # Wrap at specified width
```

---

## Branching

### `git branch`

Lists, creates, deletes, and manages branches.

```bash
git branch                          # List local branches
git branch -a                       # List all branches (local + remote)
git branch --all                    # Same as -a
git branch -r                       # List remote tracking branches
git branch --remotes                # Same as -r
git branch -v                       # Show last commit on each branch
git branch --verbose                # Same as -v
git branch -vv                      # Show last commit and upstream tracking info
git branch <name>                   # Create a new branch at HEAD
git branch <name> <commit>          # Create a new branch at specific commit
git branch -d <name>                # Delete branch (safe — merged only)
git branch --delete <name>          # Same as -d
git branch -D <name>                # Force-delete branch (even if unmerged)
git branch -d -r origin/<name>      # Delete remote tracking branch locally
git branch -m <old> <new>           # Rename a branch
git branch --move <old> <new>       # Same as -m
git branch -M <old> <new>           # Force rename (overwrite if new name exists)
git branch -c <old> <new>           # Copy a branch
git branch --copy <old> <new>       # Same as -c
git branch -C <old> <new>           # Force copy
git branch -u <remote>/<branch>     # Set upstream tracking for current branch
git branch --set-upstream-to=<remote>/<branch>  # Same as -u
git branch --unset-upstream         # Remove upstream tracking from current branch
git branch --track <name> <remote>/<branch>  # Create branch tracking remote
git branch --no-track <name>        # Create branch without tracking
git branch --contains <commit>      # List branches containing commit
git branch --no-contains <commit>   # List branches NOT containing commit
git branch --merged                 # List branches merged into HEAD
git branch --merged <commit>        # List branches merged into commit
git branch --no-merged              # List branches NOT merged into HEAD
git branch --no-merged <commit>     # List branches NOT merged into commit
git branch --points-at <commit>     # List branches pointing at commit
git branch --list <pattern>         # List branches matching pattern
git branch -l <pattern>             # Same as --list <pattern>
git branch --sort=<key>             # Sort by: version:refname, creatordate, etc.
git branch --format=<format>        # Custom output format
git branch --show-current           # Print name of current branch
git branch --column                 # Show in columns
git branch --edit-description       # Open editor to set branch description
git branch --color                  # Force color output
git branch --no-color               # Disable color output
git branch -i <name>                # Ignore case when matching pattern
git branch --ignore-case <name>     # Same as -i
git branch --abbrev=<n>             # Abbreviate object names
git branch --no-abbrev              # Don't abbreviate
git branch -q                       # Quiet output
git branch --quiet                  # Same as -q
```

### `git checkout`

Switches branches or restores working directory files.

```bash
git checkout <branch>              # Switch to a branch
git checkout -                     # Switch to previous branch
git checkout -b <branch>           # Create and switch to a new branch
git checkout -B <branch>           # Create/reset branch and switch
git checkout -b <branch> <remote>/<branch>  # Create branch tracking remote
git checkout --track <remote>/<branch>      # Create tracking branch with same name
git checkout -t <remote>/<branch>  # Shorthand for --track
git checkout <commit>              # Detach HEAD at a specific commit
git checkout <tag>                 # Detach HEAD at a tag
git checkout -- <file>             # Discard changes in working dir (restore from index)
git checkout <commit> -- <file>    # Restore file from specific commit to index+workdir
git checkout HEAD -- <file>        # Restore file from last commit
git checkout HEAD~1 -- <file>      # Restore file from 1 commit back
git checkout --merge <branch>      # 3-way merge during branch switch
git checkout -m <branch>           # Same as --merge
git checkout --conflict=<style>    # Set conflict style: merge or diff3
git checkout -p <branch>           # Interactively select hunks from branch
git checkout --patch <branch>      # Same as -p
git checkout --ours -- <file>      # Resolve conflict by choosing "our" version
git checkout --theirs -- <file>    # Resolve conflict by choosing "their" version
git checkout --orphan <branch>     # Create orphan branch (no history)
git checkout --ignore-other-worktrees <branch>  # Checkout even if in another worktree
git checkout -f <branch>           # Force checkout, discard local changes
git checkout --force <branch>      # Same as -f
git checkout -q                    # Quiet output
git checkout --quiet               # Same as -q
git checkout --detach              # Detach HEAD even when checking out a branch
git checkout --no-guess            # Don't guess remote branch name
git checkout --guess               # Guess remote branch name (default)
git checkout --overlay             # Overlay mode (default)
git checkout --no-overlay          # Remove files not in target branch
```

> **Note:** `git checkout` has been partially superseded by `git switch` (for switching branches) and `git restore` (for restoring files) introduced in Git 2.23.

### `git switch`

Switches branches (cleaner alternative to `git checkout` for branch switching).

```bash
git switch <branch>                # Switch to branch
git switch -                       # Switch to previous branch
git switch -c <branch>             # Create and switch to new branch
git switch --create <branch>       # Same as -c
git switch -C <branch>             # Force-create (reset if exists) and switch
git switch --force-create <branch> # Same as -C
git switch -c <branch> <commit>    # Create branch at specific commit
git switch -c <branch> <remote>/<branch>  # Create tracking branch
git switch --track <remote>/<branch>      # Create branch tracking remote
git switch -t <remote>/<branch>    # Same as --track
git switch --no-track <branch>     # Create without tracking
git switch --detach <commit>       # Detach HEAD at commit
git switch -d <commit>             # Same as --detach
git switch --orphan <branch>       # Create orphan branch
git switch -m                      # Merge current changes during switch
git switch --merge                 # Same as -m
git switch --conflict=<style>      # Set conflict marker style: merge or diff3
git switch -f                      # Force switch, discard local changes
git switch --force                 # Same as -f
git switch --discard-changes       # Same as -f
git switch --guess                 # Guess remote branch name (default)
git switch --no-guess              # Don't guess remote branch name
git switch -q                      # Quiet output
git switch --quiet                 # Same as -q
git switch --recurse-submodules    # Also switch submodules
git switch --no-recurse-submodules # Don't switch submodules
git switch --progress              # Show progress
git switch --no-progress           # Don't show progress
```

### `git restore`

Restores working directory files or the index (introduced in Git 2.23).

```bash
git restore <file>                 # Restore file in working dir from index
git restore .                      # Restore all files in working dir from index
git restore --staged <file>        # Unstage file (restore index from HEAD)
git restore -S <file>              # Same as --staged
git restore --worktree <file>      # Restore file in working directory (default)
git restore -W <file>              # Same as --worktree
git restore --staged --worktree <file>  # Restore both index and working dir from HEAD
git restore --source=<commit> <file>    # Restore from specific commit
git restore -s <commit> <file>     # Same as --source
git restore --source=HEAD~2 <file> # Restore file from 2 commits ago
git restore -p <file>              # Interactively choose hunks to restore
git restore --patch <file>         # Same as -p
git restore --ours <file>          # Restore to "our" version during conflict
git restore --theirs <file>        # Restore to "their" version during conflict
git restore --merge <file>         # Recreate conflict markers
git restore --conflict=<style>     # Set conflict style
git restore --ignore-unmerged      # Don't fail on unmerged entries
git restore --no-overlay           # Remove files not in tree being restored
git restore --overlay              # Don't remove files not in tree (default)
git restore --recurse-submodules   # Also restore submodules
git restore --no-recurse-submodules # Don't restore submodules
git restore --pathspec-from-file=<file>  # Read pathspec from file
git restore --pathspec-file-nul    # NUL separator for pathspec from file
git restore -q                     # Quiet output
git restore --quiet                # Same as -q
```

### `git merge`

Joins two or more development histories together.

```bash
git merge <branch>                  # Merge branch into current branch
git merge <branch1> <branch2>       # Merge multiple branches into current
git merge --no-ff <branch>          # Create a merge commit even if fast-forward is possible
git merge --ff                      # Fast-forward if possible (default)
git merge --ff-only <branch>        # Only fast-forward; fail if not possible
git merge --squash <branch>         # Squash all commits into one staged change
git merge -m "message"              # Set merge commit message
git merge --message="msg"           # Same as -m
git merge -e                        # Open editor for merge message
git merge --edit                    # Same as -e
git merge --no-edit                 # Don't open editor for message
git merge --commit                  # Commit result (default)
git merge --no-commit               # Perform merge but don't commit (for inspection)
git merge --stat                    # Show diffstat (default)
git merge --no-stat                 # Don't show diffstat
git merge -n                        # Same as --no-stat
git merge --log                     # Include branch log info in merge message
git merge --log=<n>                 # Include last n commits from merged branch
git merge --no-log                  # Don't include branch log
git merge -s <strategy>             # Use merge strategy
git merge --strategy=<strategy>     # Same as -s
git merge -X <option>               # Pass extra option to merge strategy
git merge --strategy-option=<opt>   # Same as -X
git merge --allow-unrelated-histories  # Allow merging unrelated histories
git merge --abort                   # Abort an in-progress merge
git merge --continue                # Continue after resolving conflicts
git merge --quit                    # Forget about current merge (don't abort)
git merge --autostash               # Automatically stash before and pop after merge
git merge --no-autostash            # Don't autostash
git merge --progress                # Show progress
git merge --no-progress             # Don't show progress
git merge --verify-signatures       # Verify commit signatures
git merge --no-verify-signatures    # Don't verify signatures
git merge -v                        # Verbose output
git merge --verbose                 # Same as -v
git merge -q                        # Quiet output
git merge --quiet                   # Same as -q
git merge --overwrite-ignore        # Overwrite ignored files on merge
git merge --no-overwrite-ignore     # Don't overwrite ignored files
git merge --signoff                 # Add Signed-off-by to merge commit
git merge --no-signoff              # Don't add Signed-off-by
git merge --rerere-autoupdate       # Automatically update index after rerere
git merge --no-rerere-autoupdate    # Don't autoupdate after rerere
git merge --gpg-sign                # GPG sign the merge commit
git merge --no-gpg-sign             # Don't GPG sign
```

**Merge strategies (`-s`):**

| Strategy | Description |
|---|---|
| `ort` | Default since Git 2.34 — improved octopus/recursive |
| `recursive` | Previous default for two-head merges |
| `resolve` | For two-head merges; can detect cross-criss merges |
| `octopus` | For merging more than two branches |
| `ours` | Always use current branch's tree (discard others) |
| `subtree` | Like recursive but with tree shifting |

**Strategy options (`-X`):**

| Option | Description |
|---|---|
| `ours` | Prefer our version on conflicts |
| `theirs` | Prefer their version on conflicts |
| `patience` | Use patience diff algorithm |
| `diff-algorithm=<alg>` | Set diff algorithm |
| `ignore-space-change` | Ignore whitespace changes |
| `ignore-all-space` | Ignore all whitespace |
| `ignore-space-at-eol` | Ignore trailing whitespace |
| `renormalize` | Apply line-ending normalization before merge |
| `no-renormalize` | Disable normalization |
| `subtree[=<path>]` | Shift subtree for comparison |

### `git rebase`

Reapplies commits on top of another base commit.

```bash
git rebase <branch>                # Rebase current branch onto <branch>
git rebase <upstream>              # Rebase current branch onto upstream
git rebase <upstream> <branch>     # Rebase <branch> onto <upstream>
git rebase --onto <newbase> <upstream>         # Rebase onto a different base
git rebase --onto <newbase> <upstream> <branch>
git rebase -i                      # Interactive rebase (HEAD~n needs to be specified)
git rebase -i HEAD~3               # Interactive rebase for last 3 commits
git rebase -i <commit>             # Interactive rebase from <commit> forward
git rebase --interactive HEAD~3    # Same as -i
git rebase --autosquash            # Automatically apply fixup! and squash! commits
git rebase --no-autosquash         # Don't auto-apply squash/fixup commits
git rebase --autostash             # Stash before rebase, pop after
git rebase --no-autostash          # Don't autostash
git rebase --stat                  # Show diffstat after rebase
git rebase --no-stat               # Don't show diffstat
git rebase -n                      # Same as --no-stat
git rebase --signoff               # Add Signed-off-by to all rebased commits
git rebase --no-signoff            # Don't add Signed-off-by
git rebase --committer-date-is-author-date  # Use author date as committer date
git rebase --reset-author-date     # Reset author date of rebased commits
git rebase --ignore-date           # Same as --committer-date-is-author-date
git rebase --ignore-whitespace     # Ignore whitespace differences
git rebase --whitespace=<option>   # Handle whitespace: nowarn, warn, fix, error
git rebase --fork-point            # Use reflog to find fork point (default)
git rebase --no-fork-point         # Don't use reflog for fork point
git rebase -s <strategy>           # Use specified merge strategy
git rebase --strategy=<strategy>   # Same as -s
git rebase -X <option>             # Pass option to merge strategy
git rebase --strategy-option=<opt> # Same as -X
git rebase --rebase-merges         # Preserve merge commits during rebase
git rebase --no-rebase-merges      # Don't preserve merge commits
git rebase -r                      # Same as --rebase-merges
git rebase --empty=<option>        # How to handle empty commits: drop, keep, ask
git rebase --keep-empty            # Keep commits that become empty
git rebase --no-keep-empty         # Drop commits that become empty
git rebase --skip-empty-commits    # Same as --no-keep-empty
git rebase --allow-empty-message   # Allow commits with empty messages
git rebase --abort                 # Abort in-progress rebase and restore original
git rebase --continue              # Continue after resolving conflict
git rebase --skip                  # Skip the current commit and continue
git rebase --quit                  # Abort but keep rebase-applied commits
git rebase --edit-todo             # Edit the rebase todo list mid-rebase
git rebase --show-current-patch    # Show current patch during rebase
git rebase -m                      # Use merge strategies for rebasing
git rebase --merge                 # Same as -m
git rebase -v                      # Verbose output
git rebase --verbose               # Same as -v
git rebase -q                      # Quiet output
git rebase --quiet                 # Same as -q
git rebase -p                      # Preserve merges (deprecated, use --rebase-merges)
git rebase --preserve-merges       # Same as -p (deprecated)
git rebase --gpg-sign              # GPG-sign rebased commits
git rebase --no-gpg-sign           # Don't GPG sign
git rebase --verify                # Verify commit signatures during rebase
git rebase --no-verify             # Don't verify signatures
git rebase --rerere-autoupdate     # Auto-update index with rerere result
git rebase --no-rerere-autoupdate  # Don't auto-update
git rebase --update-refs           # Update all branch refs in the rebase range
git rebase --no-update-refs        # Don't update branch refs
```

**Interactive rebase commands (in the todo list editor):**

| Command | Abbreviation | Description |
|---|---|---|
| `pick` | `p` | Use commit as-is |
| `reword` | `r` | Use commit but edit its message |
| `edit` | `e` | Pause to amend the commit |
| `squash` | `s` | Meld into previous commit, edit message |
| `fixup` | `f` | Meld into previous commit, discard message |
| `fixup -C` | `f -C` | Meld into previous, use this message |
| `exec` | `x` | Run a shell command |
| `break` | `b` | Pause here (continue with `--continue`) |
| `drop` | `d` | Remove the commit |
| `label` | `l` | Label current HEAD |
| `reset` | `t` | Reset HEAD to label |
| `merge` | `m` | Create a merge commit |

---

## Remote Repositories

### `git remote`

Manages connections to remote repositories.

```bash
git remote                          # List remote names
git remote -v                       # List remotes with their URLs
git remote --verbose                # Same as -v
git remote add <name> <url>         # Add a new remote
git remote remove <name>            # Remove a remote
git remote rm <name>                # Same as remove
git remote rename <old> <new>       # Rename a remote
git remote set-url <name> <url>     # Change the URL of a remote
git remote set-url --push <name> <url>  # Set a separate push URL
git remote set-url --add <name> <url>   # Add another push URL
git remote set-url --delete <name> <url> # Delete a push URL
git remote get-url <name>           # Show URL of a remote
git remote get-url --push <name>    # Show push URL
git remote get-url --all <name>     # Show all URLs
git remote show <name>              # Show detailed info about a remote
git remote prune <name>             # Remove stale remote-tracking branches
git remote prune --dry-run <name>   # Show what would be pruned
git remote update                   # Fetch from all remotes
git remote update <name>            # Fetch from specific remote
git remote update --prune           # Fetch and prune all remotes
git remote set-head <name> <branch> # Set default remote HEAD
git remote set-head <name> -a       # Auto-detect default remote HEAD
git remote set-head <name> -d       # Delete default remote HEAD
git remote set-branches <name> <branch>       # Set tracked branches
git remote set-branches --add <name> <branch> # Add tracked branch
```

### `git fetch`

Downloads objects and refs from a remote repository without merging.

```bash
git fetch                          # Fetch from default remote (origin)
git fetch <remote>                 # Fetch from specific remote
git fetch <remote> <branch>        # Fetch specific branch from remote
git fetch <remote> <branch>:<local>  # Fetch and store as local branch
git fetch --all                    # Fetch from all remotes
git fetch -a                       # Same as --all
git fetch --prune                  # Remove stale remote-tracking branches after fetch
git fetch -p                       # Same as --prune
git fetch --prune-tags             # Remove stale local tags
git fetch -P                       # Same as --prune-tags
git fetch --tags                   # Fetch all tags
git fetch -t                       # Same as --tags
git fetch --no-tags                # Don't automatically fetch tags
git fetch -n                       # Same as --no-tags
git fetch --depth=<n>              # Shallow fetch (limit history depth)
git fetch --deepen=<n>             # Deepen a shallow clone by n commits
git fetch --shallow-since=<date>   # Shallow from date forward
git fetch --shallow-exclude=<rev>  # Exclude commits reachable from rev
git fetch --unshallow              # Convert shallow clone to full clone
git fetch --update-shallow         # Update shallow info
git fetch --dry-run                # Show what would be done
git fetch -v                       # Verbose output
git fetch --verbose                # Same as -v
git fetch -q                       # Quiet output
git fetch --quiet                  # Same as -q
git fetch --progress               # Show progress
git fetch --no-progress            # Don't show progress
git fetch -j <n>                   # Number of parallel fetch jobs
git fetch --jobs=<n>               # Same as -j
git fetch --set-upstream           # Set upstream tracking info for fetched branch
git fetch --update-head-ok         # Allow updating HEAD (for bare repos)
git fetch --force                  # Allow updating local tracking branches
git fetch -f                       # Same as --force
git fetch --append                 # Append to existing FETCH_HEAD
git fetch --keep                   # Don't delete FETCH_HEAD after fetch
git fetch --multiple <remote>...   # Fetch from multiple remotes
git fetch --auto-gc                # Run garbage collection after fetch (default)
git fetch --no-auto-gc             # Don't run GC after fetch
git fetch --write-fetch-head       # Write FETCH_HEAD (default)
git fetch --no-write-fetch-head    # Don't write FETCH_HEAD
git fetch --recurse-submodules     # Fetch into submodules
git fetch --no-recurse-submodules  # Don't recurse into submodules
git fetch --submodule-prefix=<path> # Prepend path to submodule messages
git fetch --filter=<filter>        # Partial clone filter
git fetch --refetch                # Re-fetch all objects even if present locally
git fetch --negotiate-only         # Only negotiate, don't download objects
git fetch --show-forced-updates    # Show forced updates (default)
git fetch --no-show-forced-updates # Don't show forced updates
git fetch -o <option>              # Git protocol option
git fetch --server-option=<option> # Same as -o
git fetch --bundle-uri=<uri>       # Fetch from bundle at URI
```

### `git pull`

Fetches from a remote and merges into the current branch.

```bash
git pull                           # Fetch and merge from tracked upstream
git pull <remote>                  # Fetch and merge from remote
git pull <remote> <branch>         # Fetch and merge specific branch
git pull --rebase                  # Rebase instead of merge
git pull -r                        # Same as --rebase
git pull --rebase=false            # Force merge (override global setting)
git pull --rebase=true             # Force rebase
git pull --rebase=merges           # Rebase, preserving merges
git pull --rebase=interactive      # Interactive rebase
git pull --rebase=preserve         # Deprecated: use merges instead
git pull --no-rebase               # Merge instead of rebase
git pull --ff                      # Fast-forward if possible (default)
git pull --no-ff                   # Always create a merge commit
git pull --ff-only                 # Only fast-forward; fail otherwise
git pull --squash                  # Squash remote commits into one change
git pull --stat                    # Show diffstat after merge
git pull --no-stat                 # Don't show diffstat
git pull -n                        # Same as --no-stat
git pull --commit                  # Commit result (default)
git pull --no-commit               # Don't commit automatically
git pull --allow-unrelated-histories  # Allow merging unrelated histories
git pull --autostash               # Autostash before and after pull
git pull --no-autostash            # Don't autostash
git pull --depth=<n>               # Limit fetch depth
git pull --unshallow               # Convert shallow clone to full
git pull --update-shallow          # Update shallow info
git pull --prune                   # Remove stale remote-tracking branches
git pull -p                        # Same as --prune
git pull --tags                    # Fetch all tags
git pull --no-tags                 # Don't fetch tags
git pull -v                        # Verbose output
git pull --verbose                 # Same as -v
git pull -q                        # Quiet output
git pull --quiet                   # Same as -q
git pull --progress                # Show progress
git pull --no-progress             # Don't show progress
git pull --recurse-submodules      # Pull in submodules as well
git pull --no-recurse-submodules   # Don't pull submodules
git pull -s <strategy>             # Use merge strategy
git pull -X <option>               # Pass option to merge strategy
git pull --verify-signatures       # Verify commit signatures
git pull --jobs=<n>                # Parallel fetch jobs
git pull -j <n>                    # Same as --jobs
git pull --set-upstream            # Set upstream for current branch
```

### `git push`

Updates a remote repository with local commits.

```bash
git push                            # Push to tracked upstream
git push <remote>                   # Push to remote (matching branches)
git push <remote> <branch>          # Push branch to remote
git push <remote> <local>:<remote>  # Push local branch as differently-named remote branch
git push origin HEAD                # Push current branch by name
git push -u <remote> <branch>       # Push and set upstream tracking
git push --set-upstream <remote> <branch>  # Same as -u
git push --all                      # Push all local branches
git push --branches                 # Same as --all
git push --tags                     # Push all tags
git push --follow-tags              # Push commits and associated tags
git push --no-tags                  # Don't push tags
git push -d <remote> <branch>       # Delete remote branch
git push --delete <remote> <branch> # Same as -d
git push <remote> :<branch>         # Delete remote branch (empty refspec)
git push -f                         # Force push (dangerous!)
git push --force                    # Same as -f
git push --force-with-lease         # Force push only if remote hasn't changed
git push --force-with-lease=<refname>  # Force-with-lease on specific ref
git push --force-if-includes        # Force only if current tip of remote is included
git push --no-force-with-lease      # Disable force-with-lease
git push --atomic                   # Atomic push (all succeed or all fail)
git push --mirror                   # Push all refs (used for mirroring)
git push --dry-run                  # Show what would be pushed without pushing
git push -n                         # Same as --dry-run
git push -v                         # Verbose output
git push --verbose                  # Same as -v
git push -q                         # Quiet output
git push --quiet                    # Same as -q
git push --progress                 # Show progress
git push --no-progress              # Don't show progress
git push --prune                    # Remove remote branches with no local counterpart
git push --thin                     # Use thin pack (bandwidth optimization)
git push --no-thin                  # Don't use thin pack
git push --signed                   # GPG sign the push
git push --no-signed                # Don't GPG sign
git push --signed=if-asked          # Sign if server supports it
git push --verify                   # Verify push cert (requires server support)
git push --no-verify                # Don't verify push cert; also skip hooks
git push --recurse-submodules=check    # Check submodules are pushed
git push --recurse-submodules=on-demand # Push submodules as needed
git push --recurse-submodules=only     # Only push submodules
git push --recurse-submodules=no       # Don't push submodules
git push -o <option>                # Git protocol option
git push --push-option=<option>     # Same as -o
git push --receive-pack=<cmd>       # Specify receive-pack on remote
git push --exec=<cmd>               # Same as --receive-pack
git push --repo=<repo>              # Override configured remote
git push --ipv4                     # Use IPv4 only
git push -4                         # Same as --ipv4
git push --ipv6                     # Use IPv6 only
git push -6                         # Same as --ipv6
```

---

## Undoing Changes

### `git reset`

Resets HEAD to a specified state, optionally modifying the index and working directory.

```bash
git reset HEAD <file>              # Unstage a file (equivalent to git restore --staged)
git reset <commit>                 # Move HEAD to commit, keep index and working dir
git reset --soft <commit>          # Move HEAD; keep all changes staged
git reset --mixed <commit>         # Move HEAD; unstage changes (default)
git reset --hard <commit>          # Move HEAD; discard all changes (DESTRUCTIVE)
git reset --merge <commit>         # Move HEAD; keep uncommitted changes during reset
git reset --keep <commit>          # Move HEAD; keep working dir, fail if conflict
git reset HEAD~1                   # Undo last commit, keep changes staged (soft)
git reset --soft HEAD~1            # Undo last commit, keep everything staged
git reset --mixed HEAD~1           # Undo last commit, unstage changes
git reset --hard HEAD~1            # Undo last commit, discard all changes
git reset --hard HEAD              # Discard all uncommitted changes
git reset <file>                   # Unstage file (same as git reset HEAD <file>)
git reset -p                       # Interactively choose hunks to unstage
git reset --patch                  # Same as -p
git reset -q                       # Quiet output
git reset --quiet                  # Same as -q
git reset -N <file>                # Record file as intent-to-add
git reset --intent-to-add <file>   # Same as -N
```

### `git revert`

Creates a new commit that undoes the changes of a previous commit.

```bash
git revert <commit>                # Revert a commit (creates new commit)
git revert HEAD                    # Revert last commit
git revert HEAD~3..HEAD            # Revert last 3 commits (as separate commits)
git revert -n <commit>             # Revert but don't commit (stage only)
git revert --no-commit <commit>    # Same as -n
git revert --no-edit <commit>      # Don't open editor for revert message
git revert -e <commit>             # Open editor for revert message
git revert --edit <commit>         # Same as -e
git revert -m 1 <merge-commit>     # Revert a merge commit (1 = first parent)
git revert --mainline 1 <commit>   # Same as -m
git revert -s <commit>             # Add Signed-off-by to revert commit
git revert --signoff <commit>      # Same as -s
git revert --strategy=<strategy>   # Use merge strategy
git revert -X <option>             # Pass option to merge strategy
git revert --strategy-option=<opt> # Same as -X
git revert --rerere-autoupdate     # Auto-update index after rerere
git revert --no-rerere-autoupdate  # Don't auto-update
git revert --gpg-sign              # GPG sign the revert commit
git revert --no-gpg-sign           # Don't GPG sign
git revert --abort                 # Cancel in-progress revert
git revert --continue              # Continue after resolving conflicts
git revert --skip                  # Skip current commit in series
git revert --quit                  # Forget in-progress revert
```

### `git cherry-pick`

Applies the changes from a specific commit to the current branch.

```bash
git cherry-pick <commit>           # Apply a commit to current branch
git cherry-pick <hash1> <hash2>    # Apply multiple specific commits
git cherry-pick <hash1>..<hash2>   # Apply a range of commits (exclusive)
git cherry-pick <hash1>^..<hash2>  # Apply a range of commits (inclusive)
git cherry-pick -n <commit>        # Stage changes but don't commit
git cherry-pick --no-commit <commit>  # Same as -n
git cherry-pick -e <commit>        # Edit commit message
git cherry-pick --edit <commit>    # Same as -e
git cherry-pick --no-edit <commit> # Don't edit message
git cherry-pick -m 1 <merge>       # Cherry-pick a merge commit (parent 1)
git cherry-pick --mainline 1 <merge>  # Same as -m
git cherry-pick -x <commit>        # Append "cherry picked from..." to message
git cherry-pick -s <commit>        # Add Signed-off-by
git cherry-pick --signoff <commit> # Same as -s
git cherry-pick -S <commit>        # GPG sign the resulting commit
git cherry-pick --gpg-sign         # Same as -S
git cherry-pick --no-gpg-sign      # Don't GPG sign
git cherry-pick --ff               # Fast-forward if possible
git cherry-pick --allow-empty      # Keep empty commits
git cherry-pick --allow-empty-message  # Allow empty commit messages
git cherry-pick --keep-redundant-commits  # Keep commits that become empty
git cherry-pick -X <option>        # Pass option to merge strategy
git cherry-pick --strategy-option=<opt>   # Same as -X
git cherry-pick -s <strategy>      # Use merge strategy
git cherry-pick --strategy=<strategy>     # Same as -s
git cherry-pick --rerere-autoupdate   # Auto-update after rerere
git cherry-pick --no-rerere-autoupdate # Don't auto-update
git cherry-pick --abort            # Abort in-progress cherry-pick
git cherry-pick --continue         # Continue after resolving conflicts
git cherry-pick --skip             # Skip current commit and continue
git cherry-pick --quit             # Forget in-progress cherry-pick
```

---

## Stashing

### `git stash`

Temporarily shelves (stashes) changes so you can work on something else.

```bash
git stash                          # Stash current changes (modified tracked files)
git stash push                     # Same as git stash (explicit form)
git stash push -m "message"        # Stash with a description
git stash push --message="msg"     # Same as -m
git stash push -u                  # Also stash untracked files
git stash push --include-untracked # Same as -u
git stash push -a                  # Also stash ignored files
git stash push --all               # Same as -a
git stash push -p                  # Interactively choose hunks to stash
git stash push --patch             # Same as -p
git stash push -S                  # Staged changes only
git stash push --staged            # Same as -S
git stash push --keep-index        # Don't stash staged changes
git stash push -k                  # Same as --keep-index
git stash push -- <file>           # Stash specific file(s) only
git stash push -q                  # Quiet output
git stash push --quiet             # Same as -q
git stash list                     # List all stashes
git stash list --stat              # Show stashes with diffstat
git stash list -p                  # Show stashes with patches
git stash show                     # Show latest stash diff
git stash show stash@{n}           # Show specific stash
git stash show -p                  # Show stash as patch
git stash show -p stash@{n}        # Show specific stash as patch
git stash show --stat              # Show stash statistics
git stash pop                      # Apply latest stash and remove it
git stash pop stash@{n}            # Apply specific stash and remove it
git stash pop --index              # Also restore staged state
git stash pop -q                   # Quiet output
git stash apply                    # Apply latest stash (keep stash)
git stash apply stash@{n}          # Apply specific stash (keep stash)
git stash apply --index            # Also restore staged state
git stash apply -q                 # Quiet output
git stash drop                     # Delete latest stash
git stash drop stash@{n}           # Delete specific stash
git stash drop -q                  # Quiet output
git stash clear                    # Delete ALL stashes
git stash branch <branch>          # Create branch from stash and pop it
git stash branch <branch> stash@{n} # Create branch from specific stash
git stash create                   # Create a stash object without storing it
git stash store -m "msg" <hash>    # Store a stash object created with stash create
```

---

## Tagging

### `git tag`

Creates, lists, deletes, or verifies tags.

```bash
git tag                            # List all tags
git tag -l                         # List tags (same as no args)
git tag --list                     # Same as -l
git tag -l "v1.*"                  # List tags matching pattern
git tag -n                         # Show tag message (1 line)
git tag -n<n>                      # Show first n lines of tag message
git tag --sort=version:refname     # Sort tags by version
git tag --sort=-version:refname    # Sort tags by version descending
git tag --sort=creatordate         # Sort by creation date
git tag --contains <commit>        # List tags containing a commit
git tag --no-contains <commit>     # List tags not containing a commit
git tag --merged <commit>          # List tags merged into commit
git tag --no-merged <commit>       # List tags not merged into commit
git tag --points-at <commit>       # List tags pointing at commit
git tag --column                   # Show tags in columns
git tag --format=<format>          # Custom output format
git tag <name>                     # Create a lightweight tag at HEAD
git tag <name> <commit>            # Create a lightweight tag at commit
git tag -a <name>                  # Create an annotated tag (opens editor)
git tag --annotate <name>          # Same as -a
git tag -a <name> -m "message"     # Create annotated tag with message
git tag -a <name> <commit> -m "msg" # Annotated tag at specific commit
git tag -s <name>                  # Create a signed tag (GPG)
git tag --sign <name>              # Same as -s
git tag -u <key> <name>            # Create tag signed with specific key
git tag --local-user=<key> <name>  # Same as -u
git tag -f <name>                  # Force-create (overwrite existing tag)
git tag --force <name>             # Same as -f
git tag -d <name>                  # Delete a tag
git tag --delete <name>            # Same as -d
git tag -v <name>                  # Verify a signed tag
git tag --verify <name>            # Same as -v
git tag -e                         # Edit tag message in editor
git tag --edit                     # Same as -e
git tag --cleanup=<mode>           # How to clean up tag message
git tag -F <file>                  # Read tag message from file
git tag --file=<file>              # Same as -F
git tag -i <pattern>               # Ignore case when matching pattern
git tag --ignore-case <pattern>    # Same as -i
git tag --create-reflog            # Create a reflog for the tag
git tag --no-create-reflog         # Don't create reflog
```

---

## Searching

### `git grep`

Searches for a pattern in the working tree files.

```bash
git grep "pattern"                 # Search in all tracked files
git grep -n "pattern"              # Show line numbers
git grep --line-number "pattern"   # Same as -n
git grep -l "pattern"              # Show only file names with matches
git grep --files-with-matches "pat"# Same as -l
git grep -L "pattern"              # Show files WITHOUT matches
git grep --files-without-match "p" # Same as -L
git grep -c "pattern"              # Show match count per file
git grep --count "pattern"         # Same as -c
git grep -i "pattern"              # Case-insensitive
git grep --ignore-case "pattern"   # Same as -i
git grep -w "pattern"              # Match whole words only
git grep --word-regexp "pattern"   # Same as -w
git grep -v "pattern"              # Invert match
git grep --invert-match "pattern"  # Same as -v
git grep -E "pattern"              # Extended regex
git grep --extended-regexp "pattern" # Same as -E
git grep -G "pattern"              # Basic regex (default)
git grep --basic-regexp "pattern"  # Same as -G
git grep -F "string"               # Fixed string (no regex)
git grep --fixed-strings "string"  # Same as -F
git grep -P "pattern"              # Perl-compatible regex
git grep --perl-regexp "pattern"   # Same as -P
git grep -e "pattern"              # Explicitly specify pattern (for combining)
git grep -e "pat1" -e "pat2"       # Match either pattern (OR)
git grep --and -e "pat1" -e "pat2" # Match both patterns (AND)
git grep --not -e "pattern"        # Negate pattern
git grep --all-match               # File must match all patterns
git grep -A <n> "pattern"          # Show n lines after match
git grep -B <n> "pattern"          # Show n lines before match
git grep -C <n> "pattern"          # Show n lines context around match
git grep --context=<n>             # Same as -C
git grep -p "pattern"              # Show function name of match
git grep --show-function "pattern" # Same as -p
git grep -W "pattern"              # Show entire function containing match
git grep --function-context "pattern" # Same as -W
git grep -z                        # NUL-terminate output
git grep --null                    # Same as -z
git grep -o "pattern"              # Show only the matching part of line
git grep --only-matching "pat"     # Same as -o
git grep --break                   # Print blank line between files
git grep --heading                 # Print filename before matches
git grep -q "pattern"              # Quiet (exit code only)
git grep --quiet "pattern"         # Same as -q
git grep --color                   # Force color
git grep --no-color                # Disable color
git grep --cached "pattern"        # Search in index (staged files)
git grep --no-index "pattern"      # Search files not tracked by git
git grep --untracked "pattern"     # Also search untracked files
git grep --recurse-submodules "pat"# Search in submodules too
git grep -r "pattern"              # Same as --recurse-submodules
git grep <pattern> <commit>        # Search in committed tree
git grep <pattern> -- <file>       # Search specific file(s)
git grep <pattern> <branch>        # Search in a specific branch
git grep --max-depth=<n> "pattern" # Maximum directory recursion depth
git grep --threads=<n> "pattern"   # Number of parallel grep threads
git grep -a "pattern"              # Search binary files as text
git grep --text "pattern"          # Same as -a
git grep -I "pattern"              # Ignore binary files
git grep --open-files-in-pager "p" # Open results in pager
```

### `git bisect`

Uses binary search to find the commit that introduced a bug.

```bash
git bisect start                   # Begin bisect session
git bisect start <bad> <good>      # Start with known bad and good commits
git bisect bad                     # Mark current commit as bad
git bisect bad <commit>            # Mark specific commit as bad
git bisect good                    # Mark current commit as good
git bisect good <commit>           # Mark specific commit as good
git bisect new                     # Alias for 'bad' (for non-bug searches)
git bisect old                     # Alias for 'good'
git bisect new <commit>            # Mark commit as new (has property)
git bisect old <commit>            # Mark commit as old (lacks property)
git bisect terms                   # Show current terms (bad/good or custom)
git bisect terms --term-good       # Show what term means "good"
git bisect terms --term-bad        # Show what term means "bad"
git bisect skip                    # Skip current commit (can't test)
git bisect skip <commit>           # Skip a specific commit
git bisect skip <range>            # Skip a range of commits
git bisect reset                   # End bisect and return to original branch
git bisect reset <commit>          # Return to a specific commit after bisect
git bisect run <script>            # Automatically run script to test commits
git bisect log                     # Show bisect log so far
git bisect replay <log>            # Replay a bisect log
git bisect visualize               # Open gitk to show remaining candidates
git bisect view                    # Same as visualize
git bisect view --oneline          # View remaining commits as oneline log
```

**Example automated bisect:**

```bash
git bisect start
git bisect bad HEAD
git bisect good v1.0
git bisect run ./test.sh           # Script returns 0=good, 1=bad, 125=skip
```

---

## Worktrees

### `git worktree`

Manages multiple working trees attached to the same repository.

```bash
git worktree add <path>             # Create worktree with new branch
git worktree add <path> <branch>    # Create worktree checking out branch
git worktree add <path> <commit>    # Create worktree at specific commit
git worktree add -b <branch> <path> # Create worktree with new branch
git worktree add --detach <path>    # Create worktree in detached HEAD state
git worktree add -f <path>          # Force (allow if path already exists)
git worktree add --force <path>     # Same as -f
git worktree add --lock <path>      # Lock the worktree after creation
git worktree add --lock --reason="msg" <path>  # Lock with reason
git worktree add --checkout <path>  # Checkout (default)
git worktree add --no-checkout <path>  # Don't checkout files
git worktree add --track <path>     # Set up tracking
git worktree add --guess-remote <path>  # Guess remote branch
git worktree add --quiet <path>     # Quiet output
git worktree list                   # List all worktrees
git worktree list --porcelain       # Machine-readable list
git worktree list -v                # Verbose list
git worktree lock <worktree>        # Lock a worktree
git worktree lock --reason="msg" <worktree>  # Lock with reason
git worktree unlock <worktree>      # Unlock a worktree
git worktree move <worktree> <path> # Move a worktree to new location
git worktree move -f <worktree> <path>  # Force move
git worktree remove <worktree>      # Remove a worktree (must be clean)
git worktree remove -f <worktree>   # Force remove
git worktree prune                  # Remove worktree info for missing paths
git worktree prune --dry-run        # Show what would be removed
git worktree prune -v               # Verbose output
git worktree prune --expire=<date>  # Prune entries older than date
git worktree repair                 # Repair administrative files
git worktree repair <path>...       # Repair specified worktrees
```

---

## Submodules

### `git submodule`

Manages repository dependencies via submodules.

```bash
git submodule add <url>                    # Add submodule
git submodule add <url> <path>             # Add submodule at specified path
git submodule add -b <branch> <url>        # Track a specific branch
git submodule add --branch <branch> <url>  # Same as -b
git submodule add -f <url>                 # Force add (even if in .gitignore)
git submodule add --force <url>            # Same as -f
git submodule add --name <name> <url>      # Give submodule a name
git submodule add --depth <n> <url>        # Shallow clone submodule
git submodule status                       # Show status of submodules
git submodule status --cached              # Show index status
git submodule status --recursive           # Recurse into nested submodules
git submodule init                         # Initialize submodules
git submodule init <path>                  # Initialize specific submodule
git submodule update                       # Fetch and checkout submodules
git submodule update --init                # Init and update in one step
git submodule update --init --recursive    # Also update nested submodules
git submodule update --remote              # Fetch and update from remote tracking
git submodule update --remote --merge      # Merge remote changes
git submodule update --remote --rebase     # Rebase on remote changes
git submodule update --checkout            # Checkout commit recorded in superproject
git submodule update --no-fetch            # Don't fetch
git submodule update --depth <n>           # Shallow update
git submodule update --recommend-shallow   # Use shallow if recommended
git submodule update --no-recommend-shallow # Don't use shallow
git submodule update -j <n>                # Parallel jobs for submodule update
git submodule update --jobs <n>            # Same as -j
git submodule update -f                    # Force checkout
git submodule update --force               # Same as -f
git submodule update --filter=<filter>     # Partial clone filter
git submodule deinit <path>                # Unregister a submodule
git submodule deinit --all                 # Unregister all submodules
git submodule deinit -f <path>             # Force deinit
git submodule deinit --force <path>        # Same as -f
git submodule sync                         # Sync submodule URLs from .gitmodules
git submodule sync --recursive             # Also sync nested submodules
git submodule foreach <command>            # Run command in each submodule
git submodule foreach --recursive <cmd>    # Recurse into nested submodules
git submodule foreach --quiet <cmd>        # Don't print each submodule name
git submodule summary                      # Show submodule commit summary
git submodule summary <commit>             # Summary relative to commit
git submodule summary --files              # Compare index vs working tree
git submodule summary --cached             # Compare HEAD vs index
git submodule summary -n <n>              # Limit commits shown
git submodule set-branch -b <branch> <path>  # Set tracking branch
git submodule set-branch --default <path>    # Unset tracking branch
git submodule set-url <path> <url>           # Change submodule URL
git submodule absorbgitdirs                  # Move submodule .git into superproject
git submodule absorbgitdirs --dry-run        # Show what would be absorbed
git submodule absorbgitdirs -v               # Verbose output
```

---

## Advanced Tools

### `git reflog`

Shows the history of where HEAD and branch refs have pointed.

```bash
git reflog                         # Show HEAD reflog
git reflog show                    # Same as git reflog
git reflog show HEAD               # Show HEAD reflog
git reflog show <branch>           # Show reflog for a specific branch
git reflog show --all              # Show reflog for all refs
git reflog --oneline               # One entry per line
git reflog -n <n>                  # Show last n entries
git reflog --date=<format>         # Show dates in specified format
git reflog --format=<format>       # Custom format
git reflog expire                  # Expire old reflog entries
git reflog expire --all            # Expire entries in all refs
git reflog expire --expire=<date>  # Expire entries older than date
git reflog expire --expire-unreachable=<date>  # Expire unreachable entries
git reflog expire --dry-run        # Show what would be expired
git reflog expire --verbose        # Verbose output
git reflog expire --stale-fix      # Fix stale pack refs
git reflog delete                  # Delete specific reflog entries
git reflog delete HEAD@{2}         # Delete specific entry
git reflog delete --dry-run        # Show what would be deleted
git reflog exists <ref>            # Check if reflog exists for ref
```

### `git stash` (see Stashing section)

### `git clean`

Removes untracked files from the working directory.

```bash
git clean -n                       # Dry run (show what would be removed)
git clean --dry-run                # Same as -n
git clean -f                       # Force remove untracked files
git clean --force                  # Same as -f
git clean -d                       # Also remove untracked directories
git clean -fd                      # Force remove untracked files and dirs
git clean -i                       # Interactive mode
git clean --interactive            # Same as -i
git clean -x                       # Also remove ignored files
git clean -fx                      # Force remove including ignored files
git clean -X                       # Remove ONLY ignored files (keep untracked)
git clean -fX                      # Force remove only ignored files
git clean -fdx                     # Remove everything: untracked + ignored + dirs
git clean -e <pattern>             # Exclude pattern from removal
git clean --exclude=<pattern>      # Same as -e
git clean -q                       # Quiet output
git clean --quiet                  # Same as -q
git clean -v                       # Verbose output
git clean -- <path>                # Clean specific path only
```

### `git archive`

Creates an archive of files from a tree.

```bash
git archive HEAD                   # Archive current HEAD (tar to stdout)
git archive --format=tar HEAD      # Specify tar format
git archive --format=zip HEAD      # Specify zip format
git archive --format=tar.gz HEAD   # Compress with gzip
git archive HEAD > output.tar      # Redirect to file
git archive -o output.tar.gz HEAD  # Output to file (format from extension)
git archive --output=file.tar HEAD # Same as -o
git archive --prefix=project/ HEAD # Add prefix to all files
git archive HEAD -- path/          # Archive only a subdirectory
git archive <tag>                  # Archive a specific tag
git archive <commit>               # Archive a specific commit
git archive <branch>               # Archive a specific branch
git archive --list                 # Show available formats
git archive --worktree-attributes  # Use .gitattributes from working tree
git archive --remote=<url> HEAD    # Archive from a remote repository
git archive --exec=<cmd> HEAD      # Use custom upload-archive on remote
git archive -v HEAD                # Verbose output
git archive --verbose HEAD         # Same as -v
git archive -9 HEAD                # Maximum compression (for gzip/zip)
git archive -0 HEAD                # No compression
```

### `git gc`

Cleans up and optimizes the local repository.

```bash
git gc                             # Run cleanup (packs, prunes, etc.)
git gc --aggressive                # More thorough optimization (slower)
git gc --auto                      # Run only if needed (check thresholds)
git gc --prune=<date>              # Prune objects older than date
git gc --prune=now                 # Prune all unreachable objects immediately
git gc --no-prune                  # Don't prune unreachable objects
git gc --keep-largest-pack         # Keep the largest pack file
git gc --quiet                     # Suppress output
git gc -q                          # Same as --quiet
git gc --force                     # Force GC even if another is in progress
```

### `git fsck`

Verifies the integrity of the Git object database.

```bash
git fsck                           # Check object database integrity
git fsck --strict                  # Be stricter in checks
git fsck --full                    # Check all objects
git fsck --unreachable             # Show unreachable objects
git fsck --dangling                # Show dangling objects (default)
git fsck --no-dangling             # Don't show dangling objects
git fsck --root                    # Report root nodes
git fsck --tags                    # Report tags
git fsck --cache                   # Check index for consistency
git fsck --reflogs                 # Check reflogs
git fsck --no-reflogs              # Don't check reflogs
git fsck --lost-found              # Write dangling objects to .git/lost-found/
git fsck --name-objects            # Show names for objects where possible
git fsck --connectivity-only       # Only check connectivity
git fsck --progress                # Show progress
git fsck --no-progress             # Don't show progress
git fsck -v                        # Verbose output
git fsck --verbose                 # Same as -v
git fsck <hash>                    # Check specific object
```

### `git bundle`

Creates and inspects bundle files.

```bash
git bundle create <file> <refs>    # Create a bundle file
git bundle create file.bundle HEAD # Bundle current branch
git bundle create file.bundle --all # Bundle all refs
git bundle create file.bundle -n <n>  # Bundle last n commits
git bundle create file.bundle --since=<date> HEAD  # Bundle since date
git bundle create file.bundle <since>..<until>     # Bundle range
git bundle verify <file>           # Verify a bundle is valid
git bundle list-heads <file>       # List refs in bundle
git bundle unbundle <file>         # Extract objects from bundle
git bundle unbundle <file> <refs>  # Extract specific refs
git clone <file>.bundle <dir>      # Clone from a bundle
git fetch <file>.bundle <ref>      # Fetch from a bundle
git pull <file>.bundle <ref>       # Pull from a bundle
```

### `git format-patch`

Prepares patches for email submission.

```bash
git format-patch <commit>          # Create patches from commit to HEAD
git format-patch <since>..<until>  # Create patches for a range
git format-patch -n <n>            # Create patches for last n commits
git format-patch HEAD~3            # Patches for last 3 commits
git format-patch -o <dir>          # Output patches to directory
git format-patch --output-directory=<dir>  # Same as -o
git format-patch -s                # Add Signed-off-by
git format-patch --signoff         # Same as -s
git format-patch -S                # GPG sign patches
git format-patch --gpg-sign        # Same as -S
git format-patch --stdout          # Output to stdout (pipe to send-email)
git format-patch -k                # Don't strip [PATCH] from subject
git format-patch --keep-subject    # Same as -k
git format-patch --no-numbered     # Don't prefix [PATCH n/m]
git format-patch -N                # Same as --no-numbered
git format-patch --numbered        # Always prefix [PATCH n/m]
git format-patch -n                # Numbered patches
git format-patch --start-number=<n>  # Start numbering from n
git format-patch -v <n>            # Version of the patch set
git format-patch --reroll-count=<n>  # Same as -v
git format-patch --subject-prefix="PATCH"  # Custom subject prefix
git format-patch -p                # Include patch (default)
git format-patch --no-patch        # Don't include patch
git format-patch --stat            # Include diffstat
git format-patch --no-stat         # Don't include diffstat
git format-patch -U<n>             # Context lines in diff
git format-patch --unified=<n>     # Same as -U
git format-patch --diff-algorithm=<alg>  # Set diff algorithm
git format-patch --thread          # Add In-Reply-To/References headers
git format-patch --thread=shallow  # Thread only against cover letter
git format-patch --no-thread       # Don't add thread headers
git format-patch --in-reply-to=<msg>     # Reply-to message ID
git format-patch --cover-letter    # Create a cover letter patch
git format-patch --cover-from-description=<mode>  # Set cover from branch desc
git format-patch --ignore-if-in-upstream   # Don't output patches already upstream
git format-patch --base=<commit>   # Record base tree info
git format-patch --base=auto       # Automatically find base
git format-patch --rfc             # Shorthand for --subject-prefix="RFC PATCH"
git format-patch --interdiff=<rev> # Show interdiff of patch against previous version
git format-patch --range-diff=<rev>  # Show range-diff with previous version
git format-patch --creation-factor=<n>  # Percentage of similarity for range-diff
git format-patch --progress        # Show progress
git format-patch --add-header=<header>  # Add custom header
git format-patch --to=<address>    # Add To: header
git format-patch --cc=<address>    # Add Cc: header
git format-patch --from            # Use current committer identity for From header
git format-patch --from=<ident>    # Set From header
git format-patch --[no-]attach     # Add [no-]attachment to patch
git format-patch --inline          # Inline patches
git format-patch --suffix=<suffix> # Use custom suffix (default: .patch)
git format-patch --filename-max-length=<n>  # Set max filename length
git format-patch --no-binary       # Don't output binary changes
git format-patch --zero-commit     # Use all zeros as commit hash
git format-patch --notes           # Include notes
git format-patch --no-notes        # Don't include notes
git format-patch --notes=<ref>     # Include notes from specific ref
```

### `git apply`

Applies a patch to files.

```bash
git apply <patch>                  # Apply a patch file
git apply --stat <patch>           # Show statistics without applying
git apply --numstat <patch>        # Machine-readable statistics
git apply --check <patch>          # Check if patch applies cleanly
git apply -v <patch>               # Verbose output
git apply --verbose <patch>        # Same as -v
git apply -R <patch>               # Apply patch in reverse
git apply --reverse <patch>        # Same as -R
git apply --index <patch>          # Apply to both working tree and index
git apply --cached <patch>         # Apply to index only (no working tree)
git apply --3way <patch>           # 3-way merge on conflict
git apply -3 <patch>               # Same as --3way
git apply --reject <patch>         # Leave reject files on conflict
git apply --allow-overlap <patch>  # Allow overlapping hunks
git apply --whitespace=<action>    # Handle whitespace: nowarn, warn, fix, error
git apply --ignore-whitespace      # Ignore whitespace differences
git apply -p<n> <patch>            # Strip n leading path components
git apply --directory=<root>       # Apply patch relative to directory
git apply --include=<pattern>      # Apply only to matching files
git apply --exclude=<pattern>      # Exclude matching files from patch
git apply --binary                 # Apply binary patches
git apply --no-add                 # Only apply hunks that remove lines
git apply --unidiff-zero           # Allow hunks with zero context
git apply --inaccurate-eof         # Allow missing newline at end of file
git apply --recount                # Don't trust hunk count header
git apply --unsafe-paths           # Allow paths outside working tree
git apply --allow-empty            # Accept empty patches
```

### `git am`

Applies patches from a mailbox.

```bash
git am <mbox>                      # Apply patches from mailbox file
git am *.patch                     # Apply multiple patch files
git am --continue                  # Continue after resolving conflict
git am --resolved                  # Same as --continue
git am --abort                     # Abort current patching operation
git am --skip                      # Skip current patch
git am --quit                      # Abort but keep applied commits
git am -3                          # Use 3-way merge
git am --3way                      # Same as -3
git am --no-3way                   # Don't use 3-way merge
git am -s                          # Add Signed-off-by line
git am --signoff                   # Same as -s
git am -S                          # GPG sign applied commits
git am --gpg-sign                  # Same as -S
git am --no-gpg-sign               # Don't GPG sign
git am -k                          # Keep email subject as-is
git am --keep                      # Same as -k
git am --keep-cr                   # Keep CR at end of lines
git am --no-keep-cr                # Strip CR (default)
git am --message-id                # Copy Message-ID as Notes
git am -c <scissors>               # Cut email at scissors line
git am --scissors                  # Remove everything before scissors
git am --no-scissors               # Don't cut at scissors
git am -p<n>                       # Strip n leading path components
git am --directory=<dir>           # Apply patches relative to dir
git am --exclude=<pattern>         # Exclude files from patching
git am --include=<pattern>         # Only patch matching files
git am --reject                    # Leave .rej files on failure
git am --patch-format=<format>     # Specify patch format
git am --whitespace=<action>       # Whitespace handling
git am --ignore-whitespace         # Ignore whitespace
git am --ignore-date               # Ignore dates in patches
git am --committer-date-is-author-date  # Use author date as committer date
git am --empty=<option>            # How to handle empty commits: drop, keep, stop
git am --quiet                     # Suppress output
git am -q                          # Same as --quiet
git am -v                          # Verbose output
git am --no-verify                 # Skip pre-applypatch and applypatch-msg hooks
git am --show-current-patch        # Show current patch being applied
git am --show-current-patch=raw    # Show the raw patch
git am --show-current-patch=diff   # Show only the diff
git am --quoted-cr=<action>        # How to handle quoted CR
git am --utf8                      # Assume mbox is UTF-8 encoded
git am --no-utf8                   # Don't assume UTF-8
```

### `git notes`

Adds, removes, and reads notes attached to objects.

```bash
git notes                          # List all notes
git notes list                     # Same as git notes
git notes list <object>            # Show note for specific object
git notes add                      # Add note to current commit
git notes add <object>             # Add note to specific object
git notes add -m "message"         # Add note with message
git notes add -F <file>            # Read note from file
git notes add -C <object>          # Use note from another object
git notes add -c <object>          # Use note from object, open editor
git notes add --allow-empty        # Allow empty note
git notes add -f                   # Force overwrite existing note
git notes add --force              # Same as -f
git notes edit                     # Edit note on current commit
git notes edit <object>            # Edit note on specific object
git notes edit --allow-empty       # Allow editing to empty
git notes show                     # Show note on current commit
git notes show <object>            # Show note on specific object
git notes remove                   # Remove note from current commit
git notes remove <object>          # Remove note from specific object
git notes remove --ignore-missing  # Don't fail if no note exists
git notes copy <from> <to>         # Copy note from one object to another
git notes copy -f                  # Force overwrite
git notes append                   # Append to note on current commit
git notes append -m "text"         # Append text to note
git notes merge <notes-ref>        # Merge notes from another ref
git notes merge --commit           # Finalize manual notes merge
git notes merge --abort            # Abort notes merge
git notes get-ref                  # Print current notes ref
git notes prune                    # Remove notes for non-existent objects
git notes prune -v                 # Verbose prune
git notes prune -n                 # Dry run
```

### `git describe`

Gives a human-readable name to a commit based on the closest tag.

```bash
git describe                       # Describe current commit
git describe <commit>              # Describe specific commit
git describe HEAD                  # Describe HEAD
git describe --tags                # Also consider lightweight tags
git describe --all                 # Use all refs (branches, tags)
git describe --exact-match         # Only output exact tag name
git describe --long                # Always show long format
git describe --abbrev=<n>          # Use n characters for abbreviated hash
git describe --dirty               # Add suffix if working tree is dirty
git describe --dirty=<suffix>      # Custom dirty suffix
git describe --broken              # Show description even with broken tags
git describe --candidates=<n>      # Consider n tag candidates
git describe --match <pattern>     # Only consider tags matching pattern
git describe --exclude <pattern>   # Exclude tags matching pattern
git describe --always              # Show abbreviated hash even without tags
git describe --contains            # Describe commit relative to newer tags
git describe --first-parent        # Only follow first parent
```

### `git shortlog` (see Inspecting section)

### `git ls-files`

Shows information about files in the index and working tree.

```bash
git ls-files                       # List tracked files in index
git ls-files -c                    # Show cached (tracked) files (default)
git ls-files --cached              # Same as -c
git ls-files -d                    # Show deleted files
git ls-files --deleted             # Same as -d
git ls-files -m                    # Show modified files
git ls-files --modified            # Same as -m
git ls-files -o                    # Show untracked files
git ls-files --others              # Same as -o
git ls-files -i                    # Show ignored files
git ls-files --ignored             # Same as -i
git ls-files -s                    # Show staged files with mode and hash
git ls-files --stage               # Same as -s
git ls-files -u                    # Show unmerged files
git ls-files --unmerged            # Same as -u
git ls-files -k                    # Show files to be killed (would be overwritten)
git ls-files --killed              # Same as -k
git ls-files -t                    # Tag each filename with status
git ls-files --tag                 # Same as -t
git ls-files -v                    # Show files with lowercase letter if assumed unchanged
git ls-files -f                    # Show files with fsmonitor dirty flag
git ls-files -h                    # Show files with resolve-undo
git ls-files -H                    # Same as -h
git ls-files --full-name           # Show full path relative to repo root
git ls-files --abbrev=<n>          # Abbreviate object hashes
git ls-files --exclude=<pattern>   # Skip files matching pattern
git ls-files -x <pattern>          # Same as --exclude
git ls-files --exclude-from=<file> # Read exclude patterns from file
git ls-files -X <file>             # Same as --exclude-from
git ls-files --exclude-standard    # Use standard git exclude files
git ls-files --error-unmatch       # Exit with error if file not in index
git ls-files --with-tree=<tree>    # Show files as if tree were HEAD
git ls-files -z                    # NUL-terminate output
git ls-files --null                # Same as -z
git ls-files --eol                 # Show end-of-line info
git ls-files --recurse-submodules  # Recurse into submodules
git ls-files --sparse              # Show sparse index entries
git ls-files -- <path>             # Restrict to path
```

### `git ls-tree`

Lists the contents of a tree object.

```bash
git ls-tree HEAD                   # List tree at HEAD
git ls-tree <commit>               # List tree at specific commit
git ls-tree <tree-hash>            # List tree object directly
git ls-tree HEAD -- <path>         # List specific path
git ls-tree -r HEAD                # Recurse into subtrees
git ls-tree -r -t HEAD             # Recurse and show trees too
git ls-tree -l HEAD                # Show object sizes
git ls-tree --long HEAD            # Same as -l
git ls-tree --name-only HEAD       # Show only names
git ls-tree --name-status HEAD     # Same as --name-only (for ls-tree)
git ls-tree -z HEAD                # NUL-terminate output
git ls-tree --null HEAD            # Same as -z
git ls-tree --full-name HEAD       # Show full pathnames
git ls-tree --full-tree HEAD       # Don't limit to current directory
git ls-tree --abbrev=<n>           # Abbreviate object hashes
git ls-tree -d HEAD                # Show only trees (directories)
git ls-tree --format=<format>      # Custom output format
```

### `git cat-file`

Provides content or type information for repository objects.

```bash
git cat-file -t <hash>             # Show type of object
git cat-file -s <hash>             # Show size of object
git cat-file -p <hash>             # Pretty-print object content
git cat-file -e <hash>             # Exit 0 if object exists, 1 otherwise
git cat-file blob <hash>           # Show blob content
git cat-file tree <hash>           # Show tree content
git cat-file commit <hash>         # Show commit content
git cat-file tag <hash>            # Show tag content
git cat-file --batch               # Read objects from stdin, print info + content
git cat-file --batch=<format>      # Custom format for batch output
git cat-file --batch-check         # Like --batch but without content
git cat-file --batch-check=<fmt>   # Custom format for batch-check
git cat-file --batch-all-objects   # Iterate over all objects
git cat-file --allow-unknown-type  # Allow unknown types
git cat-file --follow-symlinks     # Follow symlinks
git cat-file --textconv <path>@<rev>  # Apply textconv filter
git cat-file --filters <path>@<rev>   # Apply file filters
git cat-file --path=<path>         # Use filters for given path
git cat-file --buffer              # Buffer output
git cat-file --no-buffer           # Don't buffer output
git cat-file -z                    # NUL-terminate input for --batch
```

### `git hash-object`

Computes the object ID of a file.

```bash
git hash-object <file>             # Compute hash of file (without storing)
git hash-object -w <file>          # Compute hash and write to object store
git hash-object --write <file>     # Same as -w
git hash-object -t blob <file>     # Specify object type
git hash-object -t tree <file>     # Hash as tree
git hash-object -t commit <file>   # Hash as commit
git hash-object -t tag <file>      # Hash as tag
git hash-object --stdin            # Read from stdin
git hash-object --stdin-paths      # Read file paths from stdin
git hash-object --path=<path>      # Use given path for attribute lookup
git hash-object --no-filters       # Don't apply smudge filters
git hash-object --literally        # Don't validate object type
```

---

## Plumbing Commands

These are lower-level commands that Git uses internally.

```bash
# Object manipulation
git write-tree                     # Create tree object from index
git read-tree <tree>               # Read tree into index
git commit-tree <tree>             # Create commit object
git update-ref <ref> <hash>        # Update the object stored in a ref
git symbolic-ref HEAD              # Read/write symbolic refs
git symbolic-ref HEAD refs/heads/main  # Set HEAD to branch
git update-index --add <file>      # Add file directly to index
git update-index --chmod=+x <file> # Update executable bit in index
git update-index --assume-unchanged <file>  # Mark file as not changing

# Refs
git rev-parse HEAD                 # Show hash of HEAD
git rev-parse <ref>                # Resolve ref to hash
git rev-parse --abbrev-ref HEAD    # Show current branch name
git rev-parse --show-toplevel      # Show repo root directory
git rev-parse --git-dir            # Show .git directory path
git rev-parse --is-inside-work-tree  # Check if in work tree

# Pack files
git pack-objects <basename>        # Create packed archive of objects
git unpack-objects                 # Unpack objects from a pack file
git index-pack <packfile>          # Build index for existing pack file
git pack-refs --all                # Pack loose refs
git prune-packed                   # Remove loose objects that exist in packs
git prune                          # Remove unreachable loose objects
git repack                         # Repack object files

# Debugging/plumbing
git merge-base <commit1> <commit2> # Find common ancestor
git merge-base --all               # Find all common ancestors
git merge-base --is-ancestor <a> <b>  # Check if a is ancestor of b
git merge-base --octopus           # Find merge base for multi-way merge
git rev-list HEAD                  # List commit objects reachable from HEAD
git rev-list --count HEAD          # Count commits reachable from HEAD
git rev-list --ancestry-path A..B  # List commits strictly between A and B
git count-objects                  # Count loose objects and disk space
git count-objects -v               # Verbose output
git for-each-ref                   # Iterate over all refs
git for-each-ref --format="%(refname:short)" refs/heads/  # List branch names

# Merge internals
git merge-file <current> <base> <other>  # 3-way merge of files
git merge-tree <base> <branch1> <branch2>  # Merge trees
git merge-index <merge-program> -a  # Run merge program on files in index

# Sending/receiving
git send-pack <remote>             # Push to remote (low-level)
git receive-pack <dir>             # Receive pack from remote (server-side)
git upload-pack <dir>              # Send pack to client (server-side)
git fetch-pack <remote>            # Fetch from remote (low-level)

# diff internals  
git diff-tree <tree1> <tree2>      # Compare two tree objects
git diff-index <tree>              # Compare tree with index or working tree
git diff-files                     # Compare index with working files
git diff-blob <blob1> <blob2>      # Compare two blob objects

# Other plumbing
git stripspace                     # Remove trailing whitespace
git check-attr <attr> -- <file>    # Check gitattribute for file
git check-ignore <file>            # Check if file is ignored
git check-ignore -v <file>         # Show which .gitignore rule matches
git check-mailmap <identity>       # Check mailmap canonical identity
git check-ref-format <refname>     # Check if ref name is valid
git interpret-trailers             # Add/parse trailer lines in commit messages
git mailinfo                       # Extract metadata from an email
git mailsplit                      # Split mbox into individual messages
git credential <action>            # Retrieve/store credentials
git credential-cache               # Cache credentials in memory
git credential-store               # Store credentials in file
git fast-import                    # Fast backend for importing data
git fast-export                    # Export repository as fast-import stream
git replace                        # Create/list/delete replace refs
git pack-redundant                 # Find redundant pack files
git unpack-file <blob>             # Create temp file with blob contents
git get-tar-commit-id              # Extract commit ID from git archive
git sh-i18n--envsubst              # Environment variable substitution for i18n
git imap-send                      # Send patches to IMAP folder
git request-pull <start> <url>     # Generate pull request summary
git patch-id                       # Compute unique patch IDs
git show-index <packidx>           # Show packed archive index
git show-ref                       # List references in local repository
git show-ref --heads               # List branch refs only
git show-ref --tags                # List tag refs only
git show-ref --verify HEAD         # Verify that a ref exists
git verify-pack <packfile>         # Validate packed git archive
git verify-tag <tag>               # Check the GPG signature of a tag
git verify-commit <commit>         # Check the GPG signature of a commit
git column                         # Display data in columns
git credential-helper-selector     # Select credential helper
```

---

## `.gitignore`

The `.gitignore` file specifies patterns for files that Git should ignore.

### Pattern Rules

```
# Comment line
*.log             # Ignore all .log files
!important.log    # But track important.log
/TODO             # Ignore TODO only at repo root
build/            # Ignore build directory anywhere
doc/*.txt         # Ignore txt files directly in doc/
doc/**/*.txt      # Ignore txt files anywhere under doc/
**/logs           # Ignore directories named logs anywhere
logs/**           # Ignore everything inside logs/
logs/**/debug.log # Ignore debug.log in any subdir of logs/
```

### Pattern Syntax

| Pattern | Description |
|---|---|
| `#` | Comment |
| `*` | Match anything except `/` |
| `**` | Match anything including `/` |
| `?` | Match any one character except `/` |
| `[abc]` | Match any character in set |
| `[a-z]` | Match any character in range |
| `!` | Negate a pattern |
| Trailing `/` | Match only directories |
| Leading `/` | Anchor to repo root |

### Multiple `.gitignore` Files

- **Repo-level:** `.gitignore` (committed, shared with team)
- **Directory-level:** `.gitignore` in any subdirectory
- **User-level:** `~/.config/git/ignore` (global, not committed)
- **Repo-specific:** `.git/info/exclude` (local only, not committed)

```bash
# Check why a file is ignored
git check-ignore -v filename

# Show all ignored files
git status --ignored

# Force-add an ignored file
git add -f ignored-file.log
```

---

## Hooks

Git hooks are scripts executed automatically at specific points in the Git workflow. They live in `.git/hooks/`.

### Client-Side Hooks

| Hook | Trigger | Use Case |
|---|---|---|
| `pre-commit` | Before `git commit` | Run linters, tests |
| `prepare-commit-msg` | After default message created | Modify default message |
| `commit-msg` | After message entered | Validate message format |
| `post-commit` | After commit created | Notifications |
| `pre-rebase` | Before `git rebase` | Prevent dangerous rebases |
| `post-checkout` | After `git checkout` | Update environment |
| `post-merge` | After `git merge` | Restore file permissions |
| `pre-push` | Before `git push` | Run tests before push |
| `pre-auto-gc` | Before automatic GC | Skip auto-cleanup |
| `post-rewrite` | After commands rewriting commits | Notifications |
| `fsmonitor-watchman` | Filesystem monitoring | Speed up status |

### Server-Side Hooks

| Hook | Trigger | Use Case |
|---|---|---|
| `pre-receive` | Before refs are updated | Access control |
| `update` | Before each ref is updated | Per-ref policy |
| `post-receive` | After all refs updated | CI/CD triggers |
| `post-update` | After all refs updated (older) | Update server info |
| `pre-auto-gc` | Before automatic GC | Skip auto-cleanup |
| `post-rereceive` | After refs are updated | Notifications |

### Creating Hooks

```bash
# Example pre-commit hook: run tests
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/sh
npm test
if [ $? -ne 0 ]; then
    echo "Tests failed. Commit aborted."
    exit 1
fi
EOF
chmod +x .git/hooks/pre-commit
```

### Skipping Hooks

```bash
git commit --no-verify             # Skip pre-commit and commit-msg hooks
git push --no-verify               # Skip pre-push hook
git merge --no-verify              # Skip merge hooks
```

---

## Git Attributes

`.gitattributes` controls how Git treats files.

```
# Force text/binary treatment
*.jpg binary
*.png binary
*.txt text

# Line ending normalization
*.sh text eol=lf
*.bat text eol=crlf
*.cs text=auto

# Diff behavior
*.docx diff=word
*.pdf diff=pdf

# Merge strategy per file
database.xml merge=ours

# Export ignore (omit from git archive)
tests/ export-ignore
.github/ export-ignore

# Linguist overrides (GitHub language stats)
vendor/* linguist-vendored
*.js linguist-language=TypeScript

# Specify diff driver
*.md diff=markdown
```

---

## Common Workflows

### Feature Branch Workflow

```bash
git checkout -b feature/my-feature main
# ... make changes ...
git add .
git commit -m "feat: add my feature"
git push -u origin feature/my-feature
# ... create PR on GitHub/GitLab ...
git checkout main
git merge --no-ff feature/my-feature
git push origin main
git branch -d feature/my-feature
git push origin --delete feature/my-feature
```

### Hotfix Workflow

```bash
git checkout -b hotfix/critical-bug main
git commit -am "fix: critical security patch"
git checkout main && git merge --no-ff hotfix/critical-bug
git tag -a v1.0.1 -m "Hotfix v1.0.1"
git push origin main --tags
git branch -d hotfix/critical-bug
```

### Interactive Rebase Cleanup

```bash
git rebase -i HEAD~5               # Clean up last 5 commits before PR
# In editor: squash fixup commits, reword messages
git push --force-with-lease        # Update remote branch safely
```

### Recovering Lost Commits

```bash
git reflog                         # Find the commit hash
git checkout -b recovery <hash>    # Create branch at lost commit
# or
git cherry-pick <hash>             # Cherry-pick it onto current branch
```

### Bisecting a Bug

```bash
git bisect start
git bisect bad HEAD
git bisect good v2.0
# Git checks out midpoint
# Test, then:
git bisect good   # or git bisect bad
# Repeat until Git identifies the culprit commit
git bisect reset  # Return to original state
```

### Splitting a Commit

```bash
git rebase -i HEAD~1               # Pick the commit as 'edit'
git reset HEAD^                    # Unstage all changes
git add -p                         # Stage first part
git commit -m "first part"
git add .                          # Stage rest
git commit -m "second part"
git rebase --continue
```

---

## Revision Syntax

Git uses a flexible syntax for specifying commits and ranges:

| Syntax | Meaning |
|---|---|
| `HEAD` | Current commit |
| `HEAD~1` or `HEAD~` | One commit before HEAD |
| `HEAD~n` | n commits before HEAD (first parent) |
| `HEAD^` | First parent of HEAD |
| `HEAD^2` | Second parent of HEAD (merge commit) |
| `HEAD^n` | nth parent of HEAD |
| `HEAD^^` | Grandparent (parent of parent) |
| `<hash>` | Specific commit by hash |
| `<hash>^{commit}` | Dereference to commit |
| `<branch>` | Tip of a branch |
| `<tag>` | A tag |
| `<remote>/<branch>` | Remote tracking branch |
| `@{upstream}` or `@{u}` | Upstream of current branch |
| `@{push}` | Push destination |
| `@{-1}` | Previously checked out branch |
| `<ref>@{n}` | nth prior value of ref in reflog |
| `<ref>@{date}` | Ref at a specific date |
| `<commit>:<path>` | File at a commit |
| `:<n>:<path>` | Staged file (n: 0=merged, 1=orig, 2=ours, 3=theirs) |

**Range syntax:**

| Syntax | Meaning |
|---|---|
| `A..B` | Commits in B but not A (reachable from B, not from A) |
| `A...B` | Commits in A or B but not both (symmetric difference) |
| `^A B` | Same as `A..B` |
| `A B --not C` | In A or B but not C |

---

## Useful Aliases

```bash
# ~/.gitconfig [alias] section

[alias]
    # Status
    st = status
    s = status -sb

    # Logging
    lg = log --oneline --graph --all --decorate
    ll = log --pretty=format:"%C(yellow)%h%Cred%d %Creset%s%Cblue [%cn]" --decorate --numstat
    hist = log --pretty=format:\"%h %ad | %s%d [%an]\" --graph --date=short

    # Branching
    co = checkout
    sw = switch
    br = branch
    bra = branch -a

    # Staging/committing
    aa = add --all
    ap = add --patch
    cm = commit -m
    ca = commit --amend --no-edit

    # Diff
    df = diff
    dc = diff --cached
    dw = diff --word-diff

    # Stash
    ss = stash push
    sp = stash pop
    sl = stash list

    # Undoing
    unstage = restore --staged
    undo = reset --soft HEAD~1
    discard = restore .

    # Remote
    pu = push -u origin HEAD
    pl = pull --rebase

    # Utils
    root = rev-parse --show-toplevel
    aliases = config --get-regexp alias
    contributors = shortlog -sn
    filecount = ls-files | wc -l
    ignored = status --ignored
    tags = tag --list --sort=-version:refname
    whoami = !git config user.name && git config user.email
    cleanup = !git branch --merged | grep -v main | grep -v master | grep -v HEAD | xargs git branch -d
```

<br><br>