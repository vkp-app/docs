# ClusterAPI

ClusterAPI is a prerequisite for the VKP as it provides a number of APIs that the VKP depends on.
The VKP Operator does not install these APIs for you as they are extensive, and since we only use a subset we would not want to enforce our opinion of its usage.

For an in-depth installation guide, read the [ClusterAPI documentation](https://cluster-api.sigs.k8s.io/introduction.html).

## Quick start

```shell
clusterctl init --infrastructure vcluster
``` 

For an example of the minimal ClusterAPI installation we use for local development and dogfooding, see the [GitHub repo](https://github.com/vkp-app/vkp/tree/main/local/clusterapi).