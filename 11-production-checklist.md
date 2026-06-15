# ✅ Production Checklist

![Scope](https://img.shields.io/badge/scope-GO--LIVE-critical?style=flat-square)

Before deploying this cluster to production, complete the following:

- [ ] 🔐 **Secrets** — replace `ReplPass123`, `SuperPass123`, and `vippass` with real,
  strong secrets.
- [ ] 🛂 **Access control** — tighten `pg_hba` and firewall rules to your actual
  application subnet only.
- [ ] 💾 **Backups** — set up [pgBackRest](https://pgbackrest.org/) or
  [Barman](https://pgbarman.org/) separately.

  > [!CAUTION]
  > **HA is not backup.** Replication does not protect against accidental deletes or
  > corruption.

- [ ] 🔒 **TLS** — enable TLS for client connections and for replication traffic.
- [ ] 🎛️ **Tuning** — set `shared_buffers` ≈ 25% RAM, `effective_cache_size` ≈ 75% RAM,
  and an appropriate `max_connections`.
- [ ] 📊 **Monitoring** — deploy Prometheus + `postgres_exporter` and watch Patroni /
  etcd health.
- [ ] ⚡ **etcd disk** — keep etcd on low-latency SSD; etcd is highly sensitive to
  fsync latency.

---

⬅️ Previous: [10. On-Prem vs OCI — What Changes](10-onprem-vs-oci.md) | 🏠 Back to: [README](../README.md)
