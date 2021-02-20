# Set up PI

## Provision
Flash ubuntu-preintalled-raspi image
Need cifs-utils installed

Add mount points for /var/lib/rancher/k3s/storage and /mnt/speicher
Add worker-fstab entries

Install tailscale [Install for Ubuntu](https://tailscale.com/kb/1039/install-ubuntu-2004)

Add loopback cni plugin and mount bpf
```
sudo mount bpffs -t bpf /sys/fs/bpf
sudo mkdir -p /opt/cni/bin
sudo mv /tmp/loopback /opt/cni/bin/loopback

```
Install k3s... --flannel-backend=none didn't work
```
NODE_TOKEN=from server at /var/lib/rancher/k3s/server/node-token


curl -sfL https://get.k3s.io | K3S_URL=https://10.64.205.130:6443 K3S_TOKEN=$NODE_TOKEN INSTALL_K3S_EXEC='--no-flannel --node-label storagespeed=slow' sh -
```