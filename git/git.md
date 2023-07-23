# Git Notes

## Pushed .idea directory into remote respository from pycharm

To remove the .idea directory from the remote repository, you can use the git rm command with the --cached flag to
remove it from the repository but keep it in your local working directory:

```bash
git rm --cached .idea
```

Then, commit the change and push it to the remote repository:

```bash
git commit -m "Removed .idea directory"
git push
```

To prevent the .idea directory from being pushed again in the future, you can add it to your .gitignore file. 

## Understanding conflicts
Initial Branches: You have two branches, "main" and "branch-A."

Committing Changes in Main Branch: Developers have been working on the "main" branch and have made some commits, including a change to a specific piece of code, let's call it "X," which resulted in "X" becoming "Z."

Committing Changes in Branch A: Meanwhile, developers have also been working on "branch-A" and have made some commits, including a change that intended to modify "X" to "Y."

Merge Attempt: When you attempt to merge "branch-A" into "main," Git considers the chronological order of commits. Since the changes in "branch-A" occurred after the latest commit in "main," Git recognizes "branch-A" as having the more recent changes.

Conflict Detection: During the merge process, Git identifies that "branch-A" is trying to change "X" to "Y," but when comparing with the current state of "main," "X" has already been changed to "Z" by some other commit on the "main" branch.

Conflict Resolution: The conflict arises because Git cannot automatically determine which change should take precedence (i.e., "X" to "Y" or "X" to "Z").

Manual Conflict Resolution: As a result, Git halts the merge process and asks you to resolve the conflict manually. You must decide whether to keep "X" as "Z" or accept the change to "Y" from "branch-A," or possibly make a different modification.

Committing Resolved Changes: After manually resolving the conflict by choosing the desired change, you commit the changes to complete the merge.

Merge Completion: The merge is now complete, and the changes from "branch-A" have been successfully integrated into the "main" branch, along with the conflict resolution you performed.

In summary, the conflict occurred because "branch-A" attempted to change "X" to "Y," but "X" had already been changed to "Z" in the "main" branch by another commit. Manual conflict resolution allows you to decide how to handle these conflicting changes and ensure that the final merged state of the code is coherent and reflects the intended modifications from both branches.

## Introduction
VCS --> DVCS

Stage a snapshot

git add
bring an untracked file under version control
Adds the file/files to the snapshot for the next commit

git add orange.html blue.html
git add . 
add all untracked files in the current directory to the next commit 

What is a snapshot? 
A snapshot represents the state of your project at a given point in time.

creating a snapshot = staging or you can say staging a snapshot
We can add or remove multiple files to a snapshot before commiting it

We can group relevant changes into distinct snapshots for meaningful progression of software instead of arbitary lines of code

Saving a version of your project is a two step process:
1. Staging a snapshot:  Telling Git what files to include in the next commit.
2. Commiting a snapshot:  Recording the staged snapshot with a descriptive message.

Commiting a snapshot will record it into the repositoy

`git log`: view project history; show commited changes\
`git status`: show staged changes
![img.png](img.png)

`git config --global user.name "Your Name"`

`git config --global user.email your.email@example.com`


![img_1.png](img_1.png)
Each circle represents a commit, the red circle designates the commit
we’re currently viewing, and the arrow points to the preceding commit.
This last part may seem counterintuitive, but it reflects the underlying
relationship between commits (that is, a new commit refers to its parent
commit).

`git log --oneline`
if output of git log is too long press space to go to end and then press q to return to command line

`git log --oneline blue.html`

Adding files to the staging area, editing the files, once you are done with editing you have a staged snapshot,
commit the snapshot

![img_2.png](img_2.png)

## Undoing changes

View an older version:\
`get checkout <commit-hash>`

"You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  `git switch -c <new-branch-name>`

Or undo this operation with:

`git switch - "`

Return to current version:\
`git checkout master`

Never make any changes in the detached head state as all your changes will be lost. You need to be on a branch to make
changes

This makes Git update our working directory to reflect the state of the
master branch’s snapshot.

All of our actions in The Basics took place on the
master branch, which is where our second and third commits still
reside. To retrieve our complete history, we just have to check out this
branch.

`git tag -a v1.0 -m "Stable version of the website"`

`git tag`

#### Undoing commited changes:

git revert <commit-hash>
This will not delete the commit, git will figure out how to undo the changes it contains and creates another commit
with the resulting content. So this new commit and the commit before the commit that was reverted will represent the
exact same snapshot. So git doesn't lose history even for reverted commits, the reverted commit is still accessible.

![img_3.png](img_3.png)

#### Undoing uncommited changes:
For tracked files:\
`git reset --hard`\
This changes all tracked files to match the most recent commit. Note that the --hard flag is what actually updates the
file.Running git reset without any flags will simply unstage the files, leaving its contents as is. git
reset only operates on the working directory and the staging area, so
our git log history remains unchanged.

