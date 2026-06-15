# 9. Verify & Test Failover

```bash
# Writes — always routed to the current primary
psql -h 10.0.0.100 -p 5000 -U postgres -c "SELECT inet_server_addr();"

# Reads — load-balanced across replicas
psql -h 10.0.0.100 -p 5001 -U postgres -c "SELECT pg_is_in_recovery();"

# Trigger a controlled failover
patronictl -c /etc/patroni/patroni.yml switchover

# Confirm a replica was promoted to Leader
patronictl -c /etc/patroni/patroni.yml list
```

> On OCI, replace `10.0.0.100` with the OCI NLB's IP address.

## Success Criteria

After `switchover`:

- A former replica shows `Leader` / `running`
- The old primary rejoins the cluster as a replica
- Writes through port `5000` continue to reach the new primary **with no client
  reconfiguration**

---

Previous: [8. Oracle Cloud (OCI) Variant](08-oci-variant.md) | Next: [10. On-Prem vs OCI — What Changes](10-onprem-vs-oci.md)
