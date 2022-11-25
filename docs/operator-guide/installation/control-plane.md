# Installing the Control Plane

!!! warning
    Ensure you have installed the Operator first as it provides the APIs used by the Control Plane.

## Helm

```shell
git clone https://github.com/vkp-app/vkp.git
cd vkp/deploy/chart
helm install vkp ./vkp -f my-values.yaml
```

## Kustomize

The Kustomize installation is fundamentally the same as Helm, as it uses the same Helm chart under the hood.

Prepare your `kustomization.yaml`:

```yaml
helmCharts:
  - name: vkp
    releaseName: vkp
    namespace: vkp-system
    version: 0.1.0
    valuesFile: my-values.yaml
```

Install:

```shell
kustomize build --enable-helm . | kubectl apply -f -
```