### k8_example

Had an opportunity to play around with DigitalOcean's kubernetes service. It was incredibly easy to setup a new cluster and get going. DigitalOcean had a nice "getting started" covering major topics to help jump start your new life with kubernetes. This is such an exciting and fast moving project!   

My goals were to set up a cluster, create a persistent volume, install a helm chart using a persistent volume. I really wanted to test out the service and I didn't quite finish up the helm chart with the volume. I will probably come back later to finish up using minikube or something else. I have a few other projects I would like to move on to right now. Below are the commands I used, mostly taken from the DO's "getting started."

Good reference links
* https://kubernetes.io/docs/tasks/tools/install-kubectl/
* https://www.digitalocean.com/docs/kubernetes/how-to/add-volumes/
* https://www.digitalocean.com/community/tutorials/how-to-install-software-on-kubernetes-clusters-with-the-helm-package-manager



##### install kubectl on macos
```bash
brew install kubernetes-cli
kubectl version
mkdir .kube
cd .kube/
# copy and paste config from cluster
vim kubeconfig.yaml
```
 
##### basic get info from cluster
```bash
kubectl --kubeconfig="kubeconfig.yaml" get nodes
kubectl --kubeconfig="kubeconfig.yaml" cluster-info
kubectl --kubeconfig="kubeconfig.yaml" config get-contexts
```

##### deploying workloads from DO intro
```bash
mkdir k8s
cd k8s/
kubectl --kubeconfig="kubeconfig.yaml" create -f ./deployment.yaml
kubectl --kubeconfig="kubeconfig.yaml" create -f ./cron_job.yaml
kubectl --kubeconfig="kubeconfig.yaml" create -f ./create_pod.yaml
kubectl --kubeconfig="kubeconfig.yaml" create -f ./create_replicaset.yaml
```

##### adding block storage volumes
```bash
kubectl --kubeconfig="kubeconfig.yaml" apply vol.yaml
kubectl --kubeconfig="kubeconfig.yaml" create -f vol.yaml
kubectl --kubeconfig="kubeconfig.yaml" create -f pod_vol.yaml
kubectl --kubeconfig="kubeconfig.yaml" get pv
```

##### installing helm and package
```bash
cd /install_helm
curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > install-helm.sh
chmod u+x install-helm.sh
vim install-helm.sh
./install-helm.sh
```

##### setup tiller

```bash
kubectl --kubeconfig="../kubeconfig.yaml" -n kube-system create serviceaccount tiller
kubectl --kubeconfig="../kubeconfig.yaml" create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
helm --help
helm init  --kubeconfig="../kubeconfig.yaml" --service-account tiller
kubectl --kubeconfig="../kubeconfig.yaml" get pods --namespace kube-system
```

##### clone helm github
```bash
git clone https://github.com/helm/charts.git
```

##### install dashboard helm chart
```bash
helm --kubeconfig="../kubeconfig.yaml" install stable/kubernetes-dashboard --name dashboard-demo
export POD_NAME=$(kubectl --kubeconfig="../kubeconfig.yaml" get pods -n default -l "app=kubernetes-dashboard,release=dashboard-demo" -o jsonpath="{.items[0].metadata.name}")
echo https://127.0.0.1:8443/
kubectl --kubeconfig="../kubeconfig.yaml" -n default port-forward $POD_NAME 8443:8443
```

##### install airflow helm chart
```bash
helm --kubeconfig="../kubeconfig.yaml" install --namespace "airflow" --name "airflow" stable/airflow
helm --kubeconfig="../kubeconfig.yaml" status "airflow"
kubectl --kubeconfig="../kubeconfig.yaml" get pods
kubectl --kubeconfig="../kubeconfig.yaml" get pods --namespace "airflow" -o jsonpath="{.items[0].metadata.name}"
kubectl --kubeconfig="../kubeconfig.yaml" logs dashboard-8457b4c946-pwmfr
```