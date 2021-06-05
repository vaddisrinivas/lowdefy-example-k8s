# lowdefy-example-k8s
A set of deployment YAMLs to deploy a Lowdefy application on Kubernetes (FOR NOW WORKS WITH NODEPORT ONLY)

To do a simple deployment -
   - refer to `config-simple-deploy/README.md`  

If you want to use "git" to get your build files, you can choose either of the options -
 - git-init -> pulls build files from git repo when the container starts
   - refer to `config-git-init/README.md`  
 - git-sync -> keeps syncing build dir from git repo at regular intervals
   - refer to `config-git-sync/README.md`
  
If you want to use a persistent volume - 
 - refer to `config-by-pv/README.md`  
