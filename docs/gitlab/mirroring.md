# GitLab – Mirroring

#### Auto Sync via GitLab UI
Open your GitLab repo and then go to **Settings > Repository > Mirroring repositories** (https://gitlab.com/USERNAME/PROJECT-NAME/-/settings/repository)

Wichtig: Mirroring privater Repos ist kostenpflichtig (GitLab Premium).

#### Mirror Github → GitLab (Script)
```bash
# Variables
ACCESS_TOKEN="xxxxxxxxxxxxxxxxxxxxxxxx"
USERNAME="xxxxxx"
API_LINK="https://api.github.com/user/repos"
GIT_LINK="git@github.com"
EXPORT_PATH="$HOME/Documents/git_projects"
PAGE_LIMIT=100
REMOTE_REPO_NAME="gitlab"
REMOTE_REPO_HOST="git@gitlab.com"
REMOTE_USERNAME="xxxxxxx"

# functions (excerpt)
fakeCommit(){
  touch .placeholder_mirror
  git add .
  REMOTE_DEFAULT_BRANCH=$( git remote show $REMOTE_REPO_NAME | awk '/HEAD branch/ {print $NF}' )
  git stash save
  git push -f $REMOTE_REPO_NAME "stash@{0}:$REMOTE_DEFAULT_BRANCH"
  git stash pop
  git rm -f .placeholder_mirror
}
```
