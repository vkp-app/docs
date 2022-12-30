# Getting setup

1. Install Minikube by following the [Minikube installation guide](https://minikube.sigs.k8s.io/docs/start/). If you're feeling up to it, you could use another tool (e.g. `k3d`, `kind`) but we recommend Minikube.
2. Create a Minikube cluster
	```bash
 	# we recommend the docker driver so YMMV with others
    minikube start --driver=docker
    ```
3. Clone the repository
    ```bash
	git clone https://github.com/vkp-app/vkp.git
    ```
4. Install `clusterctl`
    ```bash
    curl -L https://github.com/kubernetes-sigs/cluster-api/releases/download/v1.2.5/clusterctl-linux-amd64 -o /tmp/clusterctl
    install /tmp/clusterctl ~/.local/bin/clusterctl
    ```
5. Deploy dependencies
    ```bash
	cd /path/to/vkp/
    ./firstrun.sh
    ```
6. Deploy components in the following order
    ```bash
    cd apiserver/
	skaffold run
    cd ../operator/
    skaffold run
    cd ../web/
    skaffold run
    cd ../web-login/
    skaffold run
    cd ../metrics-proxy/
    skaffold run
    ```

You should now have an instance of the VKP running in your Minikube.
