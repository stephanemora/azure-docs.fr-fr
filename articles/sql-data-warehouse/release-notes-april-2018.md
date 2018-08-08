---
title: Notes de publication Azure SQL Data Warehouse avril 2018 | Microsoft Docs
description: Notes de publication pour Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: aabda90deb9634ef456f1006bccb69375f63fc97
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39323991"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>Nouveautés dans Azure SQL Data Warehouse Avril 2018
Azure SQL Data Warehouse reçoit continuellement des améliorations. Cet article décrit les nouvelles fonctionnalités et les modifications qui ont été introduites en avril 2018.

## <a name="ability-to-truncate-a-partition-before-a-switch"></a>Possibilité de tronquer une partition avant un basculement
Les clients utilisent souvent un basculement de partition comme modèle pour charger des données à partir d’une table vers une autre en modifiant les métadonnées de la table via la syntaxe `ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X`. SQL Data Warehouse ne prend pas en charge le basculement de partition lorsque la partition cible contient des données. Si la partition cible contient déjà des données, le client doit tronquer la partition cible, puis effectuer le basculement.

SQL Data Warehouse prend désormais en charge cette opération dans une seule instruction T-SQL.

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
Pour plus d’informations, consultez l’article [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="improved-query-compilation-performance"></a>Performances améliorées de la compilation des requêtes
SQL Data Warehouse a introduit un ensemble de modifications pour améliorer l’étape de compilation de requête des requêtes distribuées. Ces modifications améliorent les temps de compilation des requêtes jusqu’à **10 fois**, ce qui réduit les délais d’exécution globaux des requêtes. Ces modifications sont plus évidents sur des entrepôts de données avec un grand nombre d’objets (tables, fonctions, vues, procédures).

## <a name="dbcc-commands-do-not-consume-concurrency-slots-behavior-change"></a>Les commandes DBCC ne consomment pas d’emplacements d’accès concurrentiel (changement de comportement)
SQL Data Warehouse prend en charge un sous-ensemble des [commandes DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) de T-SQL, comme [DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql). Auparavant, ces commandes consommaient un [emplacement d’accès concurrentiel](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots), réduisant ainsi le nombre de chargements/requêtes utilisateur qui pouvaient être exécutés. Les commandes `DBCC` sont maintenant exécutées dans une file d’attente locale qui n’utilise pas d’emplacement de ressource, ce qui améliore les performances globales de l’exécution des requêtes.

## <a name="updated-error-message-for-excessive-literals-behavior-change"></a>Mise à jour du message d’erreur pour les littéraux excessifs (changement de comportement)
Auparavant, SQL Data Warehouse incluait un nombre *approximatif* lorsqu’une requête contenait trop de littéraux.
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

Le message d’erreur a été mis à jour pour indiquer uniquement que vous avez atteint la limite des littéraux.
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

Pour plus d’informations sur les limites maximales, consultez la section [Requêtes](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries) de l’article [Limites de capacité](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits).

## <a name="removed-the-syspdwdatabasemappings-view-behavior-change"></a>Suppression de la vue SYS.PDW_DATABASE_MAPPINGS (changement de comportement)
La vue `sys.pdw_database_mappings` n’est pas utilisée dans SQL Data Warehouse. Auparavant, une SÉLECTION de cette vue ne retournait aucun résultat. La vue a été supprimée. 

## <a name="next-steps"></a>Étapes suivantes
À présent que vous en savez un peu plus sur SQL Data Warehouse, découvrez comment [créer rapidement un entrepôt de données SQL][create a SQL Data Warehouse]. Si vous n’êtes pas encore familiarisé avec Azure, vous pouvez vous appuyer sur le [Glossaire Azure][Azure glossary] lorsque vous rencontrez de nouveaux termes. Ou bien, consultez ces autres ressources de SQL Data Warehouse.  

* [Témoignages de clients]
* [Blogs]
* [Demandes de fonctionnalités]
* [Vidéos]
* [Blogs de l’équipe de conseil clientèle]
* [Forum Stack Overflow]
* [Twitter]


[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs de l’équipe de conseil clientèle]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Témoignages de clients]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Demandes de fonctionnalités]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vidéos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md