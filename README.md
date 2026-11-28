#### Setting Up Git

# Install Git and check the version

git --version

# Configure username and email

git config --global user.name "Your Name"

git config --global user.email "you@example.com"

git config --list

the name and email will appear in every commit as the author.

#### Git commits to commit

# Create the hello directory and the hello.sh file

mkdir -p work/hello

cd work/hello

echo 'echo "Hello, World"' > hello.sh

# Initialize the git repository

git init

git init creates the hidden .git folder, so the folder becomes a repository.

# Check the status

git status

hello.sh is untracked, so I need to add it and commit it.

git add hello.sh

git commit -m "Add hello.sh"

# Change the content of hello.sh, stage and commit

the new content is:

```sh
#!/bin/bash

echo "Hello, $1"
```

git add hello.sh

git commit -m "Use first argument as name"

git status

the working tree is clean.

# Add comments and make two separate commits

the new content is:

```sh
#!/bin/bash

# Default is "World"
name=${1:-"World"}
echo "Hello, $name"
```

git add -p hello.sh

git add -p lets me stage only a part of the changes. Here the changed lines are next to each other so git shows only one hunk and "s" (split) doesn't work. I used "e" to edit the hunk and kept only the comment line.

git commit -m "Add comment"

git add hello.sh

git commit -m "Use default name variable"

#### History

# Show the history

git log

# One-line history

git log --oneline

it shows the first 7 characters of the hash and the commit message.

# Controlled entries

git --no-pager log -2

git --no-pager log --since="5 minutes ago"

the first one shows the last 2 commits, the second one shows the commits made in the last 5 minutes.

# Personalized format

git log --format="* %h %ad | %s%d [%an]" --date=short

%h is the short hash, %ad the date, %s the message, %d the branch names and %an the author name.

#### Check it out

# Restore the first snapshot

git log --oneline

git checkout <hash of the first commit>

cat hello.sh

# Restore the second most recent snapshot

git checkout main~1

cat hello.sh

main~1 means one commit before the last commit of main.

# Return to the latest version

git checkout main

I used the branch name so no hash is needed.

#### TAG me

# Tag the current version as v1

git tag v1

# Tag the previous version as v1-beta

git tag v1-beta main~1

# Move between the two tags

git checkout v1-beta

git checkout v1

git checkout main

# List all the tags

git tag

#### Changed your mind?

# Reverting changes

I added the bad comment in hello.sh then:

git restore hello.sh

it copies the clean version back, so the change is gone.

# Staging and cleaning

git add hello.sh

git restore --staged hello.sh

git restore hello.sh

the first restore takes the file out of the staging area, the second one removes the change from the file.

# Committing and reverting

git add hello.sh

git commit -m "Unwanted change"

git revert HEAD

git revert keeps the history and adds a new commit that does the opposite of the last one.

# Tagging and removing commits

git tag oops

git reset --hard v1

reset --hard moves main and HEAD back to v1 and removes everything after it from the branch.

git log --oneline

# Displaying logs with deleted commits

git log --oneline --all

--all shows every ref, including oops, so the deleted commits still appear.

# Cleaning unreferenced commits

git tag -d oops

git reflog expire --expire=now --all

git gc --prune=now

git log --oneline --all

tag -d deletes the tag. The reflog remembers the old positions of HEAD for about 90 days so I expire it. gc --prune=now deletes the unreachable objects. After that the commits are not in the logs anymore.

# Author information

I added "# Author: Jim Weirich" in hello.sh then:

git add hello.sh

git commit -m "Add author"

# Add the forgotten email in the last commit

I added the email in hello.sh then:

git add hello.sh

git commit --amend --no-edit

--amend replaces the last commit with a new one that has the staged changes, so there is no extra commit. The hash changes, so I should never do it on commits that are already pushed.

#### Move it

# Move hello.sh into lib

mkdir lib

git mv hello.sh lib/hello.sh

git commit -m "Move hello.sh into lib"

git mv is the same as mv and then git add.

# Create the Makefile

```make
TARGET="lib/hello.sh"

run:
	bash ${TARGET}
```

git add Makefile

git commit -m "Add Makefile"

the line under run: must start with a TAB, not spaces.

#### blobs, trees and commits

# Explore the .git directory

ls -la .git

HEAD: points to the branch I am on (ref: refs/heads/main).

config: the settings of the repository (remotes, branches, user).

objects/: the database of git, it stores the blobs, trees and commits. The folder name is the first 2 characters of the hash and the file name is the rest.

refs/: the pointers to commits. heads/ for branches, tags/ for tags and remotes/ for remote branches.

index: the staging area.

logs/: the reflog, the history of where HEAD was.

hooks/: scripts that run on some events like before a commit.

# Latest object hash, its type and content

git rev-parse HEAD

git cat-file -t <hash>

git cat-file -p <hash>

the type is commit. The content shows the tree, the parent, the author and the message.

# Dump the directory tree

git cat-file -p HEAD^{tree}

git cat-file -p HEAD:lib

git cat-file -p HEAD:lib/hello.sh

a commit points to a tree, a tree points to other trees and to blobs, and a blob is the content of a file.

#### Branching

# Create and switch to the greet branch

git checkout -b greet

