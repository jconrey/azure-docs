---
title: Set up your development environment on Linux | Microsoft Docs
description: Install the runtime and SDK and create a local development cluster on Linux. After completing this setup, you will be ready to build applications.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''

ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6

ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar

---
# Prepare your development environment on Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

To deploy and run [Azure Service Fabric applications](service-fabric-application-model.md) on your Linux development machine, install the runtime and common SDK. You can also install optional SDKs for Java and .NET Core development. 

The steps in this article assume that you are installing natively on Linux or are using the Service Fabric OneBox container image, `microsoft/service-fabric-onebox`. 

Installing the Service Fabric runtime and SDK on Windows Subsystem for Linux is not supported. However, the Azure Service Fabric command-line interface (CLI), which enables you to manage Service Fabric entities hosted elsewhere in the cloud or on-premises, is supported. For information about how to install the CLI, see [Set up the Service Fabric CLI](./service-fabric-cli.md).


## Prerequisites

* The following operating system versions are supported for development:

    * Ubuntu 16.04 (`Xenial Xerus`)

      * Ensure that the `apt-transport-https` package is installed:
         
         ```bash
         sudo apt-get install apt-transport-https
         ```
    * Red Hat Enterprise Linux 7.4 (Service Fabric preview support)


## Installation Methods

### 1. Script installation (Ubuntu)

A script is provided for convenience for installing the Service Fabric runtime and the Service Fabric common SDK along with **sfctl** CLI. Run the manual installation steps in the next section to determine what is being installed and the licenses that are being agreed to. Running the script assumes you agree to the licenses for all the software that is being installed. 

