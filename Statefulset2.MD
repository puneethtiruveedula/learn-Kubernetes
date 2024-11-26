
## Comprehensive Guide to Kubernetes StatefulSets

![](https://cdn-images-1.medium.com/max/2074/1*5oAH-UVnxwNBkW5lBHccUg.png)

## **Introduction:**

Kubernetes StatefulSets are a specialized workload API object designed for managing stateful applications. Unlike Deployments, which are suitable for stateless applications, StatefulSets are used for applications that require stable, unique network identifiers, persistent storage, and ordered, consistent deployment and scaling. StatefulSets are ideal for databases, distributed systems, and other services where each instance must be individually tracked and maintained.

![](https://cdn-images-1.medium.com/max/3840/1*oi2XGkI7OXxhixoR7LAadw.png)

**Why Use StatefulSets in Kubernetes?**

* **Stable Network Identity:** StatefulSets provide each Pod with a unique, persistent identity (hostname) that is retained across rescheduling. This is crucial for applications where each instance must be uniquely addressable, such as databases.

* **Ordered Deployment and Scaling:** Pods in a StatefulSet are created, deleted, or scaled in a strict order, ensuring that the system remains consistent during these operations. This is essential for applications that require ordered startup, shutdown, or scaling processes.

* **Persistent Storage:** StatefulSets work in conjunction with PersistentVolumeClaims (PVCs) to provide persistent storage for each Pod. Each Pod gets its own persistent storage volume, which is not shared with other Pods and is retained even if the Pod is rescheduled or restarted.

* **Graceful Rolling Updates:** StatefulSets ensure that updates are applied to Pods in a controlled manner, maintaining the stability and availability of the application during updates.

![**For high availability with three databases across three servers, ensure data consistency by using a strategy that syncs or replicates data across these databases, as storing data may not be centralized in a single database.**](https://cdn-images-1.medium.com/max/3840/1*ME9aUIaYAHtbwxkNchuzeA.png)

![**how we will make centralize and connect all that is also issue**](https://cdn-images-1.medium.com/max/3840/1*EuEfEPV2GpQ_ycKZb_UxCA.png)

* **Pod IP Change on Deletion:** When a central (master) Pod like Pod-1 is deleted and recreated, it receives a new IP address (e.g., 10.2.0.4). This change can break connections between the master Pod and other Pods (like Pod-2 and Pod-3) that rely on a stable IP for communication.

* **High Availability Challenges:** Even in a high-availability setup, where Pods are spread across different availability zones (AZs), connecting worker Pods (Pod-2 and Pod-3) to the master Pod (Pod-1) can fail if the master Pod’s IP changes. This issue can disrupt the overall functionality and reliability of the stateful application.

![](https://cdn-images-1.medium.com/max/3840/1*5tqKn7-qWHQhXITccIix1Q.png)

## Statefulsets vs Deployment:

![](https://cdn-images-1.medium.com/max/3840/1*QZY3OUwremXw-whO-uT9yA.png)

**StatefulSets**:

* Use for stateful applications requiring stable network IDs and persistent storage, **like databases.**

* It provides a static IP, so even if a Pod is deleted, the new Pod will retain the same IP address.

* It sequentially create and delete pods. **(one by one)**

**Deployment**:

* Use for stateless applications needing easy scaling and rolling updates, **like web servers.**

* A Deployment doesn’t provide a static IP for its Pods. For stable IPs, use a Service (LoadBalancer or NodePort) or a StatefulSet for consistent network identities.

* At time create and delete pods.

![**one by one created pods.**](https://cdn-images-1.medium.com/max/3840/1*VjnHfL9-Agl_oD8n0KqPjA.png)

![](https://cdn-images-1.medium.com/max/3840/1*JiObKpiiNiad4azhPSfCRA.png)

![](https://cdn-images-1.medium.com/max/3840/1*UjHkYkhMMwV4_7nZaPy1Cg.png)

## Headless Service

The Headless Service YAML file defines a Kubernetes Service that does not assign an IP address to the pods. It is used for discovering and connecting to individual pods directly.

service.yaml:

    apiVersion: v1
    kind: Service
    metadata:
      name: headless-svc
    spec:
      selector:
        app: nginx
      ports:
      - port: 80
        name: web
      clusterIP: None

**To apply the Headless Service YAML file, use the following command:**

    kubectl apply -f service.yaml

![](https://cdn-images-1.medium.com/max/2000/1*XBUveHrZlQ-nZkkhi8TAQA.png)

**To retrieve the information about the “headless-svc” service, use the following command:**

    kubectl get service headless-svc

![](https://cdn-images-1.medium.com/max/2168/1*mvNFq42v2qiVyI5fM6i-yg.png)

## StatefulSet

The StatefulSet YAML file defines a Kubernetes StatefulSet, which manages the deployment and scaling of stateful applications. In this example, it creates and manages two replicas of an nginx application.

stateful-file.yaml:

    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: web
    spec:
      serviceName: "headless-svc"
      replicas: 2
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: registry.k8s.io/nginx-slim:0.8
            ports:
            - containerPort: 80
              name: web

**To apply the StatefulSet YAML file, use the following command:**

    kubectl apply -f stateful-file.yaml

![](https://cdn-images-1.medium.com/max/2000/1*MVQDLo9gsQaH1J-lRxLTsA.png)

**To retrieve the information about the “web” StatefulSet, including the number of replicas and their current status, use the following command:**

    kubectl get stateful-file web

![](https://cdn-images-1.medium.com/max/2000/1*RLJ4sXjTaMgH_n9hhSJcew.png)

**To retrieve the information about the pods associated with the “nginx” application and continuously monitors their status, use the following command:**

    kubectl get pods -w -l app=nginx

![](https://cdn-images-1.medium.com/max/2000/1*YbwfGxOh2H9Xt5SqdDLdug.png)

**To scale the “web” StatefulSet, use the following command:**

    kubectl scale stateful-file web --replicas=4

![](https://cdn-images-1.medium.com/max/2000/1*4CO-vtbwVNnnugl4omt91g.png)

    kubectl get stateful-file web

![](https://cdn-images-1.medium.com/max/2000/1*nyhacWZ52NUeSkmlJ7o6rw.png)

    kubectl get pods -w -l app=nginx

![](https://cdn-images-1.medium.com/max/2000/1*LsH9nbKknHTcyqJ3SphEfQ.png)

**To delete the “web” StatefulSet and all associated pods, use the following command:**

    kubectl delete stateful-file web

![](https://cdn-images-1.medium.com/max/2000/1*u1-mRem3Zbp8aPab330LsA.png)

## **Storage with StatefulSet**

The Storage with StatefulSet YAML file extends the previous StatefulSet example by adding a PersistentVolumeClaim (PVC) for storage using volumeClaimTemplates field. It mounts a volume to the nginx containers in the StatefulSet.

stateful-file.yaml:-

    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: web
    spec:
      serviceName: "headless-svc"
      replicas: 2
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: registry.k8s.io/nginx-slim:0.8
            ports:
            - containerPort: 80
              name: web
            volumeMounts:
            - name: web-pvc
              mountPath: /usr/share/nginx/html
      volumeClaimTemplates:
      - metadata:
          name: web-pvc
        spec:
          accessModes: [ "ReadWriteOnce" ]
          resources:
            requests:
              storage: 1Gi
          storageClassName: ebs-sc

**To apply the Storage with StatefulSet YAML file to create the StatefulSet with persistent storage in the Kubernetes cluster, use the following command:**

    kubectl apply -f stateful-file.yaml

## **Conclusion:**

Kubernetes StatefulSets are essential for managing stateful applications that require stable identities, persistent storage, and ordered deployment processes. By understanding how to create and manage StatefulSets using both imperative and declarative methods, you can ensure that your stateful applications run reliably and consistently in a Kubernetes cluster.
