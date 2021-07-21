# k3s install

## Server
```
#/etc/rancher/k3s/config.yaml
tls-san:
- "k3s-api.home.joshcorp.co"
- "100.113.245.67"

node-taint:
- "CriticalAddonsOnly=true:NoExecute"

node-label:
- "topology.kubernetes.io/zone=cloud"
- "topology.kubernetes.io/region=linode"

node-ip: "100.113.245.67"
bind-address: "100.113.245.67"

flannel-backend: "none"
disable: ["traefik", "svclb"]
```

`iptables -t nat -A ts-postrouting -s 10.42.0.0/16 -o tailscale0 -j MASQUERADE`

```
wget https://github.com/containernetworking/plugins/releases/download/v0.9.1/cni-plugins-linux-amd64-v0.9.1.tgz
tar -xvzf cni-plugins-linux-amd64-v0.9.1.tgz -C /opt/cni/bin
```

```
curl -sfL https://get.k3s.io | sh -s -

wget https://raw.githubusercontent.com/kasuboski/k8s-gitops/master/networking/cilium.yaml
kubectl apply -f cilium.yaml
```

## Agent
```
server: "https://k3s-api.home.joshcorp.co:6443"
token-file: "/etc/rancher/k3s/token"

node-label:
- "topology.kubernetes.io/zone=cloud"
- "topology.kubernetes.io/region=oracle"

node-ip: "100.112.197.93"
```
`curl -sfL https://get.k3s.io | K3S_URL=https://k3s-api.home.joshcorp.co:6443 K3S_TOKEN=$(cat token) sh -`

This didn't work without setting the environment variables even though it's in config.yaml.
