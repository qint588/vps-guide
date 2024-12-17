Here is a comprehensive Git guide that combines both basic and advanced Git commands, written in English:

---

### **1. Checkout**

The `git checkout` command is used to switch between branches or restore working tree files.

#### Switch to an existing branch:
```bash
git checkout <branch_name>
```

#### Create a new branch and switch to it:
```bash
git checkout -b <new_branch_name>
```

#### Checkout a file or directory from a commit:
```bash
git checkout <commit_hash> -- <file_path>
```

---

### **2. Set Remote**

The `git remote` command is used to manage the set of remotes that you work with.

#### Add a new remote repository:
```bash
git remote add <remote_name> <repository_url>
```

#### Verify the remotes for your repository:
```bash
git remote -v
```

#### Change the URL of an existing remote:
```bash
git remote set-url <remote_name> <new_url>
```

---

### **3. Pull**

The `git pull` command is used to fetch and integrate changes from the remote repository into your current branch.

#### Pull changes from the remote:
```bash
git pull <remote_name> <branch_name>
```

#### Pull changes with rebase (instead of merge):
```bash
git pull --rebase <remote_name> <branch_name>
```

---

### **4. Push**

The `git push` command is used to upload local repository content to a remote repository.

#### Push changes to the remote repository:
```bash
git push <remote_name> <branch_name>
```

#### Push a new branch to the remote:
```bash
git push -u <remote_name> <branch_name>
```

---

### **5. Create New Branch**

A branch allows you to work on features or fixes without affecting the `main` branch.

#### Create a new branch:
```bash
git branch <new_branch_name>
```

#### Switch to the new branch:
```bash
git checkout <new_branch_name>
```

#### Create and switch to the new branch (shortcut):
```bash
git checkout -b <new_branch_name>
```

---

### **6. Create New Branch from a Commit**

Sometimes, you want to create a new branch from a specific commit rather than the current HEAD.

#### Create a new branch from a commit hash:
```bash
git checkout -b <new_branch_name> <commit_hash>
```

---

### **7. Merge**

The `git merge` command is used to merge one branch into another.

#### Merge a branch into the current branch:
```bash
git merge <branch_name_to_merge>
```

#### Merge a branch with a commit message:
```bash
git merge <branch_name_to_merge> -m "Merge commit message"
```

---

### **8. Cherry-Pick**

`git cherry-pick` allows you to apply a commit from another branch to your current branch.

#### Apply a specific commit from another branch:
```bash
git cherry-pick <commit_hash>
```

#### Apply a range of commits:
```bash
git cherry-pick <start_commit_hash>^..<end_commit_hash>
```

---

### **9. Remove One Commit in a Branch**

To remove a commit from your branch, you can use `git reset`.

#### Undo the last commit (but keep changes in the working directory):
```bash
git reset --soft HEAD~1
```

#### Completely remove the last commit (and all changes):
```bash
git reset --hard HEAD~1
```

#### Remove a specific commit by its hash (without changing other commits):
```bash
git rebase -i <commit_hash>^
```
In the interactive rebase screen, change `pick` to `drop` for the commit you want to remove.

---

### **10. Rollback Branch to a Commit**

To rollback the entire branch to a specific commit, you can reset or use rebase.

#### Rollback to a previous commit (and keep changes):
```bash
git reset --soft <commit_hash>
```

#### Rollback to a previous commit (discard changes):
```bash
git reset --hard <commit_hash>
```

#### Alternatively, using rebase to rollback to a specific commit:
```bash
git rebase --onto <commit_hash> <starting_commit_hash> <branch_name>
```

---

### **11. Delete a Branch**

Once you're done with a branch, you might want to delete it.

#### Delete a local branch:
```bash
git branch -d <branch_name>
```

If the branch hasn't been merged, you can force delete it:
```bash
git branch -D <branch_name>
```

#### Delete a remote branch:
```bash
git push <remote_name> --delete <branch_name>
```

---

### **12. Fix Conflict**

Git may encounter conflicts when merging or rebasing changes. To resolve conflicts, follow these steps:

#### Identify conflicts (Git will mark files with conflicts):
```bash
git status
```

