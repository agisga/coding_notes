# Batch-search-and-replace in many files

Based on <https://vi.stackexchange.com/questions/2776/vim-search-replace-all-files-in-current-project-folder>.

For example add all files in current directory:

```
:args `find . -type f`
```

Check the list of files with `:args`.

Example search-replace:

```
:argdo %s/.org)/.md)/gce | update
```

Here `e` will suppress "pattern not found" errors, and "undate" will save changes upon exiting each file.

# Resources

- A nice ebook/tutorial: <https://www.barbarianmeetscoding.com/boost-your-coding-fu-with-vscode-and-vim/table-of-contents>
