---
title: 'PowerShell : Restaurer une sauvegarde automatique d’une base de données dans SQL Database'
description: Utilisez un exemple de script Azure PowerShell pour restaurer une base de données dans SQL Database à un point antérieur dans le temps à partir de sauvegardes automatiques.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: sstein
ms.date: 03/27/2019
ms.openlocfilehash: 231a97a375a46940f461d5783f9d70c701389e78
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792784"
---
# <a name="use-powershell-to-restore-a-database-to-an-earlier-point-in-time"></a>Utiliser PowerShell pour restaurer une base de données à un point antérieur dans le temps

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Cet exemple de script PowerShell restaure une base de données dans SQL Database à un point précis dans le temps.  

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell localement, ce tutoriel nécessite Az PowerShell 1.4.0 ou ultérieur. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="sample-script"></a>Exemple de script

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crée un serveur qui héberge des bases de données et des pools élastiques. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Crée une base de données sur un serveur. |
| [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) | Obtient une sauvegarde géoredondante d’une base de données autonome ou mise en pool. |
| [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) | Restaure une base de données. |
| [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) | Supprime une base de données. |
| [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Obtient une base de données supprimée que vous pouvez restaurer. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/).

Vous trouverez des exemples supplémentaires de scripts SQL Database PowerShell dans [Scripts PowerShell Azure SQL Database](../powershell-script-content-guide.md).