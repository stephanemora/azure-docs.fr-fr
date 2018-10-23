---
title: Configurer un environnement de développement Windows pour créer des applications Service Fabric Mesh | Microsoft Docs
description: Configurez votre environnement de développement Windows afin de pouvoir créer une application de maillage Service Fabric et la déployer dans Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 08/08/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 094cab324c7a7c66f5fcb57d488661109fcdabea
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362668"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Configurer votre environnement de développement Windows pour créer des applications Service Fabric Mesh

Pour créer et exécuter des applications Azure Service Fabric Mesh sur votre ordinateur de développement Windows, installez le runtime, le Kit de développement logiciel (SDK) et les outils Service Fabric Mesh.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>Versions du système d’exploitation prises en charge

Les versions de système d’exploitation prises en charge pour le développement sont les suivantes :

* Windows 10 (Enterprise, Professional ou Education)
* Windows Server 2016

## <a name="visual-studio"></a>Visual Studio

Visual Studio 2017 est requis pour déployer des applications Service Fabric Mesh. [Installez la version 15.6.0][download-visual-studio] ou ultérieure et activez les charges de travail suivantes :

* Développement web et ASP.NET
* Développement Azure

## <a name="install-docker"></a>Installation de Docker

#### <a name="windows-10"></a>Windows 10

Téléchargez et installez la dernière version de [Docker Community Edition pour Windows][download-docker] pour prendre en charge les applications Service Fabric en conteneur utilisées par Service Fabric Mesh.

Pendant l’installation, sélectionnez **Utiliser des conteneurs Windows au lieu des conteneurs Linux** lorsque vous y êtes invité.

Si Hyper-V n’est pas activé sur votre ordinateur, le programme d’installation de Docker propose de l’activer. Pour ce faire, cliquez sur **OK** à l’invite.

#### <a name="windows-server-2016"></a>Windows Server 2016

Si le rôle Hyper-V n’est pas activé, ouvrez PowerShell en tant qu’administrateur et exécutez la commande suivante pour activer Hyper-V, puis redémarrez votre ordinateur. Pour plus d’informations, voir [Docker Enterprise Edition pour Windows Server][download-docker-server].

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Redémarrez votre ordinateur.

Ouvrez PowerShell en tant qu’administrateur et exécutez la commande suivante pour installer Docker :

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>Kit de développement logiciel (SDK) et outils

Installez le runtime, le kit de développement logiciel (SDK) et les outils Service Fabric Mesh dans l’ordre suivant.

1. Installez le [Kit de développement logiciel (SDK) Service Fabric Mesh][download-sdkmesh] à l’aide de Web Platform Installer. Cela installera également le Kit de développement logiciel (SDK) et le runtime Microsoft Azure Service Fabric.
2. Installez [l’extension Visual Studio Service Fabric Mesh Tools (préversion)][download-tools] à partir de Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Générer un cluster

Si vous utilisez Visual Studio, vous pouvez ignorer cette section, car Visual Studio crée un cluster local pour vous si vous n’en avez pas.

Pour obtenir de meilleurs performances de débogage lorsque vous créez et exécutez des applications Service Fabric, nous vous recommandons de créer un cluster de développement local à nœud unique. Ce cluster doit être en cours d’exécution chaque fois que vous déployez ou déboguez un projet Service Fabric Mesh.

Docker **doit** être en cours d’exécution pour que vous puissiez générer un cluster. Vérifiez que Docker est en cours d’exécution en ouvrant une fenêtre de terminal et en exécutant `docker ps` pour voir si une erreur se produit. Si la réponse n’indique aucune erreur, Docker est en cours d’exécution et vous êtes prêt à générer un cluster.

Après avoir installé le runtime, les kits de développement logiciel et les outils Visual Studio, créez un cluster de développement.

1. Fermez votre fenêtre PowerShell.
2. Ouvrez une nouvelle fenêtre PowerShell avec élévation de privilèges en tant qu’administrateur. Cette étape est nécessaire pour charger les modules Service Fabric récemment installés.
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