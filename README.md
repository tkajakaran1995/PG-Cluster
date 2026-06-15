# PostgreSQL HA Cluster — Deployment Runbook

Community Edition · 3-Node Co-located Cluster · Ubuntu 22.04 LTS · Patroni + etcd + HAProxy
(with an Oracle Cloud / OCI variant)

## Overview

This repository documents a minimal, production-capable PostgreSQL high-availability
topology. All three nodes are identical and each runs the full stack:

- **PostgreSQL** — the database engine
- **Patroni** — automatic failover and cluster management
- **etcd** — distributed consensus store (quorum) used by Patroni
- **HAProxy** — routes writes to the current primary and load-balances reads across replicas

Patroni manages automatic failover via the etcd quorum. HAProxy routes client traffic to
the correct node based on Patroni's health endpoint.

```
 Client apps
     |
[ VIP (Keepalived) / OCI NLB ]
     |
 +---------+---------+
 |         |         |
+v--+    +v--+     +v--+
| n1 |   | n2 |    | n3 |
+----+   +----+    +----+

Each node = PostgreSQL + Patroni + etcd + HAProxy
(etcd 3-node quorum across n1/n2/n3)
```

## Node Reference Values

| Node | IP        | Patroni name | Keepalived state / priority |
|------|-----------|--------------|------------------------------|
| n1   | 10.0.0.1  | n1           | MASTER / 101                 |
| n2   | 10.0.0.2  | n2           | BACKUP / 100                  |
| n3   | 10.0.0.3  | n3           | BACKUP / 99                   |

- **Virtual IP (on-prem):** `10.0.0.100`
- **Subnet:** `10.0.0.0/24`

## Minimum VM Specification (per node)

| Resource  | Minimum   | Notes                                  |
|-----------|-----------|-----------------------------------------|
| vCPU      | 2         | 4 recommended for production load       |
| RAM       | 8 GB      | `shared_buffers` ≈ 25% of RAM            |
| OS disk   | 30 GB     | —                                         |
| Data disk | 50 GB SSD | SSD required — etcd is fsync-sensitive   |
| Count     | 3 nodes   | Required for etcd quorum / safe failover |

> **Why 3 nodes?** etcd requires an odd-number quorum. Two nodes cannot perform safe
> automatic failover — three is the minimum for true HA.

## Deployment Guide

The runbook is split into sections for ease of navigation:

1. [Base Preparation](PG-Cluster/01-base-preparation.md)
2. [Install & Configure etcd](docs/02-etcd-setup.md)
3. [Install PostgreSQL + Patroni](docs/03-postgresql-patroni.md)
4. [Configure Patroni](docs/04-patroni-config.md)
5. [Create Patroni systemd Service](docs/05-patroni-service.md)
6. [Install HAProxy + Keepalived](docs/06-haproxy.md)
7. [Configure Keepalived VIP (on-prem only)](docs/07-keepalived.md)
8. [Oracle Cloud (OCI) Variant — NLB instead of Keepalived](docs/08-oci-variant.md)
9. [Verify & Test Failover](docs/09-verify-failover.md)
10. [On-Prem vs OCI — What Changes](docs/10-onprem-vs-oci.md)
11. [Production Checklist](docs/11-production-checklist.md)

## Quick Start

```bash
# On all 3 nodes, in order:
# 1. Base prep + hostnames/hosts file
# 2. Install & configure etcd
# 3. Install PostgreSQL + Patroni
# 4. Configure /etc/patroni/patroni.yml
# 5. Create and start the patroni systemd service (start n1 first, then n2, then n3)
# 6. Install & configure HAProxy
# 7a. On-prem: configure Keepalived VIP
# 7b. OCI: skip Keepalived, create an OCI Network Load Balancer instead
```

After deployment, verify the cluster:

```bash
patronictl -c /etc/patroni/patroni.yml list
```

Expected output — one Leader, two Replicas, all running:

```
+ Cluster: pg-cluster ---+---------+---------+----+-----------+
| Member | Host     | Role    | State   | TL | Lag in MB |
+--------+----------+---------+---------+----+-----------+
| n1     | 10.0.0.1 | Leader  | running | 1  |           |
| n2     | 10.0.0.2 | Replica | running | 1  | 0         |
| n3     | 10.0.0.3 | Replica | running | 1  | 0         |
+--------+----------+---------+---------+----+-----------+
```

## ⚠️ Before You Go to Production

This runbook uses placeholder credentials (`ReplPass123`, `SuperPass123`, `vippass`) and
permissive `pg_hba` rules for clarity. **Do not deploy these as-is.** See the
[Production Checklist](docs/11-production-checklist.md) before exposing this cluster to
real traffic.

## License

Community Edition — provided as-is for educational and operational reference purposes.
