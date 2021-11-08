## make
preview make build process
```shell
make -n
```
Pass additional compiler flags in `autotools`
```shell
./configure CFLAGS=-fPIE
```

## sanlock
Every 20 seconds sanlock reads 1MB and writes 512 bytes to monitor and renew its leases in the lockspace.
```
  sanlock client gets -h 1
```

```
  sanlock direct dump /rhev/data-center/<sp_id>/<sd_id>/images/<image_group>/<volume_id>.lease
```
Sample output:
```
    offset                            lockspace                                         resource  timestamp  own  gen lver
  00000000 77847bfc-b39c-4406-8377-a9515267318a             b6956d4e-d0e0-4cb1-84f1-373dd661b1b5 0000187302 0001 0018 2
```
## dmsetup
Remove all dm devices for vg name
```
dmsetup ls | grep my_vg | cut -f1 | xargs dmsetup remove
```

## Networking

### ip
Remove all firecracker tap interfaces
```
ip a | grep -oP 'fc-\d{1,2}-tap\d' | sudo xargs -L 1 ip link delete
```

### curl
Send requests to unix socket:
```
curl --unix-socket /tmp/firecracker.sock http://localhost
```

Pull all files from a git dir into a single a file with yaml separators:
```
for x in $(curl https://api.github.com/repos/ovirt/csi-driver-operator/contents/manifests |  jq -M '.[].download_url'); do curl -L $(echo $x | tr -d '"') >> dat && echo "---" >> dat ; done
```

### nmcli

Change DNS server via `nmcli`:
```shell
$ nmcli con mod <connection> ipv4.dns "8.8.8.8 8.8.4.4"
$ nmcli con mod <connection> ipv4.ignore-auto-dns yes
# Restart connection
nmcli con down <connection>
nmcli con up <connection>
```

### tcpdump

Dump DHCP requests/responses:
```shell
tcpdump -i fcbridge -pvn port 67 and port 68
```

### MISC

Send UDP packet
```shell
echo "This is my data" > /dev/udp/127.0.0.1/3000
```

## virtualization
### libvirt
Change default libvirt images location
```
  $ virsh pool-edit --pool <pool-name> # default is 'default'
```
Set directly:
```
  $ virsh pool-define-as --name default --type dir --target <path>
```

```
  $ virsh pool-autostart default
```

```
  $ virsh pool-start default
```

To remove a DHCP entry, remove from `/var/lib/libvirt/dnsmasq/virbr0.status`


## iscsi
Login to target
```
  iscsiadm -m node -T <iqn> -p <portal_ip:port> -l
```

## ceph
Mount cephFS
```
  mount -t ceph mon_ip:6789:/ <path> -o name=admin,secret=<key>
```

Remove all volumes from pool
```
  for i in `rados -p <pool_name> ls`; do echo $i; rados -p <pool_name> rm $i; done
```


## find
Find directories with exactly two files in them
```
find . -maxdepth 1 -type d -exec bash -c "echo -ne '{} '; ls '{}' | wc -l" \; | awk '$NF==2'
```

## qemu

Run virtual machine with disk in shell-mode:
```
qemu-kvm -hda <path-to-disk> -nographic
```
To exit: `ctrl+a, x`


## general (unsorted)
Read process's output
```
cat /proc/<pid>/fd/1
```

Move multiple files at once
```shell
$ mv -t DESTINATION SOURCES...
```

Getting IP from DHCP server on kernel boot
```
console=ttyS0 noapic reboot=k panic=1 pci=off nomodules rw ip=dhcp
```

Creating rootfs with Centos 8 Stream
```
# Create rootfs dir
$ mkosi -t directory -d centos --package vim,dnf,openssh-server,iputils --with-network --mirror=http://mirror.isoc.org.il/pub --password password

# Setup ttyS0:
$ systemd-nspawn -D image
# See https://www.thegeekdiary.com/centos-rhel-7-how-to-configure-serial-getty-with-systemd/

$ truncate -s 2G centos-rootfs
$ mkfs.ext4 centos-rootfs
$ mkdir /mnt/rootfs
$ mount centos-rootfs /mnt/rootfs/
$ cp -r image/* /mnt/rootfs/
$ umount /mnt/rootfs/

# Optional, run firecracker:
./firectl --kernel=./vmlinux  --root-drive=./crootfs --kernel-opts="console=ttyS0 noapic reboot=k panic=1 pci=off rw ip=dhcp i8042.noaux=0" --firecracker-binary=./firecracker -m=128 --tap-device=fctap/96:af:da:dd:e7:42
```

## RPMs

### Build RPM from SRPM

```shell
$ rpm -i package.src.rpm
$ cd ~/rpmbuild/SPECS/
$ dnf builddep package.spec -y
$ rpmbuild --rebuild package.src.rpm
```
