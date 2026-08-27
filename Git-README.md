
### Git A to Z Commands with Explanation and Usage

Git is a distributed version control system used to track code changes and collaborate with teams.

1. Configure Git
Check Git Version
```
git --version
```

Usage: Verify Git installation.

Configure Username
```
git config --global user.name "Reddy Prasad"
```

Usage: Sets your Git username.

Configure Email
```
git config --global user.email "user@example.com"
```

Usage: Sets email associated with commits.

View Configuration
```
git config --list
```

Usage: Displays all Git configurations.

2. Repository Initialization
Create New Repository
```
git init
```

Usage: Initializes a new Git repository.

Clone Repository
```
git clone https://github.com/user/repo.git
2
```

Usage: Downloads an existing repository.

Clone Specific Branch
```
git clone -b develop https://github.com/user/repo.git
2
```

3. Repository Status Commands
Check Status
```
git status
```

Usage: Shows modified, staged, and untracked files.

View Commit History
```
git log
```
One-Line Log
```
git log --oneline
```

Example:

```
a1b2c3 Added login module
2
d4e5f6 Fixed bug
```
Detailed Log
```
git log --stat
```

Shows modified files and line changes.

4. Adding Files
```
git add file.txt
```
Add Multiple Files
```
git add file1.txt file2.txt
```
Add All Files
```
git add .
```

or

```
git add --all
```

Usage: Stages all changes.

5. Commit Commands
Commit Changes
```
git commit -m "Added new feature"
```
Add and Commit Together
```
git commit -am "Updated code"
2
 
```

Works only for tracked files.

Modify Last Commit Message
```
git commit --amend -m "Corrected commit message"
```
6. Branch Commands
List Branches
```
git branch
```
Create Branch
```
git branch feature-login
```
Switch Branch
```
git checkout feature-login
```
Create and Switch
```
git checkout -b feature-login
```

or newer syntax:

```
git switch -c feature-login
```
View All Branches
```
git branch -a
```
Delete Branch
```
git branch -d feature-login
```

Force delete:

```
git branch -D feature-login
```
7. Push Commands
Push Branch
```
git push origin main
```
First Push
```
git push -u origin main
```

Sets upstream branch.

Push All Branches
```
git push --all
```
8. Pull Commands
Pull Latest Changes
```
git pull
```

Equivalent to:

```
git fetch
2
git merge
```
Pull Specific Branch
```
git pull origin main
```
9. Fetch Commands
Fetch Updates
```
git fetch
2

```

Downloads changes but does not merge.

Fetch All Remotes
```
git fetch --all
```
10. Merge Commands
Merge Branch
```
git merge develop
```

Merges develop into current branch.

No Fast Forward Merge
```
git merge --no-ff develop
```

Creates separate merge commit.

11. Rebase Commands
Rebase Branch
```
git rebase main
2
```

Moves current branch commits on top of main.

Interactive Rebase
```
git rebase -i HEAD~5
```

Used to:

Squash commits
Edit commits
Reorder commits
12. Stash Commands
Save Current Changes
```
git stash
2
 
```
View Stash List
```
git stash list
```
Apply Stash
```
git stash apply
```
Apply and Remove
```
git stash pop
```
Delete Stash
```
git stash drop
```
13. Reset Commands
Soft Reset
```
git reset --soft HEAD~1
```

Removes commit but keeps changes staged.

Mixed Reset
```
git reset HEAD~1
```

Removes commit and unstages files.

Hard Reset
```
git reset --hard HEAD~1
```

Deletes commit and code changes.

⚠ Use carefully.

14. Revert Commands
Revert Commit
```
git revert <commit-id>
```

Creates new commit undoing changes.

Safe for shared repositories.

15. Remote Repository Commands
View Remotes
```
git remote -v
```
Add Remote
```
git remote add origin https://github.com/user/repo.git
2
 
```
Change Remote URL
```
git remote set-url origin https://github.com/new/repo.git
Show more lines
Remove Remote
```
git remote remove origin
```
16. Tag Commands
Create Tag
```
git tag v1.0
```
Annotated Tag
```
git tag -a v1.0 -m "Release v1.0"
```
Push Tag
```
git push origin v1.0
```
Push All Tags
```
git push origin --tags
```
17. Difference Commands
Compare Files
```
git diff
```
Compare Staged Changes
```
git diff --cached
```
Compare Branches
```
git diff main develop
```
18. Restore Commands
Restore File
```
git restore file.txt
```
Restore Staged File
```
git restore --staged file.txt
```
19. Cherry-Pick

Copy commit from another branch.

```
git cherry-pick commit-id
```

Example:

```
git cherry-pick ab1234
```
20. Blame Command
```
git blame file.txt
```

Shows:

Who changed line
Commit ID
Timestamp

Useful for troubleshooting.

21. Search Commands
Search Text
```
git grep "database"
```
Search Commit Messages
```
git log --grep="bug"
```
22. Clean Commands

Remove untracked files.

```
git clean -f
```

Remove directories:

```
git clean -fd
```

Preview:

```
git clean -n
```
23. Show Commands
Show Commit
```
git show commit-id
```
Show Current Commit
```
git show HEAD
```
24. Useful Git Aliases
```
git config --global alias.st status
2
git config --global alias.co checkout
3
git config --global alias.br branch
4
git config --global alias.cm commit
```

Usage:

```
git st
2
git co main
```
25. Most Common Real-Time Commands
Daily Development
```
git clone
2
git checkout -b feature1
3
git status
4
git add .
5
git commit -m "New feature"
6
git push origin feature1
7
 
