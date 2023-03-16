---
title: Web requests (curl, wget ) for various tasks
date: 2023-03-16
---

# Web requests (curl, wget ) for various tasks


### List the available versions of a PyPi package

```bash
curl -s https://pypi.org/pypi/<package>/json | jq '.releases | keys | .[]'
```

or

```
https://pypi.org/pypi/{PKG_NAME}/json
```