# Create greeter.sh

I created the file with the code from the subject then:

git add greeter.sh

git commit -m "Add greeter"

# Update lib/hello.sh

git add lib/hello.sh

git commit -m "Use Greeter in hello.sh"

# Update the Makefile

git add Makefile

git commit -m "Update Makefile comment"

# Switch back to main and compare

git checkout main

git diff main greet -- Makefile lib/hello.sh lib/greeter.sh

greeter.sh shows as a new file because it doesn't exist on main.

# Create the README.md and commit it

echo "This is the Hello World example from the git project." > README.md

git add README.md

git commit -m "Add README"

# Commit tree diagram

```
v1---A---M---K---R              main
               \
                G1---G2---G3    greet

A = add author
M = move hello.sh into lib
K = add makefile
R = add readme
G1 = add greeter
G2 = Use Greeter in hello.sh
G3 = update makefile comment
```

I checked it with: git log --graph --oneline --all

#### Conflicts, merging and rebasing

# Merge Main into Greet Branch

git checkout greet

git merge main

no conflict here because main only added README.md.

# Switch to main branch and make changes to hello.sh file

git checkout main

I replaced the content of lib/hello.sh with the "What's your name" version then:

git add lib/hello.sh

git commit -m "Interactive hello"

# Merging Main into Greet Branch (Conflict)

git checkout greet

git merge main

there is a conflict in lib/hello.sh because both branches changed the same file. Git adds <<<<<<<, ======= and >>>>>>> in the file.

# Resolve the conflict (manually or using merge tools)

git checkout --theirs lib/hello.sh

during a merge, theirs is main (the branch I merge in), so this keeps the main version.

# After resolving, stage the changes and commit

git add lib/hello.sh

git commit

# Rebasing Greet Branch

first I went back to before the first merge. I looked for the last commit of greet with:

git log --oneline --graph

git reset --hard <hash of the last greet commit>

then:

git rebase main

there is a conflict again, so I kept the main version:

git checkout --ours lib/hello.sh

git add lib/hello.sh

git rebase --continue

in a rebase ours and theirs are swapped. Ours is main (the branch I rebase onto) and theirs is my commit that is replayed. If git says there is nothing to commit, I use git rebase --skip.

```
before:   M---R---H            main
           \
            G1---G2---G3       greet

after:    M---R---H---G1'---G2'---G3'    greet
```

# Merging Greet into Main

git checkout main

git merge greet

this is a fast-forward because greet is now on top of main.

# Fast-forward and the difference between merge and rebase

fast-forward: when the branch I merge into has no new commits of its own, git just moves the pointer forward. There is no merge commit and the history stays a straight line.

merge: keeps the history as it happened and creates a merge commit with two parents. It is safe on shared branches.

rebase: takes my commits and replays them on top of another branch. The history is a straight line but the commits are new (new hashes), so I should not rebase commits that others already have.

#### Local and remote repositories

# Clone hello as cloned_hello

cd work

git clone hello cloned_hello

# Show the logs of the cloned repository

cd cloned_hello

git log

# Display the name of the remote and more information

git remote -v

git remote show origin

the remote is called origin.

# List all remote and local branches

git branch -a

a clone only creates the local main branch. The other ones show as remotes/origin/...

# Change the README.md in the original repository

cd ../hello

I added the line "(changed in the original)" in README.md then:

git add README.md

git commit -m "Update README in original"

# Fetch the changes in cloned_hello and show the logs

cd ../cloned_hello

git fetch

git log --oneline --all

fetch only downloads the new commits, it doesn't change my local branches.

# Merge the remote main into the local main

git merge origin/main

# Add a local branch greet tracking origin/greet

git checkout -b greet --track origin/greet

# Add a remote and push main and greet

git remote add shared ../hello.git

git push shared main greet

for this the bare repository hello.git must exist first (see the next part).

# Single command equivalent to fetch and merge

git pull

git pull is git fetch followed by git merge. Example: git pull origin main

#### Bare repositories

# What is a bare repository and why is it needed?

a bare repository has no working directory, it only has the content of the .git folder. It is needed to be a central place where everybody can push and pull. If I push to a normal repository on the branch that is checked out, git refuses, because it would change the files of the person working there. A bare repository has no files checked out so pushing is safe. GitHub and Gitea use bare repositories.

# Create the bare repository hello.git from hello

cd work

git clone --bare hello hello.git

# Add hello.git as a remote to hello

cd hello

git remote add shared ../hello.git

# Change the README.md, commit and push to shared

I added the line "(Changed in the original and pushed to shared)" in README.md then:

git add README.md

git commit -m "Update README and push to shared"

git push shared main

# Pull the changes in cloned_hello

cd ../cloned_hello

git remote add shared ../hello.git

git pull shared main

I had to add the shared remote in cloned_hello because its origin is hello and not hello.git.

#### Challenges and lessons learned

- the Makefile needs a real TAB before bash.
- git add -p can't split lines that are next to each other, I had to use e to edit the hunk.
- ours and theirs are swapped in a rebase compared to a merge.
- revert is safe because it adds a new commit, but reset --hard and amend rewrite the history.
- a commit is really deleted only when no branch, tag or reflog points to it.
