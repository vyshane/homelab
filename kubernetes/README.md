# Setting Up a Kubernetes Cluster on Ubuntu 16.04 via kubeadm

We'll install Kubernetes [via kubeadm](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/) and use [Calico](https://www.projectcalico.org) for pod networking.

## All Nodes

In this section we'll prepare the master and worker nodes for Kubernetes. We'll start from a newly minted Ubuntu 16.04 on each node:

### Install Docker 17.03

```sh
apt-get update
apt-get install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
add-apt-repository "deb https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") $(lsb_release -cs) stable"
apt-get update && apt-get install -y docker-ce=$(apt-cache madison docker-ce | grep 17.03 | head -1 | awk '{print $3}')
```

### Install kubeadm, kubelet and kubectl

```sh
apt-get update && apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y kubelet kubeadm kubectl
```

Turn swap off:

```sh
swapoff -a
```

## Master Node

### Configure cgroup driver used by kubelet on Master Node

Make sure that the cgroup driver used by kubelet is the same as the one used by Docker. To check whether the Docker cgroup driver matches the kubelet config:

```sh
docker info | grep -i cgroup
cat /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
```

If the Docker cgroup driver and the kubelet config don’t match, update the latter. The flag we need to change is --cgroup-driver. If it’s already set, we can update the configuration like so:

```sh
sed -i "s/cgroup-driver=systemd/cgroup-driver=cgroupfs/g" /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
```

Otherwise, open the systemd file and add the flag to an existing environment line. Then restart the kubelet:

```sh
systemctl daemon-reload
systemctl restart kubelet
```

### Initialise Kubernetes Master

Initialise the master node by running `kubeadm init`. We need to specify the pod network CIDR for network policy to work correctly when we install [Calico](https://www.projectcalico.org) in a later step.

```sh
kubeadm init --pod-network-cidr=10.0.0.0/16
```

To be able to use kubectl as non-root user on master:

```sh
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Install Calico for networking:

```sh
kubectl apply -f https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml
kubectl apply -f https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml
```

Once Calico has been installed, confirm that it is working by checking that the kube-dns pod is running before joining the worker nodes.

```sh
shane@master1:~$ kubectl get pods --all-namespaces
NAMESPACE     NAME                              READY     STATUS    RESTARTS   AGE
kube-system   calico-node-2zrrz                 2/2       Running   0          8m
kube-system   etcd-master1                      1/1       Running   0          11m
kube-system   kube-apiserver-master1            1/1       Running   0          11m
kube-system   kube-controller-manager-master1   1/1       Running   0          11m
kube-system   kube-dns-86f4d74b45-bpsgs         3/3       Running   0          12m
kube-system   kube-proxy-pkfjx                  1/1       Running   0          12m
kube-system   kube-scheduler-master1            1/1       Running   0          11m
```

## Worker Nodes

Next we'll join the worker nodes to our new Kubernetes cluster. Run the command that was output by `kubeadm init` on each of the nodes:

```sh
kubeadm join --token <token> <master-ip>:<master-port> --discovery-token-ca-cert-hash sha256:<hash>
```

We should see nodes joining the cluster shortly:

```sh
shane@master1:~$ kubectl get nodes
NAME      STATUS     ROLES     AGE       VERSION
master1   Ready      master    22m       v1.10.3
minion1   Ready      <none>    6m        v1.10.3
minion2   Ready      <none>    4m        v1.10.3
minion3   NotReady   <none>    4m        v1.10.3
minion4   NotReady   <none>    4m        v1.10.3
```

## Configure Access from Workstation

To control the cluster remotely from our workstation, we grab the contents of `/etc/kubernetes/admin.conf` from the master node and merge it into our local `~/.kube/config` configuration file.
