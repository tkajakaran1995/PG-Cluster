# 1️⃣ Base Preparation

![Scope](https://img.shields.io/badge/scope-ALL_3_NODES-blueviolet?style=flat-square)

```bash
sudo apt update && sudo apt upgrade -y
sudo timedatectl set-ntp true

# Set hostname per node (n1 / n2 / n3)
sudo hostnamectl set-hostname n1

cat <<EOF | sudo tee -a /etc/hosts
10.0.0.1 n1
10.0.0.2 n2
10.0.0.3 n3
EOF
```

> [!TIP]
> Replace `n1` in `hostnamectl set-hostname` with `n2` or `n3` as appropriate for each
> node. The `/etc/hosts` block is identical across all three nodes.

---

⬅️ Back to: [README](../README.md) | ➡️ Next: [2. Install & Configure etcd](02-etcd-setup.md)
