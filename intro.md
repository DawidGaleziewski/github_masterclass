## Basics
git staging area allows us to add changes that will be commited as a single attomic unit

After commit the file is moved from staging area to a local repository

## adding global editor for git
```
git config --global core.editor "notepad++.exe --multiInst -nosession"
```

Good practice is always to do a pull before doing push

## add file and commit in one step

```
git commit -am 'msg'
```

## tracked files
tracked files are ones that:
- are added to stagging
- are commited

in order to check what files are tracked we can do:

```
git ls-files
```

## what happens when we add file to staging and then modify it

when using git status we will see this file twice. One as staged for commits and once as modified.

We can therefore separate our commits even in one file

Important thing to remember is that we are adding CHANGES and not just files

```
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        new file:   second_file.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   second_file.txt

```


## reverting changes

### Removing files from staging back to non staged
In order to move files from staging:

```
git reset head
```

this changes:

```
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   folder2/text2.txt
        modified:   text.txt

```

into:

```
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   text.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   folder2/text2.txt

```

It does not delete content of the file, just moves the staged file back to non staged file


### reverting files back to its commited state

```
git checkout -- folder2/text2.txt
```

This will remove all changes that were done since last commit


## moving and renaming files with git

```
git mv text.txt ./folder2/moved_file.txt
```

this will result in renamed status

```
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        renamed:    text.txt -> folder2/moved_file.txt

```

Git will stage the fact that we have renamed/ moved file

After commit git will also notice this fact

100% is the confidence level. It is best to rename the file before moving it

```
[master 4bc7a97] renamed and moved file
 1 file changed, 0 insertions(+), 0 deletions(-)
 rename folder1/{text.txt => folder2/moved_file.txt} (100%)
```

If we just move it by bash git will not this as TWO operations!. It will notice that we first delete the file and move it afterwards

```
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    start.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        moved_start.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

we can fix it by git add -A. -A flag will add all files recursively but also decide if file was moved or just deleted

## Delete files

### If file is unstaged we can just remove the file.

### if the file is commited

```
 git rm doomed.txt
```

This will:
1) delete the file
2) stage the fact that the delation took place

We can still get back the file by reseting this change

```
git reset HEAD moved_start.txt
```

File will not be however returned we still need to use checkout to return those changes

```
git checkout -- "moved_start.txt"
```

### What happens when we just remove a file by hand?

Git will detect file that was commited is no longer there

```
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    moved_start.txt

no changes added to commit (use "git add" and/or "git commit -a")

```

we can solve it by

git add -A (add and update renames and deletions)

## history

git log brings commit history in reverse order.

each commit has a sha1 identifier ie:

commit cfa9c972ca9b74de6c7108d831c1b179116e273a.

We do not need whole sha and can shorten those with:
git log --abbrev-commit

6-7 first chars of the sha1 are sufficiant for operating on them:

commit cf5d92f

## modify the displayed graph

git log --oneline --graph --decorate will return:

```
* cfa9c97 (HEAD -> master) deleted files
* cf5d92f removed the file
* 002d4a3 Delete file
* 4f5f364 added file
* a3f90fd renamed file
* 4bc7a97 renamed and moved file
* feeca4b add folders
* ddee9d5 adding two changes
* 6ff06dc second file mod
* 605ffc9 adding text
```

We can specify the range of commits

```bash
git log cf5d92f...ddee9d5
git log --oneline --decorate --graph cf5d92f...ddee9d5
```


We can specify a day range

```bash
git log --since="3 days ago"
```

We can target a certain file

```bash
git log -- test.txt
```

if we want to check what changed in a certain commit we can use git show.

```bash
git show cfa9c972ca9b74de6c7108d831c1b179116e273a
```

## git alias
In order to create git hist:

alias.nameOfTheCommand
"command params"
```bash
git config --global alias.hist "log --all --graph --decorate --oneli
ne"
```

## visual diff tool p4Merge

## compare

HEAD is a last commit on current branch.

### Working direectory with repo:

```bash
git diff HEAD
```


### staged with head
git diff --staged HEAD


## branches

when we create a new branch and check history we will see something interesting.

Our last commit has multiple labels, HEAD (last commit) master and feature-1.

This is due to fact that branches are just labels. They are nothing special. They are pointers


```bash
cfa9c97 (HEAD -> feature-1, master) deleted files
cf5d92f removed the file
002d4a3 Delete file
4f5f364 added file
a3f90fd renamed file
4bc7a97 renamed and moved file
feeca4b add folders
ddee9d5 adding two changes
6ff06dc second file mod
605ffc9 adding text

