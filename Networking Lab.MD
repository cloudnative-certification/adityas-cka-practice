# Networking Lab

* Know about network plugin
  > ip a |grep -B2 <address of node 10.3.25.9>
  
* To know mac address 
  > arp <node-address>
 
* Container Runtime Bridge
  > ip link | grep -B2 docker

* To know default gateway
  > ip route show default

* What is the port the kube-scheduler is listening on in the controlplane node?
  > netstat -ntpl | grep kube-scheduler 
 
* Identify kubelet service and identify the network plugin configured for Kubernetes
  > ps -aux | grep kubelet|grep network-plugin
 
* What is the path configured with all binaries of CNI supported plugins?
  > /opt/cni/bni
 
* What is the CNI plugin configured to be used on this kubernetes cluster?
  > Run the command: ls /etc/cni/net.d/ and identify the name of the plugin
  
* What binary executable file will be run by kubelet after a container and its associated namespace are created
  > Look at the **type** field in file /etc/cni/net.d/10-flannel.conflist
  
* Pod not scheduled 
  > describe pod
  
* Install weavenet
  > kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
  
* What is the Networking Solution used by this cluster?
  > ls /etc/cni/net.d/
  
* How many weave agents/peers are deployed in this cluster?
  > kubectl get pods -n kube-system
  
* How to know the Ip address for Weave 
  > ip addr show weave 
  
* What is the range of IP addresses configured for PODs on this cluster?
  > kubectl logs <weave-pod-name> weave -n kube-system and look for ipalloc-range
  
* What is the IP Range configured for the services within the cluster?
  > Inspect the setting on kube-api server by running on command cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep cluster-ip-range
  
* What type of proxy is the kube-proxy configured to use?
  > Check the logs of the kube-proxy pods. Command: kubectl logs kube-proxy-ft6n7 -n kube-system
  
  
* Ingress sample

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-ingress
  namespace: critical-space
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /pay
        backend:
          serviceName: pay-service
          servicePort: 8282
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-controller
  namespace: ingress-space
spec:
  replicas: 1
  selector:
    matchLabels:
      name: nginx-ingress
  template:
    metadata:
      labels:
        name: nginx-ingress
    spec:
      serviceAccountName: ingress-serviceaccount
      containers:
        - name: nginx-ingress-controller
          image: quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.21.0
          args:
            - /nginx-ingress-controller
            - --configmap=$(POD_NAMESPACE)/nginx-configuration
            - --default-backend-service=app-space/default-http-backend
          env:
            - name: POD_NAME
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
            - name: POD_NAMESPACE
              valueFrom:
                fieldRef:
                  fieldPath: metadata.namespace
          ports:
            - name: http
              containerPort: 80
            - name: https
              containerPort: 443
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-wear-watch
  namespace: app-space
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
  - http:
      paths:
      - path: /wear
        backend:
          serviceName: wear-service
          servicePort: 8080
      - path: /watch
        backend:
          serviceName: video-service
          servicePort: 8080
  ```