#### Open the conflicting files. Git will mark conflict areas with `<<<<<<<`, `=======`, and `>>>>>>>`.

#### Resolve the conflict by choosing the correct changes and removing the conflict markers.

#### Add the resolved files to the staging area:
```bash
git add <resolved_file>
```

#### Continue the merge or rebase:
```bash
git merge --continue   # If you're in the middle of a merge
git rebase --continue  # If you're in the middle of a rebase
```

---

### **13. Stash (Temporarily Save Changes)**

When you need to switch branches but aren’t ready to commit your changes, use `git stash` to temporarily store them.

#### Save changes to the stash:
```bash
git stash
```

#### Save changes with a name:
```bash
git stash save "stash_name"
```

#### View the list of stashes:
```bash
git stash list
```

#### Apply the most recent stash:
```bash
git stash apply
```

#### Apply a specific stash:
```bash
git stash apply stash@{0}
```

#### Remove a stash after applying:
```bash
git stash drop stash@{0}
```

#### Clear all stashes:
```bash
git stash clear
```

---

### **14. Rebase (Reapply Commits)**

Rebase is used to rewrite commit history, which is useful for updating your branch with the latest changes from the main branch.

#### Rebase your current branch with the main branch:
```bash
git rebase main
```

#### Resolve conflicts during rebase:
```bash
git rebase --continue
```

#### Abort rebase and return to the previous state:
```bash
git rebase --abort
```

---

### **15. Log (Commit History)**

Use `git log` to view the commit history.

#### View commit history:
```bash
git log
```

#### View a concise commit history:
```bash
git log --oneline
```

#### View commit history for a specific file:
```bash
git log <file_name>
```

---

### **16. Tag (Mark Important Points in History)**

Git tags are often used to mark release points or significant milestones.

#### Create a new tag:
```bash
git tag <tag_name>
```

#### Push a tag to the remote:
```bash
git push origin <tag_name>
```

#### View the list of tags:
```bash
git tag
```

#### Delete a tag:
```bash
git tag -d <tag_name>
```

---

### **17. Diff (Compare Changes)**

Use `git diff` to compare differences between commits, branches, or files.

#### Compare unstaged changes:
```bash
git diff
```

#### Compare two branches:
```bash
git diff <branch1> <branch2>
```

#### Compare a commit with the current file:
```bash
git diff <commit_hash> <file_name>
```

---

### **18. Alias (Create Shortcuts for Git Commands)**

Create aliases for commonly used commands to save time.

#### Create an alias for a command:
```bash
git config --global alias.<alias_name> <command>
```

For example, create an alias `lg` for `git log --oneline`:
```bash
git config --global alias.lg "log --oneline"
```

Now, you can use `git lg` instead of typing the full command.

---

### **19. Config (Git Configuration)**

You can configure Git with user-specific information like name and email.

#### Configure your username:
```bash
git config --global user.name "Your Name"
```

#### Configure your email:
```bash
git config --global user.email "youremail@example.com"
```

#### Configure the Git editor:
```bash
git config --global core.editor "nano"
```

#### View the current configuration:
```bash
git config --list
```

---

### **20. Clone (Copy a Repository)**

`git clone` is used to copy a remote repository to your local machine.

#### Clone a repository:
```bash
git clone <repository_url>
```

#### Clone a repository into a specific directory:
```bash
git clone <repository_url> <directory_name>
```

---

### **21. Fetch (Retrieve Changes from Remote)**

`git fetch` is used to retrieve updates from the remote repository without merging them into your working directory.

#### Fetch all changes from the remote:
```bash
git fetch
```

#### Fetch a specific branch from the remote:
```bash
git fetch <remote_name> <branch_name>
```

---

### **22. Clean (Remove Untracked Files)**

`git clean` helps you remove untracked files from your working directory.

#### View untracked files:
```bash
git clean -n
```

#### Remove untracked files:
```bash
git clean -f
```

---

### **Conclusion**

Git is a powerful tool for version control and collaboration in software projects. This guide covers the basic and advanced commands that are commonly used in day-to-day Git operations. Whether you are working with a team or managing your own project, mastering Git will make version control more efficient and manageable.