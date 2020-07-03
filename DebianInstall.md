# Installing DNS in Debian 10
---

## Downloading the image

The QCOW2 image for OpenStack was downloaded from the official Debian website:

- [Index of /cdimage/openstack/current-10](https://cdimage.debian.org/cdimage/openstack/current-10/)

## Creating the VM

The procedure described in the [SysAdmin Notes](https://github.com/rootzilopochtli/sysadmin-notes/blob/master/vms.md) was used.

## Updating and Installing required software

### Updating OS

```
apt-get update && apt-upgrade
```

### Installing bind9 and tools

```
apt-get install bind9 bind9-doc bind9utils chrony dnsutils
```

### Configure NTP (chrony)

```
systemctl enable chronyd

timedatectl set-timezone America/Mexico_City
```

### Setting up the DNS

- Backing up the default configuration

```
tar czvf etc.bind.bkp.tar.gz /etc/bind/
```

- Creating the master zone working directory

```
mkdir -p /var/named/zones
```

- Configuring apparmor to allow use of the master zone directory, adding the following 3 lines to /etc/apparmor.d/local/usr.sbin.named:

```
/etc/bind/** rw,
/var/named/** rw,
/var/named/zones/** rw,
```

- Reload apparmor

```
systemctl reload apparmor
```
---

**Note**: If you do not plan to use ipv6 in the DNS, it is a good idea to initialize the named daemon with ipv4 support only:

```
/etc/default/bind9

OPTIONS="**-4** -u bind"
```


## References

- [Bind9](https://wiki.debian.org/Bind9)
- [AppArmor HowToUse](https://wiki.debian.org/AppArmor/HowToUse)