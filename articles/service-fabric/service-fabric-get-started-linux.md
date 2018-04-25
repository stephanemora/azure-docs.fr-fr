---
title: Configurer votre environnement de développement sur Linux | Microsoft Docs
description: Installez le runtime et le Kit de développement logiciel (SDK), puis créez un cluster de développement local sur Linux. Une fois l’installation terminée, vous serez prêt à créer des applications.
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
ms.openlocfilehash: cf678eac16f8b13c5ffaa1d5673ca1cb47440cf9
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2018
---
# <a name="prepare-your-development-environment-on-linux"></a>Préparer votre environnement de développement sur Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Pour déployer et exécuter des [applications Azure Service Fabric](service-fabric-application-model.md) sur votre ordinateur de développement Linux, installez le runtime et le Kit de développement logiciel (SDK) courant. Vous pouvez également installer des Kits de développement logiciel (SDK) facultatifs pour le développement de Java et .NET Core. 

La procédure décrite dans cet article suppose que vous effectuez une installation en mode natif sur Linux ou que vous utilisez l’image conteneur OneBox Service Fabric, `microsoft/service-fabric-onebox`. 

L’installation du runtime Service Fabric et du Kit de développement logiciel (SDK) sur le sous-système Windows pour Linux n’est pas prise en charge. Toutefois, l’interface de ligne de commande Azure Service Fabric, qui vous permet de gérer les entités Service Fabric hébergées ailleurs dans le cloud ou en local, est prise en charge. Pour plus d’informations sur l’installation de l’interface de ligne de commande, consultez [Interface de ligne de commande Azure Service Fabric](./service-fabric-cli.md).


## <a name="prerequisites"></a>Prérequis


* Les versions de système d’exploitation prises en charge pour le développement sont les suivantes :

    * Ubuntu 16.04 (`Xenial Xerus`)

      * Vérifiez que le package `apt-transport-https` est installé :
         
         ```bash
         sudo apt-get install apt-transport-https
         ```
    * Red Hat Enterprise Linux 7.4 (prise en charge de la préversion de Service Fabric)


## <a name="installation-methods"></a>Méthodes d’installation

### <a name="1-script-installation-ubuntu"></a>1. Installation de script (Ubuntu)

Un script est fourni pour faciliter l’installation du runtime Service Fabric et du Kit de développement logiciel (SDK) Service Fabric courant avec l’interface de ligne de commande **sfctl**. Exécutez les étapes d’installation indiquées dans la section suivante afin de déterminer quels éléments sont en cours d’installation, et les licences qui sont acceptées. L’exécution du script suppose que vous acceptez les licences de tous les logiciels en cours d’installation. 

Une fois le script correctement exécuté, vous pouvez passer directement à la section [Configurer un cluster local](#set-up-a-local-cluster).

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

### <a name="2-manual-installation"></a>2. Installation manuelle
Suivez le reste de ce guide pour l’installation manuelle du runtime Service Fabric et du Kit de développement logiciel (SDK) courant.

## <a name="update-your-apt-sourcesyum-repositories"></a>Mise à jour de vos sources APT/référentiels Yum
Pour installer le Kit de développement logiciel (SDK) et le package runtime associé via l’outil apt-get command-line, vous devez d’abord mettre à jour les sources Advanced Packaging Tool (APT).

### <a name="ubuntu"></a>Ubuntu

1. Ouvrez un terminal.
2. Ajoutez le référentiel Service Fabric à votre liste de sources.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Ajoutez le référentiel `dotnet` à votre liste de sources.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Ajoutez la nouvelle clé de protection de la confidentialité Gnu (GnuPG ou GPG) à votre porte-clés APT.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Ajoutez les clés GPG Docker officielles à votre porte-clés APT.

    ```bash
    sudo apt-get install curl
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Configurer le référentiel de Docker.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. Actualisez vos listes de packages selon les référentiels nouvellement ajoutés.

    ```bash
    sudo apt-get update
    ```


### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (prise en charge de la préversion de Service Fabric)

1. Ouvrez un terminal.
2. Téléchargez et installez Extra Packages for Enterprise Linux (EPEL).

    ```bash
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sudo yum install epel-release-latest-7.noarch.rpm
    ```
3. Ajoutez un référentiel de packages EfficiOS RHEL7 à votre système.

    ```bash
    sudo wget -P /etc/yum.repos.d/ https://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
    ```

4. Importez la clé de signature du package EfficiOS dans le porte-clés GPG local.

    ```bash
    sudo rpmkeys --import https://packages.efficios.com/rhel/repo.key
    ```
5. Ajoutez un référentiel RHEL Microsoft à votre système.
   ```bash
      curl https://packages.microsoft.com/config/rhel/7.4/prod.repo > ./microsoft-prod.repo
      sudo cp ./microsoft-prod.repo /etc/yum.repos.d/
   ```
6. Installez le Kit de développement logiciel (SDK) dotnet.
   ```bash
      yum install rh-dotnet20 -y
      scl enable rh-dotnet20 bash
   ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-local-cluster-setup"></a>Installer et configurer le Kit de développement logiciel (SDK) Service Fabric pour l’installation d’un cluster local

Après avoir mis à jour vos sources, vous pouvez installer le Kit de développement logiciel (SDK). Installez le package SDK Service Fabric, confirmez l’installation et acceptez le contrat de licence.

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get install servicefabricsdkcommon
```

>   [!TIP]
>   Les commandes suivantes automatisent la validation de la licence pour les packages Service Fabric :
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (prise en charge de la préversion de Service Fabric)

```bash
sudo yum install servicefabricsdkcommon
```

Le runtime Service Fabric qui est fourni avec l’installation ci-dessus inclut les packages dans le tableau ci-dessous. 

 | | DotNetCore | Java | Python | NodeJS | 
--- | --- | --- | --- |---
Ubuntu | 2.0.0 | OpenJDK 1.8 | Implicite à partir de npm | le plus récent |
RHEL | - | OpenJDK 1.8 | Implicite à partir de npm | le plus récent |

## <a name="set-up-a-local-cluster"></a>Configurer un cluster local
  Une fois l’installation terminée, vous devez être en mesure de démarrer un cluster local.

  1. Exécutez le script de configuration du cluster.

      ```bash
      sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
      ```

  2. Ouvrez un navigateur web et accédez à [Service Fabric Explorer](http://localhost:19080/Explorer) (`http://localhost:19080/Explorer`). Si le cluster a démarré, vous devez voir le tableau de bord Service Fabric Explorer. La configuration totale du cluster peut prendre plusieurs minutes. Si votre navigateur ne parvient pas à ouvrir l’URL, ou si Service Fabric Explorer n’affiche pas que le système est prêt, patientez quelques minutes, puis réessayez.

      ![Service Fabric Explorer sur Linux][sfx-linux]

  À ce stade, vous pouvez déployer des packages d’application Service Fabric préconfigurés ou de nouveaux packages basés sur des conteneurs invités ou des exécutables invités. Pour générer de nouveaux services à l’aide des Kits de développement logiciel (SDK) .NET Core ou Java, suivez les étapes de configuration facultatives ci-dessous.


  > [!NOTE]
  > Les clusters autonomes ne sont pas pris en charge sous Linux.
  >


>   [!TIP]
    Si vous avez un produit de disque SSD disponible, il est recommandé de passer un chemin d’accès du dossier de disque SSD en utilisant `--clusterdataroot` avec devclustersetup.sh pour des performances supérieures.

## <a name="set-up-the-service-fabric-cli"></a>Configurer l’interface de ligne de commande Service Fabric

L’[interface de ligne de commande Service Fabric](service-fabric-cli.md) inclut des commandes permettant d’interagir avec des entités Service Fabric, y compris des clusters et des applications.
Suivez les instructions de [l’interface de ligne de commande Service Fabric](service-fabric-cli.md) pour installer l’interface de ligne de commande.


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Configurer les générateurs Yeoman pour des conteneurs et des exécutables invités
Service Fabric fournit des outils de génération de modèles automatique qui vous aideront à créer des applications Service Fabric à partir d’un terminal, à l’aide des générateurs de modèle Yeoman. Suivez ces étapes pour configurer les générateurs de modèle Yeoman Service Fabric :

1. Installer nodejs et NPM sur votre machine

Ubuntu
  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```

Red Hat Enterprise Linux 7.4 (prise en charge de la préversion de Service Fabric)
  ```bash
  sudo yum install nodejs
  sudo yum install npm
  ```
2. Installer le générateur de modèles [Yeoman](http://yeoman.io/) sur votre machine à partir de NPM

  ```bash
  sudo npm install -g yo
  ```
3. Installer le générateur de conteneurs Service Fabric Yeo et le générateur d’exécutables invité à partir de NPM

  ```bash
  sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
  sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
  ```

Après avoir installé les générateurs, vous devez être en mesure de créer un exécutable invité ou des services de conteneurs en exécutant `yo azuresfguest` ou `yo azuresfcontainer`, respectivement.

## <a name="set-up-net-core-20-development"></a>Configurer le développement de .NET Core 2.0

Installez le [Kit de développement logiciel (SDK) .NET Core 2.0 pour Ubuntu](https://www.microsoft.com/net/core#linuxubuntu) pour commencer à [créer des applications Service Fabric C#](service-fabric-create-your-first-linux-application-with-csharp.md). Les packages pour les applications .NET Core 2.0 Service Fabric sont hébergés sur NuGet.org, actuellement en préversion.

## <a name="set-up-java-development"></a>Configurer le développement Java

Installez JDK 1.8 et Gradle pour exécuter des tâches de build afin de générer des services Service Fabric à l’aide de Java. L’extrait de code suivant installe Open JDK 1.8, ainsi que Gradle. Les bibliothèques Java Service Fabric sont extraites à partir de Maven.


Ubuntu 
 ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

Red Hat Enterprise Linux 7.4 (prise en charge de la préversion de Service Fabric)
  ```bash
  sudo yum install java-1.8.0-openjdk-devel
  curl -s https://get.sdkman.io | bash
  sdk install gradle
  ```
 
## <a name="install-the-eclipse-plug-in-optional"></a>Installer le plug-in Eclipse (facultatif)

Vous pouvez installer le plug-in Eclipse pour Service Fabric à partir de l’environnement IDE Eclipse pour les développeurs Java ou Java EE. Vous pouvez utiliser Eclipse pour créer des applications exécutables invités Service Fabric et des applications de conteneur en plus des applications Service Fabric Java.

> [!IMPORTANT]
> Le plug-in Service Fabric nécessite Eclipse Neon ou une version ultérieure. Consultez les instructions qui suivent cette note pour savoir comment vérifier votre version d’Eclipse. Si vous avez installé une version antérieure d’Eclipse, vous pouvez télécharger des versions les plus récentes à partir du [site Eclipse](https://www.eclipse.org). Nous vous déconseillons de procéder à l’installation par dessus (par écrasement) une installation existante d’Eclipse. Vous pouvez soit la supprimer avant d’exécuter le programme d’installation ou installer la nouvelle version dans un répertoire différent. 
> 
> Sur Ubuntu, nous vous recommandons de procéder à l’installation directement depuis le site Eclipse au lieu d’utiliser le programme d’installation d’un package (`apt` ou `apt-get`). Ceci vous assure d’obtenir la version la plus récente d’Eclipse. Vous pouvez installer l’environnement IDE pour les développeurs Java ou Java EE.

1. Dans Eclipse, assurez-vous d’avoir installé Eclipse Neon ou version ultérieure et Buildship version 2.2.1 ou version ultérieure. Vous pouvez vérifier les versions des composants installés en sélectionnant **Aide** > **About Eclipse**(Au sujet d’Eclipse) > **Détails de l’installation**. Vous pouvez mettre à jour Buildship en utilisant les instructions [Eclipse Buildship : plug-ins Eclipse pour Gradle][buildship-update].

2. Pour installer le plug-in Service Fabric, sélectionnez **Aide** > **Installer un nouveau logiciel**.

3. Dans le champ **Work with** (Utiliser), tapez **http://dl.microsoft.com/eclipse**.

4. Cliquez sur **Add**.

    ![La page Logiciels disponibles][sf-eclipse-plugin]

5. Sélectionnez le plug-in **Service Fabric**, puis cliquez sur **Suivant**.

6. Terminez les étapes d’installation, puis acceptez les termes du contrat de licence utilisateur final.

Si le plug-in Eclipse Service Fabric est déjà installé, assurez-vous de disposer de la dernière version. Vous pouvez vérifier en sélectionnant **Aide** > **About Eclipse**(Au sujet d’Eclipse) > **Détails de l’installation** et en recherchant Service Fabric dans la liste des plug-ins installés. Si une version plus récente est disponible, sélectionnez **Mettre à jour**.

Pour plus d’informations, consultez la section [Plug-in Service Fabric pour le développement d’applications Java sous Eclipse](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>Mise à jour du kit de développement logiciel (SDK) et du runtime

Exécutez les commandes suivantes pour mettre à jour vers la dernière version du Kit de développement logiciel (SDK) et du runtime :

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Pour mettre à jour les fichiers binaires du kit de développement logiciel (SDK) Java à partir de Maven, vous devez mettre à jour les détails de la version du fichier binaire correspondant dans le fichier ``build.gradle`` pour pointer vers la version la plus récente. Pour savoir exactement où vous avez besoin de mettre à jour la version, vous pouvez vous référer à tout fichier ``build.gradle`` dans les exemples de prise en main de Service Fabric [ici](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> La mise à jour des packages peut entraîner l’arrêt de votre cluster de développement local. Après la mise à niveau, redémarrez votre cluster local en suivant les instructions présentes sur cette page.

## <a name="remove-the-sdk"></a>Supprimez le Kit de développement logiciel (SDK)
Exécutez la commande suivante pour supprimer les Kits de développement logiciel (SDK) Service Fabric :

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get remove servicefabric servicefabicsdkcommon
sudo npm uninstall generator-azuresfcontainer
sudo npm uninstall generator-azuresfguest
sudo apt-get install -f
```


### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (prise en charge de la préversion de Service Fabric)

```bash
sudo yum remote servicefabric servicefabicsdkcommon
sudo npm uninstall generator-azuresfcontainer
sudo npm uninstall generator-azuresfguest
```

## <a name="next-steps"></a>Étapes suivantes

* [Créer et déployer votre première application Java Service Fabric sous Linux à l’aide de Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Créer et déployer votre première application Java Service Fabric sous Linux à l’aide du plug-in Service Fabric pour Eclipse](service-fabric-get-started-eclipse.md)
* [Create your first Java application on Linux (Créer votre première application Java sur Linux)](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Prepare your development environment on OSX (Préparer votre environnement de développement sur OSX)](service-fabric-get-started-mac.md)
* [Préparer un environnement de développement Linux sur Windows](service-fabric-local-linux-cluster-windows.md)
* [Utilisez l’interface de ligne de commande Service Fabric pour gérer vos applications](service-fabric-application-lifecycle-sfctl.md)
* [Différences entre Service Fabric Windows/Linux](service-fabric-linux-windows-differences.md)
* [Automatiser la mise à jour corrective du système d’exploitation sur votre cluster Linux](service-fabric-patch-orchestration-application-linux.md)
* [Prise en main de l’interface de ligne de commande Service Fabric](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
