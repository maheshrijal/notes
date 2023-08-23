---
title: Git Push Prompting for Username & Password
date: 2023-08-12
---

If git push is prompting for user & password:

```
$ git push
Username for 'https://github.com':
```
The issue is the repository was cloned with https url instead of SSH. To fix this, simply set the remote URL to SSH URL of the repository.

```
git remote set-url origin git@github.com:username/repo.git
```