# Installing the Control Plane

!!! warning
    Ensure you have installed the Operator first as it provides the APIs used by the Control Plane.

## Values

Create a file called `values.yaml` with the following contents:

```yaml title="values.yaml"
global:
  ingress:
    domain: <your domain>
    ingressClassName: <ingress class>
prometheus:
  url: <url to management cluster Prometheus>
idp:
  clientSecret: <some secret value>
  cookieSecret: <some secret value> # (1)!
  connectors: # (2)!
    - id: mock
      name: Mock
      type: mockCallback
```

1.  Generate with `openssl rand -base64 32 | tr -- '+/' '-_'`
2.  VKP delegates authentication to your external OIDC provider and supports the full range of [Dex connectors](https://dexidp.io/docs/connectors/).

There are many more values that can be configured, however this is the minimum needed to bootstrap VKP.
For the full list, have a look at the [`values.yaml`](https://github.com/vkp-app/vkp/blob/main/deploy/chart/vkp/values.yaml) file.

## Helm

```shell
git clone https://github.com/vkp-app/vkp.git
helm install vkp ./vkp/deploy/chart/vkp -f values.yaml
```

## Kustomize

The Kustomize installation is fundamentally the same as Helm, as it uses the same Helm chart under the hood.

Prepare your `kustomization.yaml`:

```yaml
helmCharts:
  - name: vkp
    releaseName: vkp
    namespace: vkp-system
    version: 0.1.1
    valuesFile: values.yaml
```

Install:

```shell
kustomize build --enable-helm . | kubectl apply -f -
```