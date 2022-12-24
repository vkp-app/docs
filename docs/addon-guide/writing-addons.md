# Writing addons

An addon is a collection of Kubernetes manifests.
VKP uses the Kustomize application to apply manifests to enable advanced templating.

## Creating the addon

TODO

## Packaging the addon

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
