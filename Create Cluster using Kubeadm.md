## Create Cluster using Kubeadm
<details>
  <summary>Click to expand!</summary>
  
 * **Create VMs using VirtualBox**
  
  One VM will act as Master/Control plane. Their names would be updated /etc/hosts accordingly. Kubeadm, Kubelet and Docker needs to be installed in all VMs. Kubectl needs to be installed in master node.
  One VM could be created first. Then docker, kubeadm, kubectl and kubelet would be installed their. After that the vm clould be cloned. One Vm would be named as *master* and others as *node01/2/3*
 * **Install  kubeadm, kubelet, kubectl and Docker**
   * Docker Install
 ```sh
 mkdir -p /etc/apt/trusted.gpd.d
 touch /etc/apt/trusted.gpd.d/docker.gpg
 
`Install Docker Repository`

 sudo apt-get update && sudo apt-get install -y \
 apt-transport-https ca-certificates curl software-properties-common gnupg2

`Install Repository Key`
 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key --keyring /etc/apt/trusted.gpg.d/docker.gpg add -
 
`Install the apt Repository`
 sudo add-apt-repository \
 "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
 $(lsb_release -cs) \
 Stable"
 
`Install the Docker Container Engine`
 sudo apt-get update && sudo apt-get install -y \
 containerd.io=1.2.13-2 \
 docker-ce=5:19.03.11~3-0~ubuntu-$(lsb_release -cs) \
 docker-ce-cli=5:19.03.11~3-0~ubuntu-$(lsb_release -cs)
 
`Set up Docker Daemon`
 cat <<EOF | sudo tee /etc/docker/daemon.json
 {
 "exec-opts": ["native.cgroupdriver=systemd"],
 "log-driver": "json-file",
 "log-opts": {
 "max-size": "100m"
 },
 "storage-driver": "overlay2"
 }
 EOF

 sudo mkdir -p /etc/systemd/system/docker.service.d
 sudo systemctl daemon-reload
 sudo systemctl restart docker
 sudo systemctl enable docker

 echo ‘alias k=kubectl’ >> ~/.bashrc
 echo ‘swapoff -a’ >> ~/.bashrc
```
  
  * **Setting up tools**

```sh
 sudo su [Enter the root password]

 `iptable set up`
 cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
 net.bridge.bridge-nf-call-ip6tables = 1
 net.bridge.bridge-nf-call-iptables = 1
 EOF

 sudo sysctl –system

`Setting up the tools`
 sudo apt-get update && sudo apt-get install -y apt-transport-https curl
 
 curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
 cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
 deb https://apt.kubernetes.io/ kubernetes-xenial main
 EOF

 sudo apt-get update

 sudo apt-get install -y kubelet kubeadm kubectl
 sudo apt-mark hold kubelet kubeadm kubectl

 `kubelet restart`

 systemctl daemon-reload
 systemctl restart kubelet

 `Install Net tools`
 apt install net-tools
```  

* **Few Directories needed by calico** 
 ```sh
 sudo su
 mkdir -p /var/lib/calico
 touch /var/lib/calico/nodename
 mkdir -p /var/run/bird
 touch /var/run/bird/bird.ctl
 ```
 
 * **Installing Kubeadm, kubectl and kubelet**
 This need to be done in all nodes.
 ```sh
 sudo su [Enter root password]

 ifconfig   [It will give you a list of ip addresses, note down the address which is of the pattern 
 192.168.x.xxx. That is the ip address of the VM]

 kubeadm config images pull
 kubeadm init –apiserver-advertise-address= --pod-network-cidr=192.168.0.0/16 [Copy the last part of the output. That would be used by worker nodes to join the master node]

 mkdir -p $HOME/.kube
 sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
 sudo chown $(id -u):$(id -g) $HOME/.kube/config

 ##Install the pod network calico
 kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
 kubectl get nodes -w [This will provide details about the master node only]

 ## Currently no workload could be scheduled on the master node. We need to remove the taint on the master node to schedule pods on it
 kubectl taint nodes --all node-role.kubernetes.io/master-
 ```
 
 * **Worker node joins master**
 ```sh
 sudo su [Enter root password]
 
 ##Enter the kubeadm join command copied from the master node vm which would of below pattern
 kubeadm join <control-plane-host>:<control-plane-port> --token <token> --discovery-token-ca-cert-hash sha256:<hash>
 ```
 
</details>
