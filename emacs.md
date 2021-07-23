# Emacs

## Upgrade packages

Within Emacs, use `M-x list-packages` to list all packages which will automatically refresh the archive contents. Afterwards use `U` to mark all upgradable packages to be upgraded, and `x` to actually perform the new updates. Emacs will then fetch and install all upgrades, and ask you to whether to remove the old, obsolete versions afterwards.
(from <https://stackoverflow.com/questions/14836958/updating-packages-in-emacs>)

## Convert org-mode files to markdown

From <https://eengstrom.github.io/musings/convert-org-mode-to-markdown>:

```
pandoc --from=org --to=gfm org-mode-file.org > markdown.md
```

For a more involved example with batch processing, see `notes/scripts/pandoc_convert_org_to_md.sh`
