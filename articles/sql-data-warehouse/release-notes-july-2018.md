---
title: Notes de publication Azure SQL Data Warehouse juillet 2018 | Microsoft Docs
description: Notes de publication pour Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/06/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 4ec90bef9228eccb9556d07bb5680deb00e2550e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65912173"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Nouveautés dans Azure SQL Data Warehouse Juillet 2018
Azure SQL Data Warehouse reçoit continuellement des améliorations. Cet article décrit les nouvelles fonctionnalités et les modifications qui ont été introduites en juillet 2018.

## <a name="lightning-fast-query-performance"></a>Performances de requête ultra-rapides
[Azure SQL Data Warehouse](https://aka.ms/sqldw) définit les nouveaux tests de performances avec l’introduction de l’accès instantané aux données qui améliore les opérations de lecture aléatoire. L’accès instantané aux données réduit la surcharge d’opérations de déplacement de données à l’aide du SQL Server direct pour les opérations de données natives SQL Server. L’intégration avec le moteur SQL Server pour déplacer des données directement signifie que SQL Data Warehouse est désormais **67 % plus rapide qu’Amazon Redshift** grâce à une charge de travail dérivée de la norme industrielle reconnue [TPC Benchmark™ H (TPC-H)](http://www.tpc.org/tpch/).

![Azure SQL Data Warehouse est plus rapide et moins cher qu’Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/eb3b908a-464d-4847-b384-9f296083a737.png)
<sub>Source : [Rapport d’analyse Gigaom Research : Data Warehouse in the Cloud Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub> (Data Warehouse dans le point de référence cloud)

Au-delà des performances d’exécution, le rapport [Gigaom Research](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) a également mesuré le rapport prix-performance pour quantifier le coût en USD de charges de travail spécifiques. SQL Data Warehouse a été **au moins 23 % moins cher** que Redshift pour des charges de travail de 30 To. Avec la capacité de SQL Data Warehouse à mettre l’échelle les ressources de calcul en toute flexibilité ainsi qu’à suspendre et reprendre les charges de travail, les clients paient uniquement lorsque le service est en cours d’utilisation, ce qui réduit encore davantage les coûts.
![Azure SQL Data Warehouse est plus rapide et moins cher qu’Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/cb76447e-621e-414b-861e-732ffee5345a.png)
<sub>Source : [Rapport d’analyse Gigaom Research : Data Warehouse in the Cloud Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub> (Data Warehouse dans le point de référence cloud)

### <a name="query-concurrency"></a>Simultanéité de requête
SQL Data Warehouse garantit également que les données sont accessibles dans votre organisation. Microsoft a amélioré le service pour prendre en charge 128 requêtes simultanées, afin que plusieurs utilisateurs puissent interroger la même base de données sans être bloqués par d’autres demandes. En comparaison, Amazon Redshift limite le nombre maximal de requêtes simultanées à 50, limitant l’accès aux données au sein de l’organisation.

SQL Data Warehouse offre ces performances de requêtes et ces gains de simultanéité de requêtes sans augmentation des prix et en s’appuyant sur son architecture unique avec stockage et calcul découplés.

## <a name="finer-granularity-for-cross-region-and-server-restores"></a>Granularité plus fine pour les restaurations inter-serveur et inter-région
Vous pouvez maintenant effectuer des restaurations entre les régions et les serveurs à l’aide de n’importe quel point de restauration au lieu de sélectionner des sauvegardes géo-redondantes effectuées toutes les 24 heures. Les restaurations inter-serveur et inter-région sont prises en charge pour les points de restauration automatiques ou définis par l’utilisateur et offrent une granularité plus fine pour une protection des données renforcée. Davantage de points de restauration sont disponibles, ce qui garantit que votre entrepôt de données sera logiquement cohérent lors d’une restauration dans différentes régions.

![Commande Restore - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![Options de restauration - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

Pour plus d’informations, consultez le billet de blog [Accelerated and Flexible Restore Points](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) (Points de restauration flexibles et accélérés).

## <a name="20-minute-restorations"></a>Restaurations de 20 minutes
SQL Data Warehouse permet désormais de restaurer n’importe quel entrepôt de données en **moins de 20 minutes** dans la même région, quelle que soit la taille de la base de données. Le délai de restauration s’applique que la restauration se fasse sur un serveur logique identique ou distinct dans la même région. En outre, le processus de capture instantanée a été amélioré pour réduire le délai de création d’un point de restauration. Pour les niveaux de performance inférieurs (paramètres DWU inférieurs), le délai de création d’instantané a été *divisé par 2*.

Pour plus d’informations, consultez le billet de blog [Accelerated and Flexible Restore Points](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) (Points de restauration flexibles et accélérés).

## <a name="custom-restoration-configurations"></a>Configurations de restauration personnalisées
Vous pouvez désormais modifier votre niveau de performance (DWU) lors d’une restauration dans le portail Azure. Vous pouvez également effectuer une restauration vers un entrepôt de données Gen2 mis à niveau. En effectuant une restauration vers une instance de deuxième génération, vous pouvez évaluer l’impact d’une telle instance avant de [mettre à niveau votre entrepôt de données Gen1](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation).

![Configuration de restauration personnalisée - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
La procédure stockée [sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) est souvent utilisée par les outils pour obtenir des métadonnées sur les paramètres dans un lot Transact-SQL. La procédure renvoie une ligne pour chaque paramètre du lot avec les informations de type déduites pour ce paramètre. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

Le jeu de résultats inclut des métadonnées sur le paramètre `@id` (résultats partiels présentés)
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```
## <a name="sprefreshsqlmodule"></a>SP_REFRESHSQLMODULE
La procédure stockée [sp_refreshsqlmodule](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-refreshsqlmodule-transact-sql) met à jour les métadonnées pour un objet de base de données si les métadonnées sous-jacentes sont devenues obsolètes en raison de modifications des objets sous-jacents. Cela peut se produire si les tables de base d’un affichage sont modifiées et que l’affichage n’a pas été recréé. Cela vous évite de les supprimer et de recréer des objets dépendants.

L’exemple ci-dessous présente un affichage qui devient obsolète en raison de la modification de table sous-jacente. Vous remarquerez que les données sont correctes pour la première modification de colonne (1 à Mollie), mais le nom de colonne n’est pas valide et la deuxième colonne n’est pas présente. 
```sql
CREATE TABLE base_table (Id INT);
GO

INSERT INTO base_table (Id) VALUES (1);
GO

CREATE VIEW base_view AS SELECT * FROM base_table;
GO

SELECT * FROM base_view;
GO

-- Id
-- ----
-- 1

DROP TABLE base_table;
GO

CREATE TABLE base_table (fname VARCHAR(10), lname VARCHAR(10));
GO

INSERT INTO base_table (fname, lname) VALUES ('Mollie', 'Gallegos');
GO

SELECT * FROM base_view;
GO

-- Id
-- ----------
-- Mollie

EXEC sp_refreshsqlmodule @Name = 'base_view';
GO

SELECT * FROM base_view;
GO

-- fname     | lname
-- ---------- ----------
-- Mollie    | Gallegos
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
