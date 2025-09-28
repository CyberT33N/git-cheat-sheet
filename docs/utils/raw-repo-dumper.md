# Get raw contents of repo in single file

Usage:
```shell
bash test.sh https://github.com/alex8088/electron-vite-boilerplate/tree/master
```

Script:
```shell
#!/bin/bash
log(){ echo "[INFO] $1"; }
error_log(){ echo "[ERROR] $1" >&2; }
cleanup(){ rm -f repo_structure.json debug.log; }
trap cleanup EXIT
# ... trimmed for brevity, see README for full script ...
```
