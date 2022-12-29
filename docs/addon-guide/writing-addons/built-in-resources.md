# Built-in resources

Being Kubernetes based, VKP supports all Kubernetes [Custom Resource Definitions](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) (CRDs).

VKP contains a number of pre-installed CRDs to simplify use:

* Prometheus ServiceMonitor
* Dex OAuthClient

## Embedded resources

Some CRDs contain references to other Kubernetes objects which may not be automatically synced by VKP. 
For example, the OAuthClient `spec.secretRef` refers to a Kubernetes secret that will not automatically be synced.
You will need to make sure that the secret contains the `vcluster.loft.sh/force-sync: "true"` annotation so that the VKP will sync it to the management cluster.

```yaml
kind: Secret
apiVersion: v1
metadata:
  name: example
  namespace: default
  annotations:
    vcluster.loft.sh/force-sync: 'true'
stringData:
  client_secret: hunter2
type: Opaque

```

## ServiceMonitor

ServiceMonitor's created in the Virtual Cluster will be copied to the host cluster and scraped by the Prometheus stack running on the host.

## OAuthClient

OAuthClient's created in the Virtual Cluster will be copied to the host cluster and used to configure the VKP IDP (Dex) to enable "Login with OIDC" for applications running in your virtual cluster.
