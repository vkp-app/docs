# Air-gap installation

When installing in an air-gapped or disconnected environment, there are a few changes you need to make to the standard installation.

## Transferring images

VKP is distributed as a number of OCI container images and can therefore be deployed to any environment.
You will need to pull images from the internet and store them in an OCI registry in your target environment.

### Pulling images

!!! note
    This should be automated in a similar fashion to Ranchers K3S releases.

```shell
export VKP_VERSION="0.1.2"
# vcluster images
crane pull docker.io/loftsh/vcluster:0.13.0 vcluster.tgz
crane pull docker.io/rancher/k3s:v1.25.3-k3s1 k3s.tgz # (1)!
crane pull docker.io/coredns/coredns:1.8.6 coredns.tgz
# vkp images
crane pull ghcr.io/vkp-app/vkp/operator:$VKP_VERSION operator.tgz
crane pull ghcr.io/vkp-app/vkp/index:$VKP_VERSION index.tgz
crane pull ghcr.io/vkp-app/vkp/bundle:$VKP_VERSION bundle.tgz
crane pull ghcr.io/vkp-app/vkp/apiserver:$VKP_VERSION apiserver.tgz
crane pull ghcr.io/vkp-app/vkp/web:$VKP_VERSION web.tgz
crane pull ghcr.io/vkp-app/vkp/vcluster-plugin-sync:$VKP_VERSION vcluster-plugin-sync.tgz
crane pull ghcr.io/vkp-app/vkp/vcluster-plugin-hooks:$VKP_VERSION vcluster-plugin-hooks.tgz
crane pull ghcr.io/vkp-app/vkp/helm-charts/vkp:$VKP_VERSION chart.tgz
# vkp addons
crane pull ghcr.io/vkp-app/addons/dashboard-k8s:1.1.0 addon-dashboard-k8s.tgz
crane pull ghcr.io/vkp-app/addons/dashboard-okd:1.1.0 addon-dashboard-okd.tgz
crane pull ghcr.io/vkp-app/addons/podinfo:1.0.0 addon-podinfo.tgz
```

1. You will need to pull multiple K3S images, depending on the number of Kubernetes versions you choose to support.

### Pushing images

Once the tarballs are in your target environment, you will need to push them to your registry:

```shell
export VKP_VERSION="0.1.2"
crane push operator.tgz registry.example.internal/ghcr.io/vkp-app/vkp/operator:$VKP_VERSION
...
```


## Operator

Set the following environment variables on the Operator [`Subscription`](/operator-guide/installation/2-operator/#creating-the-subscription):

```yaml
spec:
  config:
    env:
      - name: TENANT_SKIP_DEFAULT_ADDONS # (1)!
        value: "true"
      - name: PAAS_CHART_REPO
        value: https://chartrepo.example.internal
      - name: RELATED_IMAGE_VCLUSTER_SYNCER
        value: registry.example.internal/docker.io/loftsh/vcluster:0.13.0
      - name: RELATED_IMAGE_COREDNS
        value: registry.example.internal/docker.io/coredns/coredns:1.8.6
      - name: RELATED_IMAGE_PLUGIN_SYNC
        value: registry.example.internal/ghcr.io/vkp-app/vkp/vcluster-plugin-sync:<vkp version>
      - name: RELATED_IMAGE_PLUGIN_HOOKS
        value: registry.example.internal/ghcr.io/vkp-app/vkp/vcluster-plugin-hooks:<vkp version>
```

1. Default addons are not currently supported in Air-gapped installation unless you have configured transparent DNS (i.e. `ghcr.io` points to a registry that you control).

## Control Plane

Set the following in your `values.yaml`:

```yaml
api:
  image:
    registry: registry.example.internal/ghcr.io
web:
  image:
    registry: registry.example.internal/ghcr.io
oauthProxy:
  image:
    registry: registry.example.internal/quay.io
dex:
  image:
    registry: registry.example.internal/ghcr.io
```

## Other changes

You will likely need to configure a [custom Certificate Authority](/operator-guide/configuration/custom-ca/).
