# Complete Git Notes -

## 🧩 1. What is Git?

Git is a distributed version control system used to track changes in source code during software development.
It helps developers collaborate, manage versions, and maintain history efficiently.

## 📘 2. Git Basics

🔹 Initialize a Repository
```powershell
git init
```

Creates a new Git repository in your current project folder.

🔹 Clone an Existing Repository
```powershell
git clone <repository-url>
```

Makes a local copy of a remote repository.

🔹 Check Repository Status
```powershell
git status
```

Shows modified, staged, and untracked files.

🔹 Add Files to Staging Area
```powershell
git add <file-name>      # Add a single file
git add .                # Add all changes
```

🔹 Commit Changes
```powershell
git commit -m "commit message"
```

Saves your staged changes permanently in local history.

🔹 View Commit History
```powershell
git log
git log --oneline        # Short view
```

## 🌿 3. Branching

Branches allow multiple developers to work on features independently.

🔹 Create a New Branch
```powershell
git branch <branch-name>
```

🔹 Switch Branches
```powershell
git checkout <branch-name>
```

🔹 Create and Switch in One Command
```powershell
git checkout -b <branch-name>
```

🔹 List All Branches
```powershell
git branch
```

🔹 Delete a Branch
```powershell
git branch -d <branch-name>      # Safe delete
git branch -D <branch-name>      # Force delete
```

## 🌍 4. Working with Remote Repositories
🔹 Add Remote Repository
```powershell
git remote add origin <repo-url>
```

🔹 View Remote Repositories
```powershell
git remote -v
```

🔹 Push Changes to Remote
```powershell
git push origin <branch-name>
```

🔹 Pull Latest Changes
```powershell
git pull origin <branch-name>
```

🔹 Fetch Remote Changes (without merging) <br> 
- git fetch gets all the latest changes from the remote (like origin) but doesn’t apply them to your current branch.
It updates your remote-tracking branches (like origin/main, origin/dev) so you can see what others have done before merging or rebasing.
```powershell
git fetch origin
```

---

## 🧭 Git Fetch 
`git fetch` is used to **download updates (commits, branches, and tags)** from a **remote repository** into your **local repository**, **without merging** them into your current branch.

It updates your **remote-tracking branches** (like `origin/main`) so you can review changes before applying them.

---

### ⚙️ Syntax
```bash
git fetch
```
or
```bash
git fetch <remote-name> <branch-name>
```

**Example:**
```bash
git fetch origin main
```
➡️ Fetches the latest commits from the `main` branch on the `origin` remote.

---

### 🧠 What Happens Internally
When you run:
```bash
git fetch origin
```
Git will:
1. Contact the remote repository (`origin`).
2. Download new commits, branches, and tags.
3. Update your **remote tracking branches**, like:
   - `origin/main`
   - `origin/dev`
4. It does **not** change your local branches or working directory.

---

### 👀 Viewing Fetched Changes
To see what new commits were fetched:
```bash
git log main..origin/main
```
This shows commits on the remote branch that you don’t yet have locally.

---

### 🔄 Common Use Cases
| Situation | Command | Purpose |
|------------|----------|----------|
| Check for new commits on remote without merging | `git fetch` | Safe inspection |
| Before merging or rebasing your branch | `git fetch origin` | Get latest remote updates |
| To update info about all remotes | `git fetch --all` | Fetch all remote repositories |

---

### ⚔️ Difference Between `git fetch` and `git pull`
| Command | Action |
|----------|---------|
| `git fetch` | Only downloads changes, does **not** modify your working branch |
| `git pull` | Downloads and **merges** (default merge behaviour , **rebase** : `git pull --rebase` ) changes into your current branch |

---

### ✅ Example Workflow
```bash
# Step 1: Get latest updates from remote
git fetch origin

# Step 2: Check what’s new
git log main..origin/main

# Step 3: Merge or rebase if needed
git merge origin/main
# or
git rebase origin/main
```

---

### 🧩 Summary
- `git fetch` = Safe way to **see remote updates** before merging.
- Does **not** modify local code.
- Commonly used before `merge` or `rebase`.

---

**In short:**  
👉 `git fetch` = *"Bring me the latest info, but don’t touch my current code yet."*


