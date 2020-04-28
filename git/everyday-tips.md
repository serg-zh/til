# Every day tips

These are short tips for everyday usage.

[Сборник советов](https://devconnected.com/category/software-engineering/)

Revert file to a repository state

``` shell
git checkout -- <file>`
```

## Tags

Checkout tag into new local branch

``` shell
git fetch --all --tags
git checkout tags/<tag_name> -b <branch_name>
```

## Branches

Update current branch to upstream

``` shell
git pull ‐‐rebase
```

Checkout remote branch into local branch and track it

``` shell
git fetch
git checkout -t origin/remote-branch
```
