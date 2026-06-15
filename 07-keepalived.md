# 7. Configure Keepalived VIP

**Applies to: ON-PREM ONLY**

> ⏭️ **Skip this step on Oracle Cloud (OCI)** — OCI's virtual network blocks VRRP /
> floating IPs. Use the [OCI NLB variant](08-oci-variant.md) instead.

Create `/etc/keepalived/keepalived.conf` — change `state` and `priority` per node.
Confirm the interface name with `ip a` (often `ens3` / `eth0`):

```ini
vrrp_script chk_haproxy {
    script "killall -0 haproxy"
    interval 2
    weight 2
}

vrrp_instance VI_1 {
    interface eth0
    state MASTER          # BACKUP on n2 / n3
    virtual_router_id 51
    priority 101          # 100 on n2, 99 on n3
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass vippass
    }
    virtual_ipaddress {
        10.0.0.100
    }
    track_script {
        chk_haproxy
    }
}
```

```bash
sudo systemctl enable --now keepalived

# VIP should appear on n1
ip a | grep 10.0.0.100
```

## Per-node values

| Node | `state`  | `priority` |
|------|----------|------------|
| n1   | `MASTER` | `101`      |
| n2   | `BACKUP` | `100`      |
| n3   | `BACKUP` | `99`       |

> 🔐 Replace `vippass` with a strong secret before deploying — see the
> [Production Checklist](11-production-checklist.md).

---

Previous: [6. Install HAProxy + Keepalived](06-haproxy.md) | Next: [8. Oracle Cloud (OCI) Variant](08-oci-variant.md)
