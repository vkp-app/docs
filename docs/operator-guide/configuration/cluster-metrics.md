# Cluster metrics

VKP defers metrics collection and display to your Prometheus monitoring setup.

By default, VKP displays four main metrics in the Cluster dashboard:

* Memory usage
* CPU usage
* Pod count
* Request throughput

## Configuring metrics

### Adding new metrics

Edit your `values.yaml` to include:

```yaml title="values.yaml"
prometheus:
  extraMetrics:
    - name: "Foobar usage" # (1)!
      metric: | # (2)!
        sum by (namespace) (container_foobar_usage{namespace="{namespace}", foobar_source~="*.-{cluster}|{cluster}-.+"})
      format: Plain # (3)!
```

1. This is the name of the metric that will be shown to users.
2. This is the actual query that will be sent to Prometheus. `{namespace}` and `{cluster}` are template values that will be replaced with the Kubernetes namespace (tenant) and Cluster name in the query.
3. This is the format that the UI will display. Must be one of `Bytes, CPU, Time, RPS or Plain`.

These metrics will be shown *in addition* to the default metrics mentioned above.

### Replacing existing metrics

Edit your `values.yaml` to include:

```yaml title="values.yaml"
prometheus:
  metrics:
    - name: "Foobar usage" # (1)!
      metric: | # (2)!
        sum by (namespace) (container_foobar_usage{namespace="{namespace}", foobar_source~="*.-{cluster}|{cluster}-.+"})
      format: Plain # (3)!
```

1. This is the name of the metric that will be shown to users.
2. This is the actual query that will be sent to Prometheus. `{namespace}` and `{cluster}` are template values that will be replaced with the Kubernetes namespace (tenant) and Cluster name in the query.
3. This is the format that the UI will display. Must be one of `Bytes, CPU, Time, RPS or Plain`.

These metrics will *replace* the default metrics mentioned at the top of this page.

## Connecting to Prometheus

### TLS

If your Prometheus instance requires TLS (i.e. the URL starts with `https://`), you will need to [configure the VKP to trust the Certificate Authority](/operator-guide/configuration/custom-ca/) that your Prometheus uses.

### Authentication

If your Prometheus instance requires authentication (e.g. in OpenShift), you will need to configure the Prometheus URL.
VKP currently only supports Basic authentication through the Prometheus URL:

```yaml title="values.yaml"
prometheus:
  url: https://username:password@thanos-querier.openshift-monitoring.svc.cluster.local:9091
```

For maximum security, you can embed environment variables within the URL string:

```yaml title="values.yaml"
prometheus:
  url: https://$PROMETHEUS_USERNAME:$PROMETHEUS_PASSWORD@thanos-querier.openshift-monitoring.svc.cluster.local:9091
api:
  # pull credentials from a secret rather than hardcoding them
  # in your values.yaml
  extraEnv:
    - name: PROMETHEUS_USERNAME
      valueFrom:
        secretKeyRef: # (1)!
          name: prometheus-creds
          key: username
    - name: PROMETHEUS_PASSWORD
      valueFrom:
        secretKeyRef:
          name: prometheus-creds
          key: password
```

1. [https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets-as-environment-variables](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets-as-environment-variables)
