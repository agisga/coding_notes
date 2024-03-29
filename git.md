# Merging

```
git checkout master
git merge new_branch
git branch -d new_branch
```

# Rewriting history

Detailed information here: <https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History>

## Combine commits that have not yet been pushed

Based on comments from <http://gitready.com/advanced/2009/03/20/reorder-commits-with-rebase.html>.

```
git rebase -i origin/master
```

## Simultaneously reorder and combine commits

Based on comments from <http://gitready.com/advanced/2009/03/20/reorder-commits-with-rebase.html>.

For example, we have this commit history:

```
pick 123 my older commit
pick 234 my intermediate commit
pick 345 my most recent commit
```

and we want to squash `345` into `123`.

Do

```
git rebase -i HEAD~3
```

and just change the lines to have

```
pick 123 my older commit
squash 345 my most recent commit
pick 234 my intermediate commit
```

and save.

# Undoing changes

## git revert

* *Invert the changes* of previous commits, and *append a new commit* with the resulting reverted content.
* `git revert` does not remove any previous commits (`git reset` does that).
* "safe" operation, because it doesn't change the project history.
* Seems this has to be done one commit at a time. For example, assume that we have `A -> B -> C -> D -> E`, and we want to go back to `C`.
  - `git revert HEAD` results in `A -> B -> C -> D -> E -> F`, where F is the same as D.
  - `git revert HEAD~2` (two back from `F`) results in `A -> B -> C -> D -> E -> F -> G`, where G is the same as C.

## git reset

* `git reset` moves both the HEAD and the branch ref pointers to a specified commit (in contrast, `git checkout` moves only the HEAD ref pointer, see "detached HEAD" state).
* Consider the *working directory tree* (i.e., modified files in the index, which are unstaged), the *staging index tree* (i.e., modified & staged), and the *commit tree* (i.e., committed chages).
* `git reset` *always* modifies the state of the commit tree (see first bullet), but there are options for modifications on the staging index or the working directory level:
  - `git reset --hard <commit>`: the staging index and working directory tree are reset to match the specified commit.
  - `git reset --mixed <commit>`: the staging index is reset to match the specified commit, but the changes in the working directory files remain; `git reset` is equivalent to `git reset --mixed HEAD`.
  - `git reset --soft <commit>`: the commit history is reset to the specified commit, but the staging index and working directory are left untouched.
* `git reset` is the "dangerous" method compared to the "safe" `git revert`: danger of permanently losing commits and working directory changes, although "orphaned" commits can be recovered with `git reflog` until they are permanently deleted by the git garbage collector (about every 30 days).

## Hard reset of a single file

* `git checkout HEAD -- my-file.txt`: update both the working copy of `my-file.txt` and its state in the index with that from HEAD. Here `--` basically means: "treat every argument after this point as a file name".

# Removing things

## git rm

* Removes paths that are known to git either from the git index only, or from the working tree and the index.
* `git rm <file>`: remove `<file>` from the working directory as well as from the git index.
* `git rm --cached <file>`: unstage and remove the path `<file>` only from the git index.
* Some other options: `-r` for recursive removal when a directory name is given.

# Untrack files in git

See <http://source.kohlerville.com/2009/02/untrack-files-in-git/>.

Simple way:

1. `git rm --cached filename`
2. add file to .gitignore

Another way is: `git update-index --assume-unchanged [path]`

# Working with a fork

## Configure a remote for a fork

Basic instruction at <https://help.github.com/articles/configuring-a-remote-for-a-fork/>:

```
git remote -v
git remote add upstream https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git
```

## Syncing a fork with its remote

Basic instructions at <https://help.github.com/articles/syncing-a-fork/>:

```
git fetch upstream
git checkout <FORK_BRANCH_TO_SYNC>
git merge upstream/master
```

# Useful resources

- <https://swcarpentry.github.io/git-novice/>
