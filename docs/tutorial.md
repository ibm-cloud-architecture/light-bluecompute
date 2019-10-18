# Tutorial

In this tutorial, you install and run a cloud-native microservices app on IBM Cloud™ by using the IBM Cloud Kubernetes Service as a [Kubernetes cluster](https://www.ibm.com/garage/method/practices/run/tool_ibm_container). The app implements a simple storefront that displays a catalog of computing devices. Users can see the product catalog from the app's web interface and know the details of the products. For a reference implementation diagram for the app, see [LightBlueCompute architecture](https://github.com/ibm-cloud-architecture/light-bluecompute/blob/master/images/ref-arch.jpg).

As you install the app, you clone a [Git repository](https://www.ibm.com/garage/method/practices/code/tool_github/) (repo) that has several [microservices](https://www.ibm.com/cloud/garage/architectures/microservices). With microservices, you can partition your app into smaller independent services that communicate with each other. This structure allows your app to be developed, deployed, and managed by different teams. By implementing microservices, you can incorporate the [Circuit Breaker pattern](https://www.ibm.com/garage/method/practices/manage/practice_circuit_breaker_pattern/) into your app so that the app can remain partially operational if a single microservice becomes unavailable.

When you install the app, these components are included:

- A web app that provides the user interface through the web browser.
- The web app also acts as a BFF component, following the [Backends for Frontends](https://samnewman.io/patterns/architectural/bff/) pattern. In this layer, front-end developers usually write back-end logic for the front end. The Web BFF is implemented by using the Node.js Express Framework. These microservices are packaged as Docker containers and are managed by a Kubernetes cluster.
- The BFF invokes a layer of reusable microservices that are written in Java™. The Java microservices run inside [IBM Cloud Kubernetes Service](https://www.ibm.com/cloud/container-service) by using [Docker](https://www.docker.com/). The Java microservices retrieve their data from databases. The catalog service retrieves items from [MySQL](https://www.mysql.com/). In this example, you run MySQL in a Docker container for development. In a production environment, it runs on an Infrastructure as a Service (IaaS) layer, [IBM Cloud infrastructure](https://cloud.ibm.com/catalog?category=vpc).

## Prerequisites

1. You must have an IBM Cloud account. The account is free and provides access to everything you need to develop, track, plan, and deploy apps. [Sign up for a trial](https://cloud.ibm.com/registration?cm_mmc=IBMBluemixGarageMethod-_-MethodSite-_-10-19-15::12-31-18-_-bm_reg). The account requires an IBMid. If you don't have an IBMid, you can create one when you register.

If you're using an IBM Cloud user ID that was created as part of an IBM Cloud account that you don't manage, the Administrator service policy role must be assigned to your IBM Cloud user ID. Without that level of access, you can't create a cluster or deploy to a cluster by using the tools in this tutorial. To request the Administrator service policy role, contact the owner of your IBM Cloud account.

2. To install and test the app, your machine must be set up to run developer commands such as `git`.

- If you use a Linux system, make sure that you can run the commands and install the appropriate packages that allow you to do so.

- If you use a Windows system, install [Git](https://git-scm.com/downloads). After you download the file, open it and run the installation wizard. Accept all default installation options. After the installation is complete, on the command line, type `git` to see the Git instructions output.

- If you use a Mac system, install [Git](https://git-scm.com/download/mac). For detailed instructions, see [Installing on Mac](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

4. Verify that Git is installed by entering this command in a terminal or on a command-line window:

```
git --version
```

You will see something like below.

```
$ git version
git version 2.20.1 (Apple Git-117)
```
## Task 1 - Install the IBM Cloud CLI and Container Service plug-in, the kubernetes CLI, and Helm

To install and test the app on IBM Cloud, you need these tools:

- IBM Cloud CLI
- IBM Cloud Kubernetes Service plug-in
- Kubernetes CLI ([kubectl](https://kubernetes.io/docs/reference/kubectl/overview/))
- [Helm](https://github.com/helm/helm), the Kubernetes package manager

1. The deployment requires the IBM Cloud Kubernetes Service plug-in. Ensure that the IBM Cloud Kubernetes Service plug-in is installed by entering this command:

```
ibmcloud ks help
```

If the plug-in is not installed, install the plug-in by entering this command:

```
ibmcloud plugin install container-registry -r 'IBM Cloud'
```

2. Install kubectl on your platform by following the instructions in the [Kubernetes documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

3. Install Helm on your platform by following these [instructions](https://github.com/helm/helm/blob/master/docs/install.md).

4. Ensure that the tools are installed by entering these commands:

```
ibmcloud --version
kubectl help
helm -h
```

You will see something like below.

```
$ ibmcloud --version
ibmcloud version 0.19.0+569dd56-2019-09-23T08:20:26+00:00
$ kubectl help
kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
  create         Create a resource from a file or from stdin.
  expose         Take a replication controller, service, deployment or pod and
expose it as a new Kubernetes Service
  run            Run a particular image on the cluster
  set            Set specific features on objects

Basic Commands (Intermediate):
  explain        Documentation of resources
  get            Display one or many resources
  edit           Edit a resource on the server
  delete         Delete resources by filenames, stdin, resources and names, or
by resources and label selector

Deploy Commands:
  rollout        Manage the rollout of a resource
  scale          Set a new size for a Deployment, ReplicaSet, Replication
Controller, or Job
  autoscale      Auto-scale a Deployment, ReplicaSet, or ReplicationController

Cluster Management Commands:
  certificate    Modify certificate resources.
  cluster-info   Display cluster info
  top            Display Resource (CPU/Memory/Storage) usage.
  cordon         Mark node as unschedulable
  uncordon       Mark node as schedulable
  drain          Drain node in preparation for maintenance
  taint          Update the taints on one or more nodes

Troubleshooting and Debugging Commands:
  describe       Show details of a specific resource or group of resources
  logs           Print the logs for a container in a pod
  attach         Attach to a running container
  exec           Execute a command in a container
  port-forward   Forward one or more local ports to a pod
  proxy          Run a proxy to the Kubernetes API server
  cp             Copy files and directories to and from containers.
  auth           Inspect authorization

Advanced Commands:
  apply          Apply a configuration to a resource by filename or stdin
  patch          Update field(s) of a resource using strategic merge patch
  replace        Replace a resource by filename or stdin
  wait           Experimental: Wait for a specific condition on one or many
resources.
  convert        Convert config files between different API versions

Settings Commands:
  label          Update the labels on a resource
  annotate       Update the annotations on a resource
  completion     Output shell completion code for the specified shell (bash or
zsh)

Other Commands:
  alpha          Commands for features in alpha
  api-resources  Print the supported API resources on the server
  api-versions   Print the supported API versions on the server, in the form of
"group/version"
  config         Modify kubeconfig files
  plugin         Provides utilities for interacting with plugins.
  version        Print the client and server version information

Usage:
  kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all
commands).
$ helm -h
The Kubernetes package manager

To begin working with Helm, run the 'helm init' command:

	$ helm init

This will install Tiller to your running Kubernetes cluster.
It will also set up any necessary local configuration.

Common actions from this point include:

- helm search:    search for charts
- helm fetch:     download a chart to your local directory to view
- helm install:   upload the chart to Kubernetes
- helm list:      list releases of charts

Environment:
  $HELM_HOME          set an alternative location for Helm files. By default, these are stored in ~/.helm
  $HELM_HOST          set an alternative Tiller host. The format is host:port
  $HELM_NO_PLUGINS    disable plugins. Set HELM_NO_PLUGINS=1 to disable plugins.
  $TILLER_NAMESPACE   set an alternative Tiller namespace (default "kube-system")
  $KUBECONFIG         set an alternative Kubernetes configuration file (default "~/.kube/config")

Usage:
  helm [command]

Available Commands:
  completion  Generate autocompletions script for the specified shell (bash or zsh)
  create      create a new chart with the given name
  delete      given a release name, delete the release from Kubernetes
  dependency  manage a chart's dependencies
  fetch       download a chart from a repository and (optionally) unpack it in local directory
  get         download a named release
  history     fetch release history
  home        displays the location of HELM_HOME
  init        initialize Helm on both client and server
  inspect     inspect a chart
  install     install a chart archive
  lint        examines a chart for possible issues
  list        list releases
  package     package a chart directory into a chart archive
  plugin      add, list, or remove Helm plugins
  repo        add, list, remove, update, and index chart repositories
  reset       uninstalls Tiller from a cluster
  rollback    roll back a release to a previous revision
  search      search for a keyword in charts
  serve       start a local http web server
  status      displays the status of the named release
  template    locally render templates
  test        test a release
  upgrade     upgrade a release
  verify      verify that a chart at the given path has been signed and is valid
  version     print the client/server version information

Flags:
      --debug                           enable verbose output
      --home string                     location of your Helm config. Overrides $HELM_HOME (default "/Users/Hemankita.Perabathini@ibm.com/.helm")
      --host string                     address of Tiller. Overrides $HELM_HOST
      --kube-context string             name of the kubeconfig context to use
      --tiller-connection-timeout int   the duration (in seconds) Helm will wait to establish a connection to tiller (default 300)
      --tiller-namespace string         namespace of Tiller (default "kube-system")

Use "helm [command] --help" for more information about a command.
```

5. Install the [IBM Cloud CLI](https://cloud.ibm.com/docs/cli/reference/ibmcloud?topic=cloud-cli-install-ibmcloud-cli&cm_mmc=IBMBluemixGarageMethod-_-MethodSite-_-10-19-15::12-31-18-_-ibmcloud-cli-install&_ga=2.9071408.428156231.1571349383-306001710.1571349383#install_use).

## Task 4 - Deploy the app to the cluster

You packaged all the application components as [Kubernetes Helm charts](https://github.com/helm/charts). You can now deploy the application by following the instructions to configure kubectl for access to the Kubernetes cluster.

1. Log in by using the API key to run the `kubectl` command.

```
ibmcloud login -a <your region's API endpoint> -r <your region> -g <your resource group> --apikey <your API key>
```

Get your api key by accessing `Manage > Access(IAM) > IBM Cloud API keys > Create an IBM Cloud API key`.

2. To set the Kubernetes environment variables, you need an export command. Retrieve the export command by entering the `ibmcloud ks cluster config` command:

```
ibmcloud ks cluster config --cluster <clustername>
```

3. Copy the resulting export command, which looks like this example:

```
export KUBECONFIG=/Users/ibm/.bluemix/plugins/container-service/clusters/cloudnativedev/kube-config-prod-mel01-cloudnativedev.yml
```

Paste the export command on the command line and run it.

4. Initialize Helm in your cluster:

```
helm init
```

The Helm client and the server side component, tiller, are initialized.

5. Add a serviceaccount to deploy tiller.

```
kubectl --namespace kube-system create serviceaccount tiller

kubectl create clusterrolebinding tiller-cluster-rule \
 --clusterrole=cluster-admin --serviceaccount=kube-system:tiller

kubectl --namespace kube-system patch deploy tiller-deploy \
 -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
```

6. Add the helm package repository that contains the reference application:

```
helm repo add ibm-light-bc https://raw.githubusercontent.com/ibm-cloud-architecture/light-bluecompute/master/lightbluecompute/charts/release
```

7. Install the reference application:

```
helm install --name bluecompute ibm-light-bc/lightbluecompute
```

In few seconds, the containers are deployed to the cluster. The output of the installation contains instructions to access the application after it is deployed.

For more information, see [Run a Cloud Native Microservices Application on a Kubernetes Cluster](https://github.com/ibm-cloud-architecture/light-bluecompute).

Also, there is an extended version of bluecompute. Currently, we are using light version of it for this tutorial. But if you wanted to check out more elaborate version of this app, refer to [Cloud Native Java Microservices - Reference implementation based on Kubernetes](https://github.com/ibm-cloud-architecture/refarch-cloudnative-kubernetes). We have it implemented in both Springboot and Microprofile.

8. Copy the application URL and paste it into your browser. Create a bookmark of the URL.

![](images/bc-home.png)
