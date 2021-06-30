# Certificates

* Check crt files
 
 > openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text
 
* Check authorization mode in cluster
 
 > Use the command kubectl describe pod kube-apiserver-controlplane -n kube-system and look for --authorization-mode

* User capability
 
 > kubectl get pods --as dev-user
 
* Image Security
 
 > k create secret docker-registry private-reg-cred --docker-email=dock_user@myprivateregistry.com --docker-username=dock_user --docker-password=dock_password --docker-server=myprivateregistry.com:5000
 > Add imagePullSecrets[].name in Pod.spec		 
 
* Identify the certificate file used for the kube-api server
 
 > Run the command cat /etc/kubernetes/manifests/kube-apiserver.yaml and look for the line --tls-cert-file
 
* Identify the Certificate file used to authenticate kube-apiserver as a client to ETCD Server
 
 > Run the command cat /etc/kubernetes/manifests/kube-apiserver.yaml and look for value of etcd-certfile flag.
 
* Identify the key used to authenticate kubeapi-server to the kubelet server
 
 > Look for kubelet-client-key option in the file /etc/kubernetes/manifests/kube-apiserver.yaml
 
* Verify crt file

> openssl x509 -in /etc/kubernetes/pki/ca.crt -text
 
