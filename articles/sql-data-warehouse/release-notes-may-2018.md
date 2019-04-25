---
title: Notes de publication Azure SQL Data Warehouse mai 2018 | Microsoft Docs
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
ms.openlocfilehash: c17cb13bff0ea9eb3b0bb2caf5bb527fa3958428
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60402698"
---
# <a name="whats-new-in-azure-sql-data-warehouse-may-2018"></a>Nouveautés dans Azure SQL Data Warehouse Mai 2018 
Azure SQL Data Warehouse reçoit continuellement des améliorations. Cet article décrit les nouvelles fonctionnalités et les modifications qui ont été introduites en mai 2018. 

## <a name="gen-2-instances"></a>Instances Gen 2
![alt](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/2528b41b-f09f-45b1-aa65-fc60d562d3bd.png) Le niveau Gen2 optimisé pour le calcul d’Azure SQL Data Warehouse définir de nouvelles normes de performances pour l’entreposage de données cloud. Les clients bénéficient désormais de requêtes cinq fois plus performantes, d’un accès concurrentiel quatre fois supérieur d’une puissance de calcul cinq fois supérieure par rapport à la génération actuelle. Désormais, 128 requêtes simultanées peuvent être traitées à partir d’un cluster unique, le service d’entreposage de données cloud le plus élevé.

Consultez l’annonce du blog [Turbocharge cloud analytics with Azure SQL Data Warehouse](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) de Rohan Kumar, Vice-président corporate, Azure Data.

## <a name="auto-statistics"></a>Statistiques automatiques
Les statistiques sont essentielles pour optimiser la génération de plan de requête dans des optimiseurs basés sur le coût modernes, comme le moteur de SQL Data Warehouse. Lorsque toutes les requêtes sont connues d’avance, déterminer quels objets statistiques doivent être créés est une tâche réalisable. Toutefois, lorsque le système est confronté à ad hoc et requêtes aléatoire qui est généralement utilisé pour les charges de travail d’entreposage de données, les administrateurs système peuvent rencontrer des problèmes prédire les statistiques doivent être créés aux plans d’exécution de requête potentiellement non optimal et de plus de temps temps de réponse de requête. L’une des façons d’atténuer ce problème consiste à créer des objets statistiques sur toutes les colonnes de la table à l’avance. Cependant, ce processus présente une altération car les objets statistiques doivent être conservés pendant le processus de chargement de la table, entraînant des temps de chargement plus longs.

SQL Data Warehouse prend désormais en charge la création automatique d’objets statistiques pour une flexibilité, productivité et facilité d’utilisation optimales pour les administrateurs système et les développeurs, tout en garantissant que le système continue à offrir des plans d’exécution de qualité et de meilleurs temps de réponse.

Pour activer ou désactiver la création automatique de statistiques dans SQL Data Warehouse, exécutez l’instruction suivante :
```sql
ALTER DATABASE { database_name } SET { AUTO_CREATE_STATISTICS { OFF | ON } } [;]
```

En tant que meilleure pratique, nous vous recommandons de définir l’option `AUTO_CREATE_STATISTICS` sur `ON`.

> [!NOTE]
> La création automatique de statistiques est *activée par défaut* pour tous les nouveaux entrepôts de données.
>  

Consultez l’article [Options ALTER DATABASE SET](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options) pour plus d’informations.

