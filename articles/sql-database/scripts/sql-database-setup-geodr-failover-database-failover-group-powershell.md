---
title: Exemple PowerShell - Groupe de basculement de géoréplication d’une instance unique Azure SQL Database | Microsoft Docs
description: Exemple de script Azure PowerShell permettant de configurer un groupe de basculement de géoréplication active pour une base de données unique dans Azure SQL Database et de la basculer.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 421972303ae6d63adf8f4a26af626a13e20a411d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59359114"
---
# <a name="use-powershell-to-configure-an-active-geo-replication-failover-group-for-a-single-database-in-azure-sql-database"></a>Utiliser PowerShell pour configurer un groupe de basculement de géoréplication active pour une base de données unique dans Azure SQL Database

Cet exemple de script PowerShell configure un groupe de basculement de géoréplication active pour une base de données unique et le fait basculer vers le réplica secondaire de la base de données.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell localement, vous aurez besoin d’AZ PowerShell version 1.4.0 ou ultérieure pour suivre ce tutoriel. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="sample-scripts"></a>Exemples de scripts

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-database-failover-group.ps1?highlight=18-21 "Set up failover group for single database")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crée un serveur SQL Database qui héberge des bases de données uniques et des pools élastiques. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Crée un pool élastique. |
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Met à jour les propriétés de la base de données ou déplace une base de données vers, hors ou entre des pools élastiques. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary)| Crée une base de données secondaire pour une base de données existante puis lance la réplication des données. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)| Obtient une ou plusieurs bases de données. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary)| Bascule d’une base de données secondaire à une base de données principale afin de lancer le basculement.|
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) | Obtient les liens de géo-réplication entre une base de données Azure SQL et un groupe de ressources ou SQL Server. |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) | Met fin à une réplication de données entre une base de données SQL et la base de données secondaire spécifiée. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Crée un groupe de basculement Azure SQL Database pour les serveurs spécifiés. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Échange les rôles des serveurs dans le groupe de basculement et transfère l’ensemble des bases de données secondaires sur le rôle principal. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Récupère un groupe de basculement Azure SQL Database spécifique ou répertorie les groupes de basculement sur un serveur. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts SQL Database PowerShell dans [Scripts PowerShell Azure SQL Database](../sql-database-powershell-samples.md).
