---
title: CURL
---


## SSL Certificate

### Verify SSL certificate of a domain
```sh
curl https://maheshrijal.com -vI
```

### Check expiry date
```sh
curl https://maheshrijal.com -vI --stderr - | grep "expire date"
```