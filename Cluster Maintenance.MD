## Cluster Upgrade
<details>
 <summary>Click to Expand!</summary>

 **Control Plane Upgrade**
 ```sh
 kubectl drain master/controlplane --ignore-daemonsets
apt update
apt install kubeadm=1.19.0-00
kubeadm upgrade apply v1.19.0
apt-get upgrade kubelet
apt install kubelet=1.19.0-00
kubectl uncordon master/controlplane
```
**Node Upgrade**
```sh
kubectl drain node01 --ignore-daemonsets
apt update
apt install kubeadm=1.19.0-00
kubeadm upgrade node
apt install kubelet=1.19.0-00
kubectl uncordon node01
```
</details>

##  ETCD Backup
<details>
  <summary>Click to expand!</summary>
  
  ```sh
ETCDCTL_API=3 etcdctl --endpoints=https://[127.0.0.1]:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key snapshot save /opt/etcd-backup.db
  ```
  
  ```sh
ETCDCTL_API=3 etcdctl  --data-dir /var/lib/etcd-from-backup snapshot restore /opt/etcd-backup.db

Put the path in volume-mount in /etc/kubernetes/manifests/etcd.yaml
  ```
</details>
