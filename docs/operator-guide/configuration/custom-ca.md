# Custom Certificate Authorities

Some environments (e.g. corporate or high-security environments) require the use of custom certificate authorities.

## Adding a custom CA

Create a Kubernetes Secret or ConfigMap in the namespace where the operator runs:

```shell
# as a secret
kubectl create secret generic my-custom-ca --from-file=ca.crt=./ca.crt -n vkp-system
# as a configmap
kubectl create configmap my-custom-ca --from-file=ca.crt=./ca.crt -n vkp-system
```

The `ca.crt` file can contain 1 or more certificate authorities.
For example, you may include a corporate certificate as well as the certificate for your local Kubernetes cluster.

!!! warning
    The CA bundle you create MUST contain the CA that the VKP Ingress TLS certificates are signed with.

Mount the Secret/ConfigMap to the Operator by editing the [`Subscription`](/operator-guide/installation/operator/#creating-the-subscription):

```yaml
spec:
  config:
    volumes:
      - name: custom-root-secret
        secret:
          secretName: my-custom-ca
      - name: custom-root-configmap
        configMap:
          name: my-custom-ca
    volumeMounts:
      - name: custom-root-secret # or custom-root-configmap
        mountPath: /var/run/secrets/paas.dcas.dev/pki
        readOnly: true
    env:
      - name: TENANT_CUSTOM_CA_FILE
        value: /var/run/secrets/paas.dcas.dev/pki/ca.crt
```

## Consuming the CA from within a Cluster

The VKP will create a ConfigMap in every namespace of the Cluster called `platform-root-ca.crt` (similar to how Kubernetes creates the `kube-root-ca.crt` ConfigMap).

This ConfigMap contains a file called `ca.crt` that can be used to verify TLS connections.

For example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example
  labels:
    app: sample
  namespace: default
spec:
  securityContext:
    runAsUser: 12345
    runAsNonRoot: true
    seccompProfile:
      type: RuntimeDefault
  volumes:
    - name: pki
      configMap:
        name: platform-root-ca.crt
  containers:
    - name: sample
      image: docker.io/curlimages/curl:latest
      command:
        - /bin/sh
      args:
        - -c
        - "while true; do curl https://foo.corporate.internal; done" # (2)!
      volumeMounts:
        - name: pki
          mountPath: /etc/ssl
          readOnly: true
      env:
        - name: SSL_CERT_FILE # (1)!
          value: /etc/ssl/ca.crt
      securityContext:
        allowPrivilegeEscalation: false
        capabilities:
          drop:
            - ALL
```

1. This environment will differ depending on the applications you use. `SSL_CERT_FILE` is very common.
2. This is a hypothetical URL that exists in your corporate environment.