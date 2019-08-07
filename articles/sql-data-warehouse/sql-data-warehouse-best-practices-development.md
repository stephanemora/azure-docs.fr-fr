---
title: Bonnes pratiques de développement pour Azure SQL Data Warehouse | Microsoft Docs
description: Recommandations et meilleures pratiques que vous devez connaître pour développer des solutions pour Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 7e0ae5e6159ae0ab4d098d717f433d2ab63770d4
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479734"
---
# <a name="development-best-practices-for-azure-sql-data-warehouse"></a>Bonnes pratiques de développement pour Azure SQL Data Warehouse
Cet article fournit des conseils et décrit les bonnes pratiques à adopter quand vous développez votre solution d’entrepôt de données. 

## <a name="reduce-cost-with-pause-and-scale"></a>Réduire les coûts avec les opérations de suspension et de mise à l’échelle
Pour plus d’informations sur la réduction des coûts à l’aide de la suspension et de la mise à l’échelle, consultez la section [Gérer le calcul](sql-data-warehouse-manage-compute-overview.md). 


## <a name="maintain-statistics"></a>Mettre à jour les statistiques
Veillez à mettre à jour vos statistiques quotidiennement ou après chaque charge.  Lorsque vous créez et mettez des statistiques à jour, vous devez toujours faire un compromis entre les performances et les coûts. Si vous trouvez que la mise à jour de toutes vos statistiques est trop longue, vous souhaiterez peut-être sélectionner les colonnes qui possèdent des statistiques ou celles nécessitant une mise à jour fréquente.  Par exemple, vous pouvez mettre à jour des colonnes de date, où de nouvelles valeurs peuvent être ajoutées de façon quotidienne. **Vous bénéficierez de performances optimales en lançant des statistiques sur les colonnes impliquées dans les jointures, celles utilisées dans la clause WHERE et celles figurant dans GROUP BY.**