```
Sync with Main
```
git checkout main
2
git pull
3
git checkout feature1
4
git merge main
```
Resolve Conflicts
```
git status
2
git add .
3
git commit
```
Release Flow
```
git checkout main
2
git pull
3
git merge develop
4
git tag v1.0
5
git push origin main --tags
```
Top Git Interview Questions
Difference between Merge and Rebase?

Merge

Preserves history
Creates merge commit

Rebase

Creates linear history
Rewrites commit history
Difference between Fetch and Pull?

Fetch

```
git fetch
```

Downloads changes only.

Pull

Shell
1
git pull
```

Downloads + merges changes.

Difference between Reset and Revert?

Reset

Changes local history
Can remove commits

Revert

Creates a new commit
Safe for shared branches
Difference between Stash and Commit?

Stash

Temporary storage
Not part of history

Commit

Permanent version history
Git Workflow Used in Enterprises
```
main
2
│
3
├── develop
4
│ │
5
│ ├── feature/login
6
│ ├── feature/payment
7
│ └── feature/profile
8
│
9
├── release/v1.0
10
│
11
└── hotfix/critical-bug
```
Typical Flow
```
git checkout develop
2
git pull
3
 
4
git checkout -b feature/new-feature
5
 
6
git add .
7
git commit -m "Implemented feature"
8
 
9
git push origin feature/new-feature
10
 
11
Create Pull Request
12
 
13
Code Review
14
 
15
Merge into develop/main
```

This covers the Git commands most commonly expected in DevOps, GitHub Actions, Azure DevOps, AWS DevOps, and senior engineer interviews.
### Git Merge vs Git Rebase: Main Differences

Both merge and rebase integrate changes from one branch into another, but they handle commit history differently.
Example Setup
```
main

A --- B --- C

feature

A --- B --- D --- E
```
1. Git Merge
Command:
```
git checkout main
git merge feature
```
Result:
```
A --- B --- C -------- M
       \            /
        D --- E ----
```
Here M is a merge commit.

Characteristics

✅ Preserves complete history

✅ Shows when branches were merged

✅ Safe for shared branches

✅ No history rewriting

❌ Creates extra merge commits

❌ History can become complex

### Interview Answer

Merge combines two branches and creates a merge commit, preserving the exact history of both branches.

2. Git Rebase

Command:
```
git checkout feature
git rebase main
```
Result:

Before:
```
A --- B --- C    (main)
       \
        D --- E (feature)
```
```
A --- B --- C --- D' --- E'
```
D and E are recreated as new commits (D', E').

Then merge:
```
git checkout main
git merge feature
```
Result:
```
A --- B --- C --- D' --- E'
```
No merge commit.

Characteristics

✅ Clean linear history

✅ Easier to read logs

✅ Preferred before creating PRs

❌ Rewrites commit history

❌ Can cause issues if used on shared/public branches

## Interview Answer

Rebase moves branch commits on top of another branch, creating a linear history without a merge commit.


### 6. Merge vs Rebase Interview Comparison

| Feature | Merge | Rebase |
| :--- | :--- | :--- |
| **Commit History** | Creates merge commit | No merge commit |
| **History Modifications** | Preserves history | Rewrites history |
| **Branch Safety** | Safe for shared branches | Avoid on shared branches |
| **Graph Structure** | Complex graph | Linear graph |
| **Collaboration** | Easier collaboration | Cleaner history |
| **Risk Level** | Non-destructive | Potentially destructive |


### Quick Comparison

| Command | Action |
| :--- | :--- |
| `git fetch` | Downloads changes only |
| `git pull` | Downloads + merges changes |
| `git push` | Uploads local commits to remote |
| `git checkout` | Switches branches or restores files |

### Real-Time Workflow

```
# Download latest changes
git fetch origin

# Review differences
git log HEAD..origin/main --oneline

# Merge changes
git pull origin main

# Create feature branch
git checkout -b feature-payment

# Make code changes
git add .
git commit -m "Added payment API"

# Push feature branch
git push origin feature-payment
```
### Interview Answer

1. git fetch downloads changes from the remote repository but does not merge them.

2. git pull downloads and merges changes into the current branch.

3. git push uploads local commits to the remote repository.

