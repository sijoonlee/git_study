# The Three Trees
|Tree|Role|
|:---|:---|
|HEAD|Last commit snapshot|
|Index|Proposed next commit snapshot|
|Working Directory|Sandbox|

### HEAD
- git cat-file -p HEAD
```
    tree d06ef42d88d6fba20261cf77354c12a478cab13f
    parent 35a6e19fdd43648417176d9faf5084447192810b
    author Sijoon Lee <shijoonlee@gmail.com> 1590430199 -0700
    committer Sijoon Lee <shijoonlee@gmail.com> 1590430199 -0700
```
- git ls-tree -r HEAD  
    to see files in HEAD
```
    100644 blob 22822d66e40916394e051700c087d2b80ff6e7c6    readme.md
```
### Index (Staging Area)
- git ls-files -s  
    to see files in Staging Area
```
    100644 e0100877308a572a30d5399e1e0089148913e532 0       readme.md
```

### Working Directory
- it's your local directory  


# Workflow
Working Directory  
<------------------------------git add  
Index (Staging Area)
<------------------------------git commit  
.git directory(repository)  
  
  
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
- --cached to keep the file in working directory
- -f to force removal after making changes since the last commit

### git mv
Two below are equivalent
```
    git mv README.md README
```

```
    mv README.md README
    git rm README.md
    git add README
```

### git log
- shows what has happened
- --patch(-p) : show the difference in each commit  
    use -2 to show the last two entries
- --stat : show stats like insertion/deletion
- --pretty=[oneline | short | full | fuller]  
ex) --pretty=oneline

```
    1243760e14075a2398c0fd2c0ba8a2e526d087e1 (HEAD -> master) test2
    35a6e19fdd43648417176d9faf5084447192810b testing
```
- --pretty=format:"%h - %an, %ar : %s"

    |Option|Description|
    |------|------------
    |%H|Commit hash|
    |%h|Abbreviated commit hash|
    |%T|Tree hash|
    |%t|Abbreviated tree hash|
    |%P|Parent hashes|
    |%p|Abbreviated parent hashes|
    |%an|Author name|
    |%ae|Author email|
    |%ad|Author date (format respects the --date=option)|
    |%ar|Author date, relative|
    |%cn|Committer name|
    |%ce|Committer email|
    |%cd|Committer date|
    |%cr|Committer date, relative|
    |%s|Subject|

- --pretty=format:"%h %s" --graph
Add ASCII graph
```
    $ git log --pretty=format:"%h %s" --graph
    * 2d3acf9 ignore errors from SIGCHLD on trap
    * 5e3ee11 Merge branch 'master' of git://github.com/dustin/grit
    |\
    | * 420eac9 Added a method for getting the current branch.
    * | 30e367c timeout code and tests
    * | 5a09431 add timeout protection to grit
    * | e1193f8 support for heads with slashes in them
    |/
    * d6016bc require time for xmlschema
    * 11d191e Merge branch 'defunkt' into local
```
- --since / --after / --until / --before  
    --since=2.weeks    
    --since="2010-1-1"  
    --since="2 years 1 day 3 minutes ago"  

- filtering  
    --grep "some keyword"  
    --author shijoonlee@gmail.com
    --committer shijoonlee@gmail.com  
    --S "some keyword" ----> show commits adding/removing the keyword  
    --no-merges -----> exclude 'merge' commits
  
### git commit --amend
1. When you commit too early  
When you forget to add something  
When you mess up the commit message  
2. Make changes, stage them
3. git commit --amend
```
    $ git commit -m 'initial commit'
    $ git add forgotten_file
    $ git commit --amend
```


# Reset Demystified
Assuming the situation below

```
        HEAD - master 
        ccccccc file.txt ---> bbbbbbb file.txt ---> aaaaaaa file.txt
```
### What does Reset?
Step 1 : Move Head (--soft)
- move what HEAD points to (changing HEAD itself is what checkout does)  
  *HEAD~* : the parent of HEAD  
  *--soft* : doesn't change Index and Working directory
```
        git reset bbbbbbb
        or git rest --soft HEAD~
        ---> this will make master point to bbbbbbb when HEAD is set to the master branch

                              HEAD - master 
        ccccccc file.txt ---> bbbbbbb file.txt ---> aaaaaaa file.txt
```
Step 2 : Updating the Index (--mixed)
- update the index with the contents of what HEAD is pointing to
```
        git reset --mixed HEAD~
```


### git reset

1. Unstaging a file
```
    git add * // this is mistake
    git reset HEAD some_file_you_want_to_unstage
```

### git checkout
1. Unmodify a file  
**be careful, the changes will disappear**
```
   git checkout -- some_file_you_want_to_revert_back
```



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
