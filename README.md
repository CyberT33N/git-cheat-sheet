# Git Sheet Sheet
Git Sheet Sheet with the most needed stuff...


<br><br>

## Command Line Cheat Sheet
- https://about.gitlab.com/images/press/git-cheat-sheet.pdf


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
```bash
## Make sure that your key will be saved in your ssh folder. As example for linux: ~/.ssh/
ssh-keygen -t ecdsa -b 521
```
<br>
Next add your public key to your git settings:
<br>- https://gitlab.com/-/profile/keys
<br>- https://github.com/settings/keys

<br><br>
Now when you clone your repo via command line you will get asked for passphrase to verify. This will be only done 1 time.

<br><br>

## How to use Private Key global
```bash
# Method 1 - Edit ~/.ssh/config and enter:
host github.com
 HostName github.com
 IdentityFile ~/.ssh/your_private_key
 User git
 ```
 
## How to store passphrase
```bash
# Method 1 - Add private key to keychain. You must 1 time verify it manually and then it will be saved
ssh-add -K ~/.ssh/your_private_key
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


# commit

## Show logs of last commits
```bash
git log
```

## commit all changes from all files
```bash
git add .
git commit -m "MY MESSAGE HERE"
```

## commit specific file
```bash
git add app.js
git commit -m "MY MESSAGE HERE"
```

<br><br>


## empty commit
```bash
git commit -a --allow-empty --allow-empty-message -m ''
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
______________________________________________________
<br><br>


# push
- Update your repo based on your commits
```bash
git push
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

<br>

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

## switch branch
```bash
git checkout branch_name
```




<br><br>
______________________________________________________
______________________________________________________
<br><br>



# Remote
- Basic Guide (https://git-scm.com/book/de/v2/Git-Grundlagen-Mit-Remotes-arbeiten)

## Check all current remote links of your project
```bash
# cd into your project and then run:
git remote -v
```

## How to use Github and GitLab on same machine

#### ~/.ssh/config
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

# reset

## reset last main commit
```bash
git reset --soft HEAD^     # Use --soft if you want to keep your changes
git reset --hard HEAD^     # Use --hard if you don't care about keeping the changes you made
```


<br><br>
______________________________________________________
<br><br>

# revert

## revert specific commit
```bash
# use git log to get commit id
git revert id_here
```

<br><br>
______________________________________________________
<br><br>

# merge

## Ignore merge conflicts
```bash
# cd into your project and then run:
rm -rf .git/MERGE*
```

<br><br>
______________________________________________________
<br><br>

# stash (https://git-scm.com/docs/git-stash)
- Use git stash when you want to record the current state of the working directory and the index, but want to go back to a clean working directory. The command saves your local modifications away and reverts the working directory to match the HEAD commit.

<br><br>

## show all stashes
```bash
git stash show
```




<br><br>
______________________________________________________
<br><br>


# Delete Files

## delete specific file from disk and repo
```bash
git rm -f your_file.txt
git push -f
```


<br><br>
______________________________________________________
______________________________________________________
<br><br>

# .gitignore
- Ignore files and folders for push
```bash
node_modules/
```

<br><br>

## Create .gitignore file and include node_modules folder
```bash
touch .gitignore && echo "node_modules/" >> .gitignore && git rm -r --cached node_modules ; git status
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
______________________________________________________
<br><br>



# Markdown
- https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet

## Colabsing menu
```markdown
# Header name
<details><summary>Click to expand..</summary>
Some stuff inside here..
</details>
```

<br><br>

# TOC (Table of Contents)
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

<br><br>

# Allowed HTML tags
- https://gist.github.com/coolaj86/89821fe046623d5503ce5c4133e70506
```markdown
h1 h2 h3 h4 h5 h6 h7 h8 br b i strong em a pre code img tt div ins del sup sub p ol ul table thead tbody tfoot blockquote dl dt dd kbd q samp var hr ruby rt rp li tr td th s strike summary details
```

