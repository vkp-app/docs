# Developing the Server components

The server-side components of the VKP are written in [Go](https://go.dev/).

## Getting started

Install the latest stable version of Go by following the instructions [here](https://go.dev/dl/).
If you're using IntelliJ/GoLand you can let the IDE do it for you.

### Deploying the APIServer

```bash
cd apiserver/
skaffold run
```

### Deploying the Metrics Proxy

```bash
cd metrics-proxy/
skaffold run
```

### Deploying the Operator

!!! info
	The VCluster plugins are automatically built by the Operator.

```bash
cd operator/
skaffold run
```

### Deploying the WebLogin

```bash
cd web-login/
skaffold run
```