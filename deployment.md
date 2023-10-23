kubectl create deployment hello-minikube1 --image=kicbase/echo-server:1.0

kubectl expose deployment hello-minikube1 --type=LoadBalancer --port=8080

```
minikube addons list
```

Start the cluster in debug mode:
minikube start --vm-driver=hyperkit v-7 --alsologtostderr

---

**CRUD commands:**
Create: kubectl create deployment [name]
Edit: kubectl edit deployment [edit]
Delete: kubectl delete deployment [name]

**Status of different K8s components:**
kubectl get nodes | pod | services | replicaset | deployment

**Debugging pods:**
Log to console: kubectl logs [podName]
Get Interactive Terminal: kubectl exec -it [podName] -- bin/bash

---

Pod is the smallest unit but, you are creating Deployment - abstraction over Pods
Replicaset is managing the replicas of a Pod
kubectl create deployment nginx-depl --image=nginx
kubectl create deployment mongo-depl --image=mongo
kubectl apply -f nginx-deployment.yaml
vim nginx-deployment.yaml

K8s knows when to create or update deployment

**Layers of Abstraction:**
Deployment manages a

kubectl CLI - for configuring the Minikube cluster
minikube CLI - for start up/deleting the cluster


---

Main types of controllers:
- Replicaset
- Deployment
- Daemonset
- Statefulset
- Job
- Cronjob

A Replicaset ensures that a specified number of Pods is running at any point in time. If you define that there should be five Pods running, the Replicaset will make sure that this happens. If there are any excess Pods, they get deleted and vice versa. This means that new Pods are launched when there’s a failure, deletion, or termination that results in a lower number of Pods than what has been declared in the Replicaset. In most cases, you will define the Replicaset in a Deployment.

---

A Deployment controller is used to run a Pod at a desired number of replicas. These Pods have no unique identities. The Deployment can specify the configuration over a standalone Pod and a Replicaset, such as what deployment strategy to use. For example, if you are upgrading an application from v1 to v2, you might consider one of the following approaches:

- Upgrade with zero downtime
- Upgrade sequentially one after the other
- Pause and resume upgrade process
- Rollback upgrade to the previous stable release

Regardless of the approach, a Deployment will help achieve this. Instead of having to create three manifest files to declare the Pod definition, the number of Pod replicas that are needed, and the preferred upgrade strategy, you can use one Deployment manifest file.

---

A Daemonset ensures that all or some nodes inside the cluster run a copy of a Pod. It is the right controller for the job of deploying one Pod per node. Once you submit the Daemonset spec (or manifest file) to the API server, then you only have one Pod scheduled on each node. Daemonsets are also used for deploying one Pod per subset of nodes. First, you need to tag the nodes with labels and use those labels inside the Daemonset manifest file. However, nodes are always added and removed from the cluster. Let’s assume a node is being added to the cluster. The Daemonset controller on the Master node monitors that node as added to the cluster, then deploys the Pod on a newly created node. When the node is deleted, the Pod is also deleted and garbage collected.

To delete the Daemonset Pods from every node where it is deployed, all you need to do is delete the Daemonset that is responsible for the deployments.

---

Deployment controllers are suitable for managing stateless applications. Statefulsets, on the other hand, are useful when running workloads that require persistent storage. They keep unique identities for each Pod they manage and use the same identity when Pods need to be rescheduled. If you are running database management systems as part of your workload, Statefulsets would be a good choice to maintain the state of the Pods as well as to manage through different life cycle events, such as scaling and updates.

---

A Kubernetes Job is a controller that supervises Pods for carrying out certain tasks. They are primarily used for batch processing. As soon as you submit a Job manifest file to the API server, the Pod will kick in and execute a task. When the task is completed, it will shut down by itself. So this is referred to as a Run-to-completion Job. Kubernetes will not automatically delete these Pods; it must be done manually.

When you submit a Job manifest file to the Kubernetes API server, it will create one or more Pods depending on the config in the manifest file. Once a Job is scheduled, the controller on the Master node will wait for the Pod to be successfully terminated. This means containers will return an exit code 0. Then the controller will take the Pod off the radar. So when the Job completes, no more Pods are created, but Pods are not deleted either. Keeping them allows you to view the logs of completed Pods to check for errors, warnings, or other diagnostic output. The Job object also remains after it is completed so that you can view its status. When you delete the Job using kubectl, all the Pods associated with that Job will be deleted.

There are some Jobs with a long-running durable execution. Before the execution completes, there is a chance that the Pod may fail. In that case, the Job controller restarts or reschedules if a Pod or node fails during execution onto another healthy node.

---

A Cronjob controller doesn’t differ much from the Job controller. The main difference is that it runs based on a user-defined schedule. The schedule is detailed using cron syntax, and the Cronjob controller will manage the automatic creation of a Job based on the schedule. In addition, you can specify the number of Jobs to run concurrently, as well as the number of succeeded and failed Jobs to retain for logging and debugging purposes.

---

One of the things that makes Kubernetes so powerful is its extensibility. Software developers can create custom controllers to extend existing Kubernetes functionality to achieve some desired behavior. For example, if your application in a Kubernetes environment requires secrets stored in an external management system like AWS Secrets Manager, a custom controller that manages this process will be useful. In fact, there is a custom controller that does this very job called the kubernetes-external-secrets. It extends the Kubernetes API by adding a Custom Resource Definition object and a custom controller to activate the object’s behavior. Custom controllers enhance your Kubernetes platform and add new desired features.