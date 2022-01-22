---
title: How to merge multi commits to one commit
author: nhvu1998
date: 2016-08-09
category: DevOps
tags: [git-merge]
---

## Merge all diff commits in another branch

Firstly, we should start working new feature/bugfix on a new branch and commit as much as we want.

After finished coding, we can merge them to master branch with **one combination commit** using `merge --squash` command

```bash
# switch to master branch
git merge --squash <feature branch>
```
