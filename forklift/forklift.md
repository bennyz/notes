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
