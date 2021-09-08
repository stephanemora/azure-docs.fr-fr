---
title: Gestion de l'espace de fichiers dans Azure SQL Database
description: Cette page explique comment gérer l’espace de fichier avec bases de données uniques et mises en pool dans Azure SQL Database et fournit des exemples de code pour déterminer si vous devez réduire une base de données unique ou mise en pool, ainsi que pour effectuer une opération de réduction de base de données.
services: sql-database
ms.service: sql-database
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, wiassaf
ms.date: 08/09/2021
ms.openlocfilehash: 27adb19b07dc67a91d1bdafb6aac54ad59eaa778
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122563498"
---
# <a name="manage-file-space-for-databases-in-azure-sql-database"></a>Gérer l'espace de fichier des bases de données dans Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Cet article décrit les différents types d'espace de stockage disponibles pour les bases de données dans Azure SQL Database, ainsi que les étapes à suivre lorsque l'espace de fichier alloué doit être explicitement géré.

> [!NOTE]
> Cet article ne s'applique pas à Azure SQL Managed Instance.

## <a name="overview"></a>Vue d’ensemble

Avec Azure SQL Database, il existe des modèles de charge de travail dans lesquels l'allocation des fichiers de données sous-jacents aux bases de données peut dépasser le nombre de pages de données utilisées. Ce scénario peut se produire quand l’espace utilisé augmente et que des données sont ensuite supprimées. La raison en est que l’espace de fichiers alloué n’est pas récupéré automatiquement quand des données sont supprimées.

La surveillance de l’utilisation de l’espace de fichiers et la réduction des fichiers de données peuvent être nécessaires dans les scénarios suivants :

- Autoriser la croissance des données dans un pool élastique quand l’espace de fichiers alloué pour ses bases de données atteint la taille maximale du pool.
- Autoriser la réduction de la taille maximale d’une base de données unique ou d’un pool élastique.
- Autoriser la modification d’une base de données unique ou d’un pool élastique pour les faire passer à un niveau de service ou à un niveau de performance avec une taille maximale inférieure.

> [!NOTE]
> Les opérations de réduction ne doivent pas être considérées comme une opération de maintenance régulière. Les fichiers de données et les fichiers journaux qui augmentent en raison d’opérations d’entreprise périodiques régulières ne nécessitent pas d’opérations de réduction. 

### <a name="monitoring-file-space-usage"></a>Surveillance de l’utilisation de l’espace de fichiers

La plupart des métriques d’espace de stockage affichées dans les API suivantes mesurent seulement la taille des pages de données utilisées :

- API de métriques basées sur Azure Resource Manager dont l’API [get-metrics](/powershell/module/az.monitor/get-azmetric) PowerShell

Cependant, les API suivantes mesurent aussi la taille de l’espace alloué pour les bases de données et les pools élastiques :

