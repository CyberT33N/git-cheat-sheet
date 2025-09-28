# Checkout

## switch branch
```bash
git checkout branch_name
```

## delete local files and switch back to state of remote branch
- When you locally delete a branch and then checkout again on it you will download the latest files from your remote branch.
```bash
git branch -D yourbranch
git checkout yourbranch
```

## Go back to specific commit and reset files
```bash
# First clone your project
git clone git://github.com/facebook/facebook-ios-sdk.git

Then checkout by commi SHA. You can find the SHA in your github/gitlab commit.
git checkout xxxxxSHA-HERExxxxx
```

## checkout tag
```bash
$ git checkout v2.0.0
# ... detached HEAD advice ...
$ git checkout v2.0-beta-0.1
# ...
$ git checkout -b version2 v2.0.0
```
