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