---
title: Data Warehouse Units (DWU, cDWU) dans Azure SQL Data Warehouse | Microsoft Docs
description: Recommandations concernant le choix du nombre idéal de Data Warehouse Units (DWU, cDWU) pour optimiser le prix et la performance ainsi que la manière de modifier leur nombre.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 05/30/2019
ms.author: martinle
ms.reviewer: igorstan
mscustom: sqlfreshmay19
ms.openlocfilehash: d20a600951a0fe586e981adf12127072df1b744c
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428016"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Data Warehouse Units (DWU) et Data Warehouse Units de calcul (cDWU)

Recommandations concernant le choix du nombre idéal de Data Warehouse Units (DWU, cDWU) pour optimiser le prix et la performance ainsi que la manière de modifier leur nombre.

## <a name="what-are-data-warehouse-units"></a>Quelles sont les unités Data Warehouse

Azure SQL Data Warehouse, l’UC, mémoire et e/s sont regroupées dans des unités d’échelle de calcul appelées Data Warehouse Units (Dwu). Une DWU représente une mesure abstraite et standardisée des ressources de calcul et de performances. Une modification apportée à votre niveau de service modifie le nombre de Dwu sont disponibles sur le système, qui à son tour ajuste les performances et le coût de votre système.

Pour augmenter les performances, vous pouvez augmenter le nombre d’unités de l’entrepôt de données. Pour moins les performances, réduire les unités d’entrepôt de données. Les coûts de stockage et de calcul sont facturés séparément. Ainsi, la modification des DWU n’a pas d’effet sur les coûts de stockage.

Les performances des DWU sont basées sur les métriques de charge de travail d’entrepôt de données suivantes :

- À quelle vitesse une requête d’entreposage de données standards peuvent analyser un grand nombre de lignes et puis effectuer une agrégation complexe. C’est une opération très gourmande en E/S et en UC.
- La vitesse à laquelle l’entrepôt de données peut recevoir des données à partir d’objets BLOB Azure Storage ou Azure Data Lake. C’est une opération très gourmande en réseau et en UC.
- La rapidité du [ `CREATE TABLE AS SELECT` ](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) commande T-SQL peut copier une table. Cette opération implique la lecture des données sur le système de stockage, leur distribution entre les nœuds de l’appliance et la réécriture dans le système de stockage. Cette opération est très gourmande en UC, E/S et réseau.

Augmentation du nombre de DWU :

- Modification linéaire des performances du système pour les analyses, les agrégations et les instructions CTAS
- Augmentation du nombre de processus de lecture et d’écriture pour les opérations de chargement PolyBase
- Augmentation du nombre maximal de requêtes simultanées et d’emplacements de concurrence

## <a name="service-level-objective"></a>Objectif de niveau de service

L’Objectif de niveau de service (SLO) est le paramètre d’extensibilité qui détermine le niveau de coût et de performance de votre entrepôt de données. Les niveaux de service pour Gen2 sont mesurés en unités cDWU (compute Data Warehouse Unit), par exemple DW2000C. Les niveaux de service Gen1 sont mesurés en unités DWU (Data Warehouse Unit), par exemple DW2000.
  > [!NOTE]
  > Azure SQL Data Warehouse Gen2 a récemment ajouté des fonctionnalités de mise à l'échelle supplémentaires pour la prise en charge des niveaux de calcul inférieurs à 100 cDWU. Les entrepôts de données Gen1 existants qui ont besoin des niveaux de calcul inférieurs peuvent désormais être mis à niveau vers Gen2 dans les régions actuellement disponibles sans aucun coût supplémentaire.  Si votre région n'est pas encore prise en charge, vous pouvez procéder à une mise à niveau vers une région qui l'est. Pour plus d’informations, consultez [Mettre à niveau vers Gen2](upgrade-to-latest-generation.md).

Dans T-SQL, le paramètre SERVICE_OBJECTIVE détermine le niveau de service et le niveau de performances pour votre entrepôt de données.

