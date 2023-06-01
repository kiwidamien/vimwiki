# Get files that have changed from master

The easiest (but most verbose) way of finding changes from master is to use the `git diff` command.

By default, `git diff` shows the difference between the working tree and the previous commit.

To get the difference between two _commits_ we can use the SHAs
```shell
git diff <commit1> <commit2>
```

To get the differnce between two branches, we can use the ellipes
```
git diff master..HEAD
```
or just
```shell
git diff master..
```

## How to eliminate unrelated changes in master

One way we could approach this is to `git merge master` first, but that makes changes to our branch.

If we are just interested in looking at the places we have updated master, we can use the following trick:
```
git diff master...
```
or 
```
git diff master...HEAD
```

What it is actually doing: compares HEAD to the point where the current branch diverged from master. This actually covers "what did I change?" without bringing in the current state of msater (in partuclar , it ignores updates hat otehrs have made to master since you started doing work)

## What if I only want a list of files that have changed?

```shell
git diff --stat <commit1> <commit2>
```
