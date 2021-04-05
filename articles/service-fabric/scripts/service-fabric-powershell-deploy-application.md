---
title: Déployer une application sur un cluster dans PowerShell
description: Exemple de script Azure PowerShell - Déployez une application sur un cluster Service Fabric.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 1d25ede5ae871eddd965594224b518ec42525451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791288"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Déployer une application sur un cluster Service Fabric

Cet exemple de script copie un package d’application dans le magasin d’images d’un cluster, inscrit le type d’application dans le cluster, supprime le package d’application inutile et crée une instance d’application à partir du type d’application.  Si des services par défaut ont été définis dans le manifeste d’application du type d’application cible, ils sont également créés à ce stade. Personnalisez les paramètres selon vos besoins. 

Si nécessaire, installez le module Service Fabric PowerShell avec le [Kit de développement logiciel (SDK) Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Après avoir exécuté l’exemple de script, vous pouvez utiliser le script présenté dans l’article [Supprimer une application](service-fabric-powershell-remove-application.md) pour supprimer l’instance d’application, désinscrire le type d’application et supprimer le package d’application du magasin d’images.

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster)| Crée une connexion à un cluster Service Fabric. |
|[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) | Copie un package d’application dans le magasin d’images du cluster.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype)| Inscrit un type d’application et la version associée sur le cluster. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication)| Crée une application à partir d’un type d’application inscrit. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage) | Supprime un package d’application Service Fabric du magasin d’images.|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Service Fabric PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/service-fabric/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell pour Azure Service Fabric sur la page [Exemples Azure PowerShell](../service-fabric-powershell-samples.md).
