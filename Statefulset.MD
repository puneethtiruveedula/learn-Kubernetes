# Kubernetes StatefulSets

StatefulSets are a Kubernetes resource used for managing stateful applications that require stable network identifiers and persistent storage.They provide  the ordering and uniqueness of pod names, which is important when running stateful applications where order matters.

StatefulSets are similar to Deployments, another Kubernetes resource used for managing pods, but there are some key differences between the two. Deployments manage stateless pods that can be scaled up, down, or replaced without affecting the application’s functionality. StatefulSets, on the other hand, manage pods with unique identities and associated persistent volumes. This means that StatefulSets provide a way to manage stateful applications like databases or message queues in a way that’s similar to how you would manage them on traditional infrastructure.

### How do StatefulSets Work?

To understand how StatefulSets work, let’s look at an example. Suppose you’re running a database application as a StatefulSet on a Kubernetes cluster. The StatefulSet might consist of several pods, each with its own unique name, such as “web-0”, “web-1”, and so on. When you scale up the StatefulSet, Kubernetes will create additional pods with new names, like “web-2”, “web-3”, and so on.

The key thing to note is that each pod in the StatefulSet has a unique name that’s based on a user-defined base name and an ordinal index. This naming scheme provides stable identities for each pod, making it easier to manage the pods when scaling up, down, or replacing them.

Another important feature of StatefulSets is that they allow you to manage persistent volumes for your pods. When a StatefulSet creates a new pod, it automatically provisions a new persistent volume and attaches it to the pod. This means that data stored on the persistent volume will survive even if the pod is terminated and a new one is created.


# StatefulSet in K8s

Before deploying an application to production, we should know the underlying architecture of the application. A term used in this context is that the application is “Stateless” or “Stateful”.

Stateless applications do not keep the record of the previous state, or each request is completely new. The only thing cluster is responsible for static content hosted on it. Stateless application has no persistent storage attached to it. Different pods across the cluster can work independently.

On the other hand, examples of Stateful Applications are databases hosted on the K8s. Pods have persistent volumes(PV) attached to them, and data replication among pods has to take place periodically.

Both Stateful and Stateless applications are deployed using different components of K8s, Stateless applications are deployed using deployment component, and Stateful applications are deployed using StatefulSet component.

## Why different components for different types of applications?

Just like the deployment component Stateful set component also helps us to replicate the pods. Both components even allow us to connect storage the same way, but what is the difference then?

Deployment creates identical replica pods; pods are created in random order with random postfix hash. For example, IDs of different replicas are “**my-backend-98fxc30e”, “my-backend-78qwefg”, **etc., and if we scale down, then the random pod is picked and deleted. Replicas are chosen randomly for the handling of the request.

In StatefulSet, replicas can’t be deleted at the same time; pods can’t be randomly addressed as replica pods are not identical. In a stateful set, pods get ID in increasing order, and this ID sticks with the pod. If, in any case, one of the pod die,s the newly generated pod will be of the same ID. Examples of replicas are **“Mongo-0”**, “**Mongo-1”, “Mongo-2” … “Mongo-8”, **and if we scale down, then first the last pod is removed, which in this case is Mongo-8, then further pods are deleted.

## **Why we need StatefulSet and its features?**

Let us consider an example in which we have MongoDB running in a pod. In this, we have pre-defined that the only master can read and write to its own volume, and the other slave nodes will copy from the master’s PV and then through slave nodes, only read operations can be done. If we allow writing operations from the slave pod, then data inconsistency will occur.

Each replica will have its own PV, and continuous data synchronization between master and slaves is done So that the new read request will get the most updated data from the slave nodes. For example, if the scaling operation is applied to the pod, then all of its predecessors must be running, and once the new pod is ready, it will replicate data from the first N-1 pod. If Mongo-4 is ready, then it will synchronise data from Mongo-3.

