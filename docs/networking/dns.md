---
title: DNS Records, DoH & DoT
---

## DNSSEC
- DNSSEC validates DNS queries by a chain of trust
- The DNS root is signed therefore all results obtained from the root servers are trusted by default

## DNS over Https (DoH)
- DNS queries and responses are encrypted and sent via the HTTP or HTTP/2 protocols.
- DoH ensures that attackers cannot forge or alter DNS traffic.
- DoH uses port 443, which is the standard HTTPS traffic port, to wrap the DNS query in an HTTPS request.
- DNS queries and responses are camouflaged within other HTTPS traffic, since it all comes and goes from the same port.
- DoH is better from a privacy perspective as DNS queries are hidden within the larger flow of HTTPS traffic.
- DoH queries cannot easily be blocked without blocking all other HTTPS traffic as well.

### DoH Resolvers

#### Cloudflare
- Block malware

```
security.cloudflare-dns.com
```

- Block malware & adult content

```
family.cloudflare-dns.com
```

#### Google
```
dns.google
```

## DNS over TLS (DoT)
- By default, DNS is sent over a plaintext connection.
- DNS over TLS (DoT) is a standard for encrypting DNS queries to keep them secure and private.
- DoT uses the same security protocol, TLS, that HTTPS websites use to encrypt and authenticate communications.
- DoT is better from a security perspective as it allows network administrators the ability to monitor and block DNS queries

### DoT Resolvers

#### Quad-9
```
dns.quad9.net
```