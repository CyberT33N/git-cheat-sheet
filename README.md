# Git Sheet Sheet
Git Sheet Sheet with the most needed stuff...












<br><br>


# Postman

<br><br>

## Import Repo to Postman
- https://www.youtube.com/watch?v=llfjyQbyeUQ








<br><br>
______________________________________________________
<br><br>

# Install
```shell
# Method #1
sudo apt-get install git



# Method #2
sudo apt install libz-dev libssl-dev libcurl4-gnutls-dev libexpat1-dev gettext cmake gcc -y

# get latest version and replace in link below
# https://github.com/git/git/tags
wget -c https://github.com/git/git/archive/refs/tags/v2.44.0.tar.gz -O - | sudo tar -xz -C /usr/src

cd /usr/src/git-*
sudo make prefix=/usr/local all
sudo make prefix=/usr/local install

git --version
```

<br><br>

# Uninstall
```shell
sudo apt-get purge git
sudo apt-get autoremove
sudo apt-get install git
```















<br><br>
<br><br>
______________________________________________________
<br><br>
<br><br>

# Terminal Editor

<br><br>

## Nano
```shell
git config core.editor "nano"
```


















<br><br>
______________________________________________________
<br><br>

## Workflows
- The next section does contain example of what commands to use in different all day scenarios

<br><br>

### Create new branch and use current code status without git add/stash
- You can just create a new switch and then checkout on it. The current code from your old branch will be copied in the new you had created and where you checkout.
```bash
git branch my-test-branch
git checkout my-test-branch
```




<br><br>
<br><br>

### Merge

<br><br>

#### Squash merge branch into current branch without merge conflicts
```bash
git checkout sourceBranch
git merge --squash targetBranch
git commit -m "Test"
git push
```

<br><br>

#### Squash merge branch into current branch with merge conflicts
```bash
git checkout sourceBranch
git merge --squash targetBranch

# Merge Conflicts with your IDE

git commit -m "Test"
git push
```

<br><br>

#### Revert merge if no commit was made by deleting the branch locally
```bash
git checkout otherBranch
git branch -D targetBranch # will delete the branch local
git checkout targetBranch
```












<br><br>
<br><br>

### Rebase

<br><br>

#### Squash merge branch into current branch without merge conflicts
```bash
git fetch -u origin develop:develop
git rebase develop

# Resolve Merge Conflicts
# If there are merge conflicts and you solve them use
# git add .
# git rebase --continue

git push -f
```







<br><br>
<br><br>



### Pull

<br><br>

#### Current branch is local outdated and there are changes remote
```bash
# hint: You have divergent branches and need to specify how to reconcile them.
# hint: You can do so by running one of the following commands sometime before
# hint: your next pull:
# hint: 
# hint:   git config pull.rebase false  # merge (the default strategy)
# hint:   git config pull.rebase true   # rebase
# hint:   git config pull.ff only       # fast-forward only
# hint: 
# hint: You can replace "git config" with "git config --global" to set a default
# hint: preference for all repositories. You can also pass --rebase, --no-rebase,
# hint: or --ff-only on the command line to override the configured default per
# hint: invocation.
# fatal: Need to specify how to reconcile divergent branches.

git config pull.rebase false
git pull
```








<br><br>
<br><br>


### Branches

#### develop, feature and feature-dev branch
```
# Pull from latest main branch in this case develop and then create feature branch
git checkout develop
git pull
git branch fix/TICKET-232/edit-custom-block/main

# Create feature-dev branch, checkout to it and then work on your ticket
git checkout fix/TICKET-232/edit-custom-block/main
git branch fix/TICKET-232/edit-custom-block/kho
git checkout fix/CCS-232/edit-custom-block/kho

# Merge feature-dev branch into feature branch
git checkout fix/TICKET-232/edit-custom-block/main
git merge --squash fix/TICKET-232/edit-custom-block/kho
git commit -m "fix(TICKET-232): Edit template"

# Rebase develop branch on feature branch
git fetch -u origin develop:develop
git rebase develop

# Push feature branch
git push --force --set-upstream origin fix/TICKET-232/edit-custom-block/main

# Create MR/PR
```







<br><br>
______________________________________________________
<br><br>


# Git

## Init repo where no .git folder exist
```bash
git init
```


<br><br>
<br><br>

## Git LFS

<br><br>

#### Upload/Commit bigger files than 100mb (https://git-lfs.github.com/)
```bash
# Download (https://github.com/git-lfs/git-lfs/releases/latest) and install the Git command line extension. Once downloaded and installed, set up Git LFS for your user account by running:
git lfs install

#In each Git repository where you want to use Git LFS, select the file types you'd like Git LFS to manage (or directly edit your .gitattributes). You can configure additional file extensions at anytime.
git lfs track "*.psd"

# Now make sure .gitattributes is tracked:
git add .gitattributes

# Note that defining the file types Git LFS should track will not, by itself, convert any pre-existing files to Git LFS, such as files on other branches or in your prior commit history. To do that, use the git lfs migrate[1] command, which has a range of options designed to suit various potential use cases.

# There is no step three. Just commit and push to GitHub as you normally would; for instance, if your current branch is named main:
git add file.psd
git commit -m "Add design file"
git push origin main
```

<br><br>


#### Uninstall from repo
```bash
git lfs uninstall
```























































































































<br><br>
______________________________________________________
<br><br>


# Git

## Init repo where no .git folder exist
```bash
git init
```









## Command Line Cheat Sheet
- https://about.gitlab.com/images/press/git-cheat-sheet.pdf






























<br><br>
______________________________________________________
<br><br>

## Most asked questions

<br><br>

