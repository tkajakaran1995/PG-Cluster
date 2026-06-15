# 6️⃣ Install HAProxy + Keepalived

![Scope](https://img.shields.io/badge/scope-ALL_3_NODES-blueviolet?style=flat-square)
![Component](https://img.shields.io/badge/component-HAProxy-106DA9?style=flat-square)
![Component](https://img.shields.io/badge/component-Keepalived-2E8B57?style=flat-square)

```bash
sudo apt install -y haproxy keepalived
```

Append the following to `/etc/haproxy/haproxy.cfg` (identical on all 3 nodes):

```haproxy
listen postgres-write
    bind *:5000
    option httpchk GET /master
    http-check expect status 200
    default-server inter 3s fall 3 rise 2 on-marked-down shutdown-sessions
    server n1 10.0.0.1:5432 maxconn 100 check port 8008
    server n2 10.0.0.2:5432 maxconn 100 check port 8008
    server n3 10.0.0.3:5432 maxconn 100 check port 8008

listen postgres-read
    bind *:5001
    option httpchk GET /replica
    http-check expect status 200
    balance roundrobin
    server n1 10.0.0.1:5432 check port 8008
    server n2 10.0.0.2:5432 check port 8008
    server n3 10.0.0.3:5432 check port 8008
```

```bash
sudo systemctl restart haproxy
sudo systemctl enable haproxy
```

> [!NOTE]
> 🔀 HAProxy uses Patroni's REST API (port `8008`) for health checks:
> - 🟢 `/master` returns 200 only on the current primary → routes write traffic (port `5000`)
> - 🔵 `/replica` returns 200 only on replicas → load-balances read traffic (port `5001`)

---

⬅️ Previous: [5. Create Patroni systemd Service](05-patroni-service.md) | ➡️ Next: [7. Configure Keepalived VIP (on-prem only)](07-keepalived.md)
