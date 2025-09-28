# .gitkeep

## Keep empty folder but still ignore all files inside
```
test-db-dumps/test_333/*
!test-db-dumps/test_333/.gitkeep
```
- Create .gitkeep file at test-db-dumps/test_333/.gitkeep

# .gitignore

```bash
node_modules
test/*
```
- You can use relative paths. e.g. your project is abc/hey/test/test.json you can just use hey/test

## Create .gitignore file and include node_modules folder
```bash
touch .gitignore && echo "node_modules/" >> .gitignore && git rm -r --cached node_modules ; git status
```

## Remove files that are already commited but the folder is listed in .gitignore
```bash
git rm -rf 'lib/main/test/test-db-dumps/test_333'
```

## Remove cached files
```bash
git rm --cached out/preload/index.js
```