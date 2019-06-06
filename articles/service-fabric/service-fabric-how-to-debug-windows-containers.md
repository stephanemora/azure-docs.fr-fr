---
title: Déboguer des conteneurs Windows avec Service Fabric et VS | Microsoft Docs
description: Découvrez comment déboguer les conteneurs Windows dans Azure Service Fabric à l’aide de Visual Studio 2019.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo, mikhegn
ms.openlocfilehash: 15f288d5400b49ec05c9ffb936fd2097cc61bae8
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428152"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Activation Déboguer les conteneurs Windows dans Azure Service Fabric à l’aide de Visual Studio 2019

Avec Visual Studio 2019, vous pouvez déboguer des applications .NET dans des conteneurs en tant que services Service Fabric. Cet article explique comment configurer votre environnement, puis déboguer une application .NET dans un conteneur exécuté dans un cluster Service Fabric local.

## <a name="prerequisites"></a>Conditions préalables

* Dans Windows 10, suivez ce guide de démarrage rapide pour [configurer Windows 10 pour exécuter des conteneurs Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* Dans Windows Server 2016, suivez ce guide de démarrage rapide pour [configurer Windows 2016 pour exécuter des conteneurs Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Configurer votre environnement Service Fabric local en suivant [Préparer votre environnement de développement sur Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Configurer votre environnement de développement pour déboguer des conteneurs

1. Vérifiez que le Docker pour le service Windows est en cours d’exécution avant de procéder à l’étape suivante.

1. Pour prendre en charge la résolution DNS entre des conteneurs, vous devrez configurer votre cluster de développement local, à l’aide du nom de l’ordinateur. Ces étapes sont également nécessaires si vous voulez adresser des services via le proxy inverse.
   1. Ouvrez PowerShell en tant qu’administrateur
   2. Accédez au dossier d’installation Cluster du SDK, qui est en général `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`.
   3. Exécuter le script `DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > Vous pouvez utiliser `-CreateOneNodeCluster` pour configurer un cluster à un nœud. La configuration par défaut crée un cluster local à cinq nœuds.
      >

      Pour en savoir plus sur le service DNS dans Service Fabric, consultez [Service DNS dans Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice). Pour plus d’informations sur l’utilisation du proxy inverse Service Fabric à partir de services s’exécutant dans un conteneur, consultez [Gestion spéciale du proxy inverse pour les services s’exécutant dans des conteneurs](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Limitations connues lors du débogage de conteneurs dans Service Fabric

Voici une liste des limitations connues du débogage de conteneurs dans Service Fabric et des résolutions possibles :

* En utilisant localhost pour ClusterFQDNorIP ne prend en charge la résolution DNS dans des conteneurs.
    * Résolution : Configurez le cluster local avec le nom de la machine (voir ci-dessus).
* Windows 10 en cours d’exécution sur une Machine virtuelle n’obtiendront pas réponse DNS pour le conteneur.
    * Résolution : Désactivez le déchargement de la somme de contrôle UDP pour IPv4 sur la carte réseau des machines virtuelles.
    * En cours d’exécution Windows 10 dégrader les performances réseau sur l’ordinateur.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Résoudre des services dans la même application à l’aide de DNS nom du service ne fonctionne pas sur Windows 10, si l’application a été déployée à l’aide de Docker Compose
    * Résolution : Utilisez servicename.applicationname pour résoudre les points de terminaison du service
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Si vous utilisez IP-address pour ClusterFQDNorIP, le changement de l’adresse IP principale sur l’hôte entraîne l’arrêt de la fonctionnalité DNS.
    * Résolution : Recréez le cluster avec la nouvelle adresse IP principale sur l’hôte ou utilisez le nom de la machine. Cette rupture est normal.
* Si le nom de domaine complet du cluster a été créé avec n’est pas résolu sur le réseau, DNS échoue.
    * Résolution : Recréez le cluster local avec l’adresse IP principale de l’hôte. Cet échec est normal.
* Lors du débogage d’un conteneur, les journaux d’activité de docker sont disponibles uniquement dans la fenêtre de sortie de Visual Studio, ils ne le sont pas via les API Service Fabric, ni non plus dans Service Fabric Explorer.

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Déboguer une application .NET exécutée dans des conteneurs docker sur Service Fabric

1. Exécutez Visual Studio en tant qu’administrateur.

1. Ouvrez une application .NET existante ou créez-en une.

1. Cliquez avec le bouton droit sur le projet et sélectionnez **Ajouter -> Prise en charge des orchestrateurs de conteneurs -> Service Fabric**

1. Appuyez sur **F5** pour lancer le débogage de l’application.

    Visual Studio prend en charge la console et les types de projets ASP.NET pour .NET et .NET Core.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les fonctionnalités de Service Fabric et conteneurs, consultez overview](service-fabric-containers-overview.md) de conteneurs Service Fabric.
