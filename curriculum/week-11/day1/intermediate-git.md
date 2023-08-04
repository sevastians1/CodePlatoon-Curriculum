# Intermediate Git

This lecture will quickly review the git skills from the previous lecture, and also explain more advanced concepts that will be useful during group projects, such as remotes, branching, and merging. 


## Objectives
The student should understand repositories and repository management including:
- basic git commands: init, add, commit, checkout, status, log, push, pull
- commit references (head, absolute, relative)
- branching, merging
- advanced git commands: diff, merge, reset, revert
- branching strategy


### What is a Git repository?
A branching, historical timeline of commits

### Working on one local branch
- Where are we now?             `git status`
- Where have we been?           `git log`
    - shows all commits before head, which MAY be all commits in the repo
    - Note: Use `git log --all --graph --decorate` to show commit history with commit labels.
- What has changed?             `git diff`
    - compares any two commits or working files - defaults to head vs working files
- Going back in time            `git checkout [commit]`, detached head
    - absolute commit references
        - head - your current checked-out commit
        - commit id e.g. c32e68b8830d... etc etc etc
        - branch name, e.g. 'master'
        - head and branches are moving labels. commit id's never move
    - relative commit references
        - `head^`, `head^^`
        - `head~1`, `head~2`
    - destroy recent commits    `git reset --hard head`
    - commit to undo a commit   `git revert`
    - recreate the past         `git checkout [commit] [file]`


```bash
# where are we now?
mkdir git-demo
cd git-demo
git init
git status
touch first-file.txt
echo 'first line of text' >> first-file.txt # or just use your editor
git status
git add .
git commit -m 'added the first line of text'
git status

# what has changed?
echo 'second line of text' >> first-file.txt #or just use your editor
git diff # no arguments, compares work dir to current checked out commit
git add .
git commit -m 'added the second line of text'
git log # each commit has a permanent label, the commit ID. There are also movable labels, such as 'master' and 'head'

git diff <commit id> # one argument, compares current checked out commit to the commit passed as an argument
git diff HEAD~1 #tilde is a relative commit label, goes back n generations. different from ^, which grabs the nth parent, used for of merge commits

# going back in time
git checkout HEAD~1 # detached head state. dont make changes while head is detached, like a time traveler who doesn't want to alter the past
git checkout master # return to the present
echo 'third line of text' >> first-file.txt
git status
git diff
git reset --hard # deletes changes in work dir since last commit
git status
git reset --hard head~1 # permanently deletes the most recent commit. you should almost never do this, especially with code that is shared with others
touch second-file.txt
git add .
git commit -m 'second file'
git touch third-file.txt
git add .
git commit -m 'third file'
git log
git revert head~1 # doesn't exactly go back in time, but it creates a new commit that undoes a previous one
git log
ls # we reverted the commit that added the second file, so it should no longer be present in our work dir
git checkout head~1 . # when checkout is used with two arguments like this, it doesn't change the current checked out commit. instead, it changes your work dir to look as it did in this commit. You can test the code, and if you like, you can commit those changes. 
git status
git add .
git commit -m 'went back to a previous state'

```
           
### Working on multiple local branches
- view branches         `git branch`
- create branches       `git branch [branch]`
- delete branches       `git branch -d [branch]`
- switch branches       `git checkout [branch]`
- merge branches        `git merge [branch]`
    - what's changed?   `git diff [branch]`
    - trivial merge - fast-forward does not create a commit
    - automatic merge - recursive strategy creates a new commit
    - manual merge - merge conflicts require YOU to create a commit

```bash
git branch # shows our current branches. by default, we only have 'master'
git branch devlope # oops, mispelled our branch name
git branch -d devlope # I haven't pushed this branch yet, so i'll fix this before anyone notices
git branch develop # commonly, a project will have two permanent branches, master and develop
git log --graph --all --decorate=full # we created a new branch, but not a new commit. both 'master' and 'develop' point to the same commit
git checkout develop # switch to our new branch, develop
git log --graph --all --decorate=full # 'head' now points to develop, not master. develop and master both point to the same commit though
touch dev-file.txt
git add .
git commit -m 'added a file in the dev branch'
git diff master
git merge master # nothing happens, because there are no new changes in master that are missing from develop
git checkout master
git merge develop # fast-forward merge, because develop has new changes that are missing from master
git log --graph --all --decorate=full # no new commits were created, we just moved the label for master
touch master-file.txt
git add .
git commit -m 'added master file'
git checkout develop
echo 'text in the dev file' >> dev-file.txt
git add .
git commit -m 'added text in dev file'
git log --graph --all --decorate=full # we can now see a split in our git graph, because our branches have diverged
git diff master
git merge master # each branch has different changes, but git can automatically create a merge commit. This is the only time when a commit can have two parents!
git checkout master
git merge develop # after successfully merging master into develop, merging the other way will always be a fast-forward merge
echo 'master says no' >> first-file.txt
git add .
git commit -m 'master added text to first file'
git checkout develop
echo 'develop says yes' >> first-file.txt
git add .
git commit -m 'develop added text to first file'
git diff master # both branches changed the same line of the same file! git cannot merge these changes automatically
git merge master # this creates a merge conflict. manually fix your files in the places where git says it's confused
git add .
git commit -m 'resolved merge conflict'
git checkout master
git merge develop # this is a fast-forward merge, since we already resolved the conflict from merging master into develop. avoid resolving merge conflicts in master, if you can.
git log --graph --all --decorate=full # our branches have converged

```


### Working on remote branches
- pushing/updating remote branches      `git push [remote] [local branch]:[remote branch]`
- deleting remote branches              `git push [remote] :[remote branch]`
- viewing remotes branches
    - view remote-tracking branches     `git branch -a`
    - list git remotes                  `git remote`
- pulling/merging from remote branches  `git pull = git fetch + git merge

```bash
# first, let's create a repo in github so we have somewhere to push our local repo
git remote # nothing to see here
git remote add origin https://github.com/raphael-codeplatoon/git-demo.git # it is VERY customary to name the first remote 'origin'
git remote -v # view your remote branches
git push origin master:master # push our local master branch onto origin's master branch
git push origin master # commonly, the source branch and target branch have the same name, so you can use the shortcut

git branch oops # another mistakenly created branch
git push origin oops # even worse, I pushed my mistake to github
git push origin :oops # deleting branches uses the same syntax! we push _nothing_ from our local repo onto the 'oops' branch in the remote repo.

# how would we update our local repo with code from other developers on our team?
# let's edit a file in github, to simulate another developer changing the code.

git fetch # this downloads the changes from github, but it doesn't affect our local branches. so where did it go?
git branch -a # this shows all branches, including remote-tracking branches, which are automatically updated to mirror the remote when you use 'git fetch' or 'git push'
git diff remotes/origin/master
git merge remotes/origin/master # merge the code we fetched from origin into our local master branch

# it's not usually necessary to manually fetch and then merge code from a remote. a common shortcut is using 'git pull origin master', which is just 'git fetch origin' followed by 'git merge remotes/origin/master'
git pull origin master

```

## Branching strategy and Git Flow
- Long-term branches
    - Master or Main - live production code, **MUST** not break
    - Develop - merges into master, **SHOULD** not break
- Short-term branches
    - feature/ - branch off develop, merge into develop
    - bugfix/ - branch off develop, merge into develop
    - hotfix/ - branch off master, merge into master


## Assignments
- [Learn Git Branching](https://learngitbranching.js.org/)
