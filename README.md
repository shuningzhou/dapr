# Actions - A high performance, lightweight serverless runtime for cloud and edge

[![Build Status](https://dev.azure.com/azure-octo/Actions/_apis/build/status/builds/actions%20build?branchName=master)](https://dev.azure.com/azure-octo/Actions/_build/latest?definitionId=5&branchName=master)

__Note: This repo is currently under heavy development.
As long as this note is here, consider docs not up-to-date at all times. Edge builds with potential bugs and/or breaking changes will be pushed daily.__

Actions is a programming model for writing cloud-native applications which are distributed, dynamically scaled, and loosely coupled in nature. Actions offers an eventing system on which compute units communicate with each other by exchanging messages.
<br>
<br>
Actions injects a side-car container/process to each compute unit. The side-car interacts with event triggers and communicates with the compute unit via standard HTTP or GRPC protocols. This enables Actions to support all existing and future programming languages without requiring developers to import frameworks or libraries.
<br>
Actions offers built-in state management, reliable messaging (at least once delivery), triggers and bindings through standard HTTP verbs or GRPC interfaces. This allows developers to write stateless, stateful and actor-like services following the same programming paradigm. And developers can freely choose consistency model, threading model and message delivery patterns.

Actions runs natively on Kubernetes, as a standalone binary on a developer's machine, on an IoT device, or as a container than can be injected into any system, in the cloud or on-premises.

Actions uses pluggable state stores and message buses such as Redis as well as gRPC to offer a wide range of communication methods, including direct action-to-action using gRPC and async Pub-Sub with guaranteed delivery and at-least-once semantics.

The Actions runtime is designed for hyper-scale performance in the cloud and on the edge.
<br>
###### Actions Standalone Deployment
![Actions Standalone](/docs/imgs/actions_standalone.png)
<br>
<br>
###### Actions Kubernetes Deployment
![Actions on Kubernetes](/docs/imgs/actions_k8s.png)

## Why Actions

Writing a successful distributed application is hard. Actions brings proven patterns and practices to ordinary developers. It unifies Functions and Actors into a simple, consistent programming model. It supports all programming languages without framework lock-in. Developers are not exposed to low-level primitives such as threading, concurrency control, partition and scaling. Instead, they can write their code just like implementing a simple web server using familiar web frameworks of their choice.

Actions is flexible in threading model and state consistency model. Developers can leverage multi-threading if they choose to, and they can choose among different consistency models. This flexibility enables power developers to implement advanced scenarios without artificial constraints.  On the other hand, a developer can choose to stay with single-threaded calls as she’s enjoyed in other Actor frameworks. And Actions is unique because developers can transit between these models without rewriting their code. 


## Features

* Supports all programming languages
* Does not require any libraries or SDKs
* Built-in Eventing system
* Built-in service discovery
* Asynchronous Pub-Sub with guaranteed delivery and at-least-once semantics
* Action to Action request response using gRPC
* State management - persist and restore
* Choice of concurrency model: Single-Threaded or Multiple
* Triggers (Azure, AWS, GCP, etc.)
* Lightweight (20.4MB binary, 4MB physical memory needed)
* Runs natively on Kubernetes
* Easy to debug - runs locally on your machine

## Setup

### Install as standalone

#### Prerequisites

Download the Actions CLI [release](https://github.com/actionscore/cli/releases) for your OS, unpack it and move it to your desired location (for Mac/Linux - ```mv actions /usr/local/bin```. For Windows, add the executable to your System PATH. (e.g. create c:\actions)


__*Note: For Windows users, run the cmd terminal in administrator mode*__

__*Note: For Linux users, if you run docker cmds with sudo, you need to use "sudo actions init*__

#### Install

```
$ actions init
⌛  Making the jump to hyperspace...
Downloading binaries and setting up components
✅  Success! Actions is up and running
```

To see that Actions has been installed successful, from a command prompt run `docker ps` command and see that `actionscore.azurecr.io/actions:latest` and `redis` container images are both running.


For getting started with the Actions CLI, go [here](https://github.com/actionscore/cli).

### Install on Kubernetes

#### Prerequisites

1. A Kubernetes cluster [(instructions)](https://kubernetes.io/docs/tutorials/kubernetes-basics/).
    
    Make sure your Kubernetes cluster is RBAC enabled.
    For AKS cluster ensure that you download the AKS cluster credentials with the following CLI

  ```cli
    az aks get-credentials -n <cluster-name> -g <resource-group>
  ```

2. *Kubectl* has been installed and configured to work with your cluster [(instructions)](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

The Actions CLI allows you to setup Actions on your local dev machine or on a Kubernetes cluster, provides debugging support, launches and manages Actions instances.

3. If you are planning to install Actions on Kubernetes using it's CLI, download the [release](https://github.com/actionscore/cli/releases) for your OS, unpack it and move it to your desired location (for Mac/Linux - ```mv actions /usr/local/bin```. For Windows, add the executable to your System PATH.)

__*Note: For Windows users, run the cmd terminal in administrator mode*__

### Install on Kubernetes using Actions CLI

To setup Actions on Kubernetes:

```
$ actions init --kubernetes
⌛  Making the jump to hyperspace...
✅  Success! Get ready to rumble
```

### Install on Kubernetes using Helm (Advanced)

**Note**: This "how-to" is a light version of our more detailed README on how to install Actions on Kubernetes using Helm. For the detailed version, go [here](https://github.com/actionscore/actions/blob/master/charts/actions-operator/README.md).

Actions' Helm chart is hosted in an [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/),
which does not yet support anonymous access to charts therein. Until this is
resolved, adding the Helm repository from which Actions can be installed requires
use of a shared set of read-only credentials.

Make sure helm is initialized in your running kubernetes cluster.

For more details on initializing helm, go [here](https://docs.helm.sh/helm/#helm)

1. Add Azure Container Registry as an helm repo
    ```
    helm repo add actionscore https://actionscore.azurecr.io/helm/v1/repo \
    --username 390401a7-d7a6-46da-b10f-3ceff7a1cdd5 \
    --password 485b3522-59bb-4152-8938-ca8b90108af6
    ```

2. Install the Actions chart on your cluster in the actions-system namespace:
    ```
    helm install actionscore/actions-operator --name actions --namespace actions-system
    ``` 

### Verify installation

Once the chart installation is done, verify the Actions operator pods are running in the `actions-system` namespace:
```
kubectl get pods --namespace actions-system
```
 
![actions_helm_success](/charts/actions-operator/img/actions_helm_success.png)

### (Optional) Installing Redis as Actions state store on Kubernetes using Helm

By itself, Actions installation does not include a state store. 
For getting a state store up and running on your Kubernetes cluster in a swift manner, we recommend installing Redis with Helm using the following command:
```
helm install stable/redis --set rbac.create=true
```   

## Samples

For more samples, please look at [samples](./samples).
* [Run Actions Locally](samples/1.hello-world)
* [Run Actions in Kubernetes](samples/2.hello-kubernetes)


## Developing Actions

### Prerequisites

1. The Go language environment [(instructions)](https://golang.org/doc/install).
   * Make sure that your GOPATH and PATH are configured correctly
   ```bash
   export GOPATH=~/go
   export PATH=$PATH:$GOPATH/bin
   ```
1. [Delve](https://github.com/go-delve/delve/tree/master/Documentation/installation) for Debugging
2. *(for windows)* [Git for Windows](https://gitforwindows.org) and [MinGW](http://www.mingw.org/)
     * Install [Git with chocolatey](https://chocolatey.org/packages/git) and ensure that Git bin directory is in PATH environment variable
    ```bash
    choco install git -y --package-parameters="/GitAndUnixToolsOnPath /WindowsTerminal /NoShellIntegration"
    ```
      * Install [MinGW with chocolatey](https://chocolatey.org/packages/mingw) and ensure that MinGW bin directory is in PATH environment variable

    ```bash
    choco install mingw
    ```

### Clone the repo

```bash
cd $GOPATH/src
mkdir -p github.com/actionscore/actions
git clone https://github.com/actionscore/actions.git github.com/actionscore/actions
```

### Build the Actions binary

You can build actions binaries via `make` tool and find the binaries in `./dist/{os}_{arch}/release/`.

> Note : for windows environment with MinGW, use `mingw32-make.exe` instead of `make`.

* Build for your current local environment

```bash
cd $GOPATH/src/github.com/actionscore/actions/
make build
```

* Cross compile for multi platforms

```bash
make build GOOS=linux GOARCH=amd64
```

### Run unit-test

```bash
make test
```

### Debug actions

We highly recommend to use [VSCode with Go plugin](https://marketplace.visualstudio.com/items?itemName=ms-vscode.Go) for your productivity. If you want to use the different editors, you can find the [list of editor plugins](https://github.com/go-delve/delve/blob/master/Documentation/EditorIntegration.md) for Delve.

This section introduces how to start debugging with Delve CLI. Please see [Delve documentation](https://github.com/go-delve/delve/tree/master/Documentation) for the detail usage.

#### Start with debugger

```bash
$ cd $GOPATH/src/github.com/actionscore/actions/actionsrt
$ dlv debug .
Type 'help' for list of commands.
(dlv) break main.main
(dlv) continue
```

#### Attach Debugger to running binary

This is useful to debug actions when the process is running.

1. Build actions binaries for debugging
   With `DEBUG=1` option, actions binaries will be generated without code optimization in `./dist/{os}_{arch}/debug/`

```bash
$ make DEBUG=1 build
```

2. Create component yaml file under `./dist/{os}_{arch}/debug/components` e.g. statstore component yaml
3. Run actions runtime
4. Find the process id and attach the debugger

```bash
$ dlv attach [pid]
```

#### Debug unit-tests

```bash
# Specify the package that you want to test
# e.g. debuggin ./pkg/actors
$ dlv test ./pkg/actors
```
