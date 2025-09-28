# tag (https://git-scm.com/docs/git-tag)

## Guides
- https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet

## Listing Your Tags
```bash
git tag
```

## search for specific tag name
```bash
git tag -l "v1.8.5*"
```

## create Annotated Tag
```bash
git tag -a v1.4 -m "my version 1.4"
git tag
# v1.4
```

## create Lightweight Tag
```bash
git tag v1.4-lw
git tag
# v1.4-lw
```

## delete tag
```bash
git tag -d v1.4-lw
# Deleted tag 'v1.4-lw' (was e7d5add)
```