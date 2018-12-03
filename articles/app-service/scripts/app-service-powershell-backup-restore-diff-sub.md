---
title: 'Exemple de script Azure PowerShell : Restaurer une application web à partir d’une sauvegarde dans un autre abonnement | Microsoft Docs'
description: 'Exemple de script Azure PowerShell : Restaurer une application web à partir d’une sauvegarde dans un autre abonnement'
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jpconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 11/21/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 7ed593e408b57246ef155ff8e36f054aacb2e063
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291577"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription"></a>Restaurer une application web à partir d’une sauvegarde dans un autre abonnement

Cet exemple de script récupère une sauvegarde réalisée à partir d’une application web existante, et restaure celle-ci dans un autre abonnement. 

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](/powershell/azure/overview), puis exécutez `Connect-AzureRmAccount` pour créer une connexion avec Azure. 

## <a name="sample-script"></a>Exemple de script

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Si vous n’avez plus besoin de l’application web, utilisez la commande suivante pour supprimer le groupe de ressources, l’application web ainsi que toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) | Ajoute le compte authentifié à utiliser pour les requêtes d’applet de commande Azure Resource Manager.  |
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Obtient une liste des sauvegardes d’une application web. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Crée une application web |
| [Restore-AzureRmWebAppBackup](/powershell/module/azurerm.websites/restore-azurermwebappbackup) | Restaure une application web à partir d’une sauvegarde. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez la [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure App Service Web Apps sur la page [Azure PowerShell Samples](../app-service-powershell-samples.md) (Exemples Azure PowerShell).
