# Restore (https://git-scm.com/docs/git-restore)
- This only works when you did not use **git add**! If you use git add then you must revert them before,

## restore specific file
```bash
git restore sample.html
```





<br>
<br>

### Git-Befehl zum Wiederherstellen der Datei aus `develop`

```bash
git fetch origin
git restore --source=origin/develop -- test\regression\test\fixtures\pvs\__snapshots__\fixutres.test.ts.snap
```

(Alternative, falls dein `develop` lokal aktuell ist:)

```bash
git restore --source=develop -- test\regression\test\fixtures\pvs\__snapshots__\fixutres.test.ts.snap
```


Method 2:
```shell
git fetch origin develop
git restore --source=origin/develop --staged --worktree -- "test\unit\modules\pvs\dampsoft\abbrevation"
```
