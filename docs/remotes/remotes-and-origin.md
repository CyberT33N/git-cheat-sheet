# What is origin? (https://stackoverflow.com/questions/9529497/what-is-origin-in-git)
- origin is an alias on your system for a particular remote repository. It's not actually a property of that repository.
- Remotes are simply an alias that store the URL of repositories. You can see what URL belongs to each remote by using
```bash
git remote -v
```

As example if you have the same repo on github and gitlab you can switch between your remote repos and origin would be just the name. So instead it could be aswell gitlab oder github

# Remote
- Basic Guide (https://git-scm.com/book/de/v2/Git-Grundlagen-Mit-Remotes-arbeiten)

## Push Github repo to Gitalab
```bash
git clone your_github_repo

# add remote repo
git remote add gitlab http://gitlab.urlhere/username/projectname.git

## Push repo to gitlab
git push gitlab
```

## Check all current remote links of your project
```bash
git remote -v
```

## How to use Github and GitLab on same machine

Add helpful aliases to push to multiple remotes (excerpt):
```gitconfig
[alias]
    pushall = "!f(){ \
        for remote in origin github bb gitlab gitlabInternal; do \
            if git remote get-url \"$remote\" > /dev/null 2>&1; then \
                echo \"Pushing to $remote...\"; \
                git push \"$remote\"; \
            fi; \
        done; \
    }; f"
```