After the script is executed successfully, you can directly skip to [Set up a local cluster](#set-up-a-local-cluster).

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

### 2. Manual Installation
For manual installation of Service Fabric runtime and common SDK, follow the rest of this guide.

## Update your APT sources/Yum Repositories
To install the SDK and the associated runtime package via the apt-get command-line tool, you must first update your Advanced Packaging Tool (APT) sources.

### Ubuntu

1. Open a terminal.
2. Add the Service Fabric repo to your sources list.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Add the `dotnet` repo to your sources list.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Add the new Gnu Privacy Guard (GnuPG, or GPG) key to your APT keyring.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Add the official Docker GPG key to your APT keyring.

    ```bash
    sudo apt-get install curl
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Set up the Docker repository.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. Refresh your package lists based on the newly added repositories.

    ```bash
    sudo apt-get update
    ```


### Red Hat Enterprise Linux 7.4 (Service Fabric preview support)

1. Open a terminal.
2. Download and install Extra Packages for Enterprise Linux(EPEL).

    ```bash
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sudo yum install epel-release-latest-7.noarch.rpm
    ```
3. Add EfficiOS RHEL7 package repository to your system.

    ```bash
    sudo wget -P /etc/yum.repos.d/ https://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
    ```

4. Import the efficios package signing key to the local GPG keyring.

    ```bash
    sudo rpmkeys --import https://packages.efficios.com/rhel/repo.key
    ```

5. Add Microsoft RHEL repository to your system.

    ```bash
    curl https://packages.microsoft.com/config/rhel/7.4/prod.repo > ./microsoft-prod.repo
    sudo cp ./microsoft-prod.repo /etc/yum.repos.d/
    ```

6. Install dotnet sdk.

    ```bash
    yum install rh-dotnet20 -y
    ```

## Install and set up the Service Fabric SDK for local cluster setup

After you have updated your sources, you can install the SDK. Install the Service Fabric SDK package, confirm the installation, and agree to the license agreement.

### Ubuntu

```bash
sudo apt-get install servicefabricsdkcommon
```

>   [!TIP]
>   The following commands automate accepting the license for Service Fabric packages:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

### Red Hat Enterprise Linux 7.4 (Service Fabric preview support)

```bash
sudo yum install servicefabricsdkcommon
```

The Service Fabric runtime which comes with the above installation includes the packages in the table below. 

 | | DotNetCore | Java | Python | NodeJS | 
--- | --- | --- | --- |---
Ubuntu | 2.0.0 | OpenJDK 1.8 | Implicit from npm | latest |
RHEL | - | OpenJDK 1.8 | Implicit from npm | latest |

## Set up a local cluster
  Once the installation completes, you should be able to start a local cluster.

  1. Run the cluster setup script.

      ```bash
      sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
      ```

  2. Open a web browser and go to [Service Fabric Explorer](http://localhost:19080/Explorer) (`http://localhost:19080/Explorer`). If the cluster has started, you should see the Service Fabric Explorer dashboard. It may take several minutes for the cluster to completely set up. If your browser fails to open the URL or if Service Fabric Explorer does not show the system ready, wait a few minutes and try again.

      ![Service Fabric Explorer on Linux][sfx-linux]

  At this point, you can deploy pre-built Service Fabric application packages or new ones based on guest containers or guest executables. To build new services by using the Java or .NET Core SDKs, follow the optional setup steps that are provided in subsequent sections.


  > [!NOTE]
  > Standalone clusters aren't supported in Linux.
  >


>   [!TIP]
    If you have an SSD disk avaiable, it is recommended to pass an SSD folder path using `--clusterdataroot` with devclustersetup.sh for superior performance.

## Set up the Service Fabric CLI

The [Service Fabric CLI](service-fabric-cli.md) has commands for interacting with Service Fabric entities,
including clusters and applications.
Follow the instructions at [Service Fabric CLI](service-fabric-cli.md) to install the CLI.


## Set up Yeoman generators for containers and guest executables
Service Fabric provides scaffolding tools that help you create Service Fabric applications from a terminal using Yeoman template generators. Follow these steps to set up the Service Fabric Yeoman template generators:

1. Install nodejs and NPM on your machine

Ubuntu
  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```

Red Hat Enterprise Linux 7.4 (Service Fabric preview support)
  ```bash
  sudo yum install nodejs
  sudo yum install npm
  ```
2. Install [Yeoman](http://yeoman.io/) template generator on your machine from NPM

  ```bash
  sudo npm install -g yo
  ```
3. Install the Service Fabric Yeo container generator and guest executable generator from NPM

  ```bash
  sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
  sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
  ```

After you have installed the generators, you should be able to create guest executable or container services by running `yo azuresfguest` or `yo azuresfcontainer`, respectively.

## Set up .NET Core 2.0 development

Install the [.NET Core 2.0 SDK for Ubuntu](https://www.microsoft.com/net/core#linuxubuntu) to start [creating C# Service Fabric applications](service-fabric-create-your-first-linux-application-with-csharp.md). Packages for .NET Core 2.0 Service Fabric applications are hosted on NuGet.org, currently in preview.

## Set up Java development

To build Service Fabric services using Java, install JDK 1.8 and Gradle to run build tasks. The following snippet installs Open JDK 1.8 along with Gradle. The Service Fabric Java libraries are pulled from Maven.


Ubuntu 
 ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

Red Hat Enterprise Linux 7.4 (Service Fabric preview support)
  ```bash
  sudo yum install java-1.8.0-openjdk-devel
  curl -s https://get.sdkman.io | bash
  sdk install gradle
  ```
 
## Install the Eclipse plug-in (optional)

You can install the Eclipse plug-in for Service Fabric from within the Eclipse IDE for Java Developers or Java EE Developers. You can use Eclipse to create Service Fabric guest executable applications and container applications in addition to Service Fabric Java applications.

> [!IMPORTANT]
> The  Service Fabric plug-in requires Eclipse Neon or a later version. See the instructions that follow this note for how to check your version of Eclipse. If you have an earlier version of Eclipse installed, you can download more recent versions from the [Eclipse site](https://www.eclipse.org). It is not recommended that you install on top of (overwrite) an existing installation of Eclipse. You can either remove it before running the installer or install the newer version in a different directory. 
> 
> On Ubuntu, we recommend installing directly from the Eclipse site rather than using a package installer (`apt` or `apt-get`). Doing so ensures that you get the most current version of Eclipse. You can install the Eclipse IDE for Java Developers or for Java EE Developers.

1. In Eclipse, make sure that you have installed Eclipse Neon or later and Buildship version 2.2.1 or later. You can check the versions of installed components by selecting **Help** > **About Eclipse** > **Installation Details**. You can update Buildship by using the instructions at [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update].

2. To install the Service Fabric plug-in, select **Help** > **Install New Software**.

3. In the **Work with** box, type **http://dl.microsoft.com/eclipse**.

4. Click **Add**.

    ![The Available Software page][sf-eclipse-plugin]

5. Select the **ServiceFabric** plug-in, and then click **Next**.

6. Complete the installation steps, and then accept the end-user license agreement.

If you already have the Service Fabric Eclipse plug-in installed, make sure that you have the latest version. You can check by selecting **Help** > **About Eclipse** > **Installation Details** and then searching for Service Fabric in the list of installed plug-ins. If a newer version is available, select **Update**.

For more information, see [Service Fabric plug-in for Eclipse Java application development](service-fabric-get-started-eclipse.md).

## Update the SDK and runtime

To update to the latest version of the SDK and runtime, run the following commands:

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
To update the Java SDK binaries from Maven, you need to update the version details of the corresponding binary in the ``build.gradle`` file to point to the latest version. To know exactly where you need to update the version, you can refer to any ``build.gradle`` file in Service Fabric getting-started samples [here](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> Updating the packages might cause your local development cluster to stop running. Restart your local cluster after an upgrade by following the instructions on this page.

## Remove the SDK
To remove the Service Fabric SDKs, run the following:

### Ubuntu

```bash
sudo apt-get remove servicefabric servicefabicsdkcommon
sudo npm uninstall generator-azuresfcontainer
sudo npm uninstall generator-azuresfguest
sudo apt-get install -f
```


### Red Hat Enterprise Linux 7.4 (Service Fabric preview support)

```bash
sudo yum remote servicefabric servicefabicsdkcommon
sudo npm uninstall generator-azuresfcontainer
sudo npm uninstall generator-azuresfguest
```

## Next steps

* [Create and deploy your first Service Fabric Java application on Linux by using Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Create and deploy your first Service Fabric Java application on Linux by using Service Fabric Plugin for Eclipse](service-fabric-get-started-eclipse.md)
* [Create your first CSharp application on Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Prepare your development environment on OSX](service-fabric-get-started-mac.md)
* [Prepare a Linux development environment on Windows](service-fabric-local-linux-cluster-windows.md)
* [Use the Service Fabric CLI to manage your applications](service-fabric-application-lifecycle-sfctl.md)
* [Service Fabric Windows/Linux differences](service-fabric-linux-windows-differences.md)
* [Automate operating system patching on your Linux cluster](service-fabric-patch-orchestration-application-linux.md)
* [Get started with Service Fabric CLI](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
