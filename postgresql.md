## Set up postgres with podman

### Install podman
[Podman Install](https://podman.io/getting-started/installation)
Podman isn't in the repos until 20.10
```
. /etc/os-release
echo "deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_${VERSION_ID}/ /" | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
curl -L "https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_${VERSION_ID}/Release.key" | sudo apt-key add -
sudo apt-get update
sudo apt-get -y upgrade
sudo apt-get -y install podman
```

### Run Postgres
Create a data directory to mount in `mkdir -p ~/postgres`
Create podman container to start later.
`sudo podman create --name postgres -v $(pwd)/postgres:/bitnami/postgresql -e POSTGRESQL_PASSWORD=postgres_root_password -p 5432:5432 bitnami/postgresql:13`

Create postgres systemd service
```
sudo podman generate systemd --new --files --name postgres
sudo mv container-postgres.service /etc/systemd/system/container-postgres.service
sudo systemctl enable container-postgres.service
sudo systemctl start container-postgres.service
```

This probably could have run rootless, but then you need to configure your user to linger. Podman has a guide for that [here](http://docs.podman.io/en/latest/markdown/podman-generate-systemd.1.html?highlight=systemd#installation-of-generated-systemd-unit-files).
