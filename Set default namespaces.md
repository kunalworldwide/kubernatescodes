You can divide a Kubernetes cluster into multiple environments by using namespaces (e.g., Dev1, Dev2, QA1, QA2, etc.), and each environment can be managed by a different user. One of the inconveniences of writing kubectl commands is that every time you write a command, you need the --namespace option at the end. People often forget this and end up creating objects (pods, services, deployments) in the wrong namespace.

With this trick, you can set the namespace preference before running kubectl commands. Run the following command before executing the kubectl commands, and it will save the namespace for all subsequent kubectl commands for your current context:

**

## kubectl config set-context $(kubectl config current-context) --namespace=mynamespace

**

Some of the most common and useful commands with namespace are listed below:

kubectl get namespaces

kubectl create namespace mynamespace

kubectl get pods --all-namespaces (List all pods with status from all namespaces.)

kubectl get po -o wide -n <namspace1> -n <namespace2> -n <namespace3> (This command will identify the pods in each namespace)

kubectl describe namespace <namespace>

kubectl config view --minify | grep namespace (This command will ensure that you set the namespace correctly for your current context.)
