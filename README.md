# MASTER

## Install Docker

https://docs.docker.com/engine/install/ubuntu/

## Configure cgroups for your Runtime

https://kubernetes.io/docs/setup/production-environment/container-runtimes/#docker

## Turn off swap

```
sudo swapoff -a
```

Disable firewall if any.

```
sudo ufw disable
```

## Install kubectl, kubeadm, kubelet

https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl

## Initialize control plane

```
sudo kubeadm init --apiserver-advertise-address=<inet_address_of_master>
```

NOTE DOWN THE kubadm join command.

If not root user:
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

If root user:
```
export KUBECONFIG=/etc/kubernetes/admin.conf
```

## Install CNI (Flannel)

```
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

Update args for `flanneld` section in kube-flannel.yaml: (My advertised address lies on eth1 network)

```
args:
- --ip-masq
- --kube-subnet-mgr
- --iface=eth1
```

```
kubectl apply -f kube-flannel.yaml
```

## Check if node is ready

```
kubectl get nodes
```

# WORKER

## Install Docker

https://docs.docker.com/engine/install/ubuntu/

## Configure cgroups for your Runtime

https://kubernetes.io/docs/setup/production-environment/container-runtimes/#docker

## Turn off swap

```
sudo swapoff -a
```

Disable firewall if any.

```
sudo ufw disable
```

## Install kubectl, kubeadm, kubelet

https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl

## Join control-plane

Run kubeadm join command which we got during control plane initialization.

```
sudo kubeadm join 192.168.34.2:6443 --token rcu1ya.o6t7ewrncg3kdsjq --discovery-token-ca-cert-hash sha256:1fd74cf081ab1947477ef1a92cdc5f5eb220f63e629ec6a9888919b77ecc0d33
```

If you lost it, then please run the following command on MASTER and you will get the proper command again:

```
kubeadm token create --print-join-command
```

# On any machine

Copy contents of `.kube/config` from control-plane node

And paste it in `.kube/config` in current machine.



# AWS (master) -> Just mentioning the difference

```
sudo kubeadm init --control-plane-endpoint=ec2-35-158-100-39.eu-central-1.compute.amazonaws.com
```

# AWS (slave) -> Just mentioning the difference

```
kubeadm join ec2-35-158-100-39.eu-central-1.compute.amazonaws.com:6443 --token e8oyka.269wjz48nq8lv9fj --discovery-token-ca-cert-hash sha256:1ea09157d3b6271fe206e73d3e73c43c10b456275beb373c545fd609813eea23
```