![](https://cdn-images-1.medium.com/max/2334/1*Nbj7W39WR38_XnW680vFuw.png)

The use of this sequential ID is when any of the pod dies or restarts, the new pod can retain state and can retain role(master, slave). In StatefulSet, if pods are being deleted, they are deleted in reverse order, from{N-1..0}.

Kubernetes docker is not perfect for stateful applications. If we want to host a database in Kubernetes, we have to work for configuring the cloning and data synchronization, managing and backup. Because of these challenges, we can choose cloud-hosted database services that will handle all these challenges for us.

**Advantages of using StatefulSet in Kubernetes:**

 1. Stable network identifiers:

Each pod in a StatefulSet gets a unique and stable network identifier, which is useful for applications requiring stable hostnames or persistent network identifiers.

2. Ordered deployment and scaling:

StatefulSet manages the creation and scaling of pods in a predictable order, ensuring that each pod is only created after the previous one has been successfully deployed.

3. Persistent storage:

StatefulSet works well with PersistentVolumes and PersistentVolumeClaims, enabling stateful applications like databases to have access to persistent storage.

4. Stateful pod management:

It provides features for performing operations on individual pods, such as scaling, rolling updates, and graceful termination, ensuring data consistency and minimal downtime for stateful applications.

**Application of StatefulSets in Kubernetes :**

**StatefulSets in Kubernetes are particularly useful for managing stateful applications that require stable identities, persistent storage, ordered deployment, and graceful scaling.**

Here are some scenarios and types of applications where StatefulSets are commonly used:

 1. **Databases:** StatefulSets are commonly used to manage databases like MySQL, PostgreSQL, MongoDB, Cassandra, etc. These databases often require persistent storage and stable network identifiers for replication, scaling, and data consistency.

 2. **Messaging Systems:** Applications utilizing messaging systems such as Apache Kafka or RabbitMQ benefit from StatefulSets due to their need for persistent storage, ordered scaling, and stable network identities for message brokers.

 3. **Stateful Microservices: **Certain microservices architectures that need persistence or maintain state, like caching systems (Redis, Memcached), benefit from StatefulSets.

 4. **Applications Requiring Ordered Deployment:** When you need to deploy pods in a specific order or require each pod to maintain its identity, such as in master-slave configurations, StatefulSets are the ideal choice.

 5. **Data Processing Pipelines:** Applications that are part of a data processing pipeline, where maintaining order and state across different stages or components is crucial, can leverage StatefulSets.

 6. **Legacy Applications:** Some legacy applications that were not originally designed to run in cloud-native environments might require stable hostnames, which StatefulSets provide.

 StatefulSets will represent the set of pods with different (unique), persistent identities, and elastic hostnames (stable). It makes you assure about the ordering of scaling and deployments

StatefulSets are valuable for applications that require one or more of the following:

 1. Stable, unique network identifiers

 2. Stable, persistent storage

 3. Ordered, graceful deployment and scaling

 4. Ordered, graceful deletion and termination

If an application doesn’t require any stable identifiers or ordered deployment, deletion, or scaling, you should deploy your application with a controller such as Deployments or ReplicaSets that provides a set of stateless replicas.

**StatefulSet Components**

 1. A Headless Service

 2. A StatefulSet

 3. A PersistentVolume

![](https://cdn-images-1.medium.com/max/2000/1*QrSDTMp-x8AZJYdQNdWacw.jpeg)

Below are manifests of a Service, StatefulSet, and Persistent volume:

    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      ports:
      - port: 80
        name: web
      clusterIP: None
      selector:
        app: nginx

2

    ---
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: web
    spec:
      selector:
        matchLabels:
          app: nginx
      serviceName: "nginx"
      replicas: 3
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx
            ports:
            - containerPort: 80
              name: web
            volumeMounts:
            - name: www
              mountPath: /usr/share/nginx/html
          volumes:
          - name: www
            persistentVolumeClaim:
              claimName: myclaim

3

    ---
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: myclaim
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 8Gi

### Create and manage StatefulSets

**Create a stateful set**

    kubectl create -f statefulset.yaml
    service "nginx" created
    statefulset.apps "web" created

It will create three Pods named web-0,web-1,web-2

    kubectl get pods

    NAME      READY     STATUS    RESTARTS   AGE

    web-0     1/1       Running   0          1m

    web-1     1/1       Running   0          46s

    web-2     1/1       Running   0          18s

Check services in Kubernetes

    kubectl get svc nginx

    NAME      TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE

    nginx     ClusterIP   None         < none >      80/TCP    2m

List your stateful sets:

    kubectl get statefulsets

Get details of a stateful set:

    kubectl describe statefulset web

Edit a stateful set:

    kubectl edit statefulset web

### Scaling a stateful set:

Scaling a StatefulSet refers to increasing or decreasing the number of replicas.

**Scale-up a stateful set:**

    kubectl scale statefulset web --replicas=5 
    statefulset.apps "web" scaled

Check and get pods

    kubectl get pods -l app=nginx

    NAME      READY     STATUS    RESTARTS   AGE

    web-0     1/1       Running   0          11m

    web-1     1/1       Running   0          10m

    web-2     1/1       Running   0          10m

    web-3     1/1       Running   0          33s

    web-4     1/1       Running   0          19s

Scale down a stateful set:

    kubectl scale statefulset web --replicas=2 
    statefulset.apps "web" scaled

Check the scaling down of pods

    kubectl get pods -w -l app=nginx

    NAME      READY     STATUS        RESTARTS   AGE

    web-0     1/1       Running       0          13m

    web-1     1/1       Running       0          12m

    web-2     0/1       Terminating   0          12m

    web-3     0/1       Terminating   0          2m

    web-4     0/1       Terminating   0          1m

Check again :

    kubectl get pods -l app=nginx

    NAME      READY     STATUS    RESTARTS   AGE

    web-0     1/1       Running   0          13m

    web-1     1/1       Running   0          12m

**Delete a stateful set**

    kubectl delete statefulset web 
    statefulset.apps "web" deleted

You must delete the Service manually.

    kubectl delete service nginx 
    service "nginx" deleted

**Limitations:**

 1. StatefulSet was a beta resource before 1.9 and not available in any Kubernetes release before 1.5.

 2. The storage for a given Pod must either be provisioned by a PersistentVolume Provisioner based on the requested storage class or pre-provisioned by an admin.

 3. Deleting and/or scaling a StatefulSet down will not delete the volumes associated with the StatefulSet. This is done to ensure data safety, which is generally more valuable than an automatic purge of all related StatefulSet resources.

 4. StatefulSets currently require a Headless Service to be responsible for the network identity of the Pods. You are responsible for creating this Service.

 5. StatefulSets do not provide any guarantees on the termination of pods when a StatefulSet is deleted. To achieve ordered and graceful termination of the pods in the StatefulSet, it is possible to scale the StatefulSet down to 0 before deletion.
