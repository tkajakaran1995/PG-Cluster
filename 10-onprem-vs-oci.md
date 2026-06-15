# 10. On-Prem vs OCI — What Changes

| | On-prem / L2 network | Oracle Cloud (OCI) |
|---|---|---|
| **Stable entry point** | Keepalived VIP (`10.0.0.100`) | OCI NLB private IP |
| **Section 7 (Keepalived)** | Configure as shown | Skip — create NLB instead |
| **Failover routing** | HAProxy + Patroni | HAProxy + Patroni (unchanged) |
| **Firewall** | Optional | VCN Security List + local `iptables` (required) |
| **Sections 1–6** | As written | Identical |

---

Previous: [9. Verify & Test Failover](09-verify-failover.md) | Next: [11. Production Checklist](11-production-checklist.md)
