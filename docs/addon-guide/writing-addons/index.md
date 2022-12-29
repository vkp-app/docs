# Writing addons

An addon is a collection of Kubernetes manifests.
VKP uses the Kustomize application to apply manifests to enable advanced templating.

## Creating the addon

Take a look at the [Kustomize documentation](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/) to understand how to package an application.

### VKP-specific tweaks

VKP uses a naive find-and-replace system to inject system variables (as well as any that may be set by your platform administrator).
Any environment variable set on the Operator that has the `__VKP_` prefix will be replaced in your addon manifests.
This allows you to apply some basic templating such as:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-application
  annotations:
    cert-manager.io/cluster-issuer: __VKP_CLUSTER_ISSUER__ # (1)!
spec:
  ingressClassName: __VKP_INGRESS_CLASS__ # (2)!
  rules:
    - host: web.__VKP_CLUSTER_URL__ # (3)!
      http:
        paths:
          - path: /
            pathType: ImplementationSpecific
            backend:
              service:
                name: my-application
                port:
                  number: 80
  tls:
    - secretName: my-application-tls
      hosts:
        - web.__VKP_CLUSTER_URL__
```

1. Nominates that the platform ClusterIssuer should be used to generate TLS certificates. This may be a trusted service such as LetsEncrypt or an internal certificate authority.
2. Configures which IngressController will be responsible for routing traffic to this Ingress. https://kubernetes.io/docs/concepts/services-networking/ingress/#ingress-class
3. Sets the URL that the Ingress will be available on to use the VKP-provided URL.

## Deploying the addon

### As a Secret

```bash
# create a secret from the current directory
kubectl create secret vkp-addon-podinfo --from-file=.

# create our clusteraddon
cat <<EOF | kubectl apply -n my-namespace -f -
apiVersion: paas.dcas.dev/v1alpha1
kind: ClusterAddon
metadata:
  name: podinfo
spec:
  displayName: "Podinfo"
  maintainer: "joe@example.org"
  description: |
    My awesome application.
  source: Community
  sourceURL: https://github.com/stefanprodan/podinfo
  logo: https://raw.githubusercontent.com/stefanprodan/podinfo/gh-pages/cuddle_clap.gif
  resources:
    - secret:
        name: vkp-addon-podinfo
EOF
```

### As a ConfigMap

```bash
# create a configmap from the current directory
kubectl create configmap vkp-addon-podinfo --from-file=.

# create our clusteraddon
cat <<EOF | kubectl apply -n my-namespace -f -
apiVersion: paas.dcas.dev/v1alpha1
kind: ClusterAddon
metadata:
  name: podinfo
spec:
  displayName: "Podinfo"
  maintainer: "joe@example.org"
  description: |
    My awesome application.
  source: Community
  sourceURL: https://github.com/stefanprodan/podinfo
  logo: https://raw.githubusercontent.com/stefanprodan/podinfo/gh-pages/cuddle_clap.gif
  resources:
    - configMap:
        name: vkp-addon-podinfo
EOF
```

### As an OCI image

!!! note
	OCI images are the recommended method of bundling addons for air-gapped usage and is the format used by all Official addons.

Install `imgpkg` by following the [Caravel documentation here](https://carvel.dev/imgpkg/docs/v0.34.0/install/).

```bash
# create an OCI image from the current directory
imgpkg push --file-exclusion ".git, charts" -i "registry.example.com/foo/bar:v1.2.3" -f .

# create our clusteraddon
cat <<EOF | kubectl apply -n my-namespace -f -
apiVersion: paas.dcas.dev/v1alpha1
kind: ClusterAddon
metadata:
  name: podinfo
spec:
  displayName: "Podinfo"
  maintainer: "joe@example.org"
  description: |
    My awesome application.
  source: Community
  sourceURL: https://github.com/stefanprodan/podinfo
  logo: https://raw.githubusercontent.com/stefanprodan/podinfo/gh-pages/cuddle_clap.gif
  resources:
    - oci:
        name: registry.example.com/foo/bar:v1.2.3
EOF
```

## Installing the addon

Follow the instructions on the [installing addons guide](/addon-guide/installing-addons/).
