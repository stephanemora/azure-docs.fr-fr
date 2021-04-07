---
title: Mettre à niveau une application Service Fabric dans PowerShell
description: Exemple de script Azure PowerShell - Mettre à niveau et superviser une application Azure Service Fabric à l’aide de PowerShell.
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
ms.openlocfilehash: 7b55dc6a400f936ac23b233e4c84a6b1aebf45cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784695"
---
# <a name="upgrade-a-service-fabric-application"></a>Mettre à niveau une application Service Fabric

Cet exemple de script met à niveau une instance d’application Service Fabric en cours d’exécution vers la version 1.3.0. Le script copie le nouveau package d’application dans le magasin d’images d’un cluster, inscrit le type d’application et supprime le package d’application inutile.  Le script démarre une mise à niveau surveillée et vérifie en continu l’état de la mise à niveau jusqu’à la fin ou la restauration de la mise à niveau. Personnalisez les paramètres selon vos besoins. 

Si nécessaire, installez le module Service Fabric PowerShell avec le [Kit de développement logiciel (SDK) Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) | Récupère toutes les applications du cluster Service Fabric ou une application en particulier.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade) | Récupère l’état d’une mise à niveau d’application Service Fabric. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) | Récupère les types d’applications Service Fabric enregistrés sur le cluster Service Fabric. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) | Annule l’enregistrement d’un type d’application Service Fabric.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) | Copie un package d’application Service Fabric dans le magasin d’images.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) | Enregistre un type d’application Service Fabric. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) | Met à niveau une application Service Fabric vers la version spécifiée du type d’application. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage) | Supprime un package d’application Service Fabric du magasin d’images.|


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Service Fabric PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/service-fabric/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell pour Azure Service Fabric sur la page [Exemples Azure PowerShell](../service-fabric-powershell-samples.md).
