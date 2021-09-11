# k8s-containerd
A Kubernetes Cluster using Vagrant, Ansible, containerd as runtime, 1 master node + 2 workers

Containerd is [adopted](https://github.com/containerd/containerd/blob/main/ADOPTERS.md) by many cloud providers and distribution of Kubernetes. [nerdctl](https://github.com/containerd/nerdctl) is a Docker-compatible CLI for containerd. Recently I found that several articles about the usage of nerdctl. Considering those factors, I think it is time to learn containerd seriously.

I used most of ansible configuration from  [containerd contrib/ansible](https://github.com/containerd/containerd/tree/main/contrib/ansible) and [my own one](https://github.com/jackliusr/calico-the-hard-way).

## Provision nodes
```bash
ssh-keygen #id_rsa and id_rsa.pub
vagrant up
```


## Setup Cluster
After nodes are provisioned, run following command  to create a cluster. Each node has two network interfaces and ip addresses and they can only communicate each other using 172.17.177.## ip addresses. Each node's IP address is added into /etc/systemd/system/kubelet.service.d/10-kubeadm.conf as "--node-ip" in KUBELET_EXTRA_ARGS. That's why --apiserver-advertise-address is used.

Nodes are provisioned with default settings which don't meet the [node requirements](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#pod-network): at least 2 GiB RAM and 2 CPUs. This will fail pre-flight checks of kubeadmin, use "--ignore-preflight-errors=all" to bypass those errors.

```bash
#ssh into controller, run at host machine
vagrant ssh controller
#run in controller
sudo kubeadm init --ignore-preflight-errors=all --apiserver-advertise-address=172.17.177.11
```

```bash
#ssh into node1 or node2,  run at host machine
vagrant ssh node1
# run at node1 and node2, your token and discovery-token-ca-cert-hash maybe are different from mine.
sudo kubeadm join 172.17.177.11:6443 --token scylxf.gak8fgwwzdssoepy \
        --discovery-token-ca-cert-hash sha256:f1ba9ecfeef1c4549256d99efa6a8302c16b84e5a1ca73d9beeba90172858e41 --ignore-preflight-errors=all
```

```bash
kubectl create deployment nginx --image=nginx --replicas=2
kubectl expose deployment nginx --port 80
kubectl port-forward svc/nginx 8080:80

#run at another shell
curl localhost:8080
```
