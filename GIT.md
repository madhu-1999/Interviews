#git

# Main
+ This is the main/master branch of git repo.
# Branch
+ Branch points to a commit in either the main branch or another branch of git repo, when it is created.
## Create new branch
```git
// Creates new branch hello
git branch hello
// Checkout new branch to make it the current branch
// Commits are pushed to current branch
git checkout hello

// Creates and checks out new branch
git checkout -b <branch-name>
```
## Delete branch
```git
//Delete branch hello
git branch -d hello
```
# Merging
+ Combine the branch with its parent to make changes final.
+ Merging in Git creates a special commit that has two unique parents. A commit with two parents essentially means "I want to include all the work from this parent over here and this one over here, _and_ the set of all their parents."
![[Pasted image 20250326135332.png]]
## git merge
```git
// Before using merge command, must be in the branch into which merge should happen
git checkout main
//To pull latest changes
git pull
// Merge bugFix into main branch
git merge bugFix
```
# Rebasing
+ The primary reason for rebasing is to maintain a linear project history. For example, consider a situation where the main branch has progressed since you started working on a feature branch. You want to get the latest updates to the main branch in your feature branch, but you want to keep your branch's history clean so it appears as if you've been working off the latest main branch.
+ Internally, Git accomplishes this by creating new commits and applying them to the specified base. It's very important to understand that even though the branch looks the same, it's composed of entirely new commits.

![Git rebase](https://wac-cdn.atlassian.com/dam/jcr:4e576671-1b7f-43db-afb5-cf8db8df8e4a/01%20What%20is%20git%20rebase.svg?cdnVersion=2630)
## git rebase
```git
//Git rebase in standard mode will automatically take the commits in your //current working branch and apply them to the head of the passed branch.
git rebase <base>
```
+ Interactive rebasing is the best way to review a series of commits you're about to rebase.
+ git will open up a UI to show you which commits are about to be copied below the target of the rebase. It also shows their commit hashes and messages, which is great for getting a bearing on what's what.
+ You can reorder commits, keep all or drop some, amend commit messages, squash(combine) commits and even edit commits
# Detaching HEAD
+ Make head point to a specific commit instead of top of current branch
```git
// Move head to given commit
git checkout <commit-hash>
//Move head down by 1 using (^)
git checkout HEAD^
//Move head down by 2
git checkout HEAD^^
//or use (~x) operator to specify number of times to move back
git checkout HEAD~2
```
# Reversing Changes
## git reset
`git reset` reverses changes by moving a branch reference backwards in time to an older commit. In this sense you can think of it as "rewriting history;" `git reset` will move a branch backwards as if the commit had never been made in the first place.
![[Screenshot 2025-03-26 at 2.45.24 PM.png]]
```git
// Revert back one commit
git reset HEAD^
```
## git revert
While resetting works great for local branches on your own machine, its method of "rewriting history" doesn't work for remote branches that others are using.
In order to reverse changes and _share_ those reversed changes with others, we need to use `git revert`

The new commit `C2'` introduces _changes_ -- it just happens to introduce changes that exactly reverses the commit of `C2`.![[Screenshot 2025-03-26 at 2.43.56 PM.png]]
```git
// Revert one commit
git reset HEAD
```
# git cherry-pick
It's a very straightforward way of saying that you would like to copy a series of commits below your current location.
```git
git cherry-pick <Commit1> <Commit2>
```