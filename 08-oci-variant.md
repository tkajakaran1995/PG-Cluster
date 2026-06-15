# 8. Oracle Cloud (OCI) Variant — NLB instead of Keepalived

On OCI, replace [Section 7 (Keepalived)](07-keepalived.md) with an OCI Network Load
Balancer (NLB). Sections 1–6 are unchanged.

## 8.1 — Skip Keepalived

Do not install or configure Keepalived. There is no VIP; the NLB provides the stable
entry point.

## 8.2 — Open ports in the VCN (Security List or NSG)

OCI Console → **Networking → VCN → Security Lists** (or attach an NSG). Add ingress rules:

| Port | Purpose | Source |
|------|---------|--------|
| 5432 | PostgreSQL | VCN CIDR `10.0.0.0/24` |
| 8008 | Patroni REST (health) | VCN CIDR `10.0.0.0/24` |
| 2379, 2380 | etcd | VCN CIDR `10.0.0.0/24` |
| 5000, 5001 | HAProxy write / read | VCN CIDR + app CIDR |

## 8.3 — Open ports locally on each Ubuntu node

OCI Ubuntu images ship with `iptables` rules that block traffic even after Security
Lists allow it:

```bash
sudo iptables -I INPUT -p tcp -m multiport \
  --dports 5432,8008,2379,2380,5000,5001 -j ACCEPT
sudo netfilter-persistent save
```

## 8.4 — Create the Network Load Balancer

OCI Console → **Networking → Load Balancers → Network Load Balancer → Create**.

Place it in the same VCN (private subnet recommended; add a public IP only if clients
are external). Then create two listeners and two backend sets:

| Path | Listener | Backend set | Health check |
|------|----------|--------------|---------------|
| Write (to primary) | TCP 5000 | n1/n2/n3 on port 5000 | TCP 5000 |
| Read (replicas) | TCP 5001 | n1/n2/n3 on port 5001 | TCP 5001 |

**How routing still works:** the NLB just forwards to HAProxy on each node. HAProxy
does the real primary/replica routing using Patroni's health endpoint (port 8008).
So the NLB health check is simple TCP — it only confirms HAProxy is alive. The Patroni
failover mechanism is completely unchanged from the on-prem setup.

## 8.5 — Connect via the NLB IP

```bash
# Writes
psql -h <NLB-IP> -p 5000 -U postgres -c "SELECT inet_server_addr();"

# Reads
psql -h <NLB-IP> -p 5001 -U postgres -c "SELECT pg_is_in_recovery();"
```

---

Previous: [7. Configure Keepalived VIP](07-keepalived.md) | Next: [9. Verify & Test Failover](09-verify-failover.md)
