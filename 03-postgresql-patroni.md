# 3. Install PostgreSQL + Patroni

**Applies to: ALL 3 NODES**

```bash
sudo apt install -y postgresql-16 postgresql-contrib python3-pip python3-psycopg2
sudo pip3 install "patroni[etcd3]"

# Patroni manages PostgreSQL — disable the default service
sudo systemctl disable --now postgresql

# Data dir must be empty for first bootstrap
sudo rm -rf /var/lib/postgresql/16/main

sudo mkdir -p /etc/patroni
sudo chown postgres:postgres /etc/patroni
```

> ⚠️ `rm -rf /var/lib/postgresql/16/main` removes any existing PostgreSQL data on this
> node. Only run this during initial cluster bootstrap on a fresh install.

---

Previous: [2. Install & Configure etcd](02-etcd-setup.md) | Next: [4. Configure Patroni](04-patroni-config.md)
