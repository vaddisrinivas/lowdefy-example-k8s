# lowdefy-example-k8s
A set of YAMLs to deploy a Lowdefy application on Kubernetes (FOR NOW WORKS WITH NODEPORT ONLY)

tested on ->  `minikube v1.20.0` on `Ubuntu 20.04` OS

To do a simple deployment -
   - use yamls from `config-simple-deploy` folder

If you want to use "git" to get your build files, you can choose either of the options -
 - git-init -> pulls build files from git repo when the container starts
   - use yamls from `config-git-init` folder  
 - git-sync -> keeps syncing build dir from git repo at regular intervals
   - use yamls from  `config-git-sync/README.md` folder
  
If you want to use a persistent volume - 
 - use yamls from `config-by-pv` folder

Other options will be updated soon 
____________________________________________________________
## How to use?
- clone this repo
- modify yamls as needed
- set KUBECONFIG to map to your relevant cluser/namespace
- then use the command `kubectl apply -f .`
____________________________________________________________

Regardless of which of the above configs you are using, here is a very generic explaination about the files included -

### deploy.yaml
- this file is responsible for creating a Kubernetes object called "Deployment"
- it contains `spec` (defination) for pods
- each pod `spec` defines what containers are a part of that pod, if there are any initContainers etc.
- under container section we can alter the env variables, config paths etc.
- in case of git-init/git-sync configs a volume local to pod is defined and shared between these containers.

### pv.yaml
This is the file which is used to create PersistentVolume.
`this will work only if your host has build files available in "/data/lowdefy/build/" and if your k8s is running on that node.
If you are running a multinode cluster, please modify this to local/nfs`
The storageclass tells k8s how you want to provision a PV for your cluster/namespace/usecase. It depends on your infra/cluster .
please refer [this link for more information](https://kubernetes.io/docs/concepts/storage/storage-classes/) and [this](https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/)


### svc.yaml
- While we use deploy.yaml to deploy the application, inorder to expose it to external world, 
  kubernetes allows us to either use ingress or to use NodePort.
- While serving Lowdefy from ingress there were some errors observed hence, for now, this repo only allows exposing Lowdefy from NodePort.
- `svc.yaml` makes a LoadBalancer and maps it to containerPort.
- NodePort here can be customized/altered as per your cluster

### lowdefy-gitsync-configmap.yaml
This file is available  only in git-init/git-sync folders -
- this file contains all the git related configurations which are made as a k8s ConfigMap and then loaded as env variables
- you can specify your git repository in place of the value specified agaisnt GIT_SYNC_REPO 
- you can specify the sync time/interval in the value specified against GIT_SYNC_TIMEOUT
- for a `initContainer` it is best to specify `GIT_SYNC_ONE_TIME`
- if your repo is public, no need to specify GIT_SYNC_USERNAME, you may remove that line entirely or just specify ""
- However, the password/token is mentioned as a secret.
- to customize further and to understand GIT_SYNC sidecar please refer to  [this link] (https://github.com/kubernetes/git-sync) 

### lowdefy-git-secrets.yaml
This file is available  only in git-init/git-sync folders -
- the password/token to clone git repo can be stored as a k8s secret and mounted only on the git-sync container as an env variable
