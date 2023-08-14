# NixOS NAS

https://github.com/Misterio77/nix-config/blob/001da06bc69e0154deadf1c3a2f89b9959e05313/hosts/common/btrfs-optin-persistence.nix
https://mt-caret.github.io/blog/posts/2020-06-29-optin-state.html
https://hanckmann.com/posts/nixos-and-erasing-my-darlings/
https://github.com/zilexa/Homeserver/blob/master/filesystem/FILESYSTEM-EXPLAINED.md
https://wiki.selfhosted.show/tools/snapraid-btrfs/

## Steps
Basically followed https://guekka.github.io/nixos-server-1/
* Create bootable USB https://nixos.org/manual/nixos/stable/index.html#sec-booting-from-usb
* Create partitions
  ```
  DISK=/dev/sda
  parted $DISK -- mklabel gpt
  parted $DISK -- mkpart primary 512MB -6GB
  parted $DISK -- mkpart primary linux-swap -6GB 100% 
  parted $DISK -- mkpart ESP fat32 1MB 512MB
  parted $DISK -- set 3 esp on
  ```
* Format
  ```
  mkfs.btrfs -L nixos "$DISK"1
  mkswap -L swap "$DISK"2
  swapon "$DISK"2
  mkfs.fat -F 32 -n boot "$DISK"3
  ```
* Install - https://nixos.org/manual/nixos/stable/index.html#sec-installation-manual-installing
  ```
  mount /dev/disk/by-label/nixos /mnt
  btrfs subvolume create /mnt/root
  btrfs subvolume create /mnt/home
  btrfs subvolume create /mnt/nix
  btrfs subvolume create /mnt/persist
  btrfs subvolume create /mnt/log
  btrfs subvolume snapshot -r /mnt/root /mnt/root-blank
  umount /mnt

  mkdir -p /mnt/boot
  mount /dev/disk/by-label/boot /mnt/boot
  mount -o subvol=root,compress=zstd,noatime /dev/disk/by-label /mnt

  mkdir /mnt/home
  mount -o subvol=home,compress=zstd,noatime /dev/disk/by-label /mnt/home

  mkdir /mnt/nix
  mount -o subvol=nix,compress=zstd,noatime /dev/disk/by-label /mnt/nix

  mkdir /mnt/persist
  mount -o subvol=persist,compress=zstd,noatime /dev/disk/by-label /mnt/persist

  mkdir -p /mnt/var/log
  mount -o subvol=log,compress=zstd,noatime /dev/disk/by-label /mnt/var/log

  nixos-generate-config --root /mnt
  # add user and make sure ssh configured
  nixos-install --root /mnt && reboot

```

## Data Drives
https://perfectmediaserver.com/installation/manual-install-ubuntu/#brand-new-drives
https://wiki.selfhosted.show/tools/snapraid-btrfs/#data-subvolumes
```
mkfs.xfs -L parity0 /dev/sda1
mkfs.btrfs -L data0 /dev/sdd1
mkfs.btrfs -L data1 /dev/sdc1

```
Add mounts to nix config by uuid. /mnt/parity0, /mnt/root/data{0,1}

```
btrfs subvolume create /mnt/root/data0/data
btrfs subvolume create /mnt/root/data1/data
btrfs subvolume create /mnt/root/data0/content
btrfs subvolume create /mnt/root/data1/content
```
Add mounts to nix config for /mnt/data{0,1} for the data subvolume and /mnt/snapraid-content/data{0,1} for the content subvolume
Add mergerfs mount
config for snapraid,snapraid-btrfs,snapper, snapraid-btrfs-runner

