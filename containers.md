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

## Troubleshooting ovirt csi driver

Get all relevant objects for csi driver
```
$ oc get all -n ovirt-csi-driver
NAME                       READY   STATUS            RESTARTS   AGE
pod/ovirt-csi-node-2nptq   0/2     PodInitializing   0          2d23h
pod/ovirt-csi-node-7t266   2/2     Running           0          15m
pod/ovirt-csi-plugin-0     0/3     PodInitializing   0          2d23h

NAME                            DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/ovirt-csi-node   1         1         1       1            1           <none>          2d23h

NAME                                READY   AGE
statefulset.apps/ovirt-csi-plugin   0/1     2d23h
```

`ovirt-csi-plugin` is a pod, that is part of the statefulset, running the controller logic (create volume, delete volume, attach volume...).
It runs the following containers: csi-external-attacher (triggers ControllerPublish/UnpublishVolume), csi-external-provisioner (mainly for Create/DeleteVolume) and ovirt-csi-driver.
`ovirt-csi-node` is a daemonset running the csi-driver-registrar (provides information about the driver with GetPluginInfo and GetPluginCapabilities) and ovirt-csi-driver.

The sidecar containers (csi-external-attacher, csi-external-provisioner, csi-driver-registrar) run alongside ovirt-csi-driver and run its code via gRPC.

Get inside the pods:
```
oc -n ovirt-csi-driver rsh -c <pod name> pod/ovirt-csi-node-2nptq
```

Watch logs:
```
oc logs pods/ovirt-csi-node-2nptq -n ovirt-csi-driver -c <pod name> | less
```

