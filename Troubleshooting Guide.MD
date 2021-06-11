# Kubernetes troubleshooting guide

## Troubleshoot Cluster
* Steps to know the issue
  * Look at logs
    /var/log/kube-apiserver.log - API Server, responsible for serving the API
    /var/log/kube-scheduler.log - Scheduler, responsible for making scheduling decisions
    /var/log/kube-controller-manager.log - Controller that manages replication controllers
    /var/log/kubelet.log - Kubelet, responsible for running containers on the node
    /var/log/kube-proxy.log - Kube Proxy, responsible for service load balancing
    
* Root causes:
  * VM(s) shutdown
  * Network partition within cluster, or between cluster and users
  * Crashes in Kubernetes software
  * Data loss or unavailability of persistent storage (e.g. GCE PD or AWS EBS volume)
  * Operator error, for example misconfigured Kubernetes software or application software
  
* Some solutions

  * restart kubelet
  * verify service logs using journalctl
  
* Specific scenarios:
    * Apiserver VM shutdown or apiserver crashing
        Results
            unable to stop, update, or start new pods, services, replication controller
            existing pods and services should continue to work normally, unless they depend on the Kubernetes API
    * Apiserver backing storage lost
        Results
            apiserver should fail to come up
            kubelets will not be able to reach it but will continue to run the same pods and provide the same service proxying
            manual recovery or recreation of apiserver state necessary before apiserver is restarted
            Supporting services (node controller, replication controller manager, scheduler, etc) VM shutdown or crashes
            currently those are colocated with the apiserver, and their unavailability has similar consequences as apiserver
            in future, these will be replicated as well and may not be co-located
            they do not have their own persistent state
   *  Individual node (VM or physical machine) shuts down
        Results
            pods on that Node stop running
    * Network partition
        Results
            partition A thinks the nodes in partition B are down; partition B thinks the apiserver is down. (Assuming the master VM ends up in partition A.
    * Kubelet software fault
        Results
            crashing kubelet cannot start new pods on the node
            kubelet might delete the pods or not
            node marked unhealthy
    replication controllers start new pods elsewhere
    * Cluster operator error
        Results
            loss of pods, services, etc
            lost of apiserver backing store
            users unable to read API
            etc.

## Troubleshoot Pods
* Pod status *Pending*
   
   If a Pod is stuck in Pending it means that it can not be scheduled onto a node. Generally this is because there are insufficient resources of one type or another that prevent scheduling. Look at the output of the kubectl describe ... command above. There should be messages from the scheduler about why it can not schedule your pod. Reasons include:

   You don't have enough resources: You may have exhausted the supply of CPU or Memory in your cluster, in this case you need to delete Pods, adjust resource requests, or add new nodes to your cluster. See Compute Resources document for more information.

   You are using hostPort: When you bind a Pod to a hostPort there are a limited number of places that pod can be scheduled. In most cases, hostPort is unnecessary, try using a Service object to expose your Pod. If you do require hostPort then you can only schedule as many Pods as there are nodes in your Kubernetes cluster.
   
   Verify kube-scheduler (Pod is pending ) and kube-controller(Pod not scaling) pods . 
   
* Pod status *Waiting*

   If a Pod is stuck in the Waiting state, then it has been scheduled to a worker node, but it can't run on that machine. Again, the information from kubectl describe ... should be informative. The most common cause of Waiting pods is a failure to pull the image. There are three things to check:

   Make sure that you have the name of the image correct.
   Have you pushed the image to the repository?
   Run a manual docker pull <image> on your machine to see if the image can be pulled.

* Pod status *Running*
   * Use logs       - kubectl logs pod-name [-c <container>]
   * Use exec       - kubectl exec -it pod-name -- command
   * Use debug      - kubectl debug -it ephemeral-demo --image=busybox --target=ephemeral-demo
   * Use debug copy - kubectl debug myapp -it --image=ubuntu --share-processes --copy-to=myapp-debug
   * Use describe 
   * Use describe rc <replication-controller>

## Troubleshoot Network

* Troubleshoot Network 
  
  * Verify network plugin is installed
  * Verify network policies [kubectl get netpol]
  * Verify kube-dns is working fine [kubectl -n kube-system get ep kube-dns, kubectl -n kube-system describe svc kube-dns]
  
  
* Troubleshoot Services

   * Verify endpoints     - kubectl get endpoints ${SERVICE_NAME} [number of endpoints must match the number of replicas]
   * Verify service pod-selectors labels matches the labels of Pods/Deployments
   * Verify service's port matches with Pod/Container Port
   * Verify service could be available by dnslookup [servicenames.default.svc.cluster.local]
   * Verify name resolution in /etc/resolv.conf
   * Verify if underlying pods are working
   * Verify if kube-proxy is working [ps auxw | grep kube-proxy] [Get logs from /var/log/kube-proxy.log]
   * Verify kubelet [ps auxw | grep kubelet]
