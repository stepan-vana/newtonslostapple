{[date]Last updated June 15, 2026}
# GitHub Overview
{[author]{pp::stepan-vana}Štěpán Váňa}
{[read_time]15 min read}
{[copy_raw_file]Copy Markdown}
{[view_as_md]View as Markdown}

GitHub is a web-based hosting service for version control using Git. It provides the distributed version control and source code management (SCM) functionality of Git, plus its own features. While Git manages the local history, GitHub acts as the central hub for collaboration, code review, continuous integration, and project management.

## How GitHub Works

Unlike Git, which is a command-line tool, GitHub revolves around a centralized web interface and APIs. Developers interact with GitHub via the Web UI, Git CLI (pushing/pulling to remotes), the GitHub CLI (`gh`), or the REST/GraphQL APIs.

### Core Concepts

Every project on GitHub utilizes a few foundational components:

| Concept | Description |
|---|---|
| **Repository** | The remote container for your project, including all files, history, issues, and PRs. |
| **Fork** | A personal copy of another user's repository that lives in your account. |
| **Pull Request (PR)** | A request to merge changes from one branch (or fork) into another, facilitating code review. |
| **Issue** | An item used to track bugs, enhancements, or other requests. |
| **Action** | An automated workflow (CI/CD) defined in YAML, triggered by GitHub events. |

---

## Authentication & Configuration

### `gh auth`

Manages authentication for the GitHub CLI.

```bash
gh auth login                      # Interactive prompt to authenticate
gh auth login --with-token         # Read token from standard input
gh auth login -w                   # Authenticate via web browser
gh auth logout                     # Log out of a GitHub host
gh auth status                     # View authentication status
gh auth setup-git                  # Configure git to use GitHub CLI as a credential helper
gh auth token                      # Print the auth token gh is configured to use

```

**Common CLI configuration (`gh config`):**

```bash
gh config set editor "vim"         # Set default editor for PRs and issues
gh config set editor "code --wait" # Set VS Code as default editor
gh config set git_protocol ssh     # Use SSH instead of HTTPS for clones
gh config set prompt enabled       # Enable interactive prompts (default)
gh config get editor               # Retrieve a config value
gh config list                     # List all config values

```

---

## Managing Repositories

### `gh repo`

Creates, clones, forks, and views repositories.

```bash
gh repo create                     # Create a new repository interactively
gh repo create <name> --public     # Create a public repository
gh repo create <name> --private    # Create a private repository
gh repo create <name> --template <repo> # Create repo from a template
gh repo create --source=. --push   # Create a remote for the current local dir and push
gh repo clone <repo>               # Clone a repository locally
gh repo clone <repo> <dir>         # Clone into a specific directory
gh repo fork                       # Fork the current repository
gh repo fork <repo> --clone        # Fork a specific repo and clone it locally
gh repo view                       # View repo description and README in terminal
gh repo view -w                    # Open the repository in a web browser
gh repo view --branch <branch>     # View a specific branch
gh repo edit                       # Edit repository settings
gh repo edit --default-branch main # Change the default branch
gh repo edit --visibility private  # Change repo visibility to private
gh repo sync                       # Sync local repo with remote (fetch and pull)
gh repo sync <owner>/<repo>        # Sync a fork with its parent
gh repo delete <repo>              # Delete a repository (requires extra scopes)
gh repo list                       # List repositories owned by you
gh repo list <org>                 # List repositories in an organization
gh repo list --language javascript # Filter list by language

```

---

## Pull Requests

### `gh pr`

Manages GitHub Pull Requests from the command line.

```bash
gh pr create                       # Create a PR interactively
gh pr create -t "Title" -b "Body"  # Create PR with specific title and body
gh pr create -B main -H feature    # PR from 'feature' into 'main'
gh pr create -d                    # Create PR as a Draft
gh pr create -f                    # Create PR using the last commit message as title/body
gh pr list                         # List open PRs
gh pr list -A <user>               # List PRs assigned to user
gh pr list -L <limit>              # Limit the number of returned PRs
gh pr list --state all             # List open and closed PRs
gh pr status                       # Show status of relevant PRs
gh pr view                         # View PR details in terminal
gh pr view <number>                # View specific PR
gh pr view -w                      # Open current PR in web browser
gh pr checkout <number>            # Checkout the branch associated with the PR
gh pr diff                         # View changes in the PR
gh pr diff --color always          # Force color output
gh pr review                       # Add a review to the PR interactively
gh pr review --approve             # Approve the PR
gh pr review --request-changes -b "Fix" # Request changes with a comment
gh pr review --comment -b "Nice!"  # Leave a comment without approving
gh pr merge                        # Merge the PR interactively
gh pr merge --squash               # Squash and merge the PR
gh pr merge --rebase               # Rebase and merge the PR
gh pr merge --admin                # Use administrator privileges to merge
gh pr merge -d                     # Delete the local and remote branch after merge
gh pr merge --auto                 # Enable auto-merge for the PR
gh pr close                        # Close the PR without merging
gh pr reopen                       # Reopen a closed PR
gh pr ready                        # Mark a draft PR as ready for review
gh pr edit                         # Edit PR title, body, reviewers, etc.
gh pr edit --add-reviewer <user>   # Request review from a user

```

