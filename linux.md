## make
preview make build process
```
  make -n
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


## ip
Remove all firecracker tap interfaces
```
  ip a | grep -oP 'fc-\d{1,2}-tap\d' | sudo xargs -L 1 ip link delete
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

## RPMs

### Build RPM from SRPM

```shell
$ rpm -i package.src.rpm
$ cd ~/rpmbuild/SPECS/
$ dnf builddep package.spec -y
$ rpmbuild --rebuild package.src.rpm
```

