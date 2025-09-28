# Helper Scripts

## Check if branch exists remote
```shell
REMOTE_NAME="origin"

if git ls-remote --heads $REMOTE_NAME $branch | grep -q $branch; then
  echo "Branch $branch exists. Proceeding with git pull."
  git pull $REMOTE_NAME $branch
else
  echo "Branch $branch does not exist on remote. So we do not pull.."
fi
```

## Check if head commit from branch a exists in branch b
```shell
if [ "$ENABLE_CUSTOM_PROJECT_PATH" = "true" ]; then
(
    cd $CUSTOM_PROJECT_PATH

    echo "Pulling latest changes for $BRANCH1"
    git pull origin $BRANCH1 || exit 1

    echo "Pulling latest changes for $BRANCH2"
    git pull origin $BRANCH2 || exit 1

    HEAD_COMMIT_BRANCH1=$(git rev-parse origin/$BRANCH1)

    if git branch -r --contains $HEAD_COMMIT_BRANCH1 | grep -q "origin/$BRANCH2"; then
        echo "The HEAD commit of $BRANCH1 exists in $BRANCH2."
    else
        echo "Error: The HEAD commit of $BRANCH1 does not exist in $BRANCH2."
        exit 1
    fi
)
fi
```