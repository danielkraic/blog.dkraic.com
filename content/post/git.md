+++
date = "2016-08-29T15:25:28+02:00"
tags = ["git", "tools"]
title = "git"

+++

# git

## config

```bash
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
git config --global core.editor nvim
git config --global core.difftool meld
git config --global core.mergetool meld

git difftool --tool-help
git mergetool --tool-help

git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status

git config --global alias.unstage 'reset HEAD --'
# git unstaged file_name
# git reset HEAD file_name

git config --global alias.visual '!gitk'
```

```bash
cat /etc/gitconfig
cat ~/.gitconfig

git config --list
git config user.name
```

## help

```bash
git help <verb>
git <verb> --help
man git-<verb>
git help config
```

## gitignore

* Blank lines or lines starting with # are ignored.
* Standard glob patterns work.
* You can start patterns with a forward slash ( / ) to avoid recursivity.
* You can end patterns with a forward slash ( / ) to specify a directory.
* You can negate a pattern by starting it with an exclamation point ( ! ).

```bash
cat .gitignore
# no .a files
*.a
# except lib.a
!lib.a
# only ignore the TODO file in the current directory, not subdir/TODO
/TODO
# ignore all files in the build/ directory
build/
# ignore doc/notes.txt, but not doc/server/arch.txt
doc/*.txt
# ignore all .pdf files in the doc/ directory
doc/**/*.pdf
        ```

## init

```bash
git init
git clone [url]
git clone [url] dir_nam
```

## status

```bash
git status
git status -s
git status --short
git add README
```

## diff

```bash
git diff
git diff --staged
git diff --cached
# --staged and --cached are synonyms
git difftool
git difftool --tool-help
```

## commit

```bash
git commit
git commit -v
git commit -m 'commit msg'
git commit -a
git commit -a -m 'commit msg'
```

# undo

```bash
git commit -m 'msg'
git add forgotten_file
git commit --amend

git reset HEAD staged_file

git checkout modified_file
```

## move and delete

```bash
git rm file
git rm --cached file # remove only from stage
git rm \*~
git mv file1 file2
```

## log

```bash
git log
git log --stat # see commit stats
git log --shortstat
git log --graph
git log --abbrev-commit
git log --name-status
git log --name-only
git log -p # print diff
git log --oneline --decorate
git log --oneline --decorate --graph --all

git log -2 # limit to last 2 commits
git log --after=2.weeks # --since, --after
git log --before=2.weeks # --until, --before
git log --since=2008-01-15
git log --author=name
git log --commiter=name
git log -Sfunction_name # grep added/removed code
git log --grep pattern # grep commit message

git log --pretty=oneline
git log --pretty=short
git log --pretty=full
git log --pretty=fuller
git log --pretty=format:"%h - %an, %ar : %s"
```

pretty log options:
* %H Commit hash
* %h Abbreviated commit hash
* %T Tree hash
* %t Abbreviated tree hash
* %P Parent hashes
* %p Abbreviated parent hashes
* %an Author name
* %ae Author email
* %ad Author date (format respects the --date=option)
* %ar Author date, relative
* %cn Committer name
* %ce Committer email
* %cd Committer date
* %cr Committer date, relative
* %s Subject

## remotes

```bash
git remote
git remote -v
git remote show remote_name
git remote add remote_name remote_url
git remote rename old_remote_name new_remote_name
git remote rm remote_name

git fetch remote_name
git fetch --all
git pull # fetch and merge (from server you originally clone from)
git push remote_name branch_name
```

## tags

```bash
git tag
git tag -l "v1.8.5*" # grep tags

# create anotated tag
git tag -a v1.4 -m "my version 1.4"
git show v1.4

# create tag into history
git log --pretty=oneline
git tag -a v1.2 9fceb02

# create lightweight tag
git tag v1.4-lw

# tags are not pushed by default, need to do it explicitly
git push origin v1.4
git push origin --tags

git checkout -b branch_name tag_name
```

## branch

```bash
git branch
git branch -v # see last commit
git branch --merged
git branch ---no-merged

git branch new_branch_name
git checkout new_branch_name

git checkout -b new_branch_name

git checkout master
git merge new_branch_name

git branch -d new_branch_name
git branch -D new_branch_name
```

## remote branches

```bash
git ls-remote
git ls-remote [remote]
git remote show [remote]

git checkout -b br1 origin/br1
# or
git checkout --track origin/br1
# or
git checkout br1

git checkout -b different_br_name origin/br1

# set existing branch to track remote branch`
git branch -u origin/serverfix

git branch -vv # see tracking branches info

# delete remote branch
git push origin --delete br1
```
