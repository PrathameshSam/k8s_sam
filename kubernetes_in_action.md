## Kubernetes in Action

## Notes

Presentation version of this content is available at https://slashpai.github.io/presentations/kubernetes_in_action/#/

---

## Let's get to know kubernetes more by executing cli commands

---

## kubectl

- The Kubernetes command-line tool, kubectl, allows you to run commands against Kubernetes clusters

- You can use kubectl to deploy applications, inspect and manage cluster resources, and view logs etc

---

## Install kubectl

https://kubernetes.io/docs/tasks/tools/install-kubectl/

---

## Verify Installation

```go
kubectl version
```
---

## Minikube

---
## Minikube

- minikube quickly sets up a local Kubernetes cluster on macOS, Linux, and Windows

---

Install: https://minikube.sigs.k8s.io/docs/start/

---

## Create minikube cluster

---

## Drivers

**Linux**

* Docker - container-based (preferred)
* KVM2 - VM-based (preferred)
* VirtualBox - VM
* None - bare-metal
* Podman - container (experimental)

**Windows**

* Hyper-V - VM (preferred)
* Docker - VM + Container (preferred)
* VirtualBox - VM

Drivers: https://minikube.sigs.k8s.io/docs/drivers/

---

## Docker as driver

_This should be default one_

```go
minikube start
```

or

```go
minikube start --driver=docker
```

---

## Let's review the cluster created with kubectl commands

---

### Kubectl context and configuration

- Set which Kubernetes cluster kubectl communicates with and modifies configuration information

---

| Command | Description | Example|
| --- | ----------- | ---------- |
|kubectl config view | Show Merged kubeconfig settings | kubectl config view |
|kubectl config get-contexts | display list of contexts| kubectl config get-contexts|
|kubectl config current-context|display the current-context| kubectl config current-context|

---

| Command | Description | Example|
| --- | ----------- | ---------- |
|kubectl config use-context my-cluster-name|set the default context to my-cluster-name| kubectl config use-context minikube|
---

## Viewing, Finding objects, resources

---

*Specify -n \<namespace> if object is not in default namespace*

| Command | Description | Example|
| --- | ----------- | ---------- |
|kubectl get pods/po/pod| List all pods in default namespace|kubectl get pods |
|kubectl get pods -A | List all pods in all namespaces| kubectl get pods -A |
|kubectl get pods --show-labels| Show labels for all pods|kubectl get pods --show-labels|

---

| Command | Description | Example|
| --- | ----------- | ---------- |
|kubectl describe | Describe object| kubectl describe pod \<pod name> |
|kubectl get deploy/deployments|List deployments| kubectl get deploy|
|kubectl get rs|List replicaset| kubectl get rs|
|kubectl get nodes/no| List all nodes in cluster|kubectl get nodes |

---
| Command | Description | Example|
| --- | ----------- | ---------- |
|kubectl get all -A| List all objects in all namespaces | kubectl get all -A |
---

## Kubernetes manifest file main components for most objects

---

```yaml
apiVersion
kind
metadata
    labels:
    name:
spec
```

---

## Creating objects

---

| Command | Description | Example|
| --- | ----------- | ---------- |
|kubectl run| Create and run a particular image in a pod | kubectl run nginx --image nginx|
| kubectl create | Create a resource from a file or from stdin| kubectl create deployment nginx-deploy --image=nginx |

---

| Command | Description | Example|
| --- | ----------- | ---------- |
| kubectl apply | Apply a configuration to a resource by filename or stdin| kubectl apply -f <manifest.yaml/manifest.json/folder path>
---

## Create Pods

---

### Dry run

```go
kubectl apply -f https://raw.githubusercontent.com/slashpai/docker_k8s_training/main/kubernetes/manifests/redis-pod.yaml --dry-run=client
```

or

```go
kubectl apply -f redis-pod.yaml --dry-run=client
```

### Actual run

```go
kubectl apply -f https://raw.githubusercontent.com/slashpai/docker_k8s_training/main/kubernetes/manifests/redis-pod.yaml
```

or

```go
kubectl apply -f redis-pod.yaml
```

---

## Creating Deployments

---

### Dry run

```go
kubectl apply -f https://raw.githubusercontent.com/slashpai/docker_k8s_training/main/kubernetes/manifests/redis-deploy.yaml --dry-run=client
```

or

```go
kubectl apply -f redis-deploy.yaml --dry-run=client
```

### Actual run

```go
kubectl apply -f https://raw.githubusercontent.com/slashpai/docker_k8s_training/main/kubernetes/manifests/redis-deploy.yaml
```

or

```go
kubectl apply -f redis-deploy.yaml
```

---

## Understanding the manifest files

---

## Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis
  labels:
    app: redis
spec:
  containers:
  - name: redis
    image: redis   
```

---

## Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: redis
  name: redis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - image: redis
        name: redis
```
---

