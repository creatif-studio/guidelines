# GlusterFS

GlusterFS (Gluster File System) is an open source Distributed File System that can scale out in building-block fashion to store multiple petabytes of data.

## Installation

Please run this command line by line:

Update the apt package index and install packages to allow apt to use a repository over HTTPS

```bash
sudo apt install -y software-properties-common
```

Add GlusterFS official GPG key

```bash
sudo add-apt-repository ppa:gluster/glusterfs-7
sudo apt update -y
```

Install GlusterFS

```bash
sudo apt install glusterfs-server
systemctl enable glusterd
systemctl start glusterd
systemctl status glusterd
```

## Configuration

Make sure to create a virtual hosts for every virtual machine

```bash
cat /etc/hosts
192.168.2.111 gluster1
192.168.2.112 gluster2
192.168.2.113 gluster3
192.168.2.114 gluster4
192.168.2.115 gluster5
```

Format your additional disk for every virtual machine

```bash
lsblk
mkfs.xfs /dev/vdc
```

Run this command in every virtual machine, and also change the number `1` to another numbers.

```bash
mkdir -p /gluster/bricks/1
echo '/dev/vdc /gluster/bricks/1 xfs defaults 0 0' >> /etc/fstab
mount -a
mkdir /gluster/bricks/1/brick
```

Peer every gluster vm with this commands (run only on gluster1)

```bash
gluster peer probe gluster2
gluster peer probe gluster3
gluster peer probe gluster4
gluster peer probe gluster5
gluster peer status
```

Setup gluster replicated volumes (run only on gluster1)

```bash
gluster volume create gfs \
replica 5 \
gluster1:/gluster/bricks/1/brick \
gluster2:/gluster/bricks/2/brick \
gluster3:/gluster/bricks/3/brick \
gluster4:/gluster/bricks/4/brick \
gluster5:/gluster/bricks/5/brick

gluster volume start gfs
gluster volume status gfs
```

Setup security and authentication for this volume

```bash
gluster volume set gfs auth.allow 192.168.2.111,192.168.2.112,192.168.2.113,192.168.2.114,192.168.2.115
```

Mount the glusterFS volume where applications can access the files, run on every virtual machine

```bash
echo 'localhost:/gfs /mnt glusterfs defaults,_netdev,backupvolfile-server=localhost 0 0' >> /etc/fstab
mount.glusterfs localhost:/gfs /mnt
```

Verify mounted glusterfs volume

```bash
df -Th
```
