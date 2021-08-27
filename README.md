# Git tips

Things I've found useful in using git on the command line


# Admin

## Find the url of remote

`git config --get remote.origin.url`


# Adding

## Checking the add

`git add --dry-run .`

will show you what files will be added at the next commit. You can update `.gitignore` and try again until you get it right. 

## undoing an add before a commit

`git reset`

# Files

## Largest files in repo: 

`git ls-tree -r -t -l --full-name HEAD | sort -n -k 4 | tail -n 10`

## History of a file

`git log -- FILENAME`

## Revert a file

Get the history as above, and copy the commit ID of the time you want to roll back the file to. Then check that out like this: 

`git checkout [commit ID] -- path/to/file`


## Remove file from git history but keep on local filesystem

*This can get very complicated!* For simple cases, this is supposed to work: 

`git rm --cached FILENAME`

But experience has shown that this can leave files in other branches, and also not delete them from the `.git` folder. So there's several steps to this. Assuming you've listed the largest files in the repo using the command shown above, and we've got a file called `hugefile` that's clogging up the repo, the steps are as follows. 

### Use `filter-branch` to remove the file from all history

`git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch hugefile' --prune-empty --tag-name-filter cat -- --all`

### Set up and run a garbage collection routine

This removes the file from all branches through history. Unfortunately the `.git` folder contents may not be updated: we need to run the garbage collector to do this. The best answer in [https://stackoverflow.com/questions/11050265/remove-large-pack-file-created-by-git] shows how to do this (nb run the following as-is, nothing needs to be replaced in these commands): 

```
git for-each-ref --format='delete %(refname)' refs/original | git update-ref --stdin
git reflog expire --expire=now --all
git gc --aggressive --prune=now
```

### Force the push to remote origin

If you do a normal `add/commit/push` cycle you'll come up against issues when the local repo is compared to remote. You need to tell git you are altering history

`git push origin --force --all`


# Branching

[https://www.nobledesktop.com/learn/git/git-branches]

To list the local branches currently present: 

`git branch`

To list the remote branches

`git branch -r`

To create a new local branch called "nlb""

`git checkout -b nlb`

To create a corresponding remote branch (and implicitly push to it) if it doesn't exist:

`git push -u origin nlb`

Once the remote branch exists, you can just `git push`

## Branch from an ealier commit:

[https://stackoverflow.com/questions/2816715/branch-from-a-previous-commit-using-git]

just add the hash of the earlier version to the branching command when you create the branch

`git branch newbranch 2ece193dc53778952cf5592ef2af103e9fec7c9a`



# Merging

if you want to pull changes from the `master` branch into the current branch you're working on: 

`git merge origin/master`

- make sure you pull changes from remote, and that you have checked out 

# Mirroring

Before you can duplicate a repository and push to your new copy, or mirror, of the repository, you must create the new repository on GitHub. In these examples, exampleuser/new-repository or exampleuser/mirrored are the mirrors.

Create a bare clone of the repository.

`git clone --bare https://github.com/exampleuser/old-repository.git`

Mirror-push to the new repository.

`cd old-repository.git`
`git push --mirror https://github.com/exampleuser/new-repository.git`

Remove the temporary local repository you created earlier.

`cd ..`
`rm -rf old-repository.git`

Note that you'll have to remember to update the mirror when you update the original repo. The mirror doesn't include things like `.gitignore` and the `.git` folder and its contents.  

