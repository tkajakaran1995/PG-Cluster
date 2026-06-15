# 4️⃣ Configure Patroni

![Scope](https://img.shields.io/badge/scope-ALL_3_NODES-blueviolet?style=flat-square)
![Component](https://img.shields.io/badge/component-Patroni-2E8B57?style=flat-square)

Create `/etc/patroni/patroni.yml` — change `name` and the 3 IPs per node:

```yaml
scope: pg-cluster
name: n1

restapi:
  listen: 10.0.0.1:8008
  connect_address: 10.0.0.1:8008

etcd3:
  hosts: 10.0.0.1:2379,10.0.0.2:2379,10.0.0.3:2379

bootstrap:
  dcs:
    ttl: 30
    loop_wait: 10
    retry_timeout: 10
    maximum_lag_on_failover: 1048576
    postgresql:
      use_pg_rewind: true
      parameters:
        wal_level: replica
        hot_standby: "on"
        max_wal_senders: 10
        max_replication_slots: 10
  initdb:
    - encoding: UTF8
    - data-checksums
  pg_hba:
    - host replication replicator 10.0.0.0/24 md5
    - host all all 10.0.0.0/24 md5

postgresql:
  listen: 10.0.0.1:5432
  connect_address: 10.0.0.1:5432
  data_dir: /var/lib/postgresql/16/main
  bin_dir: /usr/lib/postgresql/16/bin
  authentication:
    replication:
      username: replicator
      password: ReplPass123
    superuser:
      username: postgres
      password: SuperPass123
```

> [!WARNING]
> 🔐 **Security note:** `ReplPass123` and `SuperPass123` are placeholder credentials.
> Replace them with strong, unique secrets before deploying — see the
> [Production Checklist](11-production-checklist.md).

## 🔧 Per-node values to change

| Field | 🟢 n1 | 🟡 n2 | 🟠 n3 |
|---|:---:|:---:|:---:|
| `name` | `n1` | `n2` | `n3` |
| `restapi.listen` / `connect_address` | `10.0.0.1:8008` | `10.0.0.2:8008` | `10.0.0.3:8008` |
| `postgresql.listen` / `connect_address` | `10.0.0.1:5432` | `10.0.0.2:5432` | `10.0.0.3:5432` |

> [!NOTE]
> The `etcd3.hosts`, `bootstrap`, and `authentication` sections are **identical**
> across all nodes.

---

⬅️ Previous: [3. Install PostgreSQL + Patroni](03-postgresql-patroni.md) | ➡️ Next: [5. Create Patroni systemd Service](05-patroni-service.md)