---

### 🔍 Where to See Fetched Changes

When you run:
```bash
git fetch
```
Git **downloads all new commits** from the remote repository (like GitHub) and **updates your remote-tracking branches** (such as `origin/main` or `origin/feature-x`).  
However, it **does not modify your current working branch**.

---

### 🧾 Where to View Changes

#### 1. Check Remote Tracking Branches
After fetching, the latest remote commits are saved under:
```
refs/remotes/origin/<branch-name>
```
To see them:
```bash
git log origin/main
```
Shows the new commits fetched from the remote `main` branch.

---

#### 2. Compare Local vs Remote Branch
To see what new commits exist on the remote but not locally:
```bash
git log main..origin/main
```
Here,
- `main` → your local branch  
- `origin/main` → the remote branch after fetching

This displays all commits that exist on the remote but not locally.

---

#### 3. Show File-Level Differences
To check what exact changes were made to files:
```bash
git diff main..origin/main
```
This compares your local files with the remote ones.

---

#### 4. Check Branch Tracking Status
To view which branches are ahead or behind their remote counterparts:
```bash
git branch -vv
```
It shows details like:
```
* main      abc1234 [behind 2] Fix bug in API
  feature-x def5678 [ahead 3] Add dashboard
```

---

#### ✅ Typical Workflow Example
```bash
# Step 1: Fetch latest commits
git fetch origin

# Step 2: Compare with your local branch
git log main..origin/main

# Step 3: View the difference in code
git diff main..origin/main

# Step 4: Merge or rebase to apply
git merge origin/main
# or
git rebase origin/main
```

---

#### ⚠️ Key Reminder
`git fetch` only updates your remote-tracking references.  
Your **working directory** and **local branches** remain untouched until you explicitly `merge` or `rebase`.

---

**Summary:**
> After `git fetch`, new commits are visible in `origin/<branch>`.  
> Inspect with `git log` or `git diff`.  
> Local code remains unchanged until you merge or rebase.



## ⚙️ 5. Merging and Rebasing



###  Concept Summary

| **Aspect** | **Merge** | **Rebase** |
|-------------|------------|-------------|
| **Purpose** | Combine two branches’ histories by creating a new **merge commit**. | Move or replay commits from one branch on top of another branch’s latest commit. |
| **Commit History** | Preserves all commits and shows a branching structure (non-linear). | Creates a clean, linear commit history (looks like the feature branch was developed after the latest main changes). |
| **Creates new commit?** | ✅ Yes — a *merge commit* is created. | ❌ No merge commit — commits are rewritten with new hashes. |
| **Use when** | You want to preserve complete history of how branches were merged. | You want a clean, linear history without merge commits. |
| **Safe for shared branches?** | ✅ Yes | ⚠️ No — rebase changes commit hashes, which causes conflicts for others who already have the old history. |



## 🧩 Example Setup

Let’s assume we have two branches:

- **`main`** — production or master branch  
- **`feature`** — new feature branch

### Initial commits before branching:

```
A --- B --- C   (main)
            \
             D --- E   (feature)
```

Here:
- `A, B, C` are commits on `main`
- `D, E` are commits on `feature` (branch created from commit `C`)

Now, new commits `F` and `G` are added to `main`:

```
A --- B --- C --- F --- G   (main)
            \
             D --- E        (feature)
```

---

### 🔹 1. Using `git merge main` on feature branch

If you are on the **feature** branch and run:

```bash
git checkout feature
git merge main
```

Git merges `main` into `feature` and creates a **new merge commit** `H`:

```
A --- B --- C --- F --- G   (main)
            \               \
             D --- E --- H    (feature)
```

### ✅ Advantages
- Safe for shared branches  
- Shows exactly when branches diverged and merged  

### ❌ Disadvantages
- Creates an extra merge commit  
- History becomes non-linear and slightly harder to read  

---

### 🔹 2. Using `git rebase main` on feature branch

If you are on the **feature** branch and run:

```bash
git checkout feature
git rebase main
```

Git **replays** commits `D` and `E` on top of the latest `main` commit (`G`):

```
A --- B --- C --- F --- G --- D' --- E'   (feature)
```

