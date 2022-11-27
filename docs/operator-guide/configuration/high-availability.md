# High Availability

High availability (HA) enables Clusters to continue running even if a critical Pod stops working (e.g. due to Node updates on the management cluster).

## How it works

VKP takes advantage of the HA support added to [VCluster](https://www.vcluster.com/docs/operator/high-availability) in version `0.13.0`.
VCluster requires running an external datastore, which is managed by the VKP.

As users create Clusters, the VKP will add users/databases to the PostgreSQL Cluster and automatically configure the Cluster to use PostgreSQL as its backing datastore.

## Creating the database

### Install the CrunchyData PostgreSQL Operator

Follow the instructions here: https://access.crunchydata.com/documentation/postgres-operator/v5/ (*note: you do **not** need the enterprise version.*)

An example deployment that we use for development, can be found [here](https://github.com/vkp-app/vkp/tree/main/local/pgo).

### Create a database instance:

!!! warning 
    This database instance should only be used for test deployments and is not configured for scale or appropriate backups.
    Please take a moment to familiarise yourself with all the available parameters.

```yaml
apiVersion: postgres-operator.crunchydata.com/v1beta1
kind: PostgresCluster
metadata:
  name: vkp
  namespace: vkp-system # (1)!
spec:
  image: registry.developers.crunchydata.com/crunchydata/crunchy-postgres:ubi8-14.5-1
  postgresVersion: 14
  instances:
    - name: vkp
      replicas: 2
      dataVolumeClaimSpec:
        accessModes:
          - "ReadWriteOnce"
        resources:
          requests:
            storage: 5Gi
  backups:
    pgbackrest:
      image: registry.developers.crunchydata.com/crunchydata/crunchy-pgbackrest:ubi8-2.40-1
      repos:
        - name: repo1
          volume:
            volumeClaimSpec:
              accessModes:
                - "ReadWriteOnce"
              resources:
                requests:
                  storage: 5Gi
  proxy:
    pgBouncer:
      image: registry.developers.crunchydata.com/crunchydata/crunchy-pgbouncer:ubi8-1.17-1
      replicas: 1
```

1. Putting the database in the `vkp-system` namespace is optional.

## Configuring the VKP

Add the following to your [`Subscription`](/operator-guide/installation/operator/#creating-the-subscription):

```yaml
spec:
  config:
    env:
      - name: CLUSTER_ALLOW_HA
        value: "true"
      - name: CLUSTER_POSTGRES_RESOURCE_NAME
        value: vkp
      - name: CLUSTER_POSTGRES_RESOURCE_NAMESPACE
        value: vkp-system
```

Add the following to your [`values.yaml`](/operator-guide/installation/control-plane/#values):

```yaml
web:
  extraEnv:
    - name: FF_ALLOW_HA
      value: "true"
```