# Workflows
- The next section does contain example of what commands to use in different all day scenarios

## Ticket > Merge Request (MR) Prozess
- https://github.com/CyberT33N/ci-cd-cheat-sheet/blob/main/README.md#ticket--merge-request-mr-prozess

## Create new branch and use current code status without git add/stash
- You can just create a new switch and then checkout on it. The current code from your old branch will be copied in the new you had created and where you checkout.
```bash
git branch my-test-branch
git checkout my-test-branch
```

## Squash merge branch into current branch without merge conflicts
```bash
git checkout sourceBranch
git merge --squash targetBranch
git commit -m "Test"
git push
```

## Squash merge branch into current branch with merge conflicts
```bash
git checkout sourceBranch
git merge --squash targetBranch
# Resolve merge conflicts in IDE
git commit -m "Test"
git push
```

## Revert merge if no commit was made by deleting the branch locally
```bash
git checkout otherBranch
git branch -D targetBranch
git checkout targetBranch
```