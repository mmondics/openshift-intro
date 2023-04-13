
# openshift-intro
 
In this tutorial, you will walk through an introduction to OpenShift Container Platform (OCP) from both the web console and the OpenShift command line interface, `oc`. This tutorial is based on the [Getting Started](https://docs.openshift.com/container-platform/4.12/getting_started/openshift-web-console.html) portion of the OpenShift Container Platform documentation, but modified to run on various platform architectures (including IBM zSystems, IBM Power, and x86) as well as adding more explanations for the concepts it covers.

## Connection Instructions

**Perform all steps in this lab (both in the web console and the command line) inside the Apache Guacamole Virtual Machine. Your local workstation will fail to connect to the cluster.**

OpenShift cluster URL: <https://console-openshift-console.apps.ocpzdev.pbm.ihost.com/dashboards>

| Name                                    | NN | OCP Username | OCP Password |
|-----------------------------------------|----|--------------|--------------|
|     Adriel Gomes Borges                 | 01 | zcxws01      | zcxws01      |
|     Alexandre Perissinotto do Amaral    | 02 | zcxws02      | zcxws02      |
|     Anderson C. Mattos                  | 03 | zcxws03      | zcxws03      |
|     Andre Cintas Donizette              | 04 | zcxws04      | zcxws04      |
|     Andre Henrique Valle                | 05 | zcxws05      | zcxws05      |
|     Andre Paulli Rondello               | 06 | zcxws06      | zcxws06      |
|     David Guimaraes Chu                 | 07 | zcxws07      | zcxws07      |
|     Elaine I. Nishikawa                 | 08 | zcxws08      | zcxws08      |
|     Esdras Almeida Freitas              | 09 | zcxws09      | zcxws91      |
|     Fabio Rodriguez Lopes               | 10 | zcxws10      | zcxws10      |
|     Felipe Castro Geraldo               | 11 | zcxws11      | zcxws11      |
|     Flavio Yukio Nagai                  | 12 | zcxws12      | zcxws12      |
|     Gilberto Dos Santos Menezes         | 13 | zcxws13      | zcxws13      |
|     Guilherme Andre Garcia Bueno        | 14 | zcxws14      | zcxws14      |
|     Jose Roberto Montanhez Junior       | 15 | zcxws15      | zcxws15      |
|     Leonardo P. Gatti                   | 16 | zcxws16      | zcxws16      |
|     Leonilson Sousa Alencar Fil         | 17 | zcxws17      | zcxws17      |
|     Matheus Oliveira Cezarani           | 18 | zcxws18      | zcxws18      |
|     Paola Silva Goncalves               | 19 | zcxws19      | zcxws19      |
|     Thiago Augusto Duarte               | 20 | zcxws20      | zcxws20      |

## Table of Contents

- [openshift-intro](#openshift-intro)
  - [Connection Instructions](#connection-instructions)
  - [Table of Contents](#table-of-contents)
  - [Pre-Requisites](#pre-requisites)
  - [OpenShift Overview](#openshift-overview)
  - [Overview of the OpenShift Web Console](#overview-of-the-openshift-web-console)
  - [Connect to the OpenShift Console](#connect-to-the-openshift-console)
  - [The Administrator Perspective](#the-administrator-perspective)
  - [The Developer Perspective](#the-developer-perspective)
  - [Deploying a Container Image](#deploying-a-container-image)
  - [Examining the Pod](#examining-the-pod)
  - [Scaling the Frontend Application](#scaling-the-frontend-application)
  - [Introduction to the `oc` CLI](#introduction-to-the-oc-cli)
  - [Logging in with the `oc` CLI](#logging-in-with-the-oc-cli)
  - [Exploring the `oc` CLI](#exploring-the-oc-cli)
  - [Use `oc` to Deploy a Python Application from Source Code](#use-oc-to-deploy-a-python-application-from-source-code)
  - [Deploying and Configuring MongoDB](#deploying-and-configuring-mongodb)
  - [Providing Sensitive Application with Secrets](#providing-sensitive-application-with-secrets)
  - [Metering, Monitoring, and Alerts](#metering-monitoring-and-alerts)
    - [OpenShift Metrics (Prometheus)](#openshift-metrics-prometheus)
    - [OpenShift Monitoring (Grafana)](#openshift-monitoring-grafana)
    - [OpenShift Alerting (AlertManager)](#openshift-alerting-alertmanager)
  - [Cleanup](#cleanup)
  - [Conclusion](#conclusion)
  - [Glossary of Terms](#glossary-of-terms)

## Pre-Requisites
1. Access to an OpenShift cluster
2. `oc` CLI [installed](https://docs.openshift.com/container-platform/4.12/cli_reference/openshift_cli/getting-started-cli.html)
3. Projects created for each user with the names `zcxwsNN-project` (`NN` being each user number). Administrators can create this ahead of time with any limitations or quotas they wish, or administrators can allow users to create their own projects.

## OpenShift Overview

![ocp](images/ocp.png)

[OpenShift Container Platform](https://www.redhat.com/en/technologies/cloud-computing/openshift/container-platform) is an enterprise-grade container platform based on [Kubernetes](https://kubernetes.io/). OpenShift is designed to allow applications and the data centers that support them to expand from just a few machines and applications to thousands of machines that serve millions of clients.

With its foundation in Kubernetes, OpenShift Container Platform incorporates the same technology that serves as the engine for massive telecommunications, streaming video, gaming, banking, and other applications. Its implementation in open Red Hat technologies lets you extend your containerized applications beyond a single cloud to on-premises and multi-cloud environments.

![ocp-stack](images/ocp-stack.png)

**OpenShift Container Platform**, commonly referred to as OpenShift or OCP, is a Kubernetes environment for managing the lifecycle of container-based applications and their dependencies on various computing platforms, such as bare metal, virtualized, on-premises, and in cloud. OpenShift deploys, configures and manages containers. OpenShift offers usability, stability, and customization of its components.

OpenShift utilizes a number of computing resources, known as [nodes](#glossary-of-terms). Each node runs a lightweight, secure operating system based on Red Hat Enterprise Linux (RHEL), known as Red Hat Enterprise Linux CoreOS (RHCOS).

After a node is booted and configured, it obtains the [CRI-O](#glossary-of-terms) container runtime for managing and running the images of container workloads scheduled to it. The Kubernetes agent, or [kubelet](#glossary-of-terms) schedules container workloads on the node. The kubelet is responsible for registering the node with the cluster and receiving the details of container workloads.

OpenShift configures and manages the networking, load balancing and routing of the cluster. OpenShift adds cluster services for monitoring the cluster health and performance, logging, and for managing upgrades.

The container image registry and [OperatorHub](#glossary-of-terms) provide Red Hat certified products and community-built software for providing various application services within the cluster. These applications and services manage the applications deployed in the cluster, databases, frontends and user interfaces, application runtimes and business automation, and developer services for development and testing of container applications.

You can manage applications within the cluster either manually by configuring [deployments](#glossary-of-terms) of containers running from pre-built images or through resources known as [Operators](#glossary-of-terms). OpenShift can also build and deploy custom images from *source code* so you developers can focus on *developing* - without learning new skills such as containerizing their code.

## Overview of the OpenShift Web Console

The OpenShift Container Platform web console is a user interface accessible from a web browser.

Developers can use the web console to [visualize, browse, and manage the contents of projects](https://docs.openshift.com/container-platform/4.12/web_console/odc-about-developer-perspective.html).

![openshift-console](/images/openshift-console.png)

Administrators can use the web console to manage and monitor applications running on the cluster, [along with the cluster itself](https://docs.openshift.com/container-platform/4.12/web_console/using-dashboard-to-get-cluster-information.html).

![openshift-console-admin](/images/openshift-console-admin.png)

[The web console can be customized](https://docs.openshift.com/container-platform/4.12/web_console/configuring-web-console.html) to suit an organization's needs, and when you log into the web console, you will only see the cluster resources that are available to you as allowed by the OpenShift Role Based Access Control (RBAC).

The web console runs as a group of pods on the control plane nodes in the `openshift-console` project, along with a service exposed as a route. Like any other OpenShift application the service is an internal load balancer that directs traffic to one of the OpenShift console pods. The route is what allows external access into the service, and it is the address you connect to when accessing the OpenShift web console.

The OpenShift web console is a great example of how OpenShift itself is run and managed as pods inthe same way as workloads running on the cluster.

## Connect to the OpenShift Console

1. **Open a web browser such as Firefox or Google Chrome.**

2. In the browser, **navigate to your OpenShift console.** 

    The OpenShift console typically begins with `https://console-openshift-console-`. Reach out to your OpenShift administrator if you do not have this address.

    You will now see the OpenShift console login page.

    ![openshift-console-login](/images/openshift-console-login.png)

3. **Log in with your OpenShift credentials.**

## The Administrator Perspective

Take a moment to notice the following elements in the navigation bar:

![navigation-bar.png](/images/navigation-bar.png)

Note: These buttons display on each page of the OpenShift console. Note that the Applications button might be missing from your screen, depending on your credentials and which applications are currently deployed on the cluster.

By default, the menu on the left side of the page should be activated and displaying the cluster menu.

4. In the left-side menu, **select the Administrator perspective** if it isn't already showing.

    ![administrator-perspective.png](/images/administrator-perspective.png)

    With the administrator menu showing, you are provided with a broad range of options to manage the OpenShift cluster and the applications running on it.

    ![administrator-menu.png](/images/administrator-menu.png)

    The *Administrator* perspective is the default view for the OpenShift console for users who have an administrative access level. This perspective provides visibility into options related to cluster administration, the cluster operators running the OpenShift cluster itself, as well as a broader view of the projects associated with the currently authenticated user.

    Your user credentials have the `cluster-reader` roleBinding. This is a read-only roleBinding that allows you to *see* most of what OpenShift has to offer administrators without allowing you to modify cluster objects. For example, typical non-admin users would not be able to complete the following few steps.

5. In the menu at the very bottom, **click Administration -> Cluster Settings**.

    ![cluster-settings.png](/images/cluster-settings.png)

    The cluster settings page is where administrators can see what OpenShift versions are available, and also update the cluster from within the console. OpenShift completely automates the cluster update once triggered by an administrator, including updating all of the cluster operators and the CoreOS operating system running on the nodes.

6. On the Cluster Settings page, **select the ClusterOperators tab**.

    ![cluster-settings-clusteroperators.png](/images/cluster-settings-clusteroperators.png)

    Each clusterOperator is responsible for managing resources related to a specific OpenShift function. For example, the `authentication` clusterOperator manages all of the resources (pods, routes, secrets, etc.) related to the LDAP authentication you are using to log in with the `zcxwsNN` username.

7. In the Menu, **click Home -> Projects.**

    ![home-projects.png](/images/home-projects.png)

    The rest of the page is populated by projects. A project has been created for you to work in named `zcxwsNN-project` (where NN is your user number). If your OpenShift administrator did not create this project for you, you should be able to do so yourself with the `Create Project` button in the top right of the projects page.

    Any project starting with `openshift-` or `kube-` contain the workloads running the OpenShift platform itself.

8. **Click the zcxwsNN-project hyperlink** (where NN is your user number).

    Tip: With so many Projects displayed, you can use the search bar to find yours more easily.

    You will now see the Dashboard for your project.

    ![empty-openshift-project.png](/images/empty-openshift-project.png)

    OpenShift automatically creates a few special service accounts in every project. The `default` service account takes responsibility for running the pods. OpenShift uses and injects this service account into every pod that launches.

    The following step creates a **RoleBinding** object for the `default` ServiceAccount object. The service account communicates with the OpenShift Container Platform API to learn about pods, services, and resources within the project.

9. **Click the RoleBindings tab of your project, and then click Create Binding.**

    ![create-binding.png](/images/create-binding.png)

10. **Fill out the form as follows.**

    - Name: `sa-user-account`
    - Role Name: `view`
    - Subject: `ServiceAccount`
    - Subject namespace: `zcxwsNN-project` where `NN` is your user number
    - Subject Name: `default`

    ![create-binding-2.png](/images/create-binding-2.png)

    **Then click create.**

11. **Navigate back to your project and look through the Overview tab of your project**.

    This displays information about what’s going on in your project, such as CPU and memory usage, any alerts or crashlooping pods, an inventory of all the Kubernetes resources deployed in the project, and more. You won’t see much information yet, as no workloads should be running in this project.

12. **Click the Workloads tab** to the right of YAML.

    This page displays all of the workloads in your project, so it’s empty for now.

    All objects in OpenShift can be generated using YAML files. YAML (standing for Yet Another Markup Language) is a human-readable language for configuration files. Most OpenShift object such as Deployments, Services, Routes, etc. can be modified by directly editing their YAML file in either the console or command line.

    Workloads are typically created by developers, so in the next section, you will swap to the developer perspective to deploy an application. You will return to the administrator perspective later in this lab.

## The Developer Perspective

11. In the left-side Menu, **click the Administrator dropdown, and select Developer**.

    ![developer-perspective.png](/images/developer-perspective.png)

    The *Developer* perspective provides views and workflows specific to developer use cases, while hiding many of the cluster management options typically used by administrators. This perspective provides developers with a streamlined view of the options they typically use.

    ![developer-menu.png](/images/developer-menu.png)

Switching to the Developer perspective takes you to the *Topology* view. If no workloads are deployed in the selected project, options to start building an application or visit the +Add page or are displayed.

1.  **Click the +Add button in the menu**.

    ![add-workload-notes.png](/images/add-workload-notes.png)

    There are multiple methods of deploying workloads from the OpenShift web browser, including from raw source code hosted in a Git repository, from a pre-built container image, or from an operator.

## Deploying a Container Image

The simplest way to deploy an application in OpenShift Container Platform is to run an existing container image. The following procedure deploys a front end component of an application called `national-parks-app`. This frontend web application displays an interactive map which shows the location of national parks across North America.

13. From the +Add view in the Developer perspective, **click Container images**.

14. **Enter the following values**:

    - Image name from external registry: `quay.io/mmondics/national-parks-frontend:latest`

    **Please wait until you see `Validated` underneath the image name.**

    - Application name: `national-parks-app`
    - Name: `parksmap`

    Keep the default target port as well as the route option checked.

    In the Advanced Options section, click Labels and add labels to better identify this deployment later. Labels help identify and filter components in the web console and in the command line. Add the following labels:

    - `app=national-parks-app`
    - `component=parksmap`
    - `role=frontend`

    Note: you can hit the `enter` or `tab` key to enter each individual label.

15. **Click Create**.

    You are redirected to the Topology page where you will shortly see the `parksmap` deployment in the `national-parks-app` application. Please be patient - it may take a minute or two to pull the container image into the OpenShift cluster's internal registry.

    ![topology-1](images/topology-1.png)

## Examining the Pod

The topology page gives you a visual representation of your application where you can view and monitor its components.

16. **Click the circular icon for your `parksmap` deployment**.

    This brings up a window on the right side of the screen with more options for the deployment.
    
17. **Click the details tab, if not already on it**. 
 
    ![topology-2](images/topology-2.png)

   Here you can manage its properties including number of copies, labels, or storage.

18. **Click the Resources tab**. 

    ![topology-3](images/topology-3.png)

    Here you can access the pod or its logs as well as the route where the application is accessible.

19. **Click the Actions dropdown menu**.

    ![topology-4](images/topology-4.png)

    This menu provides many options for how to manage or modify your `parksmap` deployment. Most of these options can be set either at the time you deploy the application, or afterwards as in this case.

## Scaling the Frontend Application

In Kubernetes, a Deployment object defines how an application deploys. In most cases, users use Pod, Service, ReplicaSets, and Deployment resources together. As in the previous section, OpenShift Container Platform creates each of these dependent resources for you, rather than you having to configure each one manually.

When you deployed the `national-parks-frontend` image, a deployment resource was created with only one pod deployed. However, in most cases, users will want to scale their application to have multiple copies running at the same time. This is one of the core features of Kubernetes and OpenShift that build a more highly available application by creating multiple copies of it across different physical or virtual hosts.

20. On the details tab for the `parksmap` deployment, **click the up arrow next to the blue circle that says `1 pod`**. 

    ![scaling-up.png](/images/scaling-up.png)

    This scales your application from one pod to two pods.

    ![scaled-up.png](/images/scaled-up.png)

    This is a simple demonstration of horizontal scaling with Kubernetes. You now have two instances of your pod running in the OpenShift cluster. Traffic to the `parksmap` application will now be distributed to each pod, and if for some reason a pod is lost, that traffic will be redistributed to the remaining pods until a Kubernetes starts another. If a whole compute node is lost, Kubernetes will move the pods to different compute nodes.

    OpenShift and Kubernetes also support [horizontal autoscaling](https://docs.openshift.com/container-platform/4.12/nodes/pods/nodes-pods-autoscaling.html) of pods based on CPU or memory consumption, but that is outside the scope of this lab.

Now that you are more familiar with the OpenShift web console, the next section will introduce you to the OpenShift command line interface (CLI) `oc`. You will use the `oc` CLI to deploy a backend python application to serve data to the frontend `parksmap` as well as to deploy a containerized MongoDB application.

## Introduction to the `oc` CLI

The `oc` CLI is an incredibly powerful tool to manage OpenShift clusters and applications. It is capable of performing any task you can do in the web console, and some tasks are only possible to do with `oc`.

`oc` is ideal in situations where you:

- Work directly with project source code.
- Script OpenShift Container Platform operations.
- Are restricted by bandwidth resources and cannot use the web console.

Furthermore, many people familiar with Linux and/or Kubernetes tend to find the `oc` command line an easier and more efficient method of performing tasks, rather than the web-based console.

Like with the OpenShift web console, the OpenShift command line includes functions both [for developers](https://docs.openshift.com/container-platform/4.12/cli_reference/openshift_cli/developer-cli-commands.html) and for [administrators](https://docs.openshift.com/container-platform/4.12/cli_reference/openshift_cli/administrator-cli-commands.html).

For those who are familiar with Kubernetes and its `kubectl` CLI tool, the `oc` tool is essentially analogous but with additional features.

## Logging in with the `oc` CLI

The frontend application, `parksmap`, needs a backend. In this section, you will deploy a python application named `nationalparks`. This application performs 2D geo-spatial queries against a MongoDB database to locate and return map coordinates of all national parks in North America.

21. From the OpenShift web console, **click your username in the top right corner** (i.e. `Workshop User NN`) and select `Copy login command`.

    ![copy-login-command](images/copy-login-command.png)

22. **Log in with your OpenShift credentials and click the Display Token hyperlink**.

NOTE for those in the WSC hands-on lab session, you must complete the following steps from within the WSC linux guest. Refer to the [access.md](access.md#connecting-to-openshift-via-the-cli) page. If you try to connect directly from the RHEL virtual machine terminal, the command will time out.

23. **Copy the line that begins with `oc login`, paste it into a terminal session, and execute the command**.

    Sample output: 

    ```
    $ oc login --token=uL3fHEPSGH3io0kljsgRfAMAPIIY44BhwnGxCMA3dei4 --server=https://api.example.com:6443
    Logged into "https://api.example.com:6443" as "user01" using the token provided.

    You have access to 161 projects, the list has been suppressed. You can list all projects with 'oc projects'

    Using project "user01-project".
    ```

    You are now logged into the cluster via the `oc` CLI, and you are told which project you are using.

    If you’re in a project other than your own `zcxwsNN-project`, use the following command to move into it: `oc project zcxwsNN-project`, where `NN` is your user number.

## Exploring the `oc` CLI

24. **In your terminal, test out the `oc` CLI**.

    ```text
    oc --help
    ```

    Sample output: 

    ```
    user01@lab061:~$ oc --help
    OpenShift Client

    This client helps you develop, build, deploy, and run your applications on any
    OpenShift or Kubernetes cluster. It also includes the administrative
    commands for managing a cluster under the 'adm' subcommand.

    Usage:
    oc [flags]

    Basic Commands:
    login           Log in to a server
    new-project     Request a new project
    new-app         Create a new application
    status          Show an overview of the current project
    project         Switch to another project
    projects        Display existing projects
    explain         Documentation of resources

    Build and Deploy Commands:
    rollout         Manage a Kubernetes deployment or OpenShift deployment config
    rollback        Revert part of an application back to a previous deployment
    new-build       Create a new build configuration
    start-build     Start a new build
    ```

    The `--help` flag will display all of the available options the oc CLI.

25. **Enter the following command**

    ```text
    oc new-app --help
    ```

    Sample output:

    ```
    user01@lab061:~$ oc new-app --help
    Create a new application by specifying source code, templates, and/or images

    This command will try to build up the components of an application using images, templates, or code
    that has a public repository. It will lookup the images on the local Docker installation (if
    available), a container image registry, an integrated image stream, or stored templates.

    If you specify a source code URL, it will set up a build that takes your source code and converts
    it into an image that can run inside of a pod. Local source must be in a git repository that has a
    remote repository that the server can see. The images will be deployed via a deployment
    configuration, and a service will be connected to the first public port of the app. You may either
    specify components using the various existing flags or let new-app autodetect what kind of
    components you have provided.

    If you provide source code, a new build will be automatically triggered. You can use 'oc status' to
    check the progress.

    Usage:
    oc new-app (IMAGE | IMAGESTREAM | TEMPLATE | PATH | URL ...) [flags]

    Examples:
    # List all local templates and image streams that can be used to create an app
    oc new-app --list
    
    # Create an application based on the source code in the current git repository (with a public
    remote) and a Docker image
    oc new-app . --docker-image=repo/langimage
    ```

    The `--help` flag now displays all of the available options for the oc new-app command. If you get confused about any of the commands we use in this workshop, or just want more information, using this flag is a good first step.

`oc new-app` is a powerful and commonly used command in the OpenShift CLI. It has the ability to deploy applications from components that include:

- Source or binary code
- Container images
- Templates

The set of objects created by `oc new-app` depends on the artifacts passed as an input.

## Use `oc` to Deploy a Python Application from Source Code

The frontend application, `parksmap`, needs a backend. In this section, you will deploy a python application named `nationalparks`. This application performs 2D geo-spatial queries against a MongoDB database to locate and return map coordinates of all national parks in North America.

26. **Deploy the python backend with the following `oc new-app` command**.

    ```text
    oc new-app python~https://github.com/mmondics/national-parks --context-dir source/nationalparks-py --name nationalparks -l 'app=national-parks-app,component=nationalparks,role=backend,app.kubernetes.io/part-of=national-parks-app,app.kubernetes.io/name=python'
    ```

    A few things to notice about this command:

    - The `oc new-app` command is not being run against a specific container image. It is being run against python *source code* that exists in GitHub [here](https://github.com/mmondics/national-parks/tree/main/source/nationalparks-py). Although a Dockerfile exists in the directory, it is not being used due to the `pyhon~` option in the command. OpenShift is using its *source-to-image* capability to create its own Dockerfile and containerize the application from its source code. If you wanted to, you could have omitted the `python~` option and OpenShift would use the Dockerfile in the directory.
    - `--name` flag provides the name for the python Deployment
    - `-l` sets the following key=value pairs as labels on the deployment
    
    Sample Output:

    ```text
    ➜  ~ oc new-app python~https://github.com/mmondics/national-parks --context-dir source/nationalparks-py --name nationalparks -l "app=national-parks-app,component=nationalparks,role=backend,app.kubernetes.io/part-of=national-parks-app,app.kubernetes.io/name=python"
    warning: Cannot check if git requires authentication.
    --> Found image ad4c417 (2 weeks old) in image stream "openshift/python" under tag "3.9-ubi8" for "python"

        Python 3.9
        ----------
        Python 3.9 available as container is a base platform for building and running various Python 3.9 applications and frameworks. Python is an easy to learn, powerful programming language. It has efficient high-level data structures and a simple but effective approach to object-oriented programming. Python's elegant syntax and dynamic typing, together with its interpreted nature, make it an ideal language for scripting and rapid application development in many areas on most platforms.

        Tags: builder, python, python39, python-39, rh-python39

        * A source build using source code from https://github.com/mmondics/national-parks will be created
        * The resulting image will be pushed to image stream tag "nationalparks:latest"
        * Use 'oc start-build' to trigger a new build

    --> Creating resources with label app=national-parks-app,app.kubernetes.io/name=python,app.kubernetes.io/part-of=national-parks-app,component=nationalparks,role=backend ...
        imagestream.image.openshift.io "nationalparks" created
        buildconfig.build.openshift.io "nationalparks" created
    Warning: would violate PodSecurity "restricted:v1.24": allowPrivilegeEscalation != false (container "nationalparks" must set securityContext.allowPrivilegeEscalation=false), unrestricted capabilities (container "nationalparks" must set securityContext.capabilities.drop=["ALL"]), runAsNonRoot != true (pod or container "nationalparks" must set securityContext.runAsNonRoot=true), seccompProfile (pod or container "nationalparks" must set securityContext.seccompProfile.type to "RuntimeDefault" or "Localhost")
        deployment.apps "nationalparks" created
        service "nationalparks" created
    --> Success
        Build scheduled, use 'oc logs -f buildconfig/nationalparks' to track its progress.
        Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
        'oc expose service/nationalparks'
        Run 'oc status' to view your app.
    ```

    The output from the `oc new-app` command tells you what all was created - an imagestream, buildconfig, deployment, and service. 

    The buildconfig is the configuration file that will be used to build the `nationalparks` container image. This build will automatically begin, and you can check its logs to watch the process. 

27. **Check the `nationalparks` build log**.
   
    ```text
    oc logs build/nationalparks-1
    ```

    Once you see `Push successful` at the end of the build logs, your new container image has been built and pushed into OpenShift's internal registry. It will then automatically be deployed in a pod.

28. **Check that the `nationalparks` pod is running and ready**.

    ```text
    oc get pods
    ```

    Sample output:

    ```text
    ➜  ~ oc get pods
    NAME                             READY   STATUS      RESTARTS   AGE
    nationalparks-1-build            0/1     Completed   0          19m
    nationalparks-67b69fc9b7-z5znq   1/1     Running     0          18m
    parksmap-cbc66fb69-c87k7         1/1     Running     0          64s
    ```

    Once the `nationalparks` pod is `Running` and has `1/1` containers ready, the application is successfully deployed. However, the backend python application is only accessible from within the OCP cluster. It is exposed to the outside world, as you may have noticed from the output of the `oc new-app` command: `Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:`

29. **Create a route that exposes the `nationalparks` service**.

    ```text
    oc expose service/nationalparks
    ```

    It's worth understanding a little bit about what you just did. The backend application `nationalparks` runs in one pod. However, you could easily scale the number of pods running `nationalparks` up as much as you'd like, or you can let OpenShift automatically scale out the number of pods (based on CPU or memory consumption) with a [HorizontalPodAutoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/). Each pod will be assigned its own individual IP address, and furthermore, if and when the pods regenerate, they will get brand new IP addresses. For these reasons, you cannot rely on the application's IP address in order to use it, like you would with many non-containerized applications. 

    To solve this problem, Kubernetes uses *services*. [Services](https://kubernetes.io/docs/concepts/services-networking/service/) are load balancers internal to the cluster that distribute requests among the application pods using label matching. If you want to access an application, you need to access the service that will then direct the request to one of the pods.

    However, services are **internal** to the cluster. They allow pods to communicate with other pods inside the cluster, but not with the outside world. For external access, we need to introduce another object - **routes**. 

    [Routes](https://docs.openshift.com/container-platform/4.12/rest_api/network_apis/route-route-openshift-io-v1.html) are OpenShift objects - they do not exist in upstream Kubernetes. Routes *expose* servies as publicly-accessible addresses for users and applications to interact with. When you access an OpenShift appliction in a web browser, such as the `parksmap` webpage or even the OpenShift console, you navigate to that pod's route.

    ![routes](images/routes.png)

30. **See the new route that was created**.

    ```text
    oc get route
    ```

    Sample output:

    ```text
    ➜  ~ oc get routes
    NAME            HOST/PORT                                        PATH   SERVICES        PORT       TERMINATION     WILDCARD
    nationalparks   nationalparks-user01-project.apps.example.com          nationalparks   8080-tcp                   None
    parksmap        parksmap-user01-project.apps.example.com               parksmap        8080-tcp   edge/Redirect   None
    ```

31. **Label the `nationalparks` route as the application backend**.

    ```text
    oc label route nationalparks type=parksmap-backend
    ```

32. **Navigate to the frontend `parksmap` route in a web browser**. Use the `parksmap` `HOST/PORT` value from the `oc get routes` command preceded by `http://`.

    e.g. <http://parksmap-zcxwsNN-project.apps..ocpzdev.pbm.ihost.com>

    Alternatively, you can click the small arrow button associated with the `parksmap` deployment in the OpenShift web console topology.

    ![topology-route](images/topology-route.png)

    Any way that you get there, your application should display the image below:

    ![parksmap-empty](images/parksmap-empty.png)

    You should see a map of the world, but you will not see any icons for National Parks yet. That's because we have one more application to deploy and configure - the MongoDB database that will store all of the data about National Parks and their locations.

## Deploying and Configuring MongoDB

The MongoDB you will deploy in this section will store all information about the National Parks, their names and coordinates.

33. **Deploy the MongoDB container**.

    ```text
    oc new-app quay.io/mmondics/mongodb:latest --name mongodb-nationalparks -e MONGODB_USER=mongodb -e MONGODB_PASSWORD=mongodb -e MONGODB_DATABASE=mongodb -e MONGODB_ADMIN_PASSWORD=mongodb -l "app.kubernetes.io/part-of=national-parks-app,app.kubernetes.io/name=mongodb"
    ```

    A few things to notice about this command:

    - You're deploying a pre-built container image hosted at quay.io/mmondics/mongodb. 
    - `--name` sets the name of the MongoDB deployment
    - `-e` sets environment variables within the resulting container. These can be used like any other environment variable when the container is running, and you can see them by connecting to the pod and running the `env` command
    - `-l` sets labels for this deployment

    Once again, you can check `oc get pods` to see when the MongoDB pod is ready.

    ```text
    NAME                                     READY   STATUS      RESTARTS   AGE
    mongodb-nationalparks-5d8b6f99f7-4wwst   1/1     Running     0          8s
    nationalparks-1-build                    0/1     Completed   0          58m
    nationalparks-67b69fc9b7-z5znq           1/1     Running     0          56m
    parksmap-cbc66fb69-c87k7                 1/1     Running     0          39m
    ```

    Now, you may think that you are ready to look at your frontend application again to see the backend data. However, this MongoDB requires authentication before it can be used, so in the next section, you will provide sensitive information to the application using OpenShift *secrets*.

## Providing Sensitive Application with Secrets

The *Secret* object provides a mechanism to hold sensitive information such as passwords, OpenShift Container Platform client configuration files, private source repository credentials, and so on. Secrets decouple sensitive content from the pods. You can mount secrets into containers using a volume plugin or the system can use secrets to perform actions on behalf of a pod. By default, secrets are stored *unencrypted* in etcd. Therefore secrets are securable by default, not secured. To safely secure secrets, you must turn on etcd encryption as well as configure proper role-based access controls to the secret objects and etcd. Read more about secrets [here](https://kubernetes.io/docs/concepts/configuration/secret/).

The following procedure adds the secret `nationalparks-mongodb-parameters` and mounts it to the `nationalparks` workload.

34. **Create a secret holding sensitive information** (usernames and passwords).

    ```text
    oc create secret generic nationalparks-mongodb-parameters --from-literal=DATABASE_SERVICE_NAME=mongodb-nationalparks --from-literal=MONGODB_USER=mongodb --from-literal=MONGODB_PASSWORD=mongodb --from-literal=MONGODB_DATABASE=mongodb --from-literal=MONGODB_ADMIN_PASSWORD=mongodb
    ```

35. **Update the environment variable to attach the `nationalparks-mongodb-parameters` secret to the `nationalparks` workload**.

    ```text
    oc set env --from=secret/nationalparks-mongodb-parameters deploy/nationalparks
    ```

    The `nationalparks` deployment will notice that a change has been made, and it will create a new pod with these changes applied. Check with `oc get pods` until the new pod is up and running.

36. Once the `mongodb-nationalparks` pod is running and ready, **navigate to the `/ws/data/load` endpoint for your `nationalparks` route to load the database**.

    e.g. <http://nationalparks-zcxwsNN-project.apps.ocpzdev.pbm.ihost.com/ws/data/load> where `NN` is your user number.

    If you see `"Items inserted in database: 226"`, the data was successfully loaded.

37. Finally, **return to your frontend `parksmap` application in a web browser**.

    ![parksmap-loaded](images/parksmap-loaded.png)

This interactive map is the culmination of all the Kubernetes and OpenShift objects that you created throughout the course of this tutorial. You can click on each icon on the map to see more details about each National Park.

## Metering, Monitoring, and Alerts

A significant architectural shift toward containers is underway and, as with any architectural shift, this brings new operational challenges. It can be challenging for many of the legacy monitoring tools to monitor container platforms in fast moving, often ephemeral environments. The good news is newer cloud-based offerings can ensure monitoring solutions are as scalable as the services being built and monitored. These new solutions have evolved to address the growing need to monitor your stack from the bottom to the top.

From an operations point of view, infrastructure monitoring tools collect metrics about the host or container, such as CPU load, available memory and network I/O.

The default monitoring stack is the 3-pronged open source approach of, Grafana, Alertmanager, and Prometheus.

**Prometheus** gives you finely grained metrics at a huge scale. With the right configuration, Prometheus can handle millions of time series.

**Grafana** can visualize the data being scraped by Prometheus. Grafana comes with pre-built dashboards for typical use cases, or you can create your own custom ones.

**Alertmanager** forwards alerts to a service such as Slack or another webhook . Alertmanager can use metadata to classify alerts into groups such as errors, notifications, etc.

The Grafana-Alertmanager-Prometheus monitoring stack provides a highly configurable, open source option to monitor Kubernetes workloads.

![monitoring-arch](images/monitoring-arch.png)

### OpenShift Metrics (Prometheus)

OpenShift provides a web interface to *Prometheus*, which enables you to run Prometheus Query Language (PromQL) queries and visualize the metrics on a plot. This functionality provides an extensive overview of the cluster state and helps to troubleshoot problems.

1. In the OpenShift console, **switch to the Administrator perspective** if you are not already on it.

    ![administrator-perspective](images/administrator-perspective.png)

1. In the menu bar on the left side of the page, **click Observe and then Metrics**.

    ![menu-metrics](images/menu-metrics.png)

    You will be taken to a Prometheus interface within the OpenShift console.

    ![empty-metrics](images/empty-metrics.png)

    Once you enter a query, the graph will populate.

1. **Enter the following string in the query bar**:

    ```text
    namespace:container_memory_usage_bytes:sum
    ```

2. **Hit your enter key or click the associated query result that is returned**.

    The string will populate the query text box.

3. **If the graph and table are not automatically populated. click the blue "Run Queries" button**.

    The graph should now display the memory usage over time for each namespace.

    ![memory-usage](images/memory-usage.png)

4. **Scroll down the page** to the table displaying each namespace and its memory usage in bytes.

    ![memory-table](images/memory-table.png)

    Your table will look different depending on what work is being done in the OpenShift cluster at the time.
    
    Notice that you have *observability* of the entire OpenShift cluster, even though you cannot access or edit projects other than your own. In other words, you have read-only access to the full OpenShift cluster via the Observability stack, but you read-write access within your zcxwsNN-project.

    OpenShift passes around a massive amount of data to run itself and the applications running on top of it. Prometheus is an extremely powerful data source that can return results for millions of time strings with extremely granular precision.

    Because of OpenShift’s vast data production and Prometheus’ ability to process it, certain queries can produce simply too much data to be useful. Because Prometheus makes use of labels, we can use these labels to filter data to make better sense of it.

5. **Modify your query to the following**:

    ```text
    namespace:container_memory_usage_bytes:sum{namespace="zcxwsNN-project"}
    ```

    Make sure you change the one instance of `NN` to your user number.

    Also, notice that they are squiggly brackets `{}` in the query, not regular parentheses.

6. **Click Run Queries**

    ![memory-namespaced](images/memory-namespaced.png)

    Your graph is now displaying the memory usage over time for your own project. If you see a “No datapoints found” message, select a longer timeframe using the dropdown menu in the top left of the graph.

    If you skipped ahead to this lab without completing the others, it’s possible that your project has not had workload deployed in it for more than the maximum time frame. If this is the case, run a simple application in your project, and you will see the data start to populate (refer to [Exploring the OpenShift Console](../lab001/lab001-1.md) for help with this.)

As you might have noticed, working directly with Prometheus can be tedious and requires specific PromQL queries that aren’t the easiest to work with. That’s why people typically use Prometheus for its *data source* functionality, and then move to Grafana for the *data visualization*.

### OpenShift Monitoring (Grafana)

1. **From the OpenShift menu, navigate to Observability -> Dashboards**.

    ![menu-dashboards](images/menu-dashboards.png)

    This takes you to an in-browser user interface for the Grafana monitoring solution. By default, there are various preconfigured dashboards for common use cases.

    ![default-in-browser](images/default-in-browser.png)

1. **Click the "Dashboard" dropdown in the top-left of the page, and select `Kubernetes / Compute Resources / Cluster`**.

    ![compute-resources-dashboard](/images/compute-resources-dashboard.png)

    You will see a dashboard populated with information related to the cluster’s compute resources such as CPU and memory utilization. This dashboard displays CPU usage and CPU quota/memory requests by namespace.

1. **Scroll down the page and look through the various CPU/memory/networking metrics.**

    Notice that each chart has an `Inspect` option. Clicking it will take you to the relevant Metrics page with the proper Prometheus query applied for more fine-grained detail.

For users who are familiar with the standard Grafana interface, that can also be accessed outside of the OpenShift console (rather than these pages integrated into the console).

You can find the direct link to the Grafana webpages by looking at the routes (Adminstrator -> Networking -> Routes) in the `openshift-monitoring` project.

### OpenShift Alerting (AlertManager)

Alerting with Prometheus is separated into two parts. Alerting rules in *Prometheus* send alerts to *Alertmanager*. Alertmanager then manages those alerts, including silencing, inhibition, aggregation and sending out notifications via methods such as email or chat platforms like Slack.

![alert-stack](images/alert-stack.png)

An example rules file with an alert would be:

```yaml
groups:
- name: example
  rules:
  - alert: HighRequestLatency
    expr: job:request_latency_seconds:mean5m{job="myjob"} > 0.5
    for: 10m
    labels:
      severity: page
    annotations:
      summary: High request latency
```

The optional `for` clause causes Prometheus to wait for a certain duration between first encountering a new expression output vector element and counting an alert as firing for this element. In this case, Prometheus will check that the alert continues to be active during each evaluation for 10 minutes before firing the alert. Elements that are active, but not firing yet, are in the pending state.

The `labels` clause allows specifying a set of additional labels to be attached to the alert. Any existing conflicting labels will be overwritten.

The `annotations` clause specifies a set of informational labels that can be used to store longer additional information such as alert descriptions or runbook links.

1. In the menu bar on the left side of the OpenShift console, **click Observe and then Alerting**.

    You will be taken to an Alertmanager interface within the OpenShift console.

    ![alertmanager](images/alertmanager.png)

1. **Click the Alerting Rules tab** to see the 100+ alerts that are not currently firing (hopefully!)

    ![alerting-rules](images/alerting-rules.png)

    These alerts come pre-built with the monitoring stack, and they will start firing if triggered. This list includes alerts for critical operators going down, pods crash-looping, nodes being unreachable, and many more. Feel free to look through them.

These alerts are typically sent to an external tool where relevant administrators, developers, or site-reliability engineers will be notified that they are firing. 

You cannot see and forwarded alerts for this OpenShift, but as an example, see the image below of a Slack alert generated by OpenShift alerting.

![alert-slack](images/alert-slack.png)

## Cleanup

Once you're ready to clean up your OpenShift project, follow the instructions in this section.

38. **Run the following command to clean up most of the objects in your project**. Remember to change the value of `NN`.

    ```text
    oc delete all --all -n zcxwsNN-project
    ```

    This will delete some of the objects in your project, but it will not delete the secret you created.

39. **Delete your secret with the following command**.

    ```text
    oc delete secret nationalparks-mongodb-parameters -n zcxwsNN-project
    ```

## Conclusion

To learn more about OpenShift Container Platform and see the additional tooling it provides to develop and manage more complex applications, please refer to the following links.

- [OpenShift 4.12 Documentation](https://docs.openshift.com/container-platform/4.12/welcome/index.html)
- [OpenShift Product Page](https://www.redhat.com/en/technologies/cloud-computing/openshift)
- [Red Hat Hybrid Cloud Blog](https://cloud.redhat.com/blog)
- [OpenShift Learning](https://developers.redhat.com/learn#assembly-id-70171)
- [OpenShift on IBM zSystems Datasheet](https://www.redhat.com/en/resources/openshift-ibm-z-linuxone-datasheet)

## Glossary of Terms

| Term                             | Definition                                                                                                                                                                                                                                             |
|----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Deployment                       | A Kubernetes resource object that maintains the life cycle of an application.                                                                                                                                                                          |
| Containers                       | Lightweight and executable images that consist software and all its dependencies. Because containers virtualize the operating system, you can run containers anywhere, from a data center to a public or private cloud to your local host.             |
| Node                             | A worker machine in the OpenShift Container Platform cluster. A node is either a virtual machine (VM) or a physical machine.                                                                                                                           |
| Operator                         | The preferred method of packaging, deploying, and managing a Kubernetes application in an OpenShift Container Platform cluster. An Operator takes human operational knowledge and encodes it into software that is packaged and shared with customers. |
| OperatorHub                      | A platform that contains various OpenShift Container Platform Operators to install.                                                                                                                                                                    |
| Pod                              | One or more containers with shared resources, such as volume and IP addresses, running in your OpenShift Container Platform cluster. A pod is the smallest compute unit defined, deployed, and managed.                                                |
| Role-Based Access Control (RBAC) | A key security control to ensure that cluster users and workloads have only access to resources required to execute their roles.                                                                                                                       |
| Route                            | Routes expose a service to allow for network access to pods from users and applications outside the OpenShift Container Platform instance.                                                                                                             |
| Service                          | A service exposes a running application on a set of pods.                                                                                                                                                                                              |