```

## merging

by default git does a fast-forward merge. It moves the commits from the branch we made into target branch as if the branch never existed.

This is only possible if no changes were done to the target branch

### --no-ff
If we want to preserve the fact those commits were on another branch we can use --no-ff flag.


## rebase

We use rebase when we are working on a new branch. And master has new commits and we want to update our branch as we are not finished with it.

```bash
* edb4583 (master) old feature on master
| * 7fc1832 (HEAD -> feature-2) added new feature
|/
* cfa9c97 deleted files
* cf5d92f removed the file
* 002d4a3 Delete file
* 4f5f364 added file
* a3f90fd renamed file
* 4bc7a97 renamed and moved file
* feeca4b add folders
* ddee9d5 adding two changes
* 6ff06dc second file mod
* 605ffc9 adding text
```

we can do rebase with

```bash
git rebase <brach we wat to update our current one with>;

First, rewinding head to replay your work on top of it...
Applying: added new feature
```

After rebase we will see this resault

```bash
* ab62e4b (HEAD -> feature-2) added new feature
* edb4583 (master) old feature on master
* cfa9c97 deleted files
* cf5d92f removed the file
* 002d4a3 Delete file
* 4f5f364 added file
* a3f90fd renamed file
* 4bc7a97 renamed and moved file
* feeca4b add folders
* ddee9d5 adding two changes
* 6ff06dc second file mod
* 605ffc9 adding text
```

git did:
- rewid changes done on the new feature branch
- played back change on master, on the feature branch
- applied what has happened on the feature branch again

This has flattened the history. This also will allow to ff merge to master.


Now each time we add something on the branch it will stay on top of the branch

```bash
* 47405d6 (HEAD -> feature-2) change in feature after rebase
* ab62e4b added new feature
* edb4583 (master) old feature on master
* cfa9c97 deleted files
* cf5d92f removed the file
* 002d4a3 Delete file
* 4f5f364 added file
* a3f90fd renamed file
* 4bc7a97 renamed and moved file
* feeca4b add folders
* ddee9d5 adding two changes
* 6ff06dc second file mod
* 605ffc9 adding text

```

## rebase conflicts

we can abort rebase when we have a conflict with:

git rebase --abort


## treverse commits using HEAD

we can  go back in our commit history by using the reset command

```bash
* a01c02f (HEAD -> master) added readme
*   49b463a (tag: myTag) Merge branch 'feature-2'
|\
| * 47405d6 (feature-2) change in feature after rebase
| * ab62e4b added new feature
|/
* edb4583 old feature on master
* cfa9c97 deleted files
* cf5d92f removed the file
* 002d4a3 Delete file
* 4f5f364 added file
* a3f90fd renamed file
* 4bc7a97 renamed and moved file
* feeca4b add folders

git reset HEAD^1

```

each carrot is one log ie HEAD^^^ will be 3 levels

we can also go back in the history by

```bash
git reset 4f5f364

*   49b463a (tag: myTag) Merge branch 'feature-2'
|\
| * 47405d6 (feature-2) change in feature after rebase
| * ab62e4b added new feature
|/
* edb4583 old feature on master
* cfa9c97 deleted files
* cf5d92f removed the file
* 002d4a3 Delete file
* 4f5f364 (HEAD -> master) added file
* a3f90fd renamed file
* 4bc7a97 renamed and moved file
* feeca4b add folders
* ddee9d5 adding two changes

```