---
title: Gestion de l’espace du fichier de la base de données SQL Azure | Microsoft Docs
description: Cette page explique comment gérer l’espace de fichier avec Azure SQL Database et fournit des exemples de code pour déterminer si vous devez réduire une base de données, ainsi que pour effectuer une opération de réduction de base de données.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: how-to
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 1ecc0ce08ef42f5f5935bca29e8269be2ea142f0
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39416002"
---
# <a name="manage-file-space-in-azure-sql-database"></a>Gérer l’espace du fichier de la base de données SQL Azure

Cet article décrit les différents types d’espace de stockage dans Azure SQL Database et les étapes à effectuer lorsque l’espace de fichier alloué aux bases de données et aux pools élastiques doit être géré par le client.

## <a name="overview"></a>Vue d’ensemble

Dans Azure SQL Database, les métriques de taille de stockage affichées dans le portail Azure et les API suivantes mesurent le nombre de pages de données utilisées pour les bases de données et des pools élastiques :
- API de métriques basées sur Azure Resource Manager dont l’API [get-metrics](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric) PowerShell
- T-SQL : [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- T-SQL : [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL : [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

Il existe des modèles de charges de travail dans lesquels l’allocation d’espace dans les fichiers de données sous-jacents pour des bases de données devient supérieure au nombre de pages de données utilisées dans les fichiers de données. Ce scénario peut se produire lorsque l’espace utilisé augmente, et les données sont ensuite supprimées. L’espace de fichier alloué n’est pas automatiquement récupéré lorsque les données sont supprimées. Dans de tels scénarios, l’espace alloué pour une base de données ou un pool peut dépasser les limites maximales prises en charge définies pour la base de données et, par conséquent, empêcher la croissance des données ou les modifications de niveau de performances, même si l’espace de base de données réellement utilisé est inférieur à la limite maximale d’espace. Pour atténuer ce problème, vous devrez peut-être réduire la base de données pour réduire l’espace alloué mais non utilisé dans la base de données.

Le service SQL Database ne réduit pas automatiquement les fichiers de la base de données pour récupérer l’espace alloué inutilisé en raison de l’impact potentiel sur les performances de la base de données. Toutefois, vous pouvez réduire le fichier dans une base de données au moment de votre choix en suivant les étapes décrites dans [Récupérer l’espace alloué non utilisé](#reclaim-unused-allocated-space). 

> [!NOTE]
> Contrairement aux fichiers de données, le service SQL Database réduit automatiquement les fichiers journaux dans la mesure où cette opération n’affecte pas les performances de la base de données.

## <a name="understanding-the-types-of-storage-space-for-a-database"></a>Présentation des types d’espace de stockage d’une base de données

Pour gérer l’espace de fichier, vous devez comprendre les termes suivants liés au stockage de base de données pour une base de données unique et un pool élastique.

|Terme de l’espace de stockage|Définition|Commentaires|
|---|---|---|
|**Espace de données utilisé**|La quantité d’espace utilisée pour stocker les données de la base de données dans des pages de 8 Ko.|En général, cet espace utilisé augmente (diminue) lors des insertions (suppressions). Dans certains cas, l’espace utilisé ne change pas lors des insertions ou suppressions selon la quantité et le modèle des données impliquées dans l’opération et dans toute fragmentation éventuelle. Par exemple, la suppression d’une ligne dans chaque page de données ne diminue pas forcément l’espace utilisé.|
|**Espace alloué**|La quantité d’espace de fichiers formatés mise à disposition pour stocker les données de la base de données|L’espace alloué augmente automatiquement, mais ne diminue jamais après les suppressions. Ce comportement garantit que les insertions ultérieures seront plus rapides, car l’espace n’aura pas besoin d’être reformaté.|
|**Espace alloué mais non utilisé**|La quantité d’espace de fichier de données inutilisé alloué à la base de données.|Cette quantité est la différence entre la quantité d’espace alloué et d’espace utilisé, et représente la quantité maximale d’espace qui peut être réclamée en réduisant les fichiers de base de données.|
|**Taille maximale**|La quantité maximale d’espace de données qui peut être utilisée par la base de données.|L’espace de données alloué ne peut pas croître au-delà de la taille maximale des données.|
||||

Le diagramme suivant illustre la relation entre les types d’espace de stockage.

![relations et types d’espace de stockage](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-database-for-storage-space-information"></a>Interroger une base de données pour des informations relatives à l’espace de stockage

Pour déterminer si vous avez de l’espace de données alloué mais non utilisé dans une base de données individuelle que vous souhaitez récupérer, utilisez les requêtes suivantes :

### <a name="database-data-space-used"></a>Espace de données de base de données utilisé
Modifiez la requête suivante pour retourner la quantité d’espace de données de base de données utilisée en Mo.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-allocated-and-allocated-space-unused"></a>Données de base de données allouées et espace alloué non utilisé
Modifiez la requête suivante pour retourner la quantité de données de base de données utilisée et d’espace alloué non utilisé.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-max-size"></a>Taille maximale de la base de données
Modifiez la requête suivante pour retourner la taille maximale de la base de données en octets.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Interroger un pool élastique pour des informations relatives à l’espace de stockage

Pour déterminer si vous avez de l’espace de données alloué mais non utilisé dans un pool élastique et pour chaque base de données mise en pool que vous souhaitez récupérer, utilisez les requêtes suivantes :

### <a name="elastic-pool-data-space-used"></a>Espace de données du pool élastique utilisé
Modifiez la requête suivante pour retourner la quantité d’espace de données du pool élastique utilisée en Mo.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-allocated-and-allocated-space-unused"></a>Données du pool élastique allouées et espace alloué non utilisé

Modifiez le script PowerShell suivant pour retourner une table répertoriant l’espace total et l’espace inutilisé alloués à chaque base de données dans un pool élastique. La table range les bases de données de celles avec la plus grande quantité d’espace alloué inutilisé jusqu’à celles avec la plus basse quantité d’espace alloué inutilisé.  

Les résultats de requête pour déterminer l’espace alloué à chaque base de données dans le pool peuvent être combinés pour déterminer l’espace de pool élastique alloué. L’espace de pool élastique alloué ne doit pas dépasser la taille maximale du pool élastique.  

```powershell
# Resource group name
$resourceGroupName = "rg1" 
# Server name
$serverName = "ls2" 
# Elastic pool name
$poolName = "ep1"
# User name for server
$userName = "name"
# Password for server
$password = "password"

# Get list of databases in elastic pool
$databasesInPool = Get-AzureRmSqlElasticPoolDatabase `
    -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# For each database in the elastic pool,
# get its space allocated in MB and space allocated unused in MB.
# Requires SQL Server PowerShell module – see here to install.  
foreach ($database in $databasesInPool)
{
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn `
        -Database $database.DatabaseName `
        -Username $userName `
        -Password $password `
        -Query $sqlCommand)
}
# Display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort `
    -Property DatabaseDataSpaceAllocatedUnusedInMB `
    -Descending | Format-Table
```

La capture d’écran suivante est un exemple de sortie du script :

![espace de pool élastique alloué et exemple d’espace alloué non utilisé](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-max-size"></a>Taille maximale du pool élastique

Utilisez la requête T-SQL suivante pour retourner la taille maximale de la base de données élastique en Mo.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Récupérer l’espace alloué non utilisé

Une fois que vous avez déterminé l’espace inutilisé alloué que vous souhaitez récupérer, utilisez la commande suivante pour réduire l’espace de base de données alloué. 

> [!IMPORTANT]
> Pour les bases de données dans un pool élastique, les bases de données avec le plus d’espace alloué non utilisé doivent être réduites en premier pour récupérer de l’espace de fichier plus rapidement.  

Pour réduire tous les fichiers de données dans la base de données spécifiée, utilisez la commande suivante :

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'<database_name>')
```

Pour plus d’informations sur cette commande, consultez [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

> [!IMPORTANT] 
> Envisagez de reconstruire les index de base de données. Une fois les fichiers de données de base de données réduits, les index peuvent se fragmenter et perdre en efficacité au niveau de l’optimisation des performances. Si cela se produit, les index doivent être reconstruits. Pour plus d’informations sur la fragmentation et la reconstruction d’index, consultez [Réorganiser et reconstruire des index](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les tailles maximales de base de données, consultez :
  - [Limites du modèle d’achat par vCore d’Azure SQL Database pour une base de données unique](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
  - [Limites de ressources pour des bases de données uniques suivant le modèle d’achat DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
  - [Limites du modèle d’achat par vCore d’Azure SQL Database pour les pools élastiques](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)
  - [Limites de ressources pour des pools élastiques suivant le modèle d’achat DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- Pour plus d’informations sur la commande `SHRINKDATABASE`, consultez [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 
- Pour plus d’informations sur la fragmentation et la reconstruction d’index, consultez [Réorganiser et reconstruire des index](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).