Here:
- Commits `D` and `E` are **rewritten** as new commits `D'` and `E'` (new hashes)
- History looks clean and linear

### ✅ Advantages
- Clean, linear history  
- Easier to review and understand  

### ❌ Disadvantages
- Rewrites commit history  
- Not safe for shared/public branches  

---

## 🧠 6. Handling Conflicts & Update Feature Branch

When working on a feature branch, you often need to update it with the latest `main` changes.  
You can do this using **rebase** (clean history) or **merge** (preserves history).


### Overview 
When you're working on a feature branch (for example, `making-changes`), teammates may push updates to `main`. Before merging your feature branch, it's often best to rebase it on top of the latest `main` so your changes are replayed cleanly and history stays linear. During the rebase Git may find conflicts — this document explains how to detect, resolve, and safely push the rebased branch.

Quick contract (inputs / outputs / success criteria)
- Input: a local feature branch with commits that may conflict with upstream `main`.
- Output: feature branch rebased on latest `main`, conflicts resolved, branch pushed.
- Success: `git rebase` completes without conflicts and `git push --force-with-lease` updates the remote branch.

Prerequisites
- Have a clean working tree (no uncommitted changes) or stash them first: `git stash push -m "WIP before rebase"`.
- Confirm remote is up-to-date: `git fetch origin`.

---

### 🚀 Method 1: Fetch + Rebase (Recommended for Clean History)

```bash
git checkout <your-branch>
git fetch origin
git rebase origin/main
# Resolve conflicts → edit files, git add <files>
git rebase --continue   # repeat if multiple conflicts
git push origin <your-branch> --force-with-lease
```

#### 📌 Key Points
- Keeps commit history **linear and clean**.  
- Requires `--force-with-lease` push (rewrites commit history).  
- Use `git rebase --abort` to cancel rebase if needed.

---

### 🚀 Method 2: Pull with Rebase (Shortcut for Above)

```bash
git checkout <your-branch>
git pull --rebase origin main
# Resolve conflicts → git add <files>
git rebase --continue
git push origin <your-branch> --force-with-lease
```

#### 📌 Key Points
- Combines `fetch` and `rebase` in one command.  
- Auto-pauses on conflicts for you to fix.  
- Produces a **clean, linear commit history**.

---

### 🚀 Method 3: Fetch + Merge (Safe, Keeps History)

```bash
git checkout <your-branch>
git fetch origin
git merge origin/main
# Resolve conflicts → git add <files>, git commit (if needed)
git push origin <your-branch>
```

#### 📌 Key Points
- No history rewrite — creates a **merge commit**.  
- Safer for shared/team branches.  
- Easier to understand for beginners.

---

### 🚀 Method 4: Pull with Merge (Simpler Way to Merge)

```bash
git checkout <your-branch>
git pull --merge origin main
# Resolve conflicts → git add <files>, git commit (if needed)
git push origin <your-branch>
```

#### 📌 Key Points
- Shortcut for `fetch + merge`.  
- Creates an explicit **merge commit** showing where branches combined.

---

### ⚙️ Conflict Resolution Steps (Applies to All)

1️⃣ Check which files have conflicts →  
```bash
git status
```

