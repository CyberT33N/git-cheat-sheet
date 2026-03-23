
# Delete file from all previous commits


## Windows

Install
```
py -m pip install git-filter-repo
```

Wichtig ist, dass man lokal an einer neuen Stelle einen Mirror-Clone macht, damit die Commit-Historie lokal nicht mehr vorhanden ist.

Das heißt, man muss auch sicherstellen, dass man in dem anderen Projekt vorher natürlich alle Änderungen gepusht hat. Dann würde man den Befehl unten ausführen, um es zu überschreiben und die Datei zu löschen, die man nicht haben will.

Anschließend müsste man in dem vorherigen Stand, in dem das Projekt war, den Ordner löschen und dann erneut einen Git-Clone machen.


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
