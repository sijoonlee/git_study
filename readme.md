Working Directory ------ Staging Area ------- .git directory(repository)  
--------------------------git add----------------git commit

### git add
- tracking new file
- staging modified file

### git status
- show general messages - which files were changed and so on

### git diff
- show the changes between the last commit and the unstaged
- if all staged, nothing will show up

### git diff --staged
- show the staged that will go into the next commit

### git commit
- commit the changes

### git rm
- removing files from staging area + working directory
- stages the removal of files







### MISC
set vscode as editor  
https://stackoverflow.com/questions/30024353/how-to-use-visual-studio-code-as-default-editor-for-git

1. check 'code --help' works
2. git config --global core.editor "code --wait"
3. git config --global -e 
Add below
```
[diff]
    tool = default-difftool
[difftool "default-difftool"]
    cmd = code --wait --diff $LOCAL $REMOTE
```
