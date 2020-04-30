---
title: 'Exemple PowerShell : Mettre à jour le schéma de synchronisation SQL Data Sync'
description: Exemple de script Azure PowerShell pour mettre à jour le schéma de synchronisation pour SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 00f9e217df65838e75f351dfe00fb60594acdcfc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81383491"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Utiliser PowerShell pour mettre à jour le schéma de synchronisation dans un groupe de synchronisation existant

Cet exemple PowerShell met à jour le schéma de synchronisation dans un groupe de synchronisation SQL Data Sync existant. Lorsque vous synchronisez plusieurs tables, ce script vous permet de mettre à jour efficacement le schéma de synchronisation. Cet exemple illustre l’utilisation du script **UpdateSyncSchema**, qui est disponible sur GitHub en tant que [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous avez besoin de la version 1.4.0 d’AZ PowerShell ou d’une version ultérieure pour suivre ce tutoriel. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

Pour obtenir une vue d’ensemble de SQL Data Sync, consultez [Synchroniser des données entre plusieurs bases de données locales et cloud avec Azure SQL Data Sync](../sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync ne prend pas en charge Azure SQL Database Managed Instance pour le moment.

## <a name="examples"></a>Exemples

### <a name="add-all-tables-to-the-sync-schema"></a>Ajouter toutes les tables au schéma de synchronisation

L’exemple suivant actualise le schéma de base de données et ajoute toutes les tables valides de la base de données Hub dans le schéma de synchronisation.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>Ajouter et supprimer des tables et des colonnes

L’exemple suivant ajoute `[dbo].[Table1]` et `[dbo].[Table2].[Column1]` au schéma de synchronisation et supprime `[dbo].[Table3]`.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Paramètres de script

Le script **UpdateSyncSchema** contient les paramètres suivants :

| Paramètre | Notes |
|---|---|
| $subscriptionId | Abonnement dans lequel le groupe de synchronisation est créé. |
| $resourceGroupName | Groupe de ressources dans lequel le groupe de synchronisation est créé.|
| $serverName | Nom de serveur de la base de données Hub.|
| $databaseName | Nom de la base de données Hub. |
| $syncGroupName | Nom du groupe de synchronisation. |
| $memberName | Spécifiez le nom du membre si vous voulez charger le schéma de base de données du membre de synchronisation au lieu de la base de données Hub. Si vous voulez charger le schéma de base de données à partir du Hub, laissez ce paramètre vide. |
| $timeoutInSeconds | Délai d’attente pendant l’actualisation du schéma de base de données par le script. La valeur par défaut est de 900 secondes. |
| $refreshDatabaseSchema | Spécifiez si le script doit actualiser le schéma de base de données. Si votre schéma de base de données a changé par rapport à la configuration précédente (par exemple, si vous avez ajouté une nouvelle table ou la nouvelle colonne), vous devez actualiser le schéma avant de reconfigurer il. La valeur par défaut est false. |
| $addAllTables | Si cette valeur est true, toutes les tables et colonnes valides sont ajoutées au schéma de synchronisation. Les valeurs de $TablesAndColumnsToAdd et $TablesAndColumnsToRemove sont ignorées. |
| $tablesAndColumnsToAdd | Spécifiez les tables ou les colonnes à ajouter au schéma de synchronisation. Chaque nom de table ou de colonne doit être entièrement délimité avec le nom du schéma. Par exemple : `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Plusieurs noms de table ou de colonne peuvent être spécifiés et séparés par des virgules (,). |
| $tablesAndColumnsToRemove | Spécifiez les tables ou les colonnes à supprimer du schéma de synchronisation. Chaque nom de table ou de colonne doit être entièrement délimité avec le nom du schéma. Par exemple : `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Plusieurs noms de table ou de colonne peuvent être spécifiés et séparés par des virgules (,). |

## <a name="script-explanation"></a>Explication du script

Le script **UpdateSyncSchema** utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Get-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncgroup) | Renvoie des informations sur un groupe de synchronisation. |
| [Update-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncgroup) | Met à jour un groupe de synchronisation. |
| [Get-AzSqlSyncMember](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncmember) | Renvoie des informations sur un membre de synchronisation. |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncschema) | Renvoie des informations sur un schéma de synchronisation. |
| [Update-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncschema) | Met à jour un schéma de synchronisation. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation d’Azure PowerShell](/powershell/azure/overview).

Des exemples supplémentaires de scripts SQL Database PowerShell sont disponibles dans [Scripts Azure SQL Database PowerShell](../sql-database-powershell-samples.md).

Pour plus d’informations sur SQL Data Sync, consultez :

- Vue d’ensemble - [Synchroniser des données entre plusieurs bases de données cloud et locales avec Azure SQL Data Sync](../sql-database-sync-data.md)
- Configurer Data Sync
    - Sur le portail - [Tutoriel : Configurer SQL Data Sync pour synchroniser les données entre Azure SQL Database et SQL Server en local](../sql-database-get-started-sql-data-sync.md)
    - Avec PowerShell
        - [Utilisez PowerShell pour la synchronisation entre plusieurs bases de données Azure SQL](sql-database-sync-data-between-sql-databases.md)
        - [Utiliser PowerShell pour la synchronisation entre une base de données Azure SQL et une base de données locale SQL Server](sql-database-sync-data-between-azure-onprem.md)
- Agent de synchronisation des données - [Agent de synchronisation des données pour Azure SQL Data Sync](../sql-database-data-sync-agent.md)
- Bonnes pratiques - [Bonnes pratiques pour Azure SQL Data Sync](../sql-database-best-practices-data-sync.md)
- Supervision – [Superviser SQL Data Sync avec les journaux d’activité Azure Monitor](../sql-database-sync-monitor-oms.md)
- Résolution des problèmes - [Résoudre les problèmes liés à Azure SQL Data Sync](../sql-database-troubleshoot-data-sync.md)
- Mettre à jour le schéma de synchronisation
    - Avec Transact-SQL - [Automatiser la réplication des modifications de schéma dans Azure SQL Data Sync](../sql-database-update-sync-schema.md)

Pour plus d’informations sur SQL Database, consultez :

- [Vue d’ensemble des bases de données SQL](../sql-database-technical-overview.md)
- [Gestion du cycle de vie des bases de données](https://msdn.microsoft.com/library/jj907294.aspx)
