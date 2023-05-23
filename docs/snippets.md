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