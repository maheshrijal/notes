---
title: Scripts and useful code snippets
---


# Useful code snippets and scripts


``` js title="Don't F&_k with Paste"
// Prevents websites from blocking copy/paste
var allowPaste = function(e){
  e.stopImmediatePropagation();
  return true;
};
document.addEventListener('paste', allowPaste, true);
```

``` sh title="Get the current IPv4 address"
curl -s https://api.ipify.org
```

``` sh title="Get the current IPv6 address"
curl -s https://api6.ipify.org
```

``` sh title="List the available versions of a PyPi package"
curl -s https://pypi.org/pypi/<package>/json | jq '.releases | keys | .[]'

```

## Git

```sh title="Sign all commits from the commit id"
git rebase --exec 'git commit --amend --no-edit -n -S' -i <commit id>
```

```sh title="Resign all commits in a repository"
git rebase --exec 'git commit --amend --no-edit --no-verify -S' -i --root
```

```sh title="Keep the commited date when signing using above commands"
git rebase --committer-date-is-author-date -i --root
```