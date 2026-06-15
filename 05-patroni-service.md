# 5. Create Patroni systemd Service

**Applies to: ALL 3 NODES**

Create `/etc/systemd/system/patroni.service`:

```ini
[Unit]
Description=Patroni PostgreSQL HA
After=network.target etcd.service

[Service]
Type=simple
User=postgres
Group=postgres
ExecStart=/usr/local/bin/patroni /etc/patroni/patroni.yml
Restart=on-failure
KillMode=process
TimeoutSec=30

[Install]
WantedBy=multi-user.target
```

> ⚠️ **Start order matters.** Start `n1` first (it bootstraps the cluster and becomes
> primary), then `n2`, then `n3`.

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now patroni

patronictl -c /etc/patroni/patroni.yml list
```

### Expected output

One Leader, two Replicas, all `running`:

```
+ Cluster: pg-cluster ---+---------+---------+----+-----------+
| Member | Host     | Role    | State   | TL | Lag in MB |
+--------+----------+---------+---------+----+-----------+
| n1     | 10.0.0.1 | Leader  | running | 1  |           |
| n2     | 10.0.0.2 | Replica | running | 1  | 0         |
| n3     | 10.0.0.3 | Replica | running | 1  | 0         |
+--------+----------+---------+---------+----+-----------+
```

---

Previous: [4. Configure Patroni](04-patroni-config.md) | Next: [6. Install HAProxy + Keepalived](06-haproxy.md)
