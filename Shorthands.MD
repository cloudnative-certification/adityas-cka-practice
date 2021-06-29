# kubectl command shortcuts

All this needs to be part of ~/.bashrc file. 

| Command                              | Shortcut    | Comment  |  Add in ~/.bashrc                              |
| ------------------------------------ | ----------- | -------- | ---------------------------------------------- |
| kubectl                              | k           | alias    | echo 'alias k=kubectl' >> ~/.bashrc 
| kubectl config                       | kcon        | alias    | echo 'alias kcon="kubectl config"' >> ~/.bashrc                |
| kubectl config set-context --current | kconse      | alias    | echo 'alias kconse="kubectl config set-context --current"' >> ~/.bashrc |
| kubectl config get-contexts          | kconge      | alias    | echo 'alias kconge="kubectl config get-contexts"' >> ~/.bashrc |
| kubectl get pods                     | kgp         | alias    | echo 'alias kgp="kubectl get pods" ' >> ~/.bashrc |
| kubectl get nodes                    | kgn         | alias    | echo 'alias kgn="kubectl get nodes" ' >> ~/.bashrc |
| kubectl get deployments              | kgd         | alias    | echo 'alias kgd="kubectl get deployments"' >> ~/.bashrc |
| kubectl get services                 | kgs         | alias    | echo 'alias kgs="kubectl get services"' >> ~/.bashrc |
| kubectl describe pods                | kdp         | alias    | echo 'alias kdp="kubectl describe pods"' >> ~/.bashrc |
| kubectl describe deployments         | kdd         | alias    | echo 'alias kdd="kubectl describe deployments"' >> ~/.bashrc |
| kubectl describe services            | kds         | alias    | echo 'alias kds="kubectl describe services"' >> ~/.bashrc |
| kubectl describe nodes               | kdn         | alias    | echo 'alias kdn="kubectl describe nodes"' >> ~/.bashrc |
|  --dry-run=client -o yaml            | do          | variable | echo 'export do="--dry-run=client -o yaml" ' >> ~/.bashrc |
| kube-system                          | ks          | variable | echo 'export ks=kube-system' >> ~/.bashrc |
| kubectl get pods -n kube-system      | kgpks       | alias    | echo 'alias kgpks="kubectl get pods -n kube-system"' >> ~/.bashrc |
| kubectl get deploy -n kube-system    | kgdks       | alias    | echo 'alias kgdks="kubectl get deploy -n kube-system"' >> ~/.bashrc |
| kubectl apply -f                     | ka          | alias    | echo 'alias ka="kubectl apply -f "' >> ~/.bashrc |
| kubectl create                       | kc          | alias    | echo 'alias kc="kubectl create "' >> ~/.bashrc |
| kubectl get                          | kg          | alias    | echo 'alias kg="kubectl get "' >> ~/.bashrc |
| kubectl run                          | kr          | alias    | echo 'alias kr="kubectl run "' >> ~/.bashrc |