Voir aussi [Gestion des statistiques des tableaux][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="hash-distribute-large-tables"></a>Hacher et distribuer de grandes tables
Par défaut, les tables sont distribuées par tourniquet (Round Robin).  Cela aide les utilisateurs à commencer la création de leurs tables sans avoir à déterminer comment les tables doivent être distribuées.  Les tables distribuées par tourniquet (Round Robin) peuvent offrir des performances suffisantes pour certaines charges de travail, mais souvent la sélection d’une colonne de distribution s’avérera plus efficace.  L’exemple le plus courant de meilleures performances observées avec une table distribuée par une colonne par rapport à une table Round Robin est lorsque deux grandes tables de faits sont jointes.  Par exemple, si vous avez une table de commandes, qui est distribuée par order_id, et une table de transactions, également distribuée par order_id, lorsque vous joignez votre table de commandes à votre table de transactions sur order_id, cette requête devient une requête directe, ce qui signifie que nous éliminons les opérations de déplacement de données.  Moins d’étapes signifie une requête plus rapide.  Moins de déplacement des données permet également d’obtenir des requêtes plus rapides.  Cette explication ne fait que survoler le sujet. Lors du chargement d’une table distribuée, assurez-vous que vos données entrantes ne sont pas triées sur la clé de distribution car cela ralentit vos charges.  Consultez les liens ci-dessous pour des explications très détaillées sur la façon dont la sélection d’une colonne de distribution peut améliorer les performances et pour apprendre à définir une table distribuée dans la clause WITH de l’instruction CREATE TABLES.

Voir aussi [Vue d’ensemble des tableaux][Table overview], [Table distribution][Table distribution], [Sélection d’une distribution de tableaux][Sélection d’une distribution de tableaux], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Ne pas créer trop de partitions
Bien que le partitionnement des données peut être très efficace pour mettre à jour vos données grâce au basculement de partitions ou à l’optimisation des analyses avec élimination de partition, avoir un trop grand nombre de partitions peut ralentir vos requêtes.  Souvent une stratégie de partitionnement à granularité élevée qui peut fonctionner correctement sur SQL Server peut poser des problèmes sur SQL Data Warehouse.  Un trop grand nombre de partitions peut également réduire l’efficacité des index columnstore en cluster si chaque partition possède moins d’1 million de lignes.  N’oubliez pas que, en arrière-plan, SQL Data Warehouse partitionne vos données en 60 bases de données, donc si vous créez une table avec 100 partitions, cela produit en réalité 6 000 partitions.  Chaque charge de travail est différente ; par conséquent, le meilleur conseil serait d’expérimenter le partitionnement pour voir ce qui fonctionne le mieux pour votre charge de travail.  Envisagez d’abaisser le niveau de granularité à un niveau inférieur à celui qui a fonctionné pour vous dans SQL Server.  Par exemple, utilisez plutôt des partitions hebdomadaires ou mensuelles plutôt que des partitions quotidiennes.

Voir aussi [Partitionnement de table][Table partitioning]

## <a name="minimize-transaction-sizes"></a>Minimiser la taille des transactions
Les instructions INSERT, UPDATE et DELETE s’exécutent dans une transaction, et en cas d’échec elles doivent être restaurées.  Pour minimiser le risque d’une restauration longue, réduisez si possible les tailles de transactions.  Pour ce faire, vous pouvez diviser les instructions INSERT, UPDATE et DELETE en plusieurs parties.  Par exemple, si vous disposez d’une instruction INSERT qui devrait prendre une heure, décomposez si possible l’insertion en 4 parties, qui seront chacune exécutées en 15 minutes.  Exploitez des cas spéciaux de journalisation minimale, tels que CTAS, TRUNCATE, DROP TABLE ou INSERT, dans des tables vides, afin de réduire le risque de restauration.  Un autre moyen d’éliminer les restaurations consiste à utiliser des opérations de métadonnées uniquement comme le basculement de partitions pour la gestion des données.  Par exemple, plutôt que d’exécuter une instruction DELETE pour supprimer toutes les lignes d’une table où order_date était octobre 2001, vous pouvez partitionner vos données tous les mois et ensuite extraire la partition contenant les données vers une partition vide à partir d’une autre table (voir les exemples ALTER TABLE).  Pour les tables non partitionnées, utilisez une instruction CTAS pour écrire les données que vous souhaitez conserver dans une table plutôt que l’instruction DELETE.  Si une instruction CTAS prend le même laps de temps, elle permet une opération beaucoup plus sûre car elle offre une journalisation des transactions très minime et peut être annulée rapidement si nécessaire.

Voir aussi [Comprendre les transactions][Understanding transactions], [Optimizing transactions][Optimizing transactions], [Partitionnement de tableau][Partitionnement de tableau],[TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [Create table as select (CTAS)][Create table as select (CTAS)]

## <a name="use-the-smallest-possible-column-size"></a>Utiliser la plus petite taille de colonne possible
Lorsque vous définissez votre commande DDL, l’utilisation du plus petit type de données prenant en charge vos données améliore les performances de requête.  Ceci est particulièrement important pour les colonnes CHAR et VARCHAR.  Si la valeur la plus longue dans une colonne est de 25 caractères, définissez la colonne en tant que VARCHAR(25).  Évitez de définir toutes les colonnes de caractères sur une grande longueur par défaut.  En outre, définissez des colonnes VARCHAR lorsque cela suffit, au lieu d’utiliser NVARCHAR.

Voir aussi [Vue d’ensemble des tableaux][Table overview], [Table data types][Table data types], [CREATE TABLE][CREATE TABLE]

## <a name="optimize-clustered-columnstore-tables"></a>Optimiser les tables columnstore en clusters
Les index columnstore en cluster sont l’une des méthodes les plus efficaces pour stocker vos données dans SQL Data Warehouse.  Par défaut, les tables dans SQL Data Warehouse sont créées en tant que ColumnStore en cluster.  Pour obtenir les meilleures performances pour les requêtes sur les tables columnstore, la qualité du segment est importante.  Lorsque les lignes sont écrites dans les tables columnstore avec une mémoire insuffisante, la qualité du segment columnstore peut être affectée.  La qualité du segment peut être mesurée par le nombre de lignes dans un groupe de lignes compressé.  Consultez l’article[Causes de la qualité médiocre des index columnstore][Causes of poor columnstore index quality] in the [Table indexes][Table indexes] pour obtenir des instructions étape par étape sur la détection et l’amélioration de la qualité de segment pour les tableaux columnstore en cluster.  Étant donné que la qualité des segments columnstore est importante, nous vous conseillons d’utiliser des ID d’utilisateurs qui se trouvent dans la classe de ressource de moyenne ou grande taille pour le chargement des données. L’utilisation d’[unités DWU (Data Warehouse Unit)](what-is-a-data-warehouse-unit-dwu-cdwu.md) inférieures signifie que vous souhaitez assigner une classe de ressource plus grande à votre utilisateur de chargement.

Étant donné que les tables columnstore ne transmettent généralement pas de données dans un segment columnstore compressé s’il existe moins d’1 million de lignes par table et si chaque table SQL Data Warehouse est partitionnée en 60 tables, en règle générale, les tables columnstore ne tireront aucun profit d’une requête, sauf si la table comporte plus de 60 millions de lignes.  Pour une table comportant moins de 60 millions de lignes, il ne sera peut-être pas judicieux d’avoir un index columnstore.  Mais cela ne peut pas nuire non plus.  En outre, si vous partitionnez vos données, vous souhaiterez peut-être estimer que chaque partition nécessitera 1 million de lignes pour bénéficier d’un index columnstore en cluster.  Si une table possède 100 partitions, elle devra avoir au moins 6 milliards de lignes pour bénéficier d’une banque de colonnes en cluster (60 distributions * 100 partitions * 1 million de lignes).  Si votre table ne possède pas six milliards de lignes dans cet exemple, réduisez le nombre de partitions ou envisagez plutôt d’utiliser une table de segment de mémoire.  Il peut être également intéressant de tester pour voir si de meilleures performances peuvent être obtenues avec une table de segment de mémoire ayant des index secondaires plutôt qu’avec une table columnstore.

Lorsque vous interrogez une table columnstore, les requêtes s’exécutent plus vite si vous sélectionnez uniquement les colonnes dont vous avez besoin.  

Voir aussi [Index de tableau][Table indexes], [Columnstore indexes guide][Columnstore indexes guide], [Reconstruction des index columnstore][Reconstruction des index columnstore]

## <a name="next-steps"></a>Étapes suivantes
Si vous ne trouvez pas ce que vous recherchez dans cet article, essayez d’utiliser la fonction de recherche de documents située sur le côté gauche de cette page pour rechercher tous les documents relatifs à Azure SQL Data Warehouse.  Le [Forum Azure SQL Data Warehouse][Azure SQL Data Warehouse MSDN Forum] is a place for you to ask questions to other users and to the SQL Data Warehouse Product Group.  We actively monitor this forum to ensure that your questions are answered either by another user or one of us.  If you prefer to ask your questions on Stack Overflow, we also have an [Azure SQL Data Warehouse Stack Overflow Forum][Azure SQL Data Warehouse Stack Overflow Forum].  Enfin, utilisez la page des [commentaires relatifs à Azure SQL Data Warehouse][Azure SQL Data Warehouse Feedback] afin de faire des demandes de fonctionnalités.  L’ajout de vos demandes ou la confirmation des autres demandes nous permet vraiment de hiérarchiser les fonctions.

Finally, please do use the <bpt id="p1">[</bpt>Azure SQL Data Warehouse Feedback<ept id="p1">][Azure SQL Data Warehouse Feedback]</ept> page to make feature requests.  Adding your requests or up-voting other requests really helps us prioritize features.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
