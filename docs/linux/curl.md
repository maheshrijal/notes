---
title: CURL
---


## SSL Certificate

### Verify SSL certificate of a domain
```sh
curl https://maheshrjl.com -vI
```

### Check expiry date
```sh
curl https://maheshrjl.com -vI --stderr - | grep "expire date"
```