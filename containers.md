# Docker

## Remove dead containers
```
  docker rm $(docker ps -qa --filter status=exited)
```
## Getting proper lsblk information

Running `lsblk -f` inside a container won't get us the results we want:
```
[root@a9b386ca417d /]# lsblk -f
NAME                                          FSTYPE FSVER LABEL UUID FSAVAIL FSUSE% MOUNTPOINT
sda
├─sda1
└─sda2
  └─luks-ec21b293-fb68-44e5-9875-545437969c84
    ├─fedora-root                                                        9.2G    81% /etc/hosts
    ├─fedora-swap                                                                    [SWAP]
    └─fedora-home
```

Even if mapped we mapped `/dev` and `/sys/block`:
```
docker run -v /sys/block:/sys/block -v /dev:/dev -it fedora:32 /bin/bash
```

`lsblk` seems to read `ID_FS_TYPE` from udev, namely: `/run/udev/data/b8:2` when `8:2` is the major:minor
of the device.

The solution is to map `/run/udev` to the container:
```
docker run -v /run/udev:/run/udev -v /dev:/dev -it fedora:32 /bin/bash
```

# OKD

## Override default finalizer to allow removal of operator
```
oc patch ovirtcsioperator.ovirt.csidriver.openshift.io/ovirtcsioperator -p '{"metadata":{"finalizers":[]}}' --type=merge -n openshift-ovirt-csi-operator
```

## remote shell
```
oc rsh <pod name>
```

## OCP on RHV

Creation lifecycle:
1. `CreateVolume` - creates a disk on RHV, return a `Volume` object
2. `ControllerPublishVolume` - attach the disk to the node (RHV VM)
3. `NodeStageVolume` - create a file system on the attached disk
4. `NodePublishVolume` - mount the device