#### How to checkout on another branch after you do local changes on your current branch?
- You may get a error that you have to commit or stash your current changes before you can do a checkout. Here are your solutions:
  - Delete old branch where we want to checkout
  - Create commit on the current branch
  - Stash


















<br><br>
______________________________________________________
<br><br>


# Definition of “downstream” and “upstream”
- https://stackoverflow.com/questions/2739376/definition-of-downstream-and-upstream
```bash
In terms of source control, you're "downstream" when you copy (clone, checkout, etc) from a repository. Information flowed "downstream" to you.

When you make changes, you usually want to send them back "upstream" so they make it into that repository so that everyone pulling from the same source is working with all the same changes. This is mostly a social issue of how everyone can coordinate their work rather than a technical requirement of source control. You want to get your changes into the main project so you're not tracking divergent lines of development.

Sometimes you'll read about package or release managers (the people, not the tool) talking about submitting changes to "upstream". That usually means they had to adjust the original sources so they could create a package for their system. They don't want to keep making those changes, so if they send them "upstream" to the original source, they shouldn't have to deal with the same issue in the next release.
```



# Forking
-  https://guides.github.com/activities/forking/#:~:text=After%20using%20GitHub%20by%20yourself,contribute%20to%20someone%20else's%20project.&text=Creating%20a%20%E2%80%9Cfork%E2%80%9D%20is%20producing,repository%20and%20your%20personal%20copy.






























<br><br>
______________________________________________________
<br><br>



# change timeout for session password
```bash
# if terminal closed it must be typed again
git config --global credential.helper "cache --timeout=3600"
# 0 should be unlimited
```

<br>

# disable password verify by using .git-credentials at %HOME%
```bash
git config --global credential.helper store
```




























<br><br>
______________________________________________________
<br><br>


# config

## Set the name that will be attached to your commits and tags.
```bash
git config --global user.name "yourname.."
```

<br><br>

## Set the e-mail address that will be attached to your commits and tags.
```bash
git config --global user.email "sample@mail.com"
```


<br><br>

## Enable some colorization of Git output.
```bash
git config --global color.ui auto
```

 


























<br><br>
______________________________________________________
<br><br>

