# 1. Base Preparation

**Applies to: ALL 3 NODES**

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

> Replace `n1` in `hostnamectl set-hostname` with `n2` or `n3` as appropriate for each node.
> The `/etc/hosts` block is identical across all three nodes.

Next: [2. Install & Configure etcd](02-etcd-setup.md)