2️⃣ Open conflicted files and fix manually.  
3️⃣ Remove conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`).  
4️⃣ Mark as resolved →  
```bash
git add <file>
```
5️⃣ Continue →  
```bash
git rebase --continue   # or git commit
```

---

## 💡 Summary Table

| Method | Command | History | Push Type | Best For |
|--------|----------|----------|------------|-----------|
| **Fetch + Rebase** | `git fetch origin && git rebase origin/main` | Clean | `--force-with-lease` | Personal feature branches |
| **Pull --Rebase** | `git pull --rebase origin main` | Clean | `--force-with-lease` | Fast updates |
| **Fetch + Merge** | `git fetch origin && git merge origin/main` | Merge Commit | Normal | Shared/team branches |
| **Pull --Merge** | `git pull --merge origin main` | Merge Commit | Normal | Simple workflow |

---

✅ **In Short:**  
- **Rebase** = clean, linear history (needs force push).  
- **Merge** = keeps history intact (safer for shared branches).

---

If stuck or want to cancel:

```powershell
git merge --abort
git rebase --abort
```

---


## ⚖️ Comparison (Before & After) - For Merge vs Rebase

| | **Before Merge/Rebase** | **After Merge** | **After Rebase** |
|--|--------------------------|----------------|------------------|
| **Visual (Simplified)** | `A-B-C` → `D-E` | Adds merge commit (`H`) | Rewrites commits (`D'`, `E'`) |
| **New commit created?** | — | ✅ Merge commit (`H`) | ❌ Rewritten commits |
| **History type** | Diverged | Non-linear | Linear |
| **Safe for shared branches?** | — | ✅ Yes | ⚠️ No |

---

## 🛠️ Commands Recap

### 👉 Merge
```bash
git checkout feature
git merge main
# Creates a merge commit
```

### 👉 Rebase
```bash
git checkout feature
git rebase main
# Replays commits on top of latest main
```

If conflicts occur during rebase:
```bash
# Fix conflicts manually
git add .
git rebase --continue
```

---

## 🧭 When to Use What

| **Situation** | **Recommended Command** |
|----------------|--------------------------|
| Working on shared/public branch | `git merge` |
| Working alone on private branch before PR | `git rebase` |
| Want clear, linear history | `git rebase` |
| Want to preserve full branching history | `git merge` |

---

## 🪄 Example in Real Workflow

Suppose you’re working on a `feature` branch and want to bring in the latest `main` changes.

### Option 1 — Merge
```bash
git checkout feature
git merge origin/main
```

### Option 2 — Rebase
```bash
git checkout feature
git fetch origin
git rebase origin/main
```

After rebase, your branch looks like you developed it after the latest changes — clean and easy for PR reviews.

---

## ✅ Summary

| **Command** | **Creates merge commit?** | **History type** | **Safe for team use?** |
|--------------|---------------------------|------------------|------------------------|
| `git merge`  | ✅ Yes                    | Non-linear       | ✅ Yes |
| `git rebase` | ❌ No (rewrites commits)  | Linear           | ⚠️ Only if local/private |

---

## 💡 Tip
If you’ve already pushed your feature branch and others might have pulled it, **don’t rebase** — it will rewrite commit hashes and cause confusion.  
Instead, use **merge** to safely integrate changes.

---

**In short:**
> 🧩 *Use `merge` to combine histories safely.*  
> 🧹 *Use `rebase` to make history clean.*


---

## 🔁 Why `git rebase --continue` Happens Multiple Times

### 🧩 What Happens During a Rebase

Assume this commit structure:

```
main:    A --- B --- C --- F --- G
feature:             \ 
                      D --- E
