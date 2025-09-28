# submodule

## Add submodule
```bash
git submodule add https://github.com/CyberT33N/cursor-base-rules.git .cursor-base-rules
```

## Update submodule
```bash
git submodule update --remote .cursor-base-rules
```

## Remove submodule
```bash
# Remove the submodule entry from .git/config
git submodule deinit -f path/to/submodule

# Remove the submodule directory from the superproject's .git/modules directory
rm -rf .git/modules/path/to/submodule

# Remove the entry in .gitmodules and remove the submodule directory located at path/to/submodule
git rm -f path/to/submodule
```