---

## Issues

### `gh issue`

Creates, edits, and manages GitHub Issues.

```bash
gh issue create                    # Create an issue interactively
gh issue create -t "Bug" -b "Desc" # Create issue with title and body
gh issue create -a "@me"           # Assign issue to yourself
gh issue create -l "bug,help"      # Add labels to the new issue
gh issue list                      # List open issues
gh issue list --assignee "@me"     # List issues assigned to you
gh issue list --label "bug"        # List issues with specific label
gh issue list --state closed       # List closed issues
gh issue view                      # View issue in terminal
gh issue view <number>             # View specific issue
gh issue view -w                   # Open issue in web browser
gh issue view -c                   # View issue comments
gh issue comment <number> -b "Hi"  # Add a comment to an issue
gh issue close <number>            # Close an issue
gh issue close <number> -r "fixed" # Close issue with a specific reason (fixed, not planned)
gh issue reopen <number>           # Reopen a closed issue
gh issue edit <number>             # Edit issue details
gh issue edit --add-assignee <usr> # Assign user to issue
gh issue edit --add-label "docs"   # Add label to issue
gh issue edit --remove-label "bug" # Remove label from issue
gh issue edit --milestone "v1.0"   # Add issue to a milestone
gh issue transfer <num> <repo>     # Transfer issue to another repository
gh issue pin <number>              # Pin issue to the repository

```

---

## Continuous Integration (GitHub Actions)

### `gh workflow` and `gh run`

Manages and inspects GitHub Actions workflows and runs.

```bash
gh workflow list                   # List all workflows in the repository
gh workflow view <name>            # View workflow details
gh workflow view <name> -y         # View the raw YAML of the workflow
gh workflow enable <name>          # Enable a disabled workflow
gh workflow disable <name>         # Disable an active workflow
gh workflow run <name>             # Trigger a `workflow_dispatch` run
gh workflow run <name> -f key=val  # Trigger run with input parameters

gh run list                        # List recent workflow runs
gh run list -w <workflow-name>     # List runs for a specific workflow
gh run list --branch main          # List runs for a specific branch
gh run list --status failure       # List only failed runs
gh run view <run-id>               # View details of a specific run
gh run view <run-id> --log         # View full log for a run
gh run view <run-id> --log-failed  # View logs only for failed steps
gh run watch <run-id>              # Watch a run update in real-time
gh run cancel <run-id>             # Cancel an in-progress run
gh run rerun <run-id>              # Rerun a completed/failed run
gh run rerun <run-id> --failed     # Rerun only failed jobs
gh run download <run-id>           # Download artifacts generated by the run

```

### `.github/workflows/*.yml`

The core configuration file structure for GitHub Actions.

```yaml
# Basic workflow structure
name: CI                           # Name displayed in the Actions UI
on:                                # Triggers
  push:
    branches: [ "main", "develop" ]
  pull_request:
    branches: [ "main" ]
  workflow_dispatch:               # Allows manual triggering

jobs:
  build:                           # Job ID
    runs-on: ubuntu-latest         # Runner environment
    strategy:
      matrix:
        node-version: [16.x, 18.x] # Run multiple variations of the job
    
    steps:                         # Sequential tasks
    - name: Checkout repository
      uses: actions/checkout@v4    # Use a pre-built community action
      
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: ${{ matrix.node-version }}
        
    - name: Install dependencies
      run: npm ci                  # Execute shell command
      
    - name: Run tests
      run: npm test
      env:                         # Inject environment variables
        CI: true
        SECRET_TOKEN: ${{ secrets.GITHUB_TOKEN }}

```

**Common Triggers (`on:`):**

| Trigger | Description |
| --- | --- |
| `push` | Triggered when a push is made to specific branches/tags |
| `pull_request` | Triggered when a PR is opened, synced, or reopened |
| `schedule` | Triggered at specific times using cron syntax |
| `workflow_dispatch` | Manual trigger via UI, API, or CLI |
| `repository_dispatch` | Triggered by a webhook from external systems |
| `release` | Triggered when a release is published |

---

## Releases and Packages

### `gh release`

Manage GitHub Releases to distribute software.

```bash
gh release create <tag>            # Create a release for a specific tag
gh release create <tag> <files>    # Create release and upload asset files
gh release create <tag> -t "Name"  # Specify release title
gh release create <tag> -n "Notes" # Specify release notes
gh release create <tag> --generate-notes # Auto-generate notes from PRs
gh release create <tag> -d         # Create as a draft release
gh release create <tag> -p         # Mark as a pre-release
gh release list                    # List all releases in the repo
gh release view                    # View the latest release
gh release view <tag>              # View a specific release
gh release view -w                 # Open release in browser
gh release download                # Download all assets from the latest release
gh release download <tag>          # Download assets from a specific release
gh release download -p "*.zip"     # Download only files matching pattern
gh release upload <tag> <file>     # Upload a new asset to an existing release
gh release delete <tag>            # Delete a release

```

