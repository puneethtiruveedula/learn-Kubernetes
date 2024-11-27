# Helm

❓ Helm and why is it essential for DevOps Engineers?

Helm is a package manager for Kubernetes that simplifies installing, upgrading, and managing applications using charts with just a few commands.

For DevOps engineers, Helm is essential because it automates app deployments, reducing manual effort and speeding up the process. It also tracks versioning, making it easy to manage upgrades or rollbacks for any release. Additionally, Helm ensures consistent deployments across all environments, from development to production.

Lets understand the terminology in helm:

✴️ Chart: A Helm chart is a package with all the resources needed to deploy an app in Kubernetes.
✴️ Repository: A repository is a storage location where Helm charts are shared and accessed.
✴️ Release: A release is an installed instance of a Helm chart in your Kubernetes cluster.
✴️ Values File: A values file customizes chart configurations for specific environments.
✴️ Template: A template defines how Kubernetes manifests are created based on the values file.
✴️ Revision: A revision tracks changes made to a release, enabling versioning and rollbacks.

------------------------------------------------------------------------------------------------------

Helm is a Kubernetes package manager in which the multiple numbers of YAML files such as the backend, and frontend come under one roof(helm) and deploy using helm.

**Let’s understand with the help of a simple example.**

Suppose you have an application where frontend, backend, and database code needs to deploy on Kubernetes. Now, the task becomes hectic if you have to deploy frontend and backend codes because your application will be Stateful. For frontend, backend, and database you will have to create different YAML files and deploy them too but it will be complicated to manage. So, Helm is an open-source package manager that will help you to automate the deployment of applications for Kubernetes in the simplest way.