```sql
--Gen1
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Gen2
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Niveaux de performances et Data Warehouse Units

Chaque niveau de performances utilise une unité de mesure légèrement différente pour ses DWU. Cette différence est répercutée sur la facture, car l’unité d’échelle s’applique directement à la facturation.

- Les entrepôts de données Gen1 sont mesurés en unités DWU.
- Les entrepôts de données Gen2 sont mesurés en unités cDWU.

Les DWU et les cDWU prennent en charge la mise à l’échelle du calcul (augmentation ou réduction) et la suspension du calcul lorsque vous n’avez pas besoin d’utiliser l’entrepôt de données. Ces opérations se font toutes à la demande. Gen2 utilise un cache sur disque local sur les nœuds de calcul pour améliorer les performances. Lorsque vous mettez à l’échelle ou suspendez le système, le cache est invalidé, et une période de préchauffage du cache est nécessaire pour pouvoir bénéficier de performances optimales.  

Lorsque vous augmentez les DWU, vous augmentez de façon linéaire le nombre de ressources de calcul. Gen2 fournit les meilleures performances de requête et de la mise à l’échelle la plus élevée. Les systèmes de génération 2 également exploitent au maximum du cache.

### <a name="capacity-limits"></a>Limites de capacité

Chaque serveur SQL (par exemple, myserver.database.windows.net) a un quota de [d’unités de transaction de base de données (DTU)](../sql-database/sql-database-what-is-a-dtu.md) qui autorise un nombre spécifique d’unités d’entrepôt de données. Pour plus d’informations, consultez les [limites de capacité de gestion de la charge de travail](sql-data-warehouse-service-capacity-limits.md#workload-management).

## <a name="how-many-data-warehouse-units-do-i-need"></a>Nombre d’unités l’entrepôt de données ai-je besoin

Le nombre idéal de DWU dépend en grande partie de votre charge de travail et de la quantité de données que vous avez chargées dans le système.

Étapes pour rechercher la DWU la mieux adaptée à votre charge de travail :

1. Commencez par sélectionner une DWU plus petite.
2. Surveillez les performances de votre application pendant le test des charges de données dans le système, en observant notamment le nombre de DWU sélectionné.
3. Identifiez des besoins supplémentaires pour les périodes ponctuelles de pics d’activité. Pics de charges de travail qui montrent significatives et creux dans l’activité peut devoir être mis à l’échelle fréquemment.

SQL Data Warehouse est un système de montée en puissance parallèle qui peut fournir des quantités importantes de calcul et lancer des requêtes sur une grande quantité de données. Pour tester ses capacités de mise à l’échelle, surtout avec un nombre élevé de DWU, nous vous recommandons d’effectuer la mise à l’échelle de l’ensemble de données en vous assurant que vous disposez de suffisamment de données pour alimenter les UC. Pour le test de mise à l’échelle, nous recommandons d’utiliser au moins 1 To.

> [!NOTE]
>
> Si les travaux peuvent être fractionnés entre les nœuds de calcul, les performances des requêtes augmentent uniquement avec une parallélisation renforcée. Si vous trouvez que la mise à l’échelle n’altère pas les performances, vous devrez éventuellement modifier la conception de votre table et/ou vos requêtes. Pour des conseils de paramétrage des requêtes, consultez [Gérer les requêtes utilisateur](sql-data-warehouse-overview-manage-user-queries.md).

## <a name="permissions"></a>Autorisations

La modification des DWU requiert les autorisations décrites dans [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql).

Les rôles intégrés pour les ressources Azure, comme Contributeur de base de données SQL et Contributeur de SQL Server, peuvent changer les paramètres des DWU.

## <a name="view-current-dwu-settings"></a>Afficher les paramètres d’unités DWU actuels

Pour afficher le paramètre DWU actuel :

1. Ouvrez l’Explorateur d’objets SQL Server dans Visual Studio.
2. Connectez-vous à la base de données associée au serveur SQL Database logique.
3. Sélectionnez dans la vue de gestion dynamique sys.database_service_objectives. Voici un exemple :

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Modifier les DWU

### <a name="azure-portal"></a>Portail Azure

Pour modifier les DWU ou les cDWU :

1. Ouvrez le [portail Azure](https://portal.azure.com), ouvrez votre base de données, puis cliquez sur **Mettre à l’échelle**.

2. Sous **Mettre à l’échelle**, déplacez le curseur vers la gauche ou vers la droite pour modifier le paramètre DWU.

3. Cliquez sur **Enregistrer**. Un message de confirmation s’affiche. Cliquez sur **Oui** pour confirmer ou sur **Non** pour annuler.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pour modifier les Dwu ou les Cdwu, utilisez le [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) applet de commande PowerShell. L'exemple suivant définit l'objectif de niveau de service sur DW1000 pour la base de données MySQLDW hébergée sur le serveur MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

Pour plus d’informations, consultez [Applets de commande PowerShell pour SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL

Avec T-SQL, vous pouvez afficher les paramètres actuels de DWU ou cDWU, modifier ces paramètres et vérifier la progression.

Pour modifier les DWU ou les cDWU :

1. Connectez-vous à la base de données associée à votre serveur SQL Database logique.
2. Utilisez l’instruction TSQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql). L'exemple suivant définit l'objectif de niveau de service sur DW1000 pour la base de données MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>API REST

Pour modifier les DWU, utilisez l’API REST [Créer ou mettre à jour une base de données](/rest/api/sql/databases/createorupdate). L’exemple suivant définit l’objectif de niveau de service sur DW1000 pour la base de données MySQLDW hébergée sur le serveur MyServer. Le serveur est un groupe de ressources Azure appelé ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Pour plus d’exemples d’API REST, consultez [API REST pour SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Vérifier l’état des modifications de DWU

Les modifications de DWU peuvent prendre plusieurs minutes. Si vous effectuez une mise à l’échelle automatique, envisagez d’implémenter une logique pour vous assurer que certaines opérations sont bien terminées avant de passer à une autre action.

Nous vous conseillons de vérifier l’état de la base de données en différents points de terminaison afin d’implémenter correctement l’automatisation. Si le portail vous informe de la fin d’une opération et de l’état actuel des bases de données, il ne vous permet pas de programmer la vérification de l’état.

Vous ne pouvez pas vérifier l’état de la base de données pour les opérations de montée en puissance avec le portail Azure.

Pour vérifier l’état des modifications de DWU :

1. Connectez-vous à la base de données associée à votre serveur SQL Database logique.
2. Envoyez la requête suivante pour vérifier l’état de la base de données.

```sql
SELECT    *
FROM      sys.databases
;
```

1. Envoyez la requête suivante pour vérifier l’état de l’opération.

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Cette vue de gestion dynamique retourne des informations sur les diverses opérations de gestion sur votre SQL Data Warehouse, comme l’opération et l’état de l’opération, qui est soit IN_PROGRESS ou COMPLETED.

## <a name="the-scaling-workflow"></a>Flux de travail de mise à l’échelle

Lorsque vous démarrez une opération de mise à l’échelle, le système arrête tout d’abord toutes les sessions ouvertes, annulation de toutes les transactions ouvertes pour garantir un état cohérent. La mise à l’échelle intervient uniquement une fois la restauration des transactions effectuée.  

- Pour une opération de montée en charge, le système détache de tous les nœuds de calcul, configure les nœuds de calcul supplémentaires et puis le réassocie à la couche de stockage.
- Pour une opération de mise à l’échelle vers le bas, le système détache de tous les nœuds de calcul et puis rattache uniquement les nœuds nécessaires à la couche de stockage.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la gestion des performances, consultez [Classes de ressources pour la gestion de la charge de travail](resource-classes-for-workload-management.md) et [Limites de mémoire et de concurrence](memory-and-concurrency-limits.md).
