---
title: Exemple de script Azure PowerShell - Supprimer l’application d’un cluster | Microsoft Docs
description: Exemple de script Azure PowerShell - Supprimer une application d’un cluster Service Fabric
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: c8956b8bbbbe6d0928511da3cf75bfd23a76ed21
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621772"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Supprimer une application d’un cluster Service Fabric

Cet exemple de script supprime une instance d’application Service Fabric en cours d’exécution et annule l’inscription d’un type d’application et de la version du cluster.  La suppression de l’instance d’application entraîne également celle de toutes les instances de service en cours d’exécution associées à cette application. Personnalisez les paramètres, le cas échéant. 

Si nécessaire, installez le module Service Fabric PowerShell avec le [Kit de développement logiciel (SDK) Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Supprime une instance d’application Service Fabric en cours d’exécution du cluster.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Annule l’inscription d’un type d’application Service Fabric et de sa version dans le cluster. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Service Fabric PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Vous trouverez des exemples supplémentaires de scripts PowerShell pour Azure Service Fabric sur la page [Exemples Azure PowerShell](../service-fabric-powershell-samples.md).