![](https://cdn-images-1.medium.com/max/3200/0*hlqgMPyWMcfHU-GD)

**Let’s understand again with the help of the above architecture.**

**Normal deployment**

As you know, to deploy your code you need to write a minimum of two YAML files which is deployment and service file. Those files will be deployed with the help of the kubectl command. These files act differently from each other but you know that the files are dependent on each other.

**Helm deployment**

In the help deployment, all the YAML files related to the application deployment will be in the helm chart. So, if you want to deploy your application you don’t need to deploy each YAML file one by one. Instead, you can just write one command helm install <your-chart-name> and it will deploy your entire application in one go. Helm is the Package manager for the Kubernetes which helps to make the deployment simple.

Benefits

 1. Because of the helm, the time will be saved.

 2. It makes the automation more smoothly

 3. Reduced complexity of deployments.

 4. Once files can be implemented in different environments. You just need to change the values according to the environmental requirements.

 5. Better scalability.

 6. Perform rollback at any time.

 7. Effective for applying security updates.

 8. Increase the speed of deployments and many more.

## Hands-On

Install Helm

    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
    chmod 700 get_helm.sh
    ./get_helm.sh
    helm version

![](https://cdn-images-1.medium.com/max/3200/0*4ITwow_5Q01b3WHG)

To list the all repositories

    helm list

![](https://cdn-images-1.medium.com/max/2000/0*8JG9PNlV6lP8jQj6)

Create a stable repository using the helm

    helm repo add stable https://charts.helm.sh/stable

![](https://cdn-images-1.medium.com/max/2000/0*ZtXGX6YQKpcV9Qf8)

List the repo, again

    helm repo list

![](https://cdn-images-1.medium.com/max/2000/0*sh2l5kMnWg_fOXIn)

To remove the repo

    helm repo remove stable

![](https://cdn-images-1.medium.com/max/2000/0*hjmxmHbSzaLE_gJ-)

To create our own repo

    helm create my-repo

![](https://cdn-images-1.medium.com/max/2000/0*61dr7gDxr9DtPFSy)

To see the files inside the created repo

![](https://cdn-images-1.medium.com/max/2000/0*Gby0YqfCOB6AzvP5)

Let’s do a simple demo where we will host the nginx page on Kubernetes using the helm chart.

Create a chart using the command

    helm create helloworld

![](https://cdn-images-1.medium.com/max/2000/0*oklmsH8TRn5bVeXo)

The file structure should look like the below snippet.

![](https://cdn-images-1.medium.com/max/2000/0*wZ-iwjkcI0Qst3Wa)

Go to the Helm Chart directory helloworld and edit the values.yaml file

    cd helloworld
    vim values.yaml

![](https://cdn-images-1.medium.com/max/2000/0*bSS0K2D3lrAWqKLC)

Replace the ClusterIP with NodePort

![](https://cdn-images-1.medium.com/max/2000/0*32Nd8P9898aS7nEY)

Now deploy your helm chart

To do that, you have to be present in the directory where the chart is present.

In my case, my helloworld chart is in the Helm directory. So, I have to be in the Helm directory and run the below command to install the Helm Chart.

    helm install thehelloworld hellowolrd
    helm install <custom-chart-release-name> <given-chart-name>

![](https://cdn-images-1.medium.com/max/2396/0*hUqde4uD4jXsrB_Z)

Now, check the services whether your application is running or not

    kubectl get svc

![](https://cdn-images-1.medium.com/max/2396/0*IItgYzccuMrFmFDv)

Now, access your application via browser by copying the port number(in my case 30738) with minikube private IP in starting.

To get the IP, you can simply run the command on the terminal minikube ip and use it to view the content.

As you can see in the below snippet, our application is successfully deployed with the help of Helm Chart.

![](https://cdn-images-1.medium.com/max/2000/0*fKhwV4CtBnrIFoeK)

If you want to see the minikube dashboard GUI then run the below command on the terminal.

    minikube dashboard

![](https://cdn-images-1.medium.com/max/3178/0*r4o0owp1IMRpgnsT)

Once you run the command, a new tab will open in your browser that will look like the below snippet.

![](https://cdn-images-1.medium.com/max/3200/0*MXcfO93Lq1-bB5PY)

Now, if you want to uninstall your deployment. You can simply run the below command.

    helm uninstall thehelloworld

![](https://cdn-images-1.medium.com/max/3200/0*fIZnYZ3zGTaZeRT5)

Once you uninstall the deployment you will see nothing on your K8s dashboard because the deployment has been deleted.

![](https://cdn-images-1.medium.com/max/3200/0*gf77arNQzjmy2Uvn)

## Demo of Helm Cheat Sheets

Create the helm chart

    helm create helloworld

![](https://cdn-images-1.medium.com/max/2000/0*M5invJytIlOTHm9n)

Create the release and deploy the helm chart

    helm install thehelloworld helloworld

![](https://cdn-images-1.medium.com/max/3200/0*Ia5-V6fJIs12YHmx)

Now, if you want 2 replicas instead of 1. So you will make the changes in the values.yaml file and replace the replica 2 with 1. But you have to redeploy the changes which will be done by the below command after doing the changes.

    helm upgrade thehelloworld helloworld

![](https://cdn-images-1.medium.com/max/3200/0*1DM7MKH_XU-96q5J)

Now, you think that you did something wrong or unexpected and you want to switch to the previous deployment. To do that, first of all, you need to know that to which revision number you have to switch.

To check the current revision use the below command.

    helm list -a

![](https://cdn-images-1.medium.com/max/3200/0*Opvt-ijVRp8z_BKI)

Now, I want to switch to the 1 Revision number. So, I will roll back the changes by the below command.

    helm rollback thehelloworld 1

![](https://cdn-images-1.medium.com/max/3200/0*XlNRhltJKxk7KnNb)

If you want to dry-run the chart before installation use the below command.

    helm install thehelloworld — debug — dry-run helloworld

![](https://cdn-images-1.medium.com/max/3200/0*pJSRVcN5YERKghWd)

If you want to validate your YAML files within the helm chart then, use the below command.

    helm template helloworld

![](https://cdn-images-1.medium.com/max/3200/0*Sr7zk7j40wWm9N8W)

If you want to validate your Charts then use the below command.

    helm lint helloworld

![](https://cdn-images-1.medium.com/max/3200/0*R3veWeIl0YLYDUcQ)

If you want to delete or release the deployment then, use the below command.

    helm uninstall thehelloworld

![](https://cdn-images-1.medium.com/max/3200/0*352sIr-nl6ZbVVJ-)

## Conclusion

Cheers to unlocking the Helm magic on Day 26! 🎉 Dive into Helm charts for simplified Kubernetes deployments. We will cover some advanced things related to Helm in the next Chapter(**Not on Next Day**).

---------------------------------------------------------------------------------------------------