4. git checkout is used to switch branches or restore files (modern Git often uses git switch for branch changes).

### I believe you mean Git Branch Protection (sometimes called branch protection rules).

Branch protection prevents developers from directly making risky changes to important branches such as main, master, release, or production.

### Why Use Branch Protection?

Protect critical branches from:

Direct pushes
Accidental deletions
Force pushes
Unreviewed code
Failed CI/CD deployments
Common Enterprise Setup
```
main
2
├── Protected
3
├── No direct push
4
├── PR required
5
├── Code review required
6
└── CI checks required
7
```

Workflow:

```
Feature Branch
2
↓
3
Pull Request
4
↓
5
Code Review
6
↓
7
Build Validation
8
↓
9
Merge to Main
```
How to Configure Branch Protection in GitHub

Navigate to:

```
Repository
2
→ Settings
3
→ Branches
4
→ Add Rule
```

Example branch:

```
main
```

or

```
release/*
```
Recommended Protection Rules
1. Require Pull Request Reviews

Enable:

```
✓ Require a pull request before merging
2
✓ Require approvals
```

Example:

```
Required Approvals = 2
```
2. Require Status Checks

Ensure builds pass before merge.

```
✓ Require status checks to pass
```

Examples:

```
Build
2
Unit Tests
3
SonarQube
4
Security Scan
```

GitHub blocks merge until all checks succeed.

3. Require Conversation Resolution
```
✓ Require conversation resolution
```

Prevents merging while review comments remain unresolved.

4. Restrict Direct Pushes
```
✓ Do not allow bypassing rules
```

Result:

❌

```
git push origin main
```

Allowed only through PR process.

5. Prevent Force Pushes
```
✓ Block force pushes
```

Stops:

```
git push --force
```

which can overwrite history.

6. Prevent Branch Deletion
```
✓ Prevent deletions
```

Protects important branches from accidental removal.

7. Require Linear History

Enable:

```
✓ Require linear history
```

Uses:

```
rebase
```

instead of unnecessary merge commits.

8. Restrict Who Can Push

Example:

```
Release Managers
2
DevOps Team
```

Only authorized users can push or merge.

Example Enterprise Rule
Main Branch
```
Branch: main
2
 
3
✓ Pull Request Required
4
✓ 2 Approvals Required
5
✓ SonarQube Pass
6
✓ Security Scan Pass
7
✓ Build Pass
8
✓ No Force Push
9
✓ No Deletion
10
✓ Require Conversation Resolution
```
Release Branch Protection
```
release/*
```

Rules:

```
✓ Release Manager Approval
2
✓ Successful Deployment Validation
3
✓ Tagged Release Required
```
Production Deployment Flow
```
Developer
2
↓
3
Feature Branch
4
↓
5
Pull Request
6
↓
7
2 Approvals
8
↓
9
GitHub Actions Validation
10
↓
11
Merge to Main
12
↓
13
Deploy DEV
14
↓
15
QA Approval
16
↓
17
UAT Approval
18
↓
19
PROD Approval
20
 
```
### Interview Answer

### What is Git Branch Protection?

Branch Protection Rules in GitHub protect critical branches such as main and release. They enforce controls like mandatory pull requests, code reviews, successful CI/CD checks, prevention of force pushes and branch deletion, and restricted access. In enterprise projects, we typically require at least two approvals, successful SonarQube and security scans, and passing build validations before allowing a merge into the main branch.


### How do you restrict who can push to a branch in GitHub?

I use Branch Protection Rules. In the repository settings, I create a protection rule for branches such as main or release/*, enable "Restrict who can push to matching branches," and assign only authorized users or teams like DevOps or Release Managers. I also require pull requests, approvals, and successful CI/CD checks to ensure code quality and governance before changes reach protected branches.

### How do you restrict branch creation in GitHub?

GitHub's modern approach is to use Rulesets. Under Repository or Organization Settings, I create a branch ruleset and enable Restrict Creations. Then I specify which users or teams, such as Release Managers or DevOps, are allowed to create branches matching patterns like release/* or hotfix/*. This prevents unauthorized users from creating sensitive branches while maintaining governance and release control.
### How to Restrict Branch Deletion in GitHub?
"To restrict branch deletion in GitHub, I configure Branch Protection Rules under Repository Settings. For critical branches such as main, develop, and release branches, I enable protection and disable deletions. I also require pull requests, approvals, and successful build checks before changes can be merged. In enterprise environments, I prefer Organization Rulesets to enforce consistent branch protection policies across multiple repositories."

### How do you resolve a Git merge conflict?

First, I identify the conflicted files using git status. Then I open the files and review the conflict markers (<<<<<<, =======, >>>>>>>). I decide which changes to keep or combine, remove the markers, and save the file. After that, I stage the resolved files using git add, complete the merge with git commit, and push the changes. For rebase conflicts, I use git rebase --continue after resolving the conflicts.
