---
title: AWS Elasticache
date: 2023-02-02
---

# AWS Elasticache

## Amazon ElastiCache

- Elasticache is used to get managed Redis or Memcached.
- Caches are in-memory databases with really high performance, low latency.
- Elasticache helps reduce load off of databases for read intensive workloads.
- Helps make Applications Stateless
- It is a managed service. AWS takes care of OS maintenance / patching, optimization, setup, configuration, monitoring, failure recovery & backups

!!! warning
    Using Elasticache involves heavy application code changes

## Elasticache Solution Architecture

- Application queries ElastiCache, if not available gets from RDS & store in ElastiCache
- Data available in cache: **Cache Hit**, Data not available: **Cache Miss** (Fetches data from DB & writes to cache)
- Cache must have an invalidation strategy to make sure only the most current data is used there

### User Session Store

- User logs into any of the application
- The application writes the session data into ElastiCache
- The user hits another instance of the application & the instance in the backend retrieves the data & the user stays logged in


## Redis vs Memcached

### Redis

- Multi AZ with Auto-Failover
- Read Replicas to scale reads & have HA
- Data Durability using AOF persistence
- Backup & Restore features
- IAM Authentication is supported

### Memcached

- Multi-node for partitioning of data (Sharding)
- No high availability (Replication)
- Non persistent
- No backup & restore
- Multi-threaded architecture
- Only username/password supported

!!! note
    Redis for HA, backup & Memcached when you can afford to loose the data.

## ElastiCache Security

- Elasticache Support IAM Authentication for Redis
- IAM Policies on Elasticache are only used for AWS API level security

**Redis Auth**

- You can set password/token while creating a redis cluster
- This is an additional layer of security on top of security groups
- Support SSL in flight encryption

**Memcached**

- Support `SASL-based` authentications

## Elasticache Patterns

### Lazy Loading

- All the read data is cached, data can become stale in cache

### Write Through

- Adds or update data in cache when written to a DB (no stale data)

### Session Store

- Store temporary session data in cache (Using TTL)

!!! info
    **Lazy Loading**: Only read data from DB if there is  a Cache Miss

## Use Cases

Gaming leaderboards

- **Redis Sorted Sets**: Guarantee both uniqueness & element ordering
- Each time a new element added, it's ranked in real time, then added in the correct order
