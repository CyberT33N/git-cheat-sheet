# Most asked questions

#### How to checkout on another branch after you do local changes on your current branch?
- You may get a error that you have to commit or stash your current changes before you can do a checkout. Here are your solutions:
  - Delete old branch where we want to checkout
  - Create commit on the current branch
  - Stash

# FAQ

## Filename too long Merge with strategy ort failed.
- Windows problem

Open Powershell as admin:
```shell
git config --system core.longpaths true
```
Then re-open terminal and you can make git pull :)

## Git freezing on new line when try to git push
```shell
sudo apt-get purge git
sudo apt-get autoremove
sudo apt-get install git
ssh-agent zsh
ssh-add
```