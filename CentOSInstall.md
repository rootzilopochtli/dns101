# Installing DNS in CentOS 8

## Downloading the image

The QCOW2 image was downloaded from the official CentOS website:

- [CentOS Cloud images](https://cloud.centos.org/centos/8-stream/x86_64/images/)

## Creating the VM

Perform the procedure described in the [SysAdmin Notes](https://github.com/rootzilopochtli/sysadmin-notes/blob/master/vms.md).

## Updating and Installing required software

### Updating OS

```
yum clean all && yum update
```

### Installing bind and tools

```
yum install bind bind-utils 
```

### Configure NTP (chrony)

```
systemctl enable --now chronyd

timedatectl set-timezone America/Mexico_City
```

### Setting up the DNS

- Backing up the default configuration

```
tar czvf etc.named.bkp.tar.gz /etc/named*

tar czvf var.named.bkp.tar.gz /var/named
```

- Delete the default configuration files

```
rm -rf /etc/named* /etc/rndc* /var/named/*
```

- Create the slave zone working directory

```
mkdir -p /var/named/zones/slave
```

- Label the slave work directory for SELinux

```
semanage fcontext -a -e /var/named /var/named/zones

chcon --reference /var/named '/var/named/zones(/.*)?'

restorecon -vR /var/named/zones
```

### DNS Slave Config Files

- Use the different [example files](https://github.com/rootzilopochtli/dns101/tree/master/slave) from the **DNS Slave directory**.


### Create rndc config file

```
rndc-confgen -a -b 512 -c /etc/rndc.key

cat /etc/rndc.key > /etc/rndc.conf

rm -rf /etc/rndc.key
```

---

### Zone Transfer

```
rm -rf /var/named/zones/slave/*.slave

systemctl restart named
```

## References

- [How to configure the BIND DNS service - RHEL](https://access.redhat.com/solutions/40683)
- [How to Setup DNS Server (Bind) on CentOS 8 / RHEL8](https://www.linuxtechi.com/setup-bind-server-centos-8-rhel-8/)
- [CentOS - HowTos / SELinux](https://wiki.centos.org/HowTos/SELinux)
