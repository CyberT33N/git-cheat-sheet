
# Delete file from all previous commits


## Windows

Install
```
py -m pip install git-filter-repo
```

Then if we want to delete e.g. tmp.txt
```shell
cd "$HOME\Downloads\tmp"
git clone https://github.com/CyberT33N/sandboxie-cheat-sheet.git sandboxie-cheat-sheet-clean
cd .\sandboxie-cheat-sheet-clean\
& "C:\Users\denni\AppData\Roaming\Python\Python313\Scripts\git-filter-repo.exe" --invert-paths --path "tmp.txt" --force
git remote add origin https://github.com/CyberT33N/sandboxie-cheat-sheet.git
git push origin --force --all
git push origin --force --tags
```
