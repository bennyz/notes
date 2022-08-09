# Forklift

## Logs
```
oc get pods -n konveyor-forklift # forklift-controler-<unique> pod
oc logs <pod name> main/inventory -n konveyor-forklift
```

## Interesting code locations

[Data volumes creation](https://github.com/konveyor/forklift-controller/blob/76ee1608b4850a0d47e92b508627b4e94847e29c/pkg/controller/plan/kubevirt.go#L644):

```go
func (r *KubeVirt) dataVolumes(vm *plan.VMStatus, secret *core.Secret, configMap *core.ConfigMap) (objects []cdi.DataVolume, err error) {
	_, err = r.Source.Inventory.VM(&vm.Ref)
	if err != nil {
		return
	}

	dataVolumes, err := r.Builder.DataVolumes(vm.Ref, secret, configMap)
	if err != nil {
		return
	}

	for i := range dataVolumes {
		annotations := make(map[string]string)
		if !r.Plan.Spec.Warm || Settings.RetainPrecopyImporterPods {
			annotations[AnnRetainAfterCompletion] = "true"
		}
		if r.Plan.Spec.TransferNetwork != nil {
			annotations[AnnDefaultNetwork] = path.Join(
				r.Plan.Spec.TransferNetwork.Namespace, r.Plan.Spec.TransferNetwork.Name)
		}
		dv := cdi.DataVolume{
			ObjectMeta: meta.ObjectMeta{
				Namespace:   r.Plan.Spec.TargetNamespace,
				Annotations: annotations,
				GenerateName: strings.Join(
					[]string{
						r.Plan.Name,
						vm.ID},
					"-") + "-",
			},
			Spec: dataVolumes[i],
		}
		dv.Labels = r.vmLabels(vm.Ref)
		objects = append(objects, dv)
	}

	return
}
```

For oVirt it's done [in](https://github.com/konveyor/forklift-controller/blob/367d2e9e2378ba7419881aff80fd32472a4b8538/pkg/controller/plan/adapter/ovirt/builder.go#L164)
```go
// Create DataVolume specs for the VM.
func (r *Builder) DataVolumes(vmRef ref.Ref, secret *core.Secret, configMap *core.ConfigMap) (dvs []cdi.DataVolumeSpec, err error) {
	vm := &model.Workload{}
	err = r.Source.Inventory.Find(vm, vmRef)
	if err != nil {
		err = liberr.Wrap(
			err,
			"VM lookup failed.",
			"vm",
			vmRef.String())
		return
	}
	url := r.Source.Provider.Spec.URL

	dsMapIn := r.Context.Map.Storage.Spec.Map
	for i := range dsMapIn {
		mapped := &dsMapIn[i]
		ref := mapped.Source
		sd := &model.StorageDomain{}
		fErr := r.Source.Inventory.Find(sd, ref)
		if fErr != nil {
			err = fErr
			return
		}
		for _, da := range vm.DiskAttachments {
			if da.Disk.StorageDomain == sd.ID {
				storageClass := mapped.Destination.StorageClass
				size := da.Disk.ProvisionedSize
				if da.Disk.ActualSize > size {
					size = da.Disk.ActualSize
				}
				dvSpec := cdi.DataVolumeSpec{
					Source: &cdi.DataVolumeSource{
						Imageio: &cdi.DataVolumeSourceImageIO{
							URL:           url,
							DiskID:        da.Disk.ID,
							SecretRef:     secret.Name,
							CertConfigMap: configMap.Name,
						},
					},
					Storage: &cdi.StorageSpec{
						Resources: core.ResourceRequirements{
							Requests: core.ResourceList{
								core.ResourceStorage: *resource.NewQuantity(size, resource.BinarySI),
							},
						},
						StorageClassName: &storageClass,
					},
				}
				// set the access mode and volume mode if they were specified in the storage map.
				// otherwise, let the storage profile decide the default values.
				if mapped.Destination.AccessMode != "" {
					dvSpec.Storage.AccessModes = []core.PersistentVolumeAccessMode{mapped.Destination.AccessMode}
				}
				if mapped.Destination.VolumeMode != "" {
					dvSpec.Storage.VolumeMode = &mapped.Destination.VolumeMode
				}
				dvs = append(dvs, dvSpec)
			}
		}
	}

	return
}
```
