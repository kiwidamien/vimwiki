## Recommended aliases for git

Try adding the following to your `~/.gitconfig`:
```bash
[alias]
  pushf = push --force-with-lease  # only allow force push if you are only overwriting your work
  b = !git --no-pager branch --sort=-committerdate  # list brances in order of the last time you committed to it
  unstage = reset HEAD --          # git unstage fileA.py takes it out of staging, but does not chagne the working tree copy
  s = status -uno
```
