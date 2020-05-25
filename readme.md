# Study Materials
Almost all explanations and commands are based upon [Scott Chacon and Ben Straub's Pro Git](https://git-scm.com/book/en/v2)  
Other references are listed in their own sections.

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

- double dot(..)  
  ex) git log master..branchA  
  all commits reachable from branchA that aren't reachable master  
  ex) git log origin/master..HEAD  
  useful before you push
  
- --not  
  equivalent to above: git log branchA --not master  
  more than two args, git log a b --not c

-- triple dot(...)
  find commits reachable from either of two, but not by both  
  git log --left-right master...branchA 



# Rewriting History

### git commit --amend
**don’t amend your last commit if you’ve already pushed it since amending changes the SHA-1 of the commit**
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

### git rebase
- modify a commit that is not recent one.
- example - you want to change something 3 commits before
    1. git rebase -i HEAD~3  
    You shoud provide the parent of the oldest commit, which is 4 commits ago (HEAD, HEAD~1, HEAD~2, **HEAD~3**).   
    2. You will see something like below in editor
        ``` 
        pick aaaaaaa 1st commit
        pick bbbbbbb 2nd commit 
        pick ccccccc 3rd commit
        ```
    3. change "pick" to "edit", save and close file
        ```
        edit aaaaaaa 1st commit
        pick bbbbbbb 2nd commit 
        pick ccccccc 3rd commit
        ```
    4. now you are on right after 1st commit, make some changes you want
    5. git add some_file
    6. git commit --amend
    7. git rebase --continue


- Every commit included in the range HEAD~3, ..., HEAD will be rewritten.
- It might make other dev confused

# Reset Demystified
Assuming the situation below

```
        HEAD - master 
        ccccccc file.txt ---> bbbbbbb file.txt ---> aaaaaaa file.txt

        HEAD  : ccccccc
        INDEX : ccccccc
        W. Dir: ccccccc
```
### What does Reset?
Step 1 : Move Head (--soft)
- move what HEAD points to (changing HEAD itself is what checkout does)  
  *HEAD~* : the parent of HEAD  
  *--soft* : doesn't change Index and Working directory
```
        git reset --soft HEAD~
        ---> this will make master point to bbbbbbb when HEAD is set to the master branch

                              HEAD - master 
        ccccccc file.txt ---> bbbbbbb file.txt ---> aaaaaaa file.txt

        HEAD  : bbbbbbb
        INDEX : ccccccc
        W. Dir: ccccccc
```
Step 2 : Updating the Index (--mixed)
- update the index with the contents of what HEAD is pointing to
```
        git reset --mixed HEAD~
        
        HEAD  : bbbbbbb
        INDEX : bbbbbbb
        W. Dir: ccccccc
```
Step 3 : Updating the Working directory (--hard)
- update the working directory, you will lose files between commits
```
        git reset --hard HEAD~
        
        HEAD  : bbbbbbb
        INDEX : bbbbbbb
        W. Dir: bbbbbbb
```

### Reset with a Path

- **git reset file.txt** is shorthand for  
    **git reset --mixed HEAD file.txt**
- the exact opposite of **git add**
- unstage the file, the file in working directory won't be changed by this command  
- example: unstaging a file
```
        git add * // this is mistake
        git reset HEAD some_file_you_want_to_unstage
```

### Squashing commits with reset

1. Assuming that
```
        HEAD - master 
        ccccccc file.txt ---> bbbbbbb file.txt ---> aaaaaaa file.txt

        HEAD  : ccccccc
        INDEX : ccccccc
        W. Dir: ccccccc
```
2. go back to two commits before
```
    git reset --soft HEAD~2

                                                    HEAD - master 
        ccccccc file.txt ---> bbbbbbb file.txt ---> aaaaaaa file.txt

        HEAD  : aaaaaaa
        INDEX : ccccccc
        W. Dir: ccccccc
```
3. commit
```
    git commit -m "squashing"

        HEAD - master 
        ccccccc file.txt ---> aaaaaaa file.txt

        HEAD  : ccccccc
        INDEX : ccccccc
        W. Dir: ccccccc
```

### git checkout
1. file level
- works like reset --hard
**be careful, the changes will disappear**
```
   git checkout -- some_file_you_want_to_revert_back
```
2. commit level  
change HEAD itself (to 'branch')  

### Stashing and Cleaning
Commands
1. git stash   
    - save current working directory and index state  
    - clean up working direcotry/index like right after the last commit
  
2. git stash --keep-index
    - save working directory and index
    - leave it in Index
  
3. git stash --include-untracked (-u)
    - include untracked files
    - by default, git stash doesn't include the untracked
  
4. git stash --all (-a)
    - include ignored files
  
5. git stash list
6. git stash apply
    - to recover the latest stash
    - to recover the sepcific stash  
        git stash apply stash@{n}
7. git stash drop stash@{n}
    - remove stash
8. git stash pop stash@{n}
    - apply & remove stash
9. git stash branch <new_branch_name>
    - create new branch
    - apply stash there
    - drop stash
10. git clean
    - remove files that are not tracked
    - it's safe to use **git stash --all**
    - check using **git clean -d -n** before **git clean -d -f**
    - -d // also delete dir with files
    - -f // force
    - -n or --dry-run
    - -x // delete ignored files as well
    - -i // interactive



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
