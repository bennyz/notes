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