## <a name="rejected-row-support"></a>Prise en charge de ligne rejetée
Les clients utilisent souvent [PolyBase (tables externes) pour charger des données](design-elt-data-loading.md) dans SQL Data Warehouse en raison des performances élevées et de la nature parallèle du chargement de données. PolyBase est aussi le modèle de chargement par défaut lors du chargement de données via [Azure Data Factory](https://azure.com/adf). 

SQL Data Warehouse ajoute la possibilité de définir un emplacement de ligne rejetée via le paramètre `REJECTED_ROW_LOCATION` avec l’instruction [CREATE EXTERNAL TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql). Après l’exécution d’une instruction [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) à partir de la table externe, toutes les lignes qui n’ont pas pu être chargées seront être stockées dans un fichier près de la source pour une analyse approfondie. 

Consultez le blog [Load confidently with SQL Data Warehouse PolyBase Rejected Row Location](https://azure.microsoft.com/blog/load-confidently-with-sql-data-warehouse-polybase-rejected-row-location/) pour plus d’informations sur le comportement Ligne rejetée.

L’exemple suivant montre la nouvelle syntaxe pour la spécification des lignes rejetées.

```sql
CREATE EXTERNAL TABLE [dbo].[Reject_Example]
(
    ...
)
WITH
(
    ...
    ,REJECTED_ROW_LOCATION=‘/Reject_Directory'
)
```

## <a name="alter-view"></a>ALTER VIEW
[ALTER VIEW](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql) permet à un utilisateur de modifier une vue précédemment créée sans devoir SUPPRIMER/CRÉER la vue et réappliquer les autorisations. 

L’exemple suivant modifie une vue créée précédemment.
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

## <a name="concatws"></a>CONCAT_WS
La fonction [CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql) retourne une chaîne qui résulte de la concaténation de deux ou plusieurs valeurs d’une manière de bout en bout. Elle sépare les valeurs concaténées avec le délimiteur spécifié dans le premier argument. La fonction `CONCAT_WS` est utile pour générer une sortie CSV.

L’exemple suivant montre la concaténation d’un ensemble de valeurs entières avec une virgule.
```sql
SELECT CONCAT_WS(',', 1, 2, 3) [result];
```
L’instruction retourne le résultat suivant :
```
result
---------
1,2,3
```
L’exemple suivant montre la concaténation d’un ensemble de valeurs de type de données mixte avec une virgule.
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', NEWID()) [result]
```
L’instruction retourne le résultat suivant :
```
result
---------
1,2,String,26E1F74D-5746-44DC-B47F-2FC1DA1B6E49
```

## <a name="spdatatypeinfo"></a>SP_DATATYPE_INFO
La procédure stockée par le système [sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) retourne des informations sur les types de données pris en charge par l’environnement actuel. Elle est généralement utilisée par les outils qui se connectent via des connexions ODBC pour un examen du type de données.

L’exemple suivant récupère les détails pour tous les types de données pris en charge par SQL Data Warehouse.

```sql
EXEC sp_datatype_info
```

## <a name="select-into-with-order-by-behavior-change"></a>Changement de comportement de SELECT INTO avec ORDER BY
SQL Data Warehouse bloque maintenant les requêtes `SELECT INTO` qui contiennent une clause `ORDER BY`. Auparavant, cette opération réussissait en triant en premier les données en mémoire, puis en les insérant dans la table cible, réorganisant ainsi les données afin de correspondre à la forme de table.

### <a name="previous-behavior"></a>Comportement précédent
L’instruction suivante réussissait avec une charge de traitement supplémentaire.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```

### <a name="current-behavior"></a>Comportement actuel
L’instruction suivante lève une erreur indiquant que la clause `ORDER BY` n’est pas prise en charge dans une instruction `SELECT INTO`.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```
L’instruction d’erreur retournait :
```
Msg 104381, Level 16, State 1, Line 1
The ORDER BY clause is invalid in views, CREATE TABLE AS SELECT, INSERT SELECT, SELECT INTO, inline functions, derived tables, subqueries, and common table expressions, unless TOP or FOR XML is also specified.
```

## <a name="set-parseonly-on-query-status-behavior-change"></a>État de la requête SET PARSEONLY ON (changement de comportement)
L’utilisation de la syntaxe `SET PARSEONLY ON` permet à un utilisateur de faire en sorte que le moteur SQL Data Warehouse examine la syntaxe de chaque instruction T-SQL et renvoie les messages d’erreur sans compiler ni exécuter l’instruction. Auparavant, dans la vue système [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql), ces instructions conservaient l’état `Running`. La vue `sys.dm_pdw_exec_requests` retourne maintenant l’état en tant que `Complete`.

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
