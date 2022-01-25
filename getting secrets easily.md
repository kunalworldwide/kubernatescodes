Why secrets as files is the best method 
Although it is a common practice to use secrets as environment variables, it is more secure to
mount secrets as files. Kubernetes treats secrets as environment variables securely, but the
container runtime doesn't treat them securely. To verify this, you can run the following
commands to see the secret in plain text in the Docker runtime: 
Start by getting the node that the pod using environment variables from the earlier
example is running on with the following command:
kubectl describe pod secret-using-env | grep Node 
This should show you the instance ID, as seen in Figure 10.12: 
￼
Figure 10.12: Getting the instance IDNext, get the Docker ID of the running pod:
kubectl describe pod secret-using-env | grep 'Container ID' 
This should give you the container ID: 
￼
Figure 10.13: Getting the Docker ID 
Finally, you will execute a command on the node running your container to show the
secret that was passed as an environment variable. First, let's set a couple of variables
you'll use later:
INSTANCE=<provide instance number> 
DOCKERID=<provide Docker ID> 
VMSS=$(az vmss list --query '[].name' -o tsv) 
RGNAME=$(az vmss list --query '[].resourceGroup' -o tsv) 
Note 
The previous command assumes you have a single AKS cluster with one node pool in your
subscription. If this is not the case, please change the values of VMSS and RGNAME to the
name of the value of the scale set and resource group running your cluster. 
Depending on your node version, you will run either of the following commands. For
clusters running on Kubernetes version 1.18.x or earlier, run the following command:
az vmss run-command invoke -g $RGNAME -n $VMSS --command-id \ 
RunShellScript --instance-id $INSTANCE --scripts \ 
"docker inspect -f '{{ .Config.Env }}' $DOCKERID" \ 
-o yaml | grep SECRET 
This should return an output similar to Figure 10.14: 
￼ 
Figure 10.14: The Secrets are decoded in the Docker runtime 
For clusters running version 1.19 or later, run the following command:az vmss run-command invoke -g $RGNAME -n $VMSS --command-id \ 
RunShellScript --instance-id $INSTANCE --scripts \ 
"crictl inspect --output yaml $DOCKERID" \ 
-o yaml | grep SECRET 
This will show you an output similar to Figure 10.15: 
￼ 
