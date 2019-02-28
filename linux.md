# General commands
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
