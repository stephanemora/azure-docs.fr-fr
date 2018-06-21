---
title: Déboguer des conteneurs Windows avec Service Fabric et VS | Microsoft Docs
description: Découvrez comment déboguer des conteneurs Windows dans Azure Service Fabric à l’aide de Visual Studio 2017.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/14/2018
ms.author: mikhegn
ms.openlocfilehash: bca33fe187668d38d4451b2de5b9e54d86e40ba9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659652"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2017"></a>Guide pratique pour déboguer des conteneurs Windows dans Azure Service Fabric à l’aide de Visual Studio 2017

Avec Visual Studio 2017 Update 7 (15.7), vous pouvez déboguer des applications .NET dans les conteneurs en tant que services Service Fabric. Cet article explique comment configurer votre environnement, puis déboguer une application .NET dans un conteneur exécuté dans un cluster Service Fabric local.

## <a name="prerequisites"></a>Prérequis

* Dans Windows 10, suivez ce guide de démarrage rapide pour [configurer Windows 10 pour exécuter des conteneurs Windows](https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* Dans Windows Server 2016, suivez ce guide de démarrage rapide pour [configurer Windows 2016 pour exécuter des conteneurs Windows](https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Configurer votre environnement Service Fabric local en suivant [Préparer votre environnement de développement sur Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Configurer votre environnement de développement pour déboguer des conteneurs

1. Vérifiez que le Docker pour le service Windows est en cours d’exécution avant de procéder à l’étape suivante.

1. Pour prendre en charge la résolution DNS entre les conteneurs, vous devez configurer votre cluster de développement local avec le nom de la machine.
    1. Ouvrez PowerShell en tant qu’administrateur
    1. Accédez au dossier d’installation Cluster du kit SDK, en général `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`
    1. Exécutez le script `DevClusterSetup.ps1` avec le paramètre `-UseMachineName`

    ``` PowerShell
      C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1 -UseMachineName
    ```

    > [!NOTE]
    > Vous pouvez utiliser `-CreateOneNodeCluster` pour configurer un cluster à un nœud. La configuration par défaut crée un cluster local à cinq nœuds.
    >

    Pour en savoir plus sur le service DNS dans Service Fabric, consultez [Service DNS dans Azure Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-dnsservice).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Limitations connues lors du débogage de conteneurs dans Service Fabric

Voici une liste des limitations connues du débogage de conteneurs dans Service Fabric et des résolutions possibles :

* L’utilisation de localhost pour ClusterFQDNorIP ne prend pas en charge la résolution DNS dans les conteneurs.
    * Résolution : Configurer le cluster local avec le nom de la machine (voir ci-dessus)
* L’exécution de Windows 10 sur une machine virtuelle n’obtient aucune réponse DNS en retour dans le conteneur.
    * Résolution : Désactiver le déchargement de la somme de contrôle UDP pour IPv4 sur la carte réseau des machines virtuelles
    * Notez une dégradation des performances réseau de la machine suite à cela.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* La résolution des services dans la même application en utilisant le nom du service DNS ne fonctionne pas sur Windows 10, si l’application a été déployée à l’aide de Docker Compose
    * Résolution : Utiliser servicename.applicationname pour résoudre les points de terminaison du service
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Si vous utilisez IP-address pour ClusterFQDNorIP, le changement de l’adresse IP principale sur l’hôte entraîne l’arrêt de la fonctionnalité DNS.
    * Résolution : Recréez le cluster avec la nouvelle adresse IP principale sur l’hôte ou utilisez le nom de la machine. C’est normal.
* Si le FQDN avec lequel le cluster a été créé ne peut pas être résolu sur le réseau, DNS échoue.
    * Résolution : Recréez le cluster local avec l’adresse IP principale de l’hôte. C’est normal.
* Lors du débogage d’un conteneur, les journaux de docker sont disponibles uniquement dans la fenêtre de sortie de Visual Studio, ils ne le sont pas via les API Service Fabric, ni non plus dans Service Fabric Explorer.

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Déboguer une application .NET exécutée dans des conteneurs docker sur Service Fabric

1. Exécutez Visual Studio en tant qu’administrateur.

1. Ouvrez une application .NET existante ou créez-en une.

1. Cliquez avec le bouton droit sur le projet et sélectionnez **Ajouter -> Prise en charge des orchestrateurs de conteneurs -> Service Fabric**

1. Appuyez sur **F5** pour lancer le débogage de l’application.

    Visual Studio prend en charge la console et les types de projets ASP.NET pour .NET et .NET Core.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les fonctionnalités de Service Fabric et des conteneurs, suivez ce lien : [Vue d’ensemble des conteneurs Service Fabric](service-fabric-containers-overview.md).