For Untracked files:
`git clean -f`

`git reset` and `git clean`. Both operate on the working directory, not  on the committed snapshots. Unlike `git revert`,
they
permanently undo changes, so make sure you really
want to trash what you’re working on before you use them.

 most Git commands operate on one of the
three main components of a Git repository: the **working directory**, **the staged
snapshot**, or the **committed snapshots**. The `git reset` command undoes
changes to the working directory and the staged snapshot, while `git
revert` undoes changes contained in committed snapshots. Not
surprisingly, `git status` and `git log` directly
parallel this behavior.

![img_4.png](img_4.png)

## Banches

`git branch`: show all branches

![img_5.png](img_5.png)

 The
master branch is Git’s default branch, and the asterisk next
to it tells us that it’s currently checked out. This means that the most
recent snapshot in the master branch resides in the working
directory. 

`git checkout <commit-hash>`

This command returns a message that says we’re in a detached
HEAD state and that the HEAD is now at
<commit-hash>. **The HEAD is Git’s internal way of
indicating the snapshot that is currently checked out**. This means the red
circle in each of our history diagrams actually represents Git’s
HEAD. The following figure shows the state of our repository
before and after we checked out an old commit.

![img_6.png](img_6.png)

As shown in the “before” diagram, the HEAD normally
resides on the tip of a development branch. But when we checked out the
previous commit, the HEAD moved to the middle of the branch. **We
can no longer say we’re on the master branch since it
contains more recent snapshots than the HEAD**. That's why we say that the HEAD is detached

We can't add new commits when we are in the detached HEAD state because we are not on any branch. So we create a branch
when we are in the detached HEAD state. This creates a branch which has the same snapshot as the commit.

`git branch crazy`\
`git checkout crazy`

Right now, the crazy branch, HEAD, and working
directory are the exact same as the fourth commit.  But as soon as we add(commit)
another snapshot, we’ll see a fork in our project history

![img_12.png](img_12.png)

]![img_7.png](img_7.png)

Add a new file and do
`git commit`

![img_11.png](img_11.png)

as wee can see `git log` only shows the history of the current branch

![img_9.png](img_9.png)

Note that the history before the fork is considered part of the new
branch

![img_13.png](img_13.png)

#### Renaming a file
`git status`\
`git rm crazy.html`\
`git status`\
`git add rainbow.html`\
`git status`

![img_14.png](img_14.png)

`git checkout master`\
 These two branches became completely independent
development environments after they forked. You can think of them as separate
project folders that you switch between with git checkout. They
do, however, share their first four commits.

![img_15.png](img_15.png)


#### more on branching
creating the css branch\
`git branch css`\
`git checkout css`

![img_16.png](img_16.png)

After two commits to css branch

![img_17.png](img_17.png)

`git checkout master` Returning to the master branch

![img_18.png](img_18.png)

`git merge css` merging the css branch into master

Instead of re-creating the commits in css and adding them to
the history of master, Git reuses the existing snapshots and
simply moves the tip of master to match the tip of
css. This kind of merge is called a fast-forward
merge. 

As you can see in fast forwarding no extra commit is done

![img_19.png](img_19.png)

`git branch -d` css deleting the css branch

![img_21.png](img_21.png)























CHANGES GET STAGED INTO A SNAPSHOT. First you change files. After you are done changing files
you stagq the changes into a
snapshot using the `git add` command. After you are done staging the snapshot you commit the snapshot


## Quick ref

`git init`
Create a Git repository in the current folder.

`git status`
View the status of each file in a repository.

`git add <file>`
Stage a file for the next commit.

`git commit`
Commit the staged files with a descriptive message.

`git log`
View a repository’s commit history.

`git config --global user.name "<name>"`:
Define the author name to be used in all repositories.

`git config --global user.email <email>`:
Define the author email to be used in all repositories.

`git checkout <commit-id>`
View a previous commit.

`git tag -a <tag-name> -m "<description>"`
Create an annotated tag pointing to the most recent commit.

`git revert <commit-id>`
Undo the specified commit by applying a new commit.

`git reset --hard`
Reset tracked files to match the most recent commit.

`git clean -f`
Remove untracked files.

`git reset --hard /  git clean -f`
Permanently undo uncommitted changes.

`git branch`
List all branches.

`git branch <branch-name>`
Create a new branch using the current working directory as its
base.

`git checkout <branch-name>`
Make the working directory and the HEAD match the specified
branch.

`git merge <branch-name>`
Merge a branch into the checked-out branch.

`git branch -d <branch-name>`
Delete a branch.

`git rm <file>`
Remove a file from the working directory (if applicable) and stop
tracking the file.



