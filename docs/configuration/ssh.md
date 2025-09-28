# SSH
- GitLab (https://docs.gitlab.com/ee/ssh/README.html)

## How to generate Private & Public Key (https://docs.gitlab.com/ee/ssh/README.html#generating-a-new-ssh-key-pair)
- It is recommended to create multiple ssh keys and and not use 1 for everything
```bash
## Default your key will keys will be saved to ~/.ssh/
## Your custom name keys will be stored in current directoy where you executed the command. E.g. ~/samplePubKey.pub
# ssh-keygen -t ecdsa -b 521
ssh-keygen -t ecdsa -b 521 -f /home/tuserNameHere/.ssh/github/id_ecdsa
```
- ssh-keygen works aswell on windows. 

Next add your public key to your git settings (~/.ssh/id_ecdsa.pub):
- https://gitlab.com/-/profile/keys
- https://github.com/settings/keys
- https://bitbucket.org/account/settings/ssh-keys/

Now when you clone your repo via command line you will get asked for passphrase to verify. This will be only done 1 time.

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

Method 1 (recommended:
```bash
sudo apt install keychain

keychain --eval --agents ssh ~/.ssh/github/id_ecdsa

# Add to ~/.zshrc and ~/.bashrc this
eval "$(keychain --eval --agents ssh ~/.ssh/github/id_ecdsa)"
```

Other methods (Maybe not persistents):
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