## Interacting with running Pods

| Command | Description | Example|
| --- | ----------- |---------- |
| kubectl exec | Run a command in a running container| kubectl exec -it nginx -- bash |
| kubectl logs | Fetch the logs of a pod | kubectl logs \<pod name> |

---

## Scaling resources

| Command | Description | Example|
| --- | ----------- | ---------- |
|kubectl scale | Scale resources | kubectl scale deployment redis --replicas=2|

*Note:* This won't be updating manifest file so if you delete and create resource it will create whatever replicas mentioned in file

---

## Specifying resource constraints for containers in pods

---

```yaml
apiVersion: v1s
kind: Pod
metadata:
  name: counter
spec:
  containers:
  - name: count
    image: busybox
    args: [/bin/sh, -c,
            'i=0; while true; do echo "$i: $(date)"; i=$((i+1)); sleep 1; done']
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

---

```go
kubectl apply -f https://raw.githubusercontent.com/slashpai/docker_k8s_training/main/kubernetes/manifests/busy-box-pod.yaml
```

```go
kubectl get pods
```

---


## Deleting Resources

| Command | Description | Example|
| --- | ----------- | ---------- |
|kubectl delete | Delete resources | kubectl delete deployment redis|
|kubectl delete | Delete resources | kubectl delete -f \<manifest file used to create resource>|


---

## Creating manifest file quickly

```go
kubectl create deployment nginx-deployment --image nginx --dry-run=client -o yaml > nginx-deploy.yaml
```

---

## Command Help

```go
kubectl [command] --help
```
---

kubectl cheatsheet: https://kubernetes.io/docs/reference/kubectl/cheatsheet/

---

kubectl book: https://kubectl.docs.kubernetes.io/

---

## Deploying a custom application

---

## simple-webapp:v1

* This image was created during docker session
* To verify if image present on system

    ```go
    docker images
    ```

---

For those who don't have this image

From git bash/linux terminal

```bash
git clone https://github.com/slashpai/docker_k8s_training.git
cd docker_k8s_training/docker_apps/simple_webapp
docker build . -t simple-webapp:v1
```
---

## Create deployment from this image

```go
kubectl create deployment simple-webapp --image simple-webapp:v1
```

```go
kubectl get deployments
```

```go
kubectl get pods
```

```go
kubectl describe deploy simple-webapp
```

---

## Identified reason for failure?

---

Login to minikube node

```bash
minikube ssh
```

```go
docker images
```

Type `exit` to quit from minikube

---

### Pushing directly to the in-cluster Docker daemon

- When using a container or VM driver (all drivers except none), you can reuse the Docker daemon inside minikube cluster

- This means you don’t have to build on your host machine and push the image into a docker registry

- You can just build inside the same docker daemon as minikube which speeds up local experiments

---

* To point your terminal to use the docker daemon inside minikube run this:

```bash
eval $(minikube docker-env)
```

---

* Now any ‘docker’ command you run in this current terminal will run against the docker inside minikube cluster

* If you do the following commands, it will show you the containers inside the minikube, inside minikube’s VM or Container

```go
docker ps
```
---

* Now you can ‘build’ against the docker inside minikube. which is instantly accessible to kubernetes cluster

```go
docker build . -t simple-webapp:v1
```
---

## Verify pod status now

```go
kubectl get pods
```
---

## Another way without using minikube docker daemon

```go
minikube cache add simple-webapp:v1
```

https://minikube.sigs.k8s.io/docs/commands/cache/

---

https://minikube.sigs.k8s.io/docs/handbook/pushing/

---

## Expose the application to outside cluster

_You are creating a service here with below command_

```go
kubectl expose deployment simple-webapp --type=NodePort --port=4567
```

```go
kubectl get services/svc
```

```go
minikube service simple-webapp
```

---

## Update code and build new image

---

- Try updating the simple web app (index.erb in views folder)
- Build new image use a different tag

    ```go
    docker build . -t simple-webapp:v2
    ```

- Try setting new image for deployment

    ```go
    kubectl set image deploy simple-webapp simple-webapp=simple-webapp:v2
    ```

- Run and verify change

```go
minikube service simple-webapp
```

---

## Using env variables

---

https://github.com/slashpai/docker_k8s_training/blob/main/kubernetes/manifests/simple-webapp-env.yml

```go
kubectl apply -f https://raw.githubusercontent.com/slashpai/docker_k8s_training/main/kubernetes/manifests/simple-webapp-env.yml
kubectl expose deploy simple-webapp-env --type NodePort --port 4567
minikube service simple-webapp-env
```

---

## Another way to create service

```go
kubectl create service nodeport simple-webapp --tcp 4567:4567 --dry-run=client -o yaml
```

---

## Using config maps to pass env variables

---

## Creating config map

```go
kubectl create configmap simple-webapp-cm --from-literal APP_COLOR=blue
```

```go
kubectl get configmap simple-webapp-cm
```

```go
kubectl get configmap simple-webapp-cm -o yaml
```

---

https://github.com/slashpai/docker_k8s_training/blob/main/kubernetes/manifests/simple-webapp-configmap.yml

```go
kubectl apply -f https://raw.githubusercontent.com/slashpai/docker_k8s_training/main/kubernetes/manifests/simple-webapp-configmap.yml
kubectl expose deploy simple-webapp-cm --type NodePort --port 4567
minikube service simple-webapp-cm
```
---

## Addons

Add-ons extend the functionality of Kubernetes.

https://kubernetes.io/docs/concepts/cluster-administration/addons/

---

## minikube addons

- The minikube tool includes a set of built-in addons that can be enabled, disabled and opened in the local Kubernetes environment

```go
minikube addons list
```

---

### Access the Kubernetes dashboard running within the minikube cluster

```go
mknikube dashboard
```

---

## Metrics server

```go
minikube addons enable metrics-server
```

Wait some time till metrics pods are functional

```go
kubectl top nodes
kubectl top pods
```

---

https://minikube.sigs.k8s.io/docs/commands/

---

More details: https://minikube.sigs.k8s.io/docs/

---

## Kubernetes Object Management

* Imperative vs Declarative method

* kubectl create vs kubectl apply

---

## Imperative commands

- When using imperative commands, a user operates directly on live objects in a cluster

- The user provides operations to the kubectl command as arguments or flags

- Simplest way to get started or to run a one-off task in a cluster

- Because this technique operates directly on live objects, it provides no history of previous configurations

---

## Example

kubectl create deployment nginx-deploy --image nginx

---

### Advantages compared to object configuration:

- Commands are simple, easy to learn and easy to remember
- Commands require only a single step to make changes to the cluster

---

### Disadvantages compared to object configuration:

- Commands do not integrate with change review processes
- Commands do not provide an audit trail associated with changes
- Commands do not provide a source of records except for what is live
- Commands do not provide a template for creating new objects

---

## Imperative object configuration

-  In imperative object configuration, the kubectl command specifies the operation (create, replace, etc.), optional flags and at least one file name
-  The file specified must contain a full definition of the object in YAML or JSON format

---

## Examples

Create the objects defined in a configuration file:

```go
kubectl create -f nginx.yaml
```

---

### Advantages compared to imperative commands:

- Object configuration can be stored in a source control system such as Git
-  Object configuration can integrate with processes such as reviewing changes before push and audit trails
-  Object configuration provides a template for creating new objects

---

### Disadvantages compared to imperative commands:

-  Object configuration requires basic understanding of the object schema
-  Object configuration requires the additional step of writing a YAML file

---

### Disadvantages compared to declarative object configuration:

- Imperative object configuration works best on files, not directories
- Updates to live objects must be reflected in configuration files, or they will be lost during the next replacement

---

## Declarative object configuration

---

-  User operates on object configuration files stored locally, however the user does not define the operations to be taken on the files

-  Create, update, and delete operations are automatically detected per-object by kubectl

- This enables working on directories, where different operations might be needed for different objects

---

## kubectl apply

- kubectl apply manages applications through files defining Kubernetes resources

- It creates and updates resources in a cluster through running kubectl apply

-  This is the recommended way of managing Kubernetes applications on production

---

## Example

* Process all object configuration files in the configs directory, and create or patch the live objects

* You can first diff to see what changes are going to be made, and then apply

```go
kubectl diff -f configs/
```

```go
kubectl apply -f configs/
```

---

### Advantages compared to imperative object configuration:

-  Changes made directly to live objects are retained, even if they are not merged back into the configuration files
-  Declarative object configuration has better support for operating on directories and automatically detecting operation types (create, patch, delete) per-object

---

### Disadvantages compared to imperative object configuration:

- Harder to debug and understand results when they are unexpected
-  Partial updates using diffs create complex merge and patch operations

---

Read More: https://kubernetes.io/docs/concepts/overview/working-with-objects/object-management/

---

## More kubectl commands handson

---

## Some fun application deployment for you

---

## Try deploying voting app

```bash
git clone https://github.com/dockersamples/example-voting-app.gits
```

https://github.com/dockersamples/example-voting-app#run-the-app-in-kubernetes

---

## Training Summary

- Why Kubernetes?

- Kubernetes Components and Architecture

- Kubernetes Objects

- Kubernetes commands with Handson

- Deploying custom application

- Minikube features and familiarization

---

## What Next?

- Best place to learn further https://kubernetes.io/docs/home/

- https://www.katacoda.com/learn?q=kubernetes&hPP=12&idx=scenarios&p=0&is_v=1

- https://github.com/kelseyhightower/kubernetes-the-hard-way

---

Credit to - [Jayapriya Pai](https://www.linkedin.com/in/jayapriyapai/)