- T-SQL : [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL : [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

## <a name="understanding-types-of-storage-space-for-a-database"></a>Appréhender les types d’espace de stockage d’une base de données

Il est essentiel d’appréhender les quantités d’espace de stockage suivantes pour gérer l’espace de fichier d’une base de données.

|Quantité pour une base de données|Définition|Commentaires|
|---|---|---|
|**Espace de données utilisé**|Quantité d’espace utilisée pour stocker les données de la base de données.|En général, l’espace utilisé augmente (diminue) lors des insertions (suppressions). Dans certains cas, l’espace utilisé ne change pas lors des insertions ou suppressions selon la quantité et le modèle des données impliquées dans l’opération et dans toute fragmentation éventuelle. Par exemple, la suppression d’une ligne dans chaque page de données ne diminue pas forcément l’espace utilisé.|
|**Espace de données alloué**|La quantité d’espace de fichiers formatés mise à disposition pour stocker les données de la base de données.|La quantité d’espace allouée augmente automatiquement, mais ne diminue jamais après les suppressions. Ce comportement garantit que les insertions ultérieures seront plus rapides, car l’espace n’aura pas besoin d’être reformaté.|
|**Espace de données alloué mais non utilisé**|La différence entre la quantité d’espace de données allouée et la quantité d’espace de données utilisée.|Cette quantité représente la quantité maximale d’espace libre qui peut être récupérée par la réduction des fichiers de données de la base de données.|
|**Taille maximale des données**|La quantité maximale d’espace qui peut être utilisée pour le stockage des données de la base de données.|La quantité d’espace de données allouée ne peut pas croître au-delà de la taille maximale des données.|

Le schéma suivant illustre la relation entre les différents types d’espace de stockage d’une base de données.

![relations et types d’espace de stockage](./media/file-space-manage/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Interroger une base de données unique pour des informations relatives à l’espace de stockage

Les requêtes suivantes peuvent être utilisées pour déterminer les quantités d’espace de stockage d’une base de données unique.  

### <a name="database-data-space-used"></a>Espace de données de base de données utilisé

Modifiez la requête suivante pour retourner la quantité d’espace de données de base de données utilisée.  Le résultat de la requête est exprimé en Mo.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC;
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Espace de données alloué et espace alloué non utilisé de la base de données

Utilisez la requête suivante pour retourner la quantité d’espace de données allouée de la base de données et la quantité d’espace alloué non utilisé.  Le résultat de la requête est exprimé en Mo.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB,
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS';
```

### <a name="database-data-max-size"></a>Taille maximale des données de la base de données

Modifiez la requête suivante pour retourner la taille maximale des données de la base de données.  Le résultat de la requête est exprimé en octets.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes;
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Appréhender les types d’espace de stockage d’un pool élastique

Il est essentiel d’appréhender les quantités d’espace de stockage suivantes pour gérer l’espace de fichier d’un pool élastique.

|Quantité pour un pool élastique|Définition|Commentaires|
|---|---|---|
|**Espace de données utilisé**|L’espace de données total utilisé par toutes les bases de données dans le pool élastique.||
|**Espace de données alloué**|L’espace de données total alloué par toutes les bases de données dans le pool élastique.||
|**Espace de données alloué mais non utilisé**|La différence entre la quantité d’espace de données allouée et la quantité d’espace de données utilisée par toutes les bases de données dans le pool élastique.|Cette quantité représente la quantité maximale d’espace alloué au pool élastique qui peut être récupérée par la réduction des fichiers de données de la base de données.|
|**Taille maximale des données**|La quantité maximale d’espace de données qui peut être utilisée par le pool élastique pour toutes ses bases de données.|L’espace alloué au pool élastique ne doit pas dépasser la taille maximale du pool élastique.  Si cette condition se produit, l’espace alloué qui n’est pas utilisé peut être récupéré en réduisant les fichiers de données de la base de données.|

> [!NOTE]
> Le message d’erreur « Le pool élastique a atteint sa limite de stockage » indique que suffisamment d’espace a été alloué aux objets de base de données pour respecter la limite de stockage du pool élastique, mais qu’il peut y avoir de l’espace inutilisé dans l’allocation de l’espace de données. Envisagez d’augmenter la limite de stockage du pool élastique ou, comme solution à court terme, de libérer de l’espace de données à l’aide de la section [**Récupérer l’espace alloué non utilisé**](#reclaim-unused-allocated-space) ci-dessous. Vous devez également être conscient du possible impact négatif sur les performances que peut avoir la réduction des fichiers de base de données. Voir [**Reconstruire des index**](#rebuild-indexes) ci-dessous.

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Interroger un pool élastique pour des informations relatives à l’espace de stockage

Les requêtes suivantes peuvent être utilisées pour déterminer les quantités d’espace de stockage d’un pool élastique.  

### <a name="elastic-pool-data-space-used"></a>Espace de données du pool élastique utilisé

Modifiez la requête suivante pour retourner la quantité d’espace de données du pool élastique utilisée.  Le résultat de la requête est exprimé en Mo.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC;
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Espace de données alloué et espace alloué non utilisé du pool élastique

Modifiez les exemples suivants pour retourner une table listant l’espace alloué et l’espace alloué non utilisé pour chaque base de données d’un pool élastique. La table trie les bases de données en commençant par celles qui ont la plus grande quantité d’espace alloué non utilisé jusqu’à celles qui en ont la plus petite quantité.  Le résultat de la requête est exprimé en Mo.  

Les résultats de requête permettant de déterminer l’espace alloué à chaque base de données dans le pool peuvent être cumulés pour déterminer l’espace total alloué au pool élastique. L’espace de pool élastique alloué ne doit pas dépasser la taille maximale du pool élastique.  

> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Le module AzureRM continue à recevoir des résolutions de bogues jusqu’à au moins décembre 2020. Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm. Pour en savoir plus sur leur compatibilité, consultez [Présentation du nouveau module Az Azure PowerShell](/powershell/azure/new-azureps-module-az).

Le script PowerShell nécessite le module SQL Server PowerShell. Pour l’installer, consultez [Télécharger le module PowerShell](/sql/powershell/download-sql-server-ps-module).

```powershell
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$poolName = "<poolName>"
$userName = "<userName>"
$password = "<password>"

# get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# for each database in the elastic pool, get space allocated in MB and space allocated unused in MB
foreach ($database in $databasesInPool) {
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn -Database $database.DatabaseName `
            -Username $userName -Password $password -Query $sqlCommand)
}

# display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort -Property DatabaseDataSpaceAllocatedUnusedInMB -Descending | Format-Table
```

La capture d’écran suivante est un exemple de sortie du script :

![espace de pool élastique alloué et exemple d’espace alloué non utilisé](./media/file-space-manage/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Taille maximale des données du pool élastique

Modifiez la requête T-SQL suivante pour retourner la dernière taille maximale enregistrée des données du pool élastique.  Le résultat de la requête est exprimé en Mo.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC;
```

## <a name="reclaim-unused-allocated-space"></a>Récupérer l’espace alloué non utilisé

> [!IMPORTANT]
> Les commandes de réduction ont un impact sur les performances de la base de données pendant l’exécution et, si possible, doivent être exécutées pendant les périodes de faible utilisation.

### <a name="shrinking-data-files"></a>Réduction des fichiers de données

En raison d’un impact potentiel sur les performances de la base de données, la Base de données Azure SQL ne réduit pas automatiquement les fichiers de données. Cependant, les clients peuvent réduire les fichiers de données via une opération en libre-service quand ils le souhaitent. Cela ne doit pas être une opération planifiée régulièrement, mais plutôt un événement à usage unique en réponse à une réduction majeure de la consommation d’espace utilisée par le fichier de données.

Dans la Base de données Azure SQL, pour réduire des fichiers vous pouvez utiliser `DBCC SHRINKDATABASE` ou `DBCC SHRINKFILE` les commandes :

- `DBCC SHRINKDATABASE` réduira tous les fichiers journaux et de données de la base de données, ce qui n’est généralement pas nécessaire. La commande réduit un fichier à la fois. Elle [réduira également le fichier journal](#shrinking-transaction-log-file). La Base de données Azure SQL réduit automatiquement les fichiers journaux, si nécessaire.
- `DBCC SHRINKFILE`la commande prend en charge des scénarios plus avancés :
    - Elle peut cibler des fichiers individuels en fonction des besoins, au lieu de réduire tous les fichiers de la base de données.
    - Chaque `DBCC SHRINKFILE`commande peut s’exécuter en parallèle avec d’autres `DBCC SHRINKFILE` commandes pour réduire la taille de la base de données plus rapidement, au détriment d’une utilisation des ressources et d’une probabilité de blocage plus grandes des requêtes utilisateur, si elles s’exécutent pendant la réduction.
    - Si la fin du fichier ne contient pas de données, cela peut réduire la taille de fichier allouée beaucoup plus rapidement en spécifiant l’argument TRUNCATEONLY. Cela ne nécessite pas le déplacement des données dans le fichier.
- Pour plus d’informations concernant ces commandes de réduction, consultez [DBCC SHRINKDATABASE](/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql) ou [DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql).

Les exemples suivants doivent être exécutés lors d’une connexion à la base de données utilisateur cible, et non à la `master` base de données.

Pour utiliser `DBCC SHRINKDATABASE` pour réduire l’ensemble des données et des fichiers journaux dans une base de données spécifique :

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'database_name');
```

Dans la Base de données Azure SQL, une base de données peut posséder un ou plusieurs fichiers de données. Des fichiers de données supplémentaires peuvent uniquement être créés automatiquement. Pour déterminer la disposition des fichiers de votre base de données, interrogez l'`sys.database_files`affichage catalogue à l’aide de l’exemple de script suivant :

```sql
-- Review file properties, including file_id values to reference in shrink commands
SELECT file_id,
       name,
       CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8 / 1024. AS space_used_mb,
       CAST(size AS bigint) * 8 / 1024. AS space_allocated_mb,
       CAST(max_size AS bigint) * 8 / 1024. AS max_size_mb
FROM sys.database_files
WHERE type_desc IN ('ROWS','LOG');
GO
```

Exécutez une réduction sur un fichier uniquement par le biais de la `DBCC SHRINKFILE` commande, par exemple :

```sql
-- Shrink database data file named 'data_0` by removing all unused at the end of the file, if any.
DBCC SHRINKFILE ('data_0', TRUNCATEONLY);
GO
```

Vous devez également être conscient du possible impact négatif sur les performances que peut avoir la réduction des fichiers de la base de données. Voir [Reconstruire des index](#rebuild-indexes) ci-dessous. 

### <a name="shrinking-transaction-log-file"></a>Réduction du fichier journal de transactions

Contrairement aux fichiers de données, Azure SQL Database réduit automatiquement le fichier journal de transactions afin d’éviter une utilisation excessive de l’espace, qui peut entraîner des erreurs d’insuffisance d’espace. Il n’est généralement pas nécessaire pour les clients de réduire le fichier journal de transactions.

Dans les niveaux de service Premium et Critique pour l’entreprise, si le journal de transactions devient volumineux, il peut contribuer considérablement à la l’utilisation du stockage local jusqu’à la limite de [stockage local maximal](resource-limits-logical-server.md#storage-space-governance). Si l’utilisation du stockage local est proche de la limite, les clients peuvent choisir de réduire le journal des transactions à l’aide de la commande [DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql), comme dans l’exemple suivant. Cela libère le stockage local dès la fin de la commande, sans attendre l’opération de réduction automatique périodique.

Les exemples suivants doivent être exécutés lors d’une connexion à la base de données utilisateur cible, et non à la base de données de référence.

```tsql
-- Shrink the database log file (always file_id = 2), by removing all unused space at the end of the file, if any.
DBCC SHRINKFILE (2, TRUNCATEONLY);
```

### <a name="auto-shrink"></a>Réduction automatique

Vous pouvez aussi activer la réduction automatique pour une base de données. Cependant, elle peut être moins efficace pour récupérer de l’espace de fichiers que `DBCC SHRINKDATABASE` et `DBCC SHRINKFILE`.  

La réduction automatique peut être utile dans un scénario spécifique où un pool élastique contient de nombreuses bases de données qui subissent une croissance et une réduction significatives de l’espace utilisé pour les fichiers de données. Ce scénario n’est pas très courant. 

Par défaut, la réduction automatique est désactivée, ce qui est recommandé pour la plupart des bases de données. S’il est nécessaire d’activer la réduction automatique, il est recommandé de la désactiver une fois les objectifs de gestion de l’espace atteints, au lieu de la conserver indéfiniment. Pour plus d’informations, consultez [Considérations relatives à AUTO_SHRINK](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-auto_shrink).

Pour activer la réduction automatique, exécutez la commande suivante en étant connecté dans votre base de données (et non pas dans la base de données de référence).

```sql
-- Enable auto-shrink for the current database.
ALTER DATABASE CURRENT SET AUTO_SHRINK ON;
```

Pour plus d’informations sur cette commande, consultez les options de [DATABASE SET](/sql/t-sql/statements/alter-database-transact-sql-set-options).

### <a name="index-maintenance-before-or-after-shrink"></a><a name="rebuild-indexes"></a>Maintenance de l’index avant ou après la réduction

Après l’exécution d’une opération de réduction sur les fichiers de données, les index peuvent être fragmentés et perdent leur efficacité d’optimisation des performances pour certaines charges de travail, telles que les requêtes utilisant des analyses volumineuses. Si la dégradation des performances se produit une fois l’opération de réduction terminée, envisagez la maintenance des index pour reconstruire les index. 

Si la densité de page de la base de données est faible, la réduction prendra plus de temps, car elle devra déplacer plus de pages dans chaque fichier de données. Microsoft recommande de déterminer la densité de page moyenne avant d’exécuter les commandes de réduction. Si la densité de page est faible, régénérez ou réorganisez les index pour augmenter la densité de page avant d’exécuter la réduction. Pour plus d’informations, notamment un exemple de script pour déterminer la densité des pages, consultez [optimiser la maintenance des index pour améliorer les performances des requêtes et réduire la consommation des ressources](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les tailles maximales de base de données, consultez :
  - [Limites du modèle d’achat vCore Azure SQL Database pour une base de données unique](resource-limits-vcore-single-databases.md)
  - [Limites de ressources pour des bases de données uniques suivant le modèle d’achat DTU](resource-limits-dtu-single-databases.md)
  - [Limites du modèle d’achat vCore Azure SQL Database pour les pools élastiques](resource-limits-vcore-elastic-pools.md)
  - [Limites de ressources pour des pools élastiques suivant le modèle d’achat DTU](resource-limits-dtu-elastic-pools.md)
