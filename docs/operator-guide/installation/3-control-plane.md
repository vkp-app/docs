# Installing the Control Plane

!!! warning
    Ensure you have installed the Operator first as it provides the APIs used by the Control Plane.

Prerequisites:
* ClusterAPI
* Operator
* Cert Manager

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
vkp:
  certificates:
    certManagerNamespace: cert-manager # (3)!
```

1.  Generate with `openssl rand -base64 32 | tr -- '+/' '-_'`
2.  VKP delegates authentication to your external OIDC provider and supports the full range of [Dex connectors](https://dexidp.io/docs/connectors/).
3. This can be omitted if you use the default `cert-manager` namespace. Some installations (e.g. via the OLM Operator) may use a different namespace (e.g. `openshift-operators`).

There are many more values that can be configured, however this is the minimum needed to bootstrap VKP.
For the full list, have a look at the [`values.yaml`](https://github.com/vkp-app/vkp/blob/main/deploy/chart/vkp/values.yaml) file.

## Helm

```shell
git clone https://github.com/vkp-app/vkp.git
helm install vkp oci://ghcr.io/vkp-app/vkp/helm-charts -f values.yaml
```

## Kustomize

The Kustomize installation is fundamentally the same as Helm, as it uses the same Helm chart under the hood.

Prepare your `kustomization.yaml`:

```yaml
helmCharts:
  - name: vkp
    releaseName: vkp
    namespace: vkp-system
    version: 0.3.0
    valuesFile: values.yaml
```

Install:

```shell
kustomize build --enable-helm . | kubectl apply -f -
```
