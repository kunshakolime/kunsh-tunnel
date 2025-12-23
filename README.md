# Ssh backend first

A minimal setup for running Dropbear SSH server in an isolated Alpine Linux chroot environment.

## Setup and Start

```bash
# Create workspace
mkdir websocket && cd websocket
mkdir bin

# Download and extract Alpine minirootfs
curl -O https://dl-cdn.alpinelinux.org/alpine/v3.23/releases/x86_64/alpine-minirootfs-3.23.2-x86_64.tar.gz
tar -xzf alpine-minirootfs-3.23.2-x86_64.tar.gz -C ./bin
rm alpine-minirootfs-3.23.2-x86_64.tar.gz

# Set up Dropbear
mkdir ./bin/etc/dropbear
curl -L https://raw.githubusercontent.com/kunshakolime/kunsh-tunnel/main/dropbear/2019.78/alpine/amd64/dropbear -o ./bin/usr/sbin/dropbear
curl -L https://raw.githubusercontent.com/kunshakolime/kunsh-tunnel/main/dropbear/2019.78/alpine/amd64/dropbearkey -o ./bin/usr/sbin/dropbearkey
curl -L https://raw.githubusercontent.com/kunshakolime/kunsh-tunnel/main/dropbear/2019.78/alpine/amd64/dropbearconvert -o ./bin/usr/sbin/dropbearconvert
curl -L https://raw.githubusercontent.com/kunshakolime/kunsh-tunnel/main/dropbear/2019.78/alpine/amd64/dbclient -o ./bin/usr/sbin/dbclient

# Set up urandom and shells
touch ./bin/dev/urandom
mount --bind /dev/urandom ./bin/dev/urandom
echo "/sbin/nologin" >> ./bin/etc/shells

# Create user and start server
chroot ./bin ./bin/sh -l
adduser -s /sbin/nologin -H sshfwd
dropbear -EFR -p 23 -W 100
```

Connect with: `ssh sshfwd@localhost -p 23`

## Cleanup

```bash
umount ./bin/dev/urandom
rm -rf websocket/
```
