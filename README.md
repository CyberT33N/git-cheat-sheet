# Git Sheet Sheet
Git Sheet Sheet with the most needed stuff...


<br><br>

## Command Line Cheat Sheet
https://phoenixnap.com/kb/git-commands-cheat-sheet

<br><br>
______________________________________________________
<br><br>

## SSH

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
# Method 2 - Add private key to keychain. You must 1 time verify it manually and then it will be saved
ssh-add -K ~/.ssh/id_ecdsa
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
______________________________________________________
______________________________________________________
<br><br>



# Gitlab

## Import repos from Github
- https://gitlab.com/import/github/status

## Mirror repo
Open your GitLab repo and then go to **Settings > Repository > Mirroring repositories** (https://gitlab.com/USERNAME/PROJECT-NAME/-/settings/repository)
<br><br>**IMPORTANT** - Please notice that only public repo mirror is currently free. For mirror of privat repos you must buy premium GitLab.