```

You run:
```bash
git checkout feature
git rebase main
```

Git internally does:

1. Temporarily removes your feature commits (`D`, `E`) from the branch.
2. Moves the branch pointer to the tip of the target branch (`G`).
3. Reapplies (replays) your feature commits one by one — `D`, then `E` — on top of `G`.

---

### 🔁 Why Multiple `--continue` Are Needed

Each **commit** in your feature branch is applied **individually**.

So if conflicts occur in multiple commits:
- You’ll need to fix conflicts and run  
  ```bash
  git add .
  git rebase --continue
  ```
  **once per conflicting commit**.

---

### 🧠 Example

If commits `D` and `E` both modify the same file as `main`, conflicts happen twice.

You’ll see:

```bash
Applying: D - Add login feature
CONFLICT (content): Merge conflict in login.js
```

Fix manually →  
```bash
git add login.js
git rebase --continue
```

Next commit causes another conflict:

```bash
Applying: E - Add signup feature
CONFLICT (content): Merge conflict in signup.js
```

Fix again →  
```bash
git add signup.js
git rebase --continue
```

When done:
```bash
Successfully rebased and updated refs/heads/feature.
```

---

### 🧠 Important Details

| **Stage** | **Command** | **Meaning** |
|------------|-------------|-------------|
| Rebase starts | `git rebase main` | Begins replaying commits |
| Conflict occurs | — | Git pauses rebase |
| Resolve conflicts | `git add .` | Mark conflicts as resolved |
| Continue next commit | `git rebase --continue` | Moves to next commit |
| Abort rebase | `git rebase --abort` | Cancels rebase and restores original branch |
| Skip one commit | `git rebase --skip` | Ignores current commit and moves to next one |

---

### 🔄 Why So Many Steps?

Because `git rebase` **rebuilds history commit-by-commit** —  
it must check each change against the target branch to ensure the new “replayed” version doesn’t break anything.

That’s why **each conflicting commit** needs a separate `--continue`.

---

### ✅ Pro Tip

If you have many commits and want to reduce the number of conflicts:

1. **Squash** some commits before rebasing:
   ```bash
   git rebase -i HEAD~2
   ```
   Then mark one as `squash`.

2. **Then rebase** — fewer commits = fewer conflict stops.

---

## 🧾 Summary

| **Question** | **Answer** |
|---------------|-------------|
| Do I need to run `--continue` multiple times? | ✅ Yes — once per conflicting commit during rebase. |
| Why? | Because each commit is replayed separately and may cause a new conflict. |
| What if no conflicts? | Rebase runs automatically in one go. |
| Can I skip one commit? | Yes, use `git rebase --skip`. |
| Can I cancel the entire rebase? | Yes, `git rebase --abort`. |

---

**In short:**  
> 💡 You run `git rebase --continue` for every commit that has a conflict during the rebase process.  
> Git replays each commit one by one — and pauses only when your changes need manual conflict resolution.

---

Common scenarios & troubleshooting
---------------------------------
- I forgot to stash changes before starting
  - If you started with uncommitted changes that conflict, `git rebase` will not begin. Stash first: `git stash push -m "pre-rebase"`.
- Rebase stops but `git status` shows nothing
  - You may need to `git add` files you resolved. Use `git diff --name-only --diff-filter=U` to list unmerged files.
- I messed up resolving and want to start over
  - Use `git rebase --abort` then re-run the rebase after preparing the working tree.
- I need to preserve a merge commit or avoid rebase
  - Consider `git merge origin/main` instead of rebasing; merges preserve history and are simpler when preserving merge commits is important.

Edge cases
----------
- Binary files: you must choose one version or replace with a new file — conflict markers won't help.
- Large rebase with many conflicts: consider breaking your changes into smaller commits or rebasing interactively (`git rebase -i origin/main`) to split/modify commits.
- Rewriting public history: avoid rebasing branches that others are actively using. If the branch is shared, coordinate with collaborators.

---

##  🪄 8. Pushing After Rebase

Rebasing rewrites commit history, so you need to force push:

```powershell
git push origin <branch-name> --force-with-lease
```

`--force-with-lease` ensures you don’t overwrite others’ work.


## 🧭 10. Stashing Changes

Temporarily save your uncommitted changes.

```powershell
git stash
git stash list
git stash pop       # Apply and remove stash
git stash apply     # Apply but keep stash
```

## 🧱 11. Useful Viewing Commands
```powershell
git diff                    # View unstaged changes
git diff --staged           # View staged changes
git show <commit-hash>      # Show details of a commit
git blame <file-name>       # Who changed each line
```

## 🧩 Undoing Changes in Git

Git provides multiple commands to undo changes at different stages ---
from unstaging files to undoing commits or even reverting changes in a
shared repository.\
Understanding these commands helps you safely manage your commit history
and avoid accidental data loss.

------------------------------------------------------------------------

### 🔹 1. Unstage a File

``` bash
git reset <file-name>
```

#### 📘 Description

If you have added a file to the staging area using `git add`, but later
decide not to include it in the next commit, you can unstage it using
this command.

This removes the file from the **staging area**, but your edits remain
**intact** in the working directory.

#### 💡 Example

``` bash
git add index.js
git reset index.js
```

➡️ The file `index.js` will be unstaged but your code edits remain.

#### 🧠 Key Points

-   Does **not** delete your changes.
-   Simply moves the file out of the staging area.
-   Useful when you accidentally stage a file before committing.

#### ✅ Summary

| Action               | Keeps File Changes   | Removes from Staging   | Safe for Remote |
|----------------------|----------------------|------------------------|----------------|
| `git reset <file>`   | ✅ Yes               | ✅ Yes                 | ✅ Yes         |

------------------------------------------------------------------------

### 🔹 2. Undo Last Commit (Keep Changes)

``` bash
git reset --soft HEAD~1
```

#### 📘 Description

This command moves the **HEAD pointer** and current branch reference one
commit back, **undoing the last commit**, but keeping all the changes in
the **staging area**.\
It's useful if you realize you committed too early and want to modify or
combine your changes before committing again.

#### 💡 Example

``` bash
git reset --soft HEAD~1
```

➡️ The last commit disappears from history, but the changes remain
staged (ready to be recommitted).

#### 🧠 Key Points

-   Removes the latest commit but **retains** all your changes.
-   Lets you modify commit message or add more files.
-   Safe to use before pushing to remote.

#### ✅ Summary

| Action                      | Keeps File Changes | Keeps in Staging | Removes Commit | Safe for Remote |
|-----------------------------|-------------------|------------------|----------------|----------------|
| `git reset --soft HEAD~1`   | ✅ Yes             | ✅ Yes          | ✅ Yes         | ⚠️ Only before push |

------------------------------------------------------------------------

### 🔹 3. Undo Last Commit (Discard Changes)

``` bash
git reset --hard HEAD~1
```

#### 📘 Description

This command completely removes the last commit **and all associated
changes**, both from the **staging area** and the **working
directory**.\
Your project will look exactly as it did **before** that commit.

#### 💡 Example

``` bash
git reset --hard HEAD~1
```

➡️ Your last commit and its changes are permanently removed from your
local branch.

#### ⚠️ Warning

-   This action **deletes** changes permanently from your local
    environment.
-   If the commit was already pushed to remote, this can cause conflicts
    if you force-push.

#### 🧠 Key Points

-   Irreversible locally unless backed up.
-   Commonly used to completely discard unwanted commits.
-   Use carefully, especially on shared branches.

#### ✅ Summary

| Action                      | Keeps File Changes | Keeps in Staging | Removes Commit | Safe for Remote |
|-----------------------------|-------------------|------------------|----------------|----------------|
| `git reset --hard HEAD~1`   | ❌ No              | ❌ No           | ✅ Yes         | ⚠️ Dangerous if pushed |

------------------------------------------------------------------------

### 🔹 4. Revert a Commit (Safe for Shared Repositories)

``` bash
git revert <commit-hash>
```

#### 📘 Description

Instead of deleting history, `git revert` creates a **new commit** that
undoes the effects of a previous commit.\
It is the **safest** and **recommended** way to undo changes that have
already been pushed to a remote repository.

#### 💡 Example

``` bash
git log --oneline   # to find the commit hash
git revert a1b2c3d
git push origin main
```

➡️ This adds a new commit that cancels out the changes made by commit
`a1b2c3d`.

#### 🧠 Key Points

-   Keeps commit history intact.
-   Creates a new "undo" commit.
-   Ideal for collaborative projects where you shouldn't rewrite
    history.
-   Safe even after pushing.

#### ✅ Summary

| Action                | Keeps File        | Removes Commit | Creates New Commit | Safe for Remote |
|-----------------------|-------------------|----------------|-------------------|----------------|
| `git revert <hash>`   | ✅ Yes            | ❌ No          | ✅ Yes             | ✅ Yes         |

### 🧠 Complete Comparison Table

| Goal | Command | Keeps Changes? | Keeps in Staging? | Removes Commit? | Safe for Shared Repo? |
|------|---------|---------------|------------------|----------------|---------------------|
| Unstage a file | `git reset <file>` | ✅ Yes | ❌ No | ❌ No | ✅ Yes |
| Undo last commit (keep changes) | `git reset --soft HEAD~1` | ✅ Yes | ✅ Yes | ✅ Yes | ⚠️ Before push only |
| Undo last commit (discard changes) | `git reset --hard HEAD~1` | ❌ No | ❌ No | ✅ Yes | ⚠️ Dangerous if pushed |
| Revert a commit | `git revert <commit-hash>` | ✅ Yes | ✅ Yes | ❌ No | ✅ Yes |

------------------------------------------------------------------------

### 🧰 Additional Tips

-   Use `git log --oneline` to view commit history and get commit
    hashes.

-   To undo multiple commits:

    ``` bash
    git reset --hard HEAD~3   # Undo last 3 commits
    ```

-   To revert a range of commits safely:

    ``` bash
    git revert <old_commit>^..<new_commit>
    ```

-   If you made a `--hard` reset by mistake, you can often recover
    using:

    ``` bash
    git reflog
    git reset --hard <commit_id>
    ```

------------------------------------------------------------------------

#### ✅ Quick Recommendations


  | Situation                                 | Best Command                      | Reason                                |
  |-------------------------------------------|-----------------------------------|---------------------------------------|
  | You staged a file by mistake               | `git reset <file>`                | Keeps changes, removes from staging   |
  | You committed too early                    | `git reset --soft HEAD~1`         | Undo commit but keep changes staged   |
  | You want to discard everything             | `git reset --hard HEAD~1`         | Reset fully to previous commit        |
  | You need to undo after push                | `git revert <commit-hash>`        | Safe for shared repos; keeps history  |





## 🔐 12. Tracking and Upstream Branches

When you push for the first time:

```powershell
git push -u origin <branch-name>
```

The `-u` flag sets the upstream branch, so later you can just do:

```powershell
git push
git pull
```

⚡ 13. Common Problem Scenarios
❌ Case 1: “Your branch is behind ‘origin/main’”

Fix:

```powershell
git pull --rebase origin main
```

❌ Case 2: “Merge conflict during rebase”

Fix:

```powershell
git status
# resolve manually
git add <file>
git rebase --continue
```

❌ Case 3: Accidentally merged wrong branch

Fix:

```powershell
git reset --hard HEAD~1
```

(Only if unpushed!)

❌ Case 4: You want to cancel a rebase
```powershell
git rebase --abort
```

❌ Case 5: You need a clean start (from main)
```powershell
git checkout main
git pull origin main
git checkout -b new-feature
```

## 🧩 14. Summary Table
| Action | Command | Description |
| --- | --- | --- |
| Initialize Repo | `git init` | Start a new Git repository |
| Clone Repo | `git clone <url>` | Copy remote repository |
| Stage File | `git add <file>` | Add changes for commit |
| Commit | `git commit -m "msg"` | Save staged changes |
| Push | `git push origin <branch>` | Upload commits to remote |
| Pull | `git pull origin <branch>` | Fetch + merge remote |
| Fetch | `git fetch origin` | Download remote updates |
| Rebase | `git rebase <branch>` | Replay commits on top of branch |
| Merge | `git merge <branch>` | Combine branch histories |
| Resolve Conflicts | `git add`, `git rebase --continue` | Fix merge/rebase issues |
| Undo Commit | `git reset --soft HEAD~1` | Undo last commit but keep changes |
| Force Push | `git push --force-with-lease` | Safe push after rebase |

## 🧠 15. Pro Tips

- Use `git pull --rebase` for cleaner history.
- Use `--force-with-lease` instead of `--force`.
- Always `git fetch` before merging or rebasing.
- Commit small and often — easier to review and rollback.
- Don’t rebase shared branches (it rewrites history).
- Use `.gitignore` to skip unnecessary files.

🏁 In Short

1️⃣ fetch → brings updates  
2️⃣ merge or rebase → integrates them  
3️⃣ commit → records changes  
4️⃣ push → shares changes  
5️⃣ pull → gets updates  
6️⃣ Resolve conflicts → `git add` + `git rebase --continue`

📝
Addendum: keep the rest of this file as-is: the rebase/conflict guide follows below.


FAQ
---
Q: When should I merge instead of rebase? 
A: Merge when preserving the full merge history is important (e.g., long-running integration branches) or you don't want to rewrite commits that others may rely on.

Q: What if CI fails after I push a rebased branch?
A: Inspect the failing job, fix locally (add commits), and push again. CI should run against the new branch tip.

Q: Can I rebase interactively to squash/fixup commits while resolving conflicts?
A: Yes. `git rebase -i origin/main` lets you reorder, squash, or edit commits; you'll still resolve conflicts the same way when they occur.
