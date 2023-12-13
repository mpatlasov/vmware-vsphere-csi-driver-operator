# DUMMY CHANGE -- let's check if `ci/prow/e2e-vsphere-ovn-upgrade` perma-failing
# vmware-vsphere-csi-driver-operator

An operator to deploy the [VMware vSphere CSI Driver](https://github.com/openshift/vmware-vsphere-csi-driver) in OKD.

This operator is installed by the [cluster-storage-operator](https://github.com/openshift/cluster-storage-operator).

# Quick start

Before running the operator manually, you must remove the operator installed by CSO/CVO

```shell
# Scale down CVO and CSO
oc scale --replicas=0 deploy/cluster-version-operator -n openshift-cluster-version
oc scale --replicas=0 deploy/cluster-storage-operator -n openshift-cluster-storage-operator

# Delete operator resources (daemonset, deployments)
oc -n openshift-cluster-csi-drivers delete deployment.apps/vmware-vsphere-csi-driver-operator deployment.apps/vmware-vsphere-csi-driver-controller daemonset.apps/vmware-vsphere-csi-driver-node
```

To build and run the operator locally:

```shell
# Create only the resources the operator needs to run via CLI
oc apply -f https://raw.githubusercontent.com/openshift/cluster-storage-operator/master/assets/csidriveroperators/vsphere/09_cr.yaml

# Build the operator
make

# Set the environment variables
export DRIVER_IMAGE=quay.io/openshift/origin-vsphere-csi-driver:latest
export PROVISIONER_IMAGE=quay.io/openshift/origin-csi-external-provisioner:latest
export ATTACHER_IMAGE=quay.io/openshift/origin-csi-external-attacher:latest
export RESIZER_IMAGE=quay.io/openshift/origin-csi-external-resizer:latest
export SNAPSHOTTER_IMAGE=quay.io/openshift/origin-csi-external-snapshotter:latest
export NODE_DRIVER_REGISTRAR_IMAGE=quay.io/openshift/origin-csi-node-driver-registrar:latest
export LIVENESS_PROBE_IMAGE=quay.io/openshift/origin-csi-livenessprobe:latest
export KUBE_RBAC_PROXY_IMAGE=quay.io/openshift/origin-kube-rbac-proxy:latest
export VMWARE_VSPHERE_SYNCER_IMAGE=quay.io/openshift/origin-vsphere-csi-driver-syncer

# Run the operator via CLI
./vmware-vsphere-csi-driver-operator start --kubeconfig $MY_KUBECONFIG --namespace openshift-cluster-csi-drivers
```

