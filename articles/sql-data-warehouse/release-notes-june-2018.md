---
title: Notes de publication Azure SQL Data Warehouse juin 2018 | Microsoft Docs
description: Notes de publication pour Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 672bfee600f19661c6bc68535a68fff4a0ccc43f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57835365"
---
# <a name="whats-new-in-azure-sql-data-warehouse-june-2018"></a>Nouveautés dans Azure SQL Data Warehouse Juin 2018
Azure SQL Data Warehouse reçoit continuellement des améliorations. Cet article décrit les nouvelles fonctionnalités et les modifications qui ont été introduites en juin 2018. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="user-defined-restore-points"></a>Points de restauration définis par l’utilisateur
SQL Data Warehouse prend automatiquement des instantanés de votre entrepôt de données toutes les 8 heures pour respecter un objectif de point de récupération (RPO) de huit heures. Bien que ces instantanés automatisés facilitent l’administration liée à l’exécution de votre entrepôt de données, il est nécessaire de prendre des instantanés aux moments critiques en fonction des besoins de votre entreprise. Par exemple, prendre un instantané juste avant une charge importante de données ou le déploiement de nouveaux scripts dans l’entrepôt de données pour activer un point de restauration juste avant l’opération. 

SQL Data Warehouse prend désormais en charge [points de restauration défini par l’utilisateur](https://azure.microsoft.com/blog/quick-recovery-time-with-sql-data-warehouse-using-user-defined-restore-points/) via la [New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint) applet de commande.

```PowerShell
New-AzSqlDatabaseRestorePoint
    -ResourceGroupName $ResourceGroupName
    -ServerName $ServerName
    -DatabaseName $DatabaseName
    -RestorePointLabel $RestorePointName
```

## <a name="column-level-security"></a>Sécurité au niveau de la colonne
La gestion de l’accès aux données et de leur sécurité dans votre entrepôt de données est essentielle pour établir une confiance avec vos clients et partenaires. SQL Data Warehouse [prend désormais en charge la sécurité au niveau de la colonne (CLS)](https://azure.microsoft.com/blog/column-level-security-is-now-supported-in-azure-sql-data-warehouse/) qui vous permet de modifier les autorisations permettant d’afficher les données sensibles, en limitant l’accès utilisateur à des colonnes spécifiques dans vos tables sans avoir à reconcevoir votre entrepôt de données.

La sécurité au niveau de la colonne vous permet de contrôler l’accès aux colonnes de table selon le contexte d’exécution de l’utilisateur ou leur appartenance au groupe à l’aide de l’instruction T-SQL [GRANT](https://docs.microsoft.com/azure/sql-data-warehouse/column-level-security) standard. La logique de restriction d’accès se situe dans la couche de base de données plutôt qu’à l’écart des données d’une autre application, ce qui simplifie la mise en œuvre globale.


```sql
CREATE USER Nurse WITHOUT LOGIN;   
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO Nurse;   
EXECUTE AS USER = 'Nurse';
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12 
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="objectschemaname"></a>OBJECT_SCHEMA_NAME
La fonction [OBJECT_SCHEMA_NAME()](https://docs.microsoft.com/sql/t-sql/functions/object-schema-name-transact-sql) retourne le nom de schéma de base de données pour les objets étendus aux schémas. Cette fonction est devenu courante dans les outils ETL en cas de validation de schéma d’objet. 

```sql
SELECT
    OBJECT_SCHEMA_NAME([object_id]) [table_schema]
    , [name]                        [table_name]
FROM
    [sys].[tables];
```

**Exemples de résultats**
```
table_schema    | table_name
-----------------------------
dbo               customer
dbo               lineitem
dbo               nation
dbo               orders
```

## <a name="support-for-the-systimezoneinfo-view"></a>Prise en charge de la vue sys.time_zone_info
La vue [sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) retourne des informations sur les fuseaux horaires pris en charge dans Azure SQL Data Warehouse.

```sql
SELECT * FROM [sys].[time_zone_info];
```

**Exemples de résultats**
```
name                            | current_utc_offset | is_currently_dst
-------------------------------------------------------------------------
Pacific Standard Time (Mexico)    -07:00               1
US Mountain Standard Time         -07:00               0
Mountain Standard Time (Mexico)   -06:00               1
Central Standard Time             -05:00               1
```

## <a name="auto-stats-operations-appear-in-sysdmpdwexecrequests-behavior-change"></a>Les opérations Auto Stats apparaissent dans sys.dm_pdw_exec_requests (changement de comportement)

Avec l’introduction de [Création automatique des statistiques](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistics), Azure SQL Data Warehouse génère des statistiques pour optimiser l’exécution des requêtes. La version de juin 2018 ajoute la capacité à surveiller lorsque les statistiques sont générées automatiquement en ajoutant un enregistrement dans la vue [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) à chaque fois qu’une opération [CREATE STATISTICS](https://docs.microsoft.com/sql/t-sql/statements/create-statistics-transact-sql) est exécutée.

```sql
SELECT
    [start_time]
    , [end_time]
    , [command]
FROM
    [sys].[dm_pdw_exec_requests]
WHERE
    [command] LIKE 'CREATE STATISTICS _WA_Sys%';
```
**Exemples de résultats**
```
start_time                | end_time                | command
------------------------------------------------------------------------------------------------------------------------------
2018-06-06 19:06:26.173    2018-06-06 19:06:26.173    CREATE STATISTICS _WA_Sys_00000001_63D998CC ON dbo.LineItem(l_orderkey);
```

## <a name="next-steps"></a>Étapes suivantes
À présent que vous en savez un peu plus sur SQL Data Warehouse, découvrez comment [créer rapidement un entrepôt SQL Data Warehouse][create a SQL Data Warehouse]. Si vous n’êtes pas encore familiarisé avec Azure, vous pouvez vous appuyer sur le [Glossaire Azure][Azure glossary] lorsque vous rencontrez de nouveaux termes. Ou bien, consultez ces autres ressources de SQL Data Warehouse.  

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
[Forum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vidéos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