---

## Searching GitHub

GitHub provides powerful search qualifiers that can be used in the UI search bar or via the CLI (`gh search`).

```bash
gh search repos "machine learning" # Search for repositories
gh search repos --language python  # Search repos written in Python
gh search repos --stars ">1000"    # Search repos with >1000 stars
gh search code "TODO"              # Search code across GitHub
gh search code "TODO" --extension js # Search only in JavaScript files
gh search prs "bug fix"            # Search Pull Requests
gh search issues "crash"           # Search Issues
gh search issues --state open      # Search only open issues
gh search issues --assignee @me    # Search issues assigned to you

```

**UI Search Qualifiers:**

| Qualifier | Example | Description |
| --- | --- | --- |
| `in:` | `in:name,description` | Restrict search to specific fields |
| `user:` / `org:` | `org:github` | Search within a user or organization |
| `repo:` | `repo:torvalds/linux` | Search within a specific repository |
| `is:` | `is:pr is:open` | Filter by state (open, closed, merged) |
| `author:` | `author:defunkt` | Search by the creator |
| `mentions:` | `mentions:stepan-vana` | Issues/PRs where user is mentioned |
| `language:` | `language:rust` | Filter by programming language |
| `stars:` | `stars:10..50` | Filter by number of stars (range) |
| `pushed:` | `pushed:>2024-01-01` | Filter by last commit date |
| `size:` | `size:<10000` | Filter by repository size in KB |

---

## GitHub Specific Files

GitHub looks for specific files to enable community and project management features.

| File Path | Purpose |
| --- | --- |
| `README.md` | The landing page of your repository. |
| `LICENSE` | Defines the repository's open source license. |
| `.gitignore` | Files Git should ignore. |
| `.github/CODEOWNERS` | Defines individuals/teams that automatically get requested for review. |
| `.github/CONTRIBUTING.md` | Guidelines for how outside users should contribute. |
| `.github/ISSUE_TEMPLATE/*.yml` | Forms/markdown templates used when users create new Issues. |
| `.github/PULL_REQUEST_TEMPLATE.md` | Template populated in the body of new Pull Requests. |
| `.github/dependabot.yml` | Configuration for automated dependency updates. |
| `.github/security.md` | Instructions for responsibly reporting security vulnerabilities. |

---

## Common Workflows

### GitHub Flow

GitHub Flow is a lightweight, branch-based workflow.

1. **Create a branch:** `git checkout -b feature/new-button`
2. **Make changes and commit:** `git commit -m "Add new button"`
3. **Push to GitHub:** `git push origin feature/new-button`
4. **Open a Pull Request:** `gh pr create`
5. **Discuss and Review:** Address comments, push more commits if needed.
6. **Merge:** Merge the PR via UI or CLI (`gh pr merge`).
7. **Deploy & Cleanup:** Deploy main, delete the branch (`gh pr close -d`).

### Forking Workflow (Open Source)

Used heavily in open source where you do not have write access to the main repository.

```bash
gh repo fork <upstream-repo> --clone   # Create your fork and clone it
cd <repo>
git checkout -b fix/typo               # Create a branch on your fork
# ... make changes ...
git commit -m "Fix documentation typo"
git push origin fix/typo               # Push to your fork
gh pr create                           # Create a PR against the upstream repo
# Maintainers review and merge.
gh repo sync                           # Keep your local fork updated with upstream

```

---

## Useful GitHub CLI Aliases

You can configure aliases directly in the `gh` CLI to speed up common workflows.

```bash
# Set up aliases using: gh alias set <alias-name> '<command>'

gh alias set pv 'pr view -w'                 # Open current PR in browser
gh alias set rv 'repo view -w'               # Open current repo in browser
gh alias set co 'pr checkout'                # Quick PR checkout: gh co 123
gh alias set wip 'pr create -d -f'           # Quick create draft PR
gh alias set lspr 'pr list -A @me'           # List PRs assigned to me
gh alias set runs 'run list'                 # List workflow runs
gh alias set watch 'run watch'               # Watch latest run
gh alias set approve 'pr review --approve'   # Approve a PR
gh alias set me 'api user'                   # View my user data via API

```

---

## Keyboard Shortcuts (Web UI)

When browsing GitHub in a web browser, pressing `?` brings up a list of keyboard shortcuts.

| Shortcut | Context | Action |
| --- | --- | --- |
| `t` | Repository | Activate file finder (fuzzy search) |
| `s` or `/` | Anywhere | Focus the search bar |
| `.` (period) | Repository | Open the repository in github.dev (Web VS Code) |
| `y` | File view | Expand a URL to its canonical form (locks commit hash) |
| `b` | File view | Open Blame view |
| `l` | Code | Jump to line (when viewing a file) |
| `r` | Issue / PR | Reply to a comment or thread |
| `m` | Issue / PR | Focus the "Milestone" dropdown |
| `a` | Issue / PR | Focus the "Assignee" dropdown |
| `g` then `n` | Anywhere | Go to your Notifications |
| `Cmd` + `K` | Anywhere | Open the command palette |

<br><br>