---
title: Configurer un environnement de développement Windows pour les applications de maillage Service Fabric | Microsoft Docs
description: Configurez votre environnement de développement Windows afin de pouvoir créer une application de maillage Service Fabric et la déployer dans Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/11/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 96549696013a2dd94741090a0a017b57a3b1e19e
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125159"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-applications"></a>Configuration de votre environnement de développement Windows pour générer des applications Service Fabric

Pour générer et exécuter des applications Azure Service Fabric sur votre machine de développement Windows, installez le runtime, le Kit de développement logiciel (SDK) et les outils Service Fabric.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>Versions du système d’exploitation prises en charge

Les versions de système d’exploitation prises en charge pour le développement sont les suivantes :

* Windows 10 (Enterprise, Professional ou Education)
* Windows Server 2016

## <a name="enable-hyper-v"></a>Activer Hyper-V

Hyper-V doit être activé pour pouvoir créer des applications Service Fabric. 

### <a name="windows-10"></a>Windows 10

Ouvrez PowerShell en tant qu’administrateur et exécutez la commande suivante :

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

Redémarrez votre ordinateur. Pour plus d’informations sur l’activation d’Hyper-V, voir [Installer Hyper-V sous Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

### <a name="windows-server-2016"></a>Windows Server 2016

Ouvrez PowerShell en tant qu’administrateur et exécutez la commande suivante :

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Redémarrez votre ordinateur. Pour plus d’informations sur l’activation d’Hyper-V, voir [Installer le rôle Hyper-V sous Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="visual-studio"></a>Visual Studio

Visual Studio 2017 est requis pour déployer des applications Service Fabric. [Installez la version 15.6.0][download-visual-studio] ou ultérieure et activez les charges de travail suivantes :

- Développement web et ASP.NET
- Développement Azure

## <a name="docker"></a>Docker

Installez Docker pour prendre en charge les applications Service Fabric en conteneur utilisées par Service Fabric Mesh.

### <a name="windows-10"></a>Windows 10

Téléchargez et installez la dernière version de [Docker Community Edition pour Windows][download-docker]. 

Pendant l’installation, sélectionnez **Utiliser des conteneurs Windows au lieu des conteneurs Linux** lorsque vous y êtes invité. Vous devrez vous déconnecter puis vous reconnecter. Après vous être reconnecté, si vous n’aviez pas encore activé Hyper-V, vous pouvez être invité à le faire. Activez Hyper-V et redémarrez votre ordinateur.

Après avoir redémarré votre ordinateur, Docker vous invitera à activer la fonctionnalité **Conteneurs**. Activez-la et redémarrez votre ordinateur.

### <a name="windows-server-2016"></a>Windows Server 2016

Utilisez les commandes PowerShell suivantes pour installer Docker. Pour plus d’informations, voir [Docker Enterprise Edition pour Windows Server][download-docker-server].

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

Redémarrez votre ordinateur.

## <a name="sdk-and-tools"></a>Kit de développement logiciel (SDK) et outils

Installez le runtime, le kit de développement logiciel (SDK) et les outils Service Fabric Mesh dans leur ordre dépendant.

1. Installez le [Kit de développement logiciel (SDK) Service Fabric Mesh][download-sdkmesh] à l’aide de Web Platform Installer. Cela installera également le Kit de développement logiciel (SDK) et le runtime Microsoft Azure Service Fabric.
2. Installez [l’extension Visual Studio Service Fabric Tools (préversion)][download-tools] à partir de Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Générer un cluster

Pour obtenir de meilleurs performances de débogage lorsque vous créez et exécutez des applications Service Fabric, nous vous recommandons de créer un cluster de développement local à nœud unique. Ce cluster doit être en cours d’exécution chaque fois que vous déployez ou déboguez un projet Service Fabric Mesh.

Docker **doit** être en cours d’exécution pour que vous puissiez générer un cluster. Vérifiez que Docker est en cours d’exécution en ouvrant une fenêtre de terminal et en exécutant `docker ps` pour voir si une erreur se produit. Si la réponse n’indique aucune erreur, Docker est en cours d’exécution et vous êtes prêt à générer un cluster.

Après avoir installé le runtime, les kits de développement logiciel et les outils Visual Studio, créez un cluster de développement.

1. Fermez votre fenêtre PowerShell.
2. Ouvrez une nouvelle fenêtre PowerShell avec élévation de privilèges en tant qu’administrateur. Cette étape est nécessaire pour charger les modules Service Fabric que vous avez installés.
3. Exécutez la commande PowerShell suivante pour créer un cluster de développement :

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster -UseMachineName
    ```

4. Pour démarrer le gestionnaire de cluster local, exécutez la commande PowerShell suivante :

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```

Vous êtes maintenant prêt à créer des applications Service Fabric Mesh !

## <a name="next-steps"></a>Étapes suivantes

Consultez le didacticiel [Créer une application Azure Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.ServiceFabricMesh
[download-visual-studio]: https://www.visualstudio.com/downloads/
