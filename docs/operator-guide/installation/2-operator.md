# Installing the Operator

The Operator is the most important component of the VKP is it orchestrates the creation and management of the Virtual Clusters.
It was built using the [OperatorSDK](https://sdk.operatorframework.io/).

Because of this, the recommended method of installation is via the Operator Lifecycle Model.

## Installing the OLM

> If you're on OpenShift, you can skip this step.

Follow the OLM installation guide [here](https://github.com/operator-framework/operator-lifecycle-manager/blob/master/doc/install/install.md).

## Creating the CatalogSource

On OpenShift, you can use the guided setup or create the CatalogSource manually.

```yaml
apiVersion: operators.coreos.com/v1alpha1
kind: CatalogSource
metadata:
  name: vkp-catalog
  namespace: olm
spec:
  displayName: Virtual Kubernetes Platform Catalog
  image: 'ghcr.io/vkp-app/vkp/index:main'
  publisher: VKP
  sourceType: grpc
  updateStrategy:
    registryPoll:
      interval: 10m
```

## Creating the Subscription

> On OpenShift (or if you're using the OpenShift Console), you can install the Operator via the guided installation provided by the OperatorHub.

!!! note
    If you have not installed ClusterAPI, the Operator will go into CrashLoopBackOff after a few minutes.

```yaml
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  labels:
    operators.coreos.com/operator.vkp-system: ''
  name: operator
  namespace: vkp-system
spec:
  channel: alpha
  installPlanApproval: Automatic
  name: operator
  source: vkp-catalog
  sourceNamespace: olm
  startingCSV: operator.v0.1.1
  config:
    env:
      - name: PAAS_IS_OPENSHIFT # (1)!
        value: "true"
```

1. This only need to be set if you're using OpenShift.
