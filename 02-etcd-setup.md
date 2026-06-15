# 2️⃣ Install & Configure etcd

![Scope](https://img.shields.io/badge/scope-ALL_3_NODES-blueviolet?style=flat-square)
![Component](https://img.shields.io/badge/component-etcd-419EDA?style=flat-square)

```bash
sudo apt install -y etcd-server etcd-client
```

Edit `/etc/default/etcd` — change `ETCD_NAME` and all IPs per node:

```ini
ETCD_NAME="n1"
ETCD_LISTEN_PEER_URLS="http://10.0.0.1:2380"
ETCD_LISTEN_CLIENT_URLS="http://10.0.0.1:2379,http://127.0.0.1:2379"
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://10.0.0.1:2380"
ETCD_ADVERTISE_CLIENT_URLS="http://10.0.0.1:2379"
ETCD_INITIAL_CLUSTER="n1=http://10.0.0.1:2380,n2=http://10.0.0.2:2380,n3=http://10.0.0.3:2380"
ETCD_INITIAL_CLUSTER_TOKEN="pg-etcd"
ETCD_INITIAL_CLUSTER_STATE="new"
```

Enable and start etcd:

```bash
sudo systemctl enable --now etcd
sudo systemctl restart etcd

# Verify all 3 members are present
etcdctl member list
```

> [!IMPORTANT]
> On `n2` and `n3`, set `ETCD_NAME` to `n2` / `n3` respectively, and update the
> `*_URLS` values to use that node's own IP (e.g. `10.0.0.2`, `10.0.0.3`).
> `ETCD_INITIAL_CLUSTER` remains identical across all three nodes.

---

⬅️ Previous: [1. Base Preparation](01-base-preparation.md) | ➡️ Next: [3. Install PostgreSQL + Patroni](03-postgresql-patroni.md)
