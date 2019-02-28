---
title: "Exemple de script Azure PowerShell : Restaurer une sauvegarde d'application dans un autre abonnement | Microsoft Docs"
description: 'Exemple de script Azure PowerShell : Restaurer une application web à partir d’une sauvegarde dans un autre abonnement'
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jpconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: e8879b67b79523cc35fb28a707a6c77f3efb75c0
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56584977"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>Restaurer une application web à partir d’une sauvegarde dans un autre abonnement à l'aide de PowerShell

Cet exemple de script récupère une sauvegarde réalisée à partir d’une application web existante, et restaure celle-ci dans un autre abonnement. 

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](/powershell/azure/overview), puis exécutez `Connect-AzAccount` pour créer une connexion avec Azure. 

## <a name="sample-script"></a>Exemple de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Si vous n’avez plus besoin de l’application web, utilisez la commande suivante pour supprimer le groupe de ressources, l’application web ainsi que toutes les ressources associées.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Add-AzAccount](/powershell/module/az.accounts/connect-azaccount) | Ajoute le compte authentifié à utiliser pour les requêtes d’applet de commande Azure Resource Manager.  |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Obtient une liste des sauvegardes d’une application web. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Crée une application web |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Restaure une application web à partir d’une sauvegarde. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez la [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure App Service Web Apps sur la page [Azure PowerShell Samples](../samples-powershell.md) (Exemples Azure PowerShell).