## SSH
- GitLab (https://docs.gitlab.com/ee/ssh/README.html)

## How to generate Private & Public Key (https://docs.gitlab.com/ee/ssh/README.html#generating-a-new-ssh-key-pair)
- It is recommended to create multiple ssh keys and and not use 1 for everything
```bash
## Default your key will keys will be saved to ~/.ssh/
## Your custom name keys will be stored in current directoy where you executed the command. E.g. ~/samplePubKey.pub
# ssh-keygen -t ecdsa -b 521
ssh-keygen -t ecdsa -b 521 -f /home/tuserNameHere/.ssh/github/id_ecdsa
```

<br><br>

Next add your public key to your git settings (~/.ssh/id_ecdsa.pub):
- https://gitlab.com/-/profile/keys
- https://github.com/settings/keys
- https://bitbucket.org/account/settings/ssh-keys/

<br><br>

Now when you clone your repo via command line you will get asked for passphrase to verify. This will be only done 1 time.

<br><br>

## How to use Private Key global
```bash
# Method 1 - Edit ~/.ssh/config and enter:
host github.com
 HostName github.com
 IdentityFile /home/tuserNameHere/.ssh/github/id_ecdsa
 User git
 ```
 
## How to store passphrase
- Add your private key. If you have have multiple ssh keys and you e.g. create the new ssh key here /home/tuserNameHere/.ssh/github/id_ecdsa then you have to add it in order to use it on your machine
```bash
# Method #1
eval $(ssh-agent -s)
ssh-add /home/tuserNameHere/.ssh/github/id_ecdsa

# Method 2 - Use this command on your private key e.g.
ssh-add /home/tuserNameHere/.ssh/github/id_ecdsa

# Method 3 (Not sure if this will work)
ssh-add

# Method 4 - Add private key to keychain. You must 1 time verify it manually and then it will be saved
ssh-add -K /home/tuserNameHere/.ssh/github/id_ecdsa
reboot
```







































<br><br>
______________________________________________________
<br><br>


# clone
- Download repo and create folder related to the repo name
```bash
git clone https://github.com/CyberT33N/Socket.io-Chat-APP-Example.git
```

## Clone repo with specific branch
```bash
git clone -b my-branch git@github.com:user/myproject.git
```

## Clone all repos
- If you use SSH key for auth instead of the Access Token then you must add your private key to your machine that git clone command will work!
```bash
# ---- VARIABLE ----
ACCESS_TOKEN="xxxxxxxxxxxxxxxxxxxxxx"
USERNAME="xxxxxxxxxx"

API_LINK="https://api.github.com/user/repos"
GIT_LINK="git@github.com"

EXPORT_PATH="$HOME/Documents/git_projects"

# ---- cd to current directory ----
cd "$EXPORT_PATH"; pwd
printf "\nWe will display now the current directory used:"; echo "$EXPORT_PATH"
printf "\n\nWe will clone now all your repos!\n\nPlease wait.. This maybe take some time..\n"

# ---- clone all git repos - ACCESS TOKEN ----
# for line in $(curl "$API_LINK?access_token=$ACCESS_TOKEN"  | grep -o "$GIT_LINK:$USERNAME/[^ ,\"]\+");
# do printf "\nCurrent repo link: $line"; git clone $line; done

# ---- clone all git repos - SSH KEY ----
# Notice that we have a limit of 100 items per page. If you want to get more than 100 repos this script must be edited..
for line in $(curl "$API_LINK?access_token=$ACCESS_TOKEN&per_page=100"  | grep -o "$GIT_LINK:$USERNAME/[^ ,\"]\+");
do printf "\nCurrent repo link: $line" &&
  git clone $line &
done
wait


# ---- END AREA ----
printf "\nWe finished the .sh file :) - Created by Dennis Demand( github.com/CyberT33N )\n"

```






























<br><br>
______________________________________________________
<br><br>

# squash
- https://www.devroom.io/2011/07/05/git-squash-your-latests-commits-into-one/
- With git it’s possible to squash previous commits into one. This is a great way to group certain changes together before sharing them with others.
```bash
# * df71a27 - (HEAD feature_x) Updated CSS for new elements (4 minutes ago)
# * ba9dd9a - Added new elements to page design (15 minutes ago)
# * f392171 - Added new feature X (1 day ago)
# * d7322aa - (origin/feature_x) Proof of concept for feature X (3 days ago)

git rebase -i HEAD~3

# text editor will open. Pick the commit you want to squash all others to.
pick f392171 Added new feature X
squash ba9dd9a Added new elements to page design
squash df71a27 Updated CSS for new elements

# * f392171 - Added new feature X (1 day ago)
```









































<br><br><br><br>
______________________________________________________
<br><br>



# Log




<br><br>

## Show logs of last commits
```bash
git log

# beautify
git log --all --graph --decorate --oneline
```







<br><br>

## Show all commit hashes/id
```bash
# This will be for all branches
git log --pretty=format:"%h"

# This will be for specific branch
git log main..feat/CCS-1114/new-data-structure/main --pretty=format:"%h"

## This will be for specific branch and show more details than only commit hash
git log main..feat/CCS-1114/new-data-structure/main --oneline
```































<br><br><br><br>
______________________________________________________
<br><br>


# status (https://git-scm.com/docs/git-status)
- display state of project (current branch and more..)
- display changes

```bash
git status
```

































<br><br>
______________________________________________________
<br><br>


# add


## add all files in folder recursive
- You should always add each file manually to prevent mistakes. Only use this command when you are 100% sure.
```bash
git add .

# add parent folder
git add ..
```






































<br><br>
______________________________________________________
<br><br>


# commit

## Commit Message Conventions
- https://github.com/angular/angular.js/blob/master/DEVELOPERS.md#-git-commit-guidelines
- https://gist.github.com/stephenparish/9941e89d80e2bc58a153
```bash
feat (feature)
fix (bug fix)
docs (documentation)
style (formatting, missing semi colons, …)
refactor
test (when adding missing tests)
chore (maintain)
```

<br><br>

## commit all changes from all files
```bash
git add .
git commit -m "TITLE" -m "DESCRIPTION"
```

<br><br>

## commit specific file
```bash
git add app.js
git commit -m "TITLE" -m "DESCRIPTION"
```

<br><br>


## empty commit
```bash
git commit -a --allow-empty --allow-empty-message -m ''
```

<br><br>



## overwrite/delete last commit (amend)
```bash
git add .
git commit --amend -m "an updated commit message"
git push -f


# Skip commit message dialog by using message from last commit
git add . && git commit --amend --reuse-message HEAD && git push -f
```
































<br><br>
______________________________________________________
<br><br>

# What is origin? (https://stackoverflow.com/questions/9529497/what-is-origin-in-git)
- origin is an alias on your system for a particular remote repository. It's not actually a property of that repository.
- Remotes are simply an alias that store the URL of repositories. You can see what URL belongs to each remote by using
```bash
git remote -v
```

As example if you have the same repo on github and gitlab you can switch between your remote repos and origin would be just the name. So instead it could be aswell gitlab oder github









































<br><br>
______________________________________________________
<br><br>

# submodule

<br><br>

## Remove submodule
```bash
# Remove the submodule entry from .git/config
git submodule deinit -f path/to/submodule

# Remove the submodule directory from the superproject's .git/modules directory
rm -rf .git/modules/path/to/submodule

# Remove the entry in .gitmodules and remove the submodule directory located at path/to/submodule
git rm -f path/to/submodule
```
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	






<br><br>
______________________________________________________
<br><br>



# fetch (https://git-scm.com/docs/git-fetch)

## difference between fetch and pull
- https://stackoverflow.com/questions/292357/what-is-the-difference-between-git-pull-and-git-fetch
- pull does a git fetch followed by a git merge
- You can do a git fetch at any time to update your remote-tracking branches under refs/remotes/<remote>/. But your local files fill NOT update


<br><br>

## Force pull by overwriting local files
```bash
git fetch --all

# if you are on the master branch
git reset --hard origin/master

# if other branch
# git reset --hard origin/<branch_name>
```

<br><br>

## Fetch from specific remote repo
```bash
git fetch -u origin localbranch:remotebranch

# What is origin?
# https://github.com/CyberT33N/git-cheat-sheet#what-is-origin-httpsstackoverflowcomquestions9529497what-is-origin-in-git

#
```

































<br><br>
______________________________________________________
<br><br>


# pull
- Download latest version of your repo
```bash
git pull
```

<br><br>


# force pull by deleting last local changes
- Download latest version of your repo
```bash
git reset --hard HEAD
git pull
```


































<br><br>
______________________________________________________
<br><br>


# push (https://git-scm.com/docs/git-push)
- Update your repo based on your commits
```bash
git push
```
<br><br>

# fatal: You are not currently on a branch. To push the history leading to the current (detached HEAD)
```bash
git push origin HEAD:branchname --force
```


<br><br>

## Push to specific remote repo
```bash
git push REMOTE_REPO_NAME BRANCH
```

## Push to all remote repos
```bash
git remote | xargs -L1 git push --all
```









































<br><br>
______________________________________________________
______________________________________________________
<br><br>

# Branch

<br><br>

## get current Branch
```
git rev-parse --abbrev-ref HEAD
# git pull origin $(git rev-parse --abbrev-ref HEAD)
```

<br><br>


## Fixing the “GH001: Large files detected. You may want to try Git Large File Storage.”
- It turned out that GitHub only allows for 100 MB file. The problem is that I can’t simply remove the file because it is tracked inside the previous commits so I have to remove this file completely from my repo. The command that allow you to do it is:
```bash
git filter-branch -f --index-filter 'git rm --cached --ignore-unmatch fixtures/11_user_answer.json'
```

<br><br>	
	
## Show all branches local and remote
```bash
git branch -av
```

<br><br>

## Get default branch
```bash
git remote show <remote_name> | awk '/HEAD branch/ {print $NF}'

#REMOTE_DEFAULT_BRANCH=$( git remote show $REMOTE_REPO_NAME | awk '/HEAD branch/ {print $NF}' )
#printf "\nDefault Remote Branch: $REMOTE_DEFAULT_BRANCH \n"
```
<br><br>

## Check all branches
```bash
git branch
```

## Check current branch 
```bash
git branch --show-current
```

<br><br>


## Create branch
```bash
git branch branch_name
```


<br><br>

## delete branch
```bash
// delete branch locally
git branch -d localBranchName

// delete branch remotely
git push origin --delete remoteBranchName
```



<br><br>

## rename branch
```bash
git branch -m "old-branch-name-here" "new-branch-name-here"
```






























































<br><br>
______________________________________________________
______________________________________________________
<br><br>

# Checkout

<br><br>

## switch branch
```bash
git checkout branch_name
```

<br><br>

## delete local files and switch back to state of remote branch
- When you locally delete a branch and then checkout again on it you will download the latest files from your remote branch.
```bash
git branch -D yourbranch
git checkout yourbranch
```



<br><br>

## Go back to specific commit and reset files
```bash
# First clone your project
git clone git://github.com/facebook/facebook-ios-sdk.git

Then checkout by commi SHA. You can find the SHA in your github/gitlab commit.
git checkout xxxxxSHA-HERExxxxx
```




<br><br>


## checkout tag
- If you want to view the versions of files a tag is pointing to, you can do a git checkout of that tag, although this puts your repository in “detached HEAD” state, which has some ill side effects:
```bash
$ git checkout v2.0.0
Note: switching to 'v2.0.0'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false

HEAD is now at 99ada87... Merge pull request #89 from schacon/appendix-final

$ git checkout v2.0-beta-0.1
Previous HEAD position was 99ada87... Merge pull request #89 from schacon/appendix-final
HEAD is now at df3f601... Add atlas.json and cover image










In “detached HEAD” state, if you make changes and then create a commit, the tag will stay the same, but your new commit won’t belong to any branch and will be unreachable, except by the exact commit hash. Thus, if you need to make changes — say you’re fixing a bug on an older version, for instance — you will generally want to create a branch:

$ git checkout -b version2 v2.0.0
Switched to a new branch 'version2'
```







































<br><br>
______________________________________________________
______________________________________________________
<br><br>



# Remote
- Basic Guide (https://git-scm.com/book/de/v2/Git-Grundlagen-Mit-Remotes-arbeiten)


<br><br>


## Push Github repo to Gitalab
```bash
git clone your_github_repo

# add remote repo
git remote add gitlab http://gitlab.urlhere/username/projectname.git

## Push repo to gitlab
git push gitlab
```

<br><br>


## Check all current remote links of your project
```bash
git remote -v
```

<br><br>

## How to use Github and GitLab on same machine

	
Pushing to Multiple Git Repos

## All in one script example
```shell

mkdir ~/Projects
cd ~/Projects
git clone git@github.com:CyberT33N/dev-environment.git
git clone git@github.com:CyberT33N/errormanager.git
git clone git@github.com:CyberT33N/puppeteerservices.git
git clone git@github.com:CyberT33N/errormanager.git

	
# ---- DEV ENVIRONMENT ----
cd ~/Projects/dev-environment

# method 1
git remote add github git@github.com:CyberT33N/dev-environment.git
git remote add bb git@bitbucket.org:CyberT33N/dev-environment.git
git remote add gitlab git@gitlab.com:CyberT33N/dev-environment.git
git remote add gitlabInternal git@gitlab.local.com:CyberT33N/dev-environment.git

# Method #2 - Maybe not needed when you sue method #1
# git remote set-url --add --push origin git@github.com:CyberT33N/dev-environment.git
# git remote set-url --add --push origin git@bitbucket.org:CyberT33N/dev-environment.git
# git remote set-url --add --push origin git@gitlab.com:CyberT33N/dev-environment.git
# git remote set-url --add --push gitlabInternal git@gitlab.local.com:CyberT33N/dev-environment.git

```
	
	
	
	
-----------------------------

If a project has to have multiple git repos (e.g. Bitbucket and
Github) then it's better that they remain in sync.

Usually this would involve pushing each branch to each repo in turn,
but actually Git allows pushing to multiple repos in one go.

If in doubt about what git is doing when you run these commands, just
edit ``.git/config`` (`git-config(1)`_) and see what it's put there.


Remotes
=======

Suppose your git remotes are set up like this::

    git remote add github git@github.com:muccg/my-project.git
    git remote add bb git@bitbucket.org:ccgmurdoch/my-project.git
    git remote add gitlabInternal git@gitlab.local.com:websites/my-project.git

The ``origin`` remote probably points to one of these URLs.


Remote Push URLs
================

To set up the push URLs do this::

    git remote set-url --add --push origin git@github.com:muccg/my-project.git
    git remote set-url --add --push origin git@bitbucket.org:ccgmurdoch/my-project.git
    git remote set-url --add --push gitlabInternal git@gitlab.local.com:websites/my-project.git

It will change the ``remote.origin.pushurl`` config entry. Now pushes
will send to both of these destinations, rather than the fetch URL.

Check it out by running::

    git remote show origin


Then run::
	
    git push github && git push gitlab
	
	
Add this to your ~/.gitconfig::
	
    [alias]
        pushall = "!f(){ for i in `git remote`; do git push $i; done; };f"
	
    [alias]
        pushallforce = "!f(){ for i in `git remote`; do git push -f $i; done; };f"

	
	
Remove all remote repos
================
```branch
for remote_name in $(git remote); do git remote remove "${remote_name}"; done
```
	
Per-branch
==========

A branch can push and pull from separate remotes. This might be useful
in rare circumstances such as maintaining a fork with customizations
to the upstream repo. If your branch follows ``github`` by default::

    git branch --set-upstream-to=github next_release

(That command changed ``branch.next_release.remote``.)

Then git allows branches to have multiple ``branch.<name>.pushRemote``
entries. You must edit the ``.git/config`` file to set them.


Pull Multiple
=============

You can't pull from multiple remotes at once, but you can fetch from
all of them::

    git fetch --all

Note that fetching won't update your current branch (that's why
``git-pull`` exists), so you have to merge -- fast-forward or
otherwise.

For example, this will octopus merge the branches if the remotes got
out of sync::

    git merge github/next_release bb/next_release

<br><br>
	
#### Method #2
```bash
host github.com
 HostName github.com
 IdentityFile ~/.ssh/id_ecdsa
 User git
 
 # GitLab.com
Host gitlab.com
  Preferredauthentications publickey
  IdentityFile ~/.ssh/id_ecdsa

# Private GitLab instance
Host gitlab.company.com
  Preferredauthentications publickey
  IdentityFile ~/.ssh/id_ecdsa
```

#### Add new remote repo
```bash
# cd into your project and then run:
git remote add REPO_NAME git@gitlab.com:USERNAME/PROJECTNAME.git
```
































<br><br>
______________________________________________________
<br><br>

# clean

<br><br>

## Clean all untracked files that will be ignored by .gitignore
```
git clean -f -d -x -i -e node_modules
```

































<br><br>
______________________________________________________
<br><br>

# reset
- **--soft** will be used by default when you use git reset. Use --soft if you want to keep your changes
- Use **--hard** if you don't care about keeping the changes you made

<br><br>

## reset last head commit
```bash
git checkout <branch-to-modify-head>
git reset --soft HEAD^
git reset --hard HEAD^
git push -f  # force push the last commit to your repo. The deleted commit will be also deleted from your repo
```


<br><br>

## replace last head commit with another commit
```bash
git checkout <branch-to-modify-head>
git reset --hard <commit-hash-id-to-put-as-head>
git push -f
```

<br><br>

## Remove specific amount of head commits but keep local changes
```bash
# git log um zu checken wie viele der letzten Commits vereint werden sollen. Sonst kann man es auch im MR sehen

# Gehe zum gewünschten Branch
git checkout dein_branch

# Dynamisch die letzten z.b. 4 Commits lokal löschen
# Alternativ kannst du es auch einzeln machen mit git reset --soft HEAD^ was die sichere Variante ist..
git reset --soft HEAD~4

# Sicherstellen, dass die betroffenen Commits oben nicht mehr in der History auftauchen:
git log

# Sicherstellen, dass die Dateien die vorher committed waren da sind
git status

# Die vorher committeten Dateien stagen, zb:
git add .

# sicherstellen, dass die gewollten Dateien gestaged sind:
git status

mkbranch

# force pushen, um die commit history im gitlab mit der lokalen zu überschreiben
git push -f
```

<br><br>

## Reset last merge
```bash
# cd into your project and then run:
rm -rf .git/MERGE*
```


<br><br>

## Cancel current merge process
```bash
git reset --hard HEAD
git clean -d -f 
```








































<br><br>
______________________________________________________
<br><br>

# revert (https://www.atlassian.com/git/tutorials/undoing-changes/git-revert)
- The git revert command can be considered an 'undo' type command, however, it is not a traditional undo operation. Instead of removing the commit from the project history, it figures out how to invert the changes introduced by the commit and appends a new commit with the resulting inverse content. This prevents Git from losing history, which is important for the integrity of your revision history and for reliable collaboration.

## revert specific commit
```bash
# use git log to get commit id
git revert id_here
```

## abort revert operation
```bash
git revert --abort
```











































<br><br>
______________________________________________________
<br><br>

# rebase
- does same thing like merge

<br><br>


## rebase master branch into feature branch
```bash
git checkout feature
git commit -m "any cool changes"

git checkout master
git push

git checkout feaure
git rebase master

git push
```


<br><br>


## difference between merge and rebase (https://www.atlassian.com/de/git/tutorials/merging-vs-rebasing)
- merge will create a merge commit on the branch where you are merging
- rebase instead will keep the history of all commits and you can see linear all commits and do not have a merge commit


# interactive rebasing

## create interactive rebasing
```bash
git checkout feature
git rebase -i master

# -i will show all commits and display how your your branch would look like after the rebasing
```









































<br><br>
______________________________________________________
<br><br>

# Remove/Delete

<br><br>

## Remove folder and then create commit
```bash
git add -A
git commit -m 'deleting directory'
git push origin master
```

	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	










<br><br>
______________________________________________________
<br><br>

# merge





## Merge Request
- You can request merge request via Dashboard of Gitlab. In GitHub it is called pull request but means the same.

<br><br>

## Merge Conflict
- After resolve merge conflict use **git commit**

<br><br>

## Ignore merge conflicts
```bash
# cd into your project and then run:
rm -rf .git/MERGE*
```

<br><br>


## merge master branch into feature branch
```bash
git checkout feature
git merge master

# or in 1 line
git checkout feature git merge master

# or in short
git merge feature master
```

















































<br><br>
______________________________________________________
<br><br>

# stash (https://git-scm.com/docs/git-stash)
- Use git stash when you want to record the current state of the working directory and the index, but want to go back to a clean working directory. The command saves your local modifications away and reverts the working directory to match the HEAD commit.
```bash
git stash
```


<br><br>

## Guide
- https://git-scm.com/book/en/v2/Git-Tools-Stashing-and-Cleaning

<br><br>

## show all stashes
```bash
git stash show
```

<br><br>

## list your stashed changes
```bash
git stash list
```

<br><br>

## create new branch from stash
```bash
1. git stash
2. git stash list
3. git stash branch my_feature_branch stash@{0}
```





<br><br><br><br>

## Difference between pop & apply (https://stackoverflow.com/questions/15286075/difference-between-git-stash-pop-and-git-stash-apply).)
- git stash pop throws away the (topmost, by default) stash after applying it, whereas git stash apply leaves it in the stash list for possible later reuse (or you can then git stash drop it).

<br><br>

## apply most recent stash
```bash
git stash apply
```

<br><br>

## apply older stash
```bash
git stash apply stash@{n}
```

<br><br>

## apply stash but Throws away the (topmost, by default) stash after applying it. (https://git-scm.com/docs/git-stash#Documentation/git-stash.txt-pop--index-q--quietltstashgt)
```bash
git stash pop
```



<br><br>

## go back to latest local state after you did pop/apply
```bash
git reset --hard HEAD^
```




<br><br><br><br>

## Remove a single stash entry from the list of stash entries.
```bash
git stash drop
```
























































<br><br>
______________________________________________________
<br><br>

# tag (https://git-scm.com/docs/git-tag)
- Like most VCSs, Git has the ability to tag specific points in a repository’s history as being important. Typically, people use this functionality to mark release points (v1.0, v2.0 and so on). In this section, you’ll learn how to list existing tags, how to create and delete tags, and what the different types of tags are.

<br><br>

## Guides
- https://git-scm.com/book/en/v2/Git-Basics-Tagging

<br><br>

## Listing Your Tags
```bash
git tag
```

<br><br>

## search for specific tag name
```bash
git tag -l "v1.8.5*"
```

<br><br>

## create Annotated Tag
- Annotated tags, however, are stored as full objects in the Git database. They’re checksummed; contain the tagger name, email, and date; have a tagging message; and can be signed and verified with GNU Privacy Guard (GPG). It’s generally recommended that you create annotated tags so you can have all this information; but if you want a temporary tag or for some reason don’t want to keep the other information, lightweight tags are available too.
```bash
# The -m specifies a tagging message, which is stored with the tag. If you don’t specify a message for an annotated tag, Git launches your editor so you can type it in.
git tag -a v1.4 -m "my version 1.4"
git tag
# v1.4
```


<br><br>

## create Lightweight Tag
- Another way to tag commits is with a lightweight tag. This is basically the commit checksum stored in a file — no other information is kept. To create a lightweight tag, don’t supply any of the -a, -s, or -m options, just provide a tag name:
```bash
git tag v1.4-lw
git tag
# v 1.4-lw

```




<br><br>

## delete tag
- To delete a tag on your local repository, you can use git tag -d <tagname>. For example, we could remove our lightweight tag above as follows:
```bash
git tag -d v1.4-lw
# Deleted tag 'v1.4-lw' (was e7d5add)
```
 
 
 



















































<br><br>
______________________________________________________
<br><br>


# Restore (https://git-scm.com/docs/git-restore)
- This only works when you did not use **git add**! If you use git add then you must revert them before,

## restore specific file
```bash
git restore sample.html
```
























































<br><br>
______________________________________________________
<br><br>

# cherry-pick (https://git-scm.com/docs/git-cherry-pick)
- Given one or more existing commits, apply the change each one introduces, recording a new commit for each. This requires your working tree to be clean (no modifications from the HEAD commit).

When it is not obvious how to apply a change, the following happens:

<br> 1. The current branch and HEAD pointer stay at the last commit successfully made.

<br> 2. The CHERRY_PICK_HEAD ref is set to point at the commit that introduced the change that is difficult to apply.

<br> 3. Paths in which the change applied cleanly are updated both in the index file and in your working tree.

<br> 4. For conflicting paths, the index file records up to three versions, as described in the "TRUE MERGE" section of git-merge[1]. The working tree files will include a description of the conflict bracketed by the usual conflict markers <<<<<<< and >>>>>>>.

<br> 5. No other modifications are made.

<br><br>


Syntax:
```bash
git cherry-pick [--edit] [-n] [-m parent-number] [-s] [-x] [--ff]
		  [-S[<keyid>]] <commit>…​
git cherry-pick (--continue | --skip | --abort | --quit)
```

<br><br>















































<br><br>
______________________________________________________
<br><br>


# Delete Files

## delete specific file from disk and repo
```bash
git rm -f your_file.txt
git push -f
```

	
## delete folder recursive from disk and repo
```bash
git rm -rf --cached .idea
```








































<br><br>
______________________________________________________
______________________________________________________
<br><br>

<br><br>

# .gitkeep

<br><br>

## Keep empty folder but still ignore all files inside
```
test-db-dumps/test_333/*
!test-db-dumps/test_333/.gitkeep
```
- Create .gitkeep file at test-db-dumps/test_333/.gitkeep



<br><br>
<br><br>

# .gitignore
- Ignore files and folders for push
```bash
node_modules
test/*
```
- You can use relative paths. e.g. your project is abc/hey/test/test.json you can just use hey/test

<br><br>

## Create .gitignore file and include node_modules folder
```bash
touch .gitignore && echo "node_modules/" >> .gitignore && git rm -r --cached node_modules ; git status
```

<br><br>

## Remove files that are already commited but the folder is listed in .gitignore
```bash
git rm -rf 'lib/main/test/test-db-dumps/test_333'
```





























<br><br>
<br><br>
______________________________________________________
______________________________________________________
<br><br>
<br><br>


# Helper Scripts

## Check if head commit from branch a exists in branch b
```shell

if [ "$ENABLE_CUSTOM_PROJECT_PATH" = "true" ]; then
(
    cd $CUSTOM_PROJECT_PATH

    # Pull the latest changes for both branches
    echo "Pulling latest changes for $BRANCH1"
    git pull origin $BRANCH1
    if [ $? -ne 0 ]; then
        echo "Failed to pull $BRANCH1"
        exit 1
    fi

    echo "Pulling latest changes for $BRANCH2"
    git pull origin $BRANCH2
    if [ $? -ne 0 ]; then
        echo "Failed to pull $BRANCH2"
        exit 1
    fi

    # Get the HEAD commit hash of the first branch
    HEAD_COMMIT_BRANCH1=$(git rev-parse origin/$BRANCH1)

    # Check if the HEAD commit of the first branch is present in the second branch
    if git branch -r --contains $HEAD_COMMIT_BRANCH1 | grep -q "origin/$BRANCH2"; then
        echo "The HEAD commit of $BRANCH1 exists in $BRANCH2."
    else
        echo "Error: The HEAD commit of $BRANCH1 does not exist in $BRANCH2. This means that $BRANCH2 is not up-to-date with $BRANCH1. (Check open MR or create MR: $BRANCH1 -> $BRANCH2)"
        exit 1
    fi
)
fi
```







































<br><br>
______________________________________________________
______________________________________________________
<br><br>





# Gitlab

## Import repos from Github
- https://gitlab.com/import/github/status


<br><br>


## Mirror

#### Auto Sync via Gitlab UI
Open your GitLab repo and then go to **Settings > Repository > Mirroring repositories** (https://gitlab.com/USERNAME/PROJECT-NAME/-/settings/repository)
<br><br>**IMPORTANT** - Please notice that only public repo mirror is currently free. For mirror of privat repos you must buy premium GitLab.

<br><br>

#### Mirror Github 2 GitLab
```bash
: 'This script will clone all your Github repos and then mirror them to GitLab.
This is usefully when you want to keep your repos sync and dont have GitLab Premium.
You should use this script on your server as cron job.

Currently your GitLab account already must contain all the repos you want to mirror.
You can easily achieve this by using: https://gitlab.com/import/github/status

This script will only work when your master branch (normally called main/master) is not protected by GitLab.
You can manually unprotect your master branch at your repo at:
- Settings > Repository > Protected Branch (https://gitlab.com/USERNAME/REPONAME/-/settings/repository)

Alternative when you host GitLab on your own server you can go to admin settings
and change the default settings for protected branch to not do this manually for every repo.

This script is currently limited to 100 repos cause of the Github API page limit.
'



# ---- VARIABLE ----
ACCESS_TOKEN="xxxxxxxxxxxxxxxxxxxxxxxx"
USERNAME="xxxxxx"

API_LINK="https://api.github.com/user/repos"
GIT_LINK="git@github.com"


EXPORT_PATH="$HOME/Documents/git_projects"

# I guess the github limit is 100 item per page. So if you got more than 100 repos this script is not working
PAGE_LIMIT=100

REMOTE_REPO_NAME="gitlab"
REMOTE_REPO_HOST="git@gitlab.com"
REMOTE_USERNAME="xxxxxxx"








CD(){ cd "$1"; printf "\nCD() - We will display now the current directory used:"; pwd; } # CD(){


fakeCommit(){ printf "\n---- fakeCommit() ----\n"
  # create temp file and add files
  touch .placeholder_mirror
  git add .

  # get default branch name
  REMOTE_DEFAULT_BRANCH=$( git remote show $REMOTE_REPO_NAME | awk '/HEAD branch/ {print $NF}' )
  printf "Default Remote Branch name: $REMOTE_DEFAULT_BRANCH\n"

  # dirty workaround for empty push - create stash, push it and then delete it
  git stash save
  git push -f $REMOTE_REPO_NAME "stash@{0}:$REMOTE_DEFAULT_BRANCH"
  git stash pop

  # remove temp file from repo and disc
  git rm -f .placeholder_mirror
} # fakeCommit(){







getRepoName(){ printf "\n---- getRepoName() ----\n"
  # match repo name - git@github.com:USERNAME/REPONAME.git
  [[ $line =~ ([._a-zA-Z0-9-]+).git$ ]]
  printf "Matched project name: ${BASH_REMATCH[0]}\n"

  # replace .git from project name
  REPONAME=$(echo ${BASH_REMATCH[0]} | sed 's/.git//g')
  #printf "\n Matched project name without .git: $REPONAME";
} # getRepoName(){





repoNotExist(){ printf "\n---- repoNotExist() - Repo name: $REPONAME----\n"; git clone $line; } # repoNotExist(){

repoExist(){ printf "\n---- repoExist() - Repo name: $REPONAME----\n"
  # tempMirror folder already exist because of script crashes etc.. we delete it now
  rm -rf $REPONAME/tempMirror

  # Clone just the repository's .git folder (excluding files as they are already in `existing-dir`) into an empty temporary directory
  git clone --no-checkout $line $REPONAME/tempMirror  # might want --no-hardlinks for cloning local repo

  # check if .git folder exist. If true delete it.
  [ -d $REPONAME/.git ] && rm -rf $REPONAME/.git

  # Move the .git folder to the directory with the files.
  # This makes `existing-dir` a git repo.
  mv $REPONAME/tempMirror/.git $REPONAME

  # Delete the temporary directory
  rm -rf $REPONAME/tempMirror
  cd $REPONAME

  # git thinks all files are deleted, this reverts the state of the repo to HEAD.
  # WARNING: any local changes to the files will be lost.
  git reset --hard HEAD
} # repoExist(){










addRemoteRepo(){ printf "\n---- addRemoteRepo() ----\n"
  # cd into the repo we cloned
  CD $REPONAME

  # add gitlab remote repo
  git remote add $REMOTE_REPO_NAME $REMOTE_REPO_HOST:$REMOTE_USERNAME/$REPONAME.git
} # addRemoteRepo(){



main(){ printf "\n---- main() ----\nCurrent repo link: $line"

  # cd back to the git project main folder and get next Repo Name
  CD $EXPORT_PATH
  getRepoName

  # if repo folder does not exist we clone repo and automatically create the folder
  # if repo folder exist we clone to temp dir and then replace the old repo
  [ -d $REPONAME ] && repoExist || repoNotExist

  # add gitlab remote repo as second remote link
  addRemoteRepo

  # create empty fake commit that will not be in history
  fakeCommit

  # mirror local repo by push to remote repo
  git push -f $REMOTE_REPO_NAME
} # main(){


# ---- get all repos by using the API ----
printf "\n\nWe will clone now all your repos!\n\nPlease wait.. This maybe take some time..\n"
for line in $(curl "$API_LINK?access_token=$ACCESS_TOKEN&per_page=$PAGE_LIMIT" | grep -o "$GIT_LINK:$USERNAME/[^ ,\"]\+")
  do main &
done; wait; printf "\nWe finished the .sh file :) - Created by Dennis Demand( github.com/CyberT33N )\n"


```































































































<br><br>
______________________________________________________
<br><br>

## Error
	
	
<br><br>

# fatal: in unpopulated submodule
```
git rm --cached foldernamehere -f
```

<br><br>

# You have divergent branches and need to specify how to reconcile them
```
git pull --ff-only
	
## If you get error fatal: Not possible to fast-forward, aborting
git pull --rebase
```





































































































<br><br>
______________________________________________________
______________________________________________________
<br><br>



# Markdown
- https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet



<br><br>


## Colabsing menu

<br>

#### Code:
```markdown
# Header name
<details><summary>Click to expand..</summary>
Some stuff inside here..
</details>
```

<br>

#### Live:
<details><summary>Click to expand..</summary>
Some stuff inside here..
</details>







<br><br>
______________________________________________________
<br><br>


## TOC (Table of Contents)

#### Code:
```markdown
# Some data.. 
1. [Example](#example)
2. [Example2](#example2)
3. [Third Example](#third-example)
4. [Fourth Example](#fourth-examplehttpwwwfourthexamplecom)


## Example
## Example2
## Third Example


## [Fourth Example](http://www.fourthexample.com) 
```

<br>

#### Live:
# Some data.. 
1. [Example](#example)
2. [Example2](#example2)
3. [Third Example](#third-example)
4. [Fourth Example](#fourth-examplehttpwwwfourthexamplecom)


## Example
## Example2
## Third Example


## [Fourth Example](http://www.fourthexample.com) 











<br><br>
______________________________________________________
<br><br>

## Allowed HTML tags
- https://gist.github.com/coolaj86/89821fe046623d5503ce5c4133e70506
```markdown
h1 h2 h3 h4 h5 h6 h7 h8 br b i strong em a pre code img tt div ins del sup sub p ol ul table thead tbody tfoot blockquote dl dt dd kbd q samp var hr ruby rt rp li tr td th s strike summary details
```














<br><br>
<br><br>
______________________________________________________
______________________________________________________
<br><br>
<br><br>

## Tables
- https://www.tablesgenerator.com/markdown_tables

<br>

#### Code:
```markdown
|Redis Type|Javascript Type|
|---|---|
|string|String|
|list|Array of String|
|set|Array of String|
|hash|Object(keys have String values)|
|float|String|
|integer|number|
```

<br>

#### Live:
|Redis Type|Javascript Type|
|---|---|
|string|String|
|list|Array of String|
|set|Array of String|
|hash|Object(keys have String values)|
|float|String|
|integer|number|



















<br><br>
<br><br>
______________________________________________________
______________________________________________________
<br><br>
<br><br>

## Images

<br>

#### Code:
```markdown
![Test Image 4](https://github.com/tograh/testrepository/3DTest.png)
```

<br>

#### Live:
![Test Image 4](https://github.com/tograh/testrepository/3DTest.png)
























































<br><br>
<br><br>
______________________________________________________
______________________________________________________
<br><br>
<br><br>

## Listing

#### Code:
```markdown
- [Root Topic](#root-topic)
  - [1. Overview](#1-overview)
  - [2 Basic components](#2-basic-components)
    - [2.1. Message composer](#21-message-composer)
    - [2.2 Message senders](#22-message-senders)
```

<br>

### Live:
- [Root Topic](#root-topic)
  - [1. Overview](#1-overview)
  - [2 Basic components](#2-basic-components)
    - [2.1. Message composer](#21-message-composer)
    - [2.2 Message senders](#22-message-senders)
    
    
    
    
    
    
    
    






<br><br>
______________________________________________________
<br><br>

## FAQ

### Git freezing on new line when try to git push
```shell
# First remove git
sudo apt-get purge git
sudo apt-get autoremove
sudo apt-get install git

## If you used a passphrase which was stored before you must save it again:
ssh-agent zsh # If you do not use ZSH then make ssh-agent bash
ssh-add
```

