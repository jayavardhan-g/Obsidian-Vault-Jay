# Initialization
## Repo
git init 
git add *filename*/.
git commit -m "Commit message"
git commit --amend

## Config
git config user.email "jayavardhan@gmail.com"
git config --global user.email "jayavardhan.gollapalli@gmail.com"
git config --edit
git config --global --edit

```
[user]
	email = jayavardhan.gollapalli@gmail.com
	name = Jayavardhan
	
[alias]
	cmm = commit -m 
	cm = commit
	confge = config --global --edit
	confg = config --global
	conf = config
```
Add aliases (abbreviations) for easier access

# diff

git diff —staged ( Shows changes happened between the committed changes and now staged changes)
git diff branch1 branch2 ( Shows changes between branch1 and branch2 )
git diff branch1..branch2 (Same as above)
git diff 9h008..105dg7 (Same as above for the different commits)

# .gitignore


# Stash

git stash
git stash pop ( Applies changes, deletes stash )
git stash apply ( Applies changes, doesn’t delete stash )
git stash list , git stash apply stash@{#Number}

# Branch

## Switch
git switch existing_branch
git switch -c new_branch
git switch --detach *commit_hash*

## Restore

git restore . ( Discard local changes - doesn't discard staged changes)
git restore --staged *file_name* ( Unstages staged changes - doesn't discard anything )
git restore --staged --worktree . ()

--staged = -s
--worktree = -w

## Checkout

git checkout hash → To go back to a particular commit
git checkout main → To come back to the latest commit in the main branch after going back
git checkout HEAD~3 → To go back 3 commits


NOTE : Switch and Restore are the new commands replacing the Checkout commands


# Reset

git reset --soft *commit_name* ( Moves the branch pointer back to a previous commit - leaves all changes staged )

git reset --mixed *commit_name* ( Moves the branch pointer to previous commit - keeps code, unstages changes )

git reset --hard *commit_name* ( Moves the branch pointer to previous commit - unstages all staged, deletes all changes )


## Uses

- Delete commits

git reset --soft HEAD~1 (The commit is gone. Your changes are now **staged** and ready to be committed again)

git reset HEAD~1 (The commit is gone. Your changes are now **unstaged** on your disk)

git reset --hard HEAD~1 (The commit and all the code inside it are **permanently deleted** from your working directory)

git revert HEAD (Creates a new commit deleting all the code changes we made in the HEAD commit)


# Reflog

git reflog

Contains log of all the previous things
We can use it to restore the changes when accidently doing a hard reset



# Frequent cases

## Interchange branch names

Change pointers
	git branch -f main hash_b
	git branch -f feature hash_a

Push to github
	git push origin main --force-with-lease
	git push origin feature --force-with-lease

Have to be on a different branch for this to work
							
							OR
							


Renaming the branches
	git branch -m branch-a temp-name
	git branch -m branch-a temp-name
	git branch -m temp-name branch-b

Deleting old branches
	git push origin --delete branch-a branch-b

Pushing new branches
	git push origin branch-a branch-b

Reset upstream tracking
	git switch branch-a
	git push -u origin branch-a

	git switch branch-b
	git push -u origin branch-b

## Move main to specific commit

git reset --hard *commit_hash*
