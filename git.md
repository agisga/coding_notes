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
