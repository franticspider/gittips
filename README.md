# Git tips

Things I've found useful in using git on the command line

# Checking the add

`git add --dry-run`

will show you what files will be added at the next commit. You can update `.gitignore` and try again until you get it right. 

# Diffing with a recent version



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

Note that you'll have to remember to update the mirror when you update the original repo

