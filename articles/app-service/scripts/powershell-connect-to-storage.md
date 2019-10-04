---
title: Exemple de script Azure PowerShell - Connecter une application à un compte de stockage | Microsoft Docs
description: Exemple de script Azure PowerShell - Connecter une application App Service à un compte de stockage
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: e4831bdc-2068-4883-9474-0b34c2e3e255
ms.service: app-service
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 1eae5c4b94a59b09e73454fdfcea4e9f041652f2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098353"
---
# <a name="connect-an-app-service-app-to-a-storage-account"></a>Connecter une application App Service à un compte de stockage

Dans ce scénario, vous allez apprendre à créer un compte de stockage Azure et une application App Service. Ensuite, vous allez lier le compte de stockage à l’application en vous appuyant sur les paramètres de l’application.

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](/powershell/azure/overview), puis exécutez `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="sample-script"></a>Exemple de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-storage/connect-to-storage.ps1 "Connect an app to a storage account")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Une fois l’exemple de script exécuté, vous pouvez utiliser la commande suivante pour supprimer le groupe de ressources, l’application App Service et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Crée un plan App Service. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Crée une application App Service. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Crée un compte de stockage. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Obtient les clés d’accès pour le compte Azure Storage. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Modifie la configuration d’une application App Service. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure App Service sur la page [Exemples Azure PowerShell](../samples-powershell.md).
