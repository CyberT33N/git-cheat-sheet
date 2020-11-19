# Git Sheet Sheet
Git Sheet Sheet with the most needed stuff...


<br><br>

## Command Line Cheat Sheet
- https://phoenixnap.com/kb/git-commands-cheat-sheet
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
# SSH_PRIVATEKEY_PATH="$HOME/.ssh/id_ecdsa"
EXPORT_PATH="$HOME/Documents/git_projects"

# ---- cd to current directory ----
cd "$EXPORT_PATH"
pwd
printf "\nWe will display now the current directory used:"
echo "$EXPORT_PATH"
printf "\n\nWe will clone now all your repos!\n\nPlease wait.. This maybe take some time..\n"

# ---- clone all git repos - ACCESS TOKEN ----
# for line in $(curl "$API_LINK?access_token=$ACCESS_TOKEN"  | grep -o "$GIT_LINK:$USERNAME/[^ ,\"]\+");
# do printf "\nCurrent repo link: $line"; git clone $line; done

# ---- clone all git repos - SSH KEY ----
# Notice that we have a limit of 100 items per page. If you want to get more than 100 repos this script must be edited..
for line in $(curl "$API_LINK?access_token=$ACCESS_TOKEN&per_page=100"  | grep -o "$GIT_LINK:$USERNAME/[^ ,\"]\+");
do printf "\nCurrent repo link: $line"; git clone $line; done


# ---- END AREA ----
printf "\nWe finished the .sh file :) - Created by Dennis Demand( github.com/CyberT33N )\n"

```

<br><br>
______________________________________________________
<br><br>


# commit

## commit all changes from all files
```bash
# Method #1 | -a= all  -m= commit message
git commit -a -m "MY MESSAGE HERE"

# Method #2
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

# merge

## Ignore merge conflicts
```bash
# cd into your project and then run:
rm -rf .git/MERGE*
```


<br><br>
______________________________________________________
<br><br>



# Gitlab

## Import repos from Github
- https://gitlab.com/import/github/status

## Mirror repo
Open your GitLab repo and then go to **Settings > Repository > Mirroring repositories** (https://gitlab.com/USERNAME/PROJECT-NAME/-/settings/repository)
<br><br>**IMPORTANT** - Please notice that only public repo mirror is currently free. For mirror of privat repos you must buy premium GitLab.



