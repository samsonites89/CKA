# Kubernetes Installation

## Installing with Kubeadm

1. run `kubeadm init` on head **cp** node
   + This command will generate a *token* and a *SHA256 Hash* value.
2. create a network for ip-per-pod criteria
   + `calico` : flat, layer 3 network. No IP encapsulation. Used in production. simple, flexible, scales well.
   + `flannel` : layer 3, IPv4 network between the nodes of a cluster. 
   + `weave-net` : typically used as an add-on for a CNI-enabled Kubernetes cluster.
   + `kube-router` : alpha stage, do-it-all network (lb, firewall,router specific for k8s) 
   + `romana` : aimed at network and security automation 
3. run `kubeadm join` on worker nodes.

### `kubeadm`

Installing with `kubeadm`, all the resources for the `control plane` node will
be located under `/etc/kubernetes/manifests`. These are `static pod`s !!

use `kubeadm init` to install `cp node`. use `join` to add  `worker nodes`

### upgrading with `kubeadm`

here's a brief walkthrough of upgrade with `kubeadm`

1. update  `kubeadm`
2. starting with `cp` node,
   1. drain node
   2. update with `kubeadm` (i.e. `kubeadm upgrade plan` , `kubeadm upgrade apply`, `kubeadm upgrade node`)
   3. update `kubelet`
   4. restart daemon and `kubelet`


