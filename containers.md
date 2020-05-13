# Docker

## Remove dead containers
```
  docker rm $(docker ps -qa --filter status=exited)
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
