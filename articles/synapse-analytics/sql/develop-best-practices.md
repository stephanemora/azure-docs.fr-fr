---
title: Meilleures pratiques de développement pour SQL Synapse
description: Recommandations et meilleures pratiques à connaître si vous développez pour SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 3356817fbaef955e05358f9c92619f3c04f747d7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81426949"
---
# <a name="development-best-practices-for-synapse-sql"></a>Meilleures pratiques de développement pour SQL Synapse
Cet article fournit des conseils et décrit les bonnes pratiques à adopter quand vous développez votre solution d’entrepôt de données. 

## <a name="development-best-practices-for-synapse-sql"></a>Meilleures pratiques de développement pour SQL Synapse

### <a name="reduce-cost-with-pause-and-scale"></a>Réduire les coûts avec les opérations de suspension et de mise à l’échelle

Pour plus d’informations sur la réduction des coûts à l’aide de la suspension et de la mise à l’échelle, consultez la section [Gérer le calcul](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Mettre à jour les statistiques

Veillez à mettre à jour vos statistiques quotidiennement ou après chaque charge.  Lorsque vous créez et mettez des statistiques à jour, vous devez toujours faire un compromis entre les performances et les coûts. Si vous trouvez que la mise à jour de toutes vos statistiques prend trop de temps, sélectionnez les colonnes qui contiennent des statistiques ou nécessitent une mise à jour fréquente.  

Par exemple, vous pouvez mettre à jour des colonnes de date, où de nouvelles valeurs peuvent être ajoutées quotidiennement. **Vous tirerez le plus grand avantage de disposer de statistiques sur des colonnes impliquées dans des jointures, utilisées dans la clause WHERE et figurant dans GROUP BY.**

Consultez également [Gestion des statistiques sur les tables](develop-tables-statistics.md), [CREATE STATISTICS](develop-tables-statistics.md) et [UPDATE STATISTICS][UPDATE STATISTICS].

### <a name="hash-distribute-large-tables"></a>Hacher et distribuer de grandes tables

Par défaut, les tables sont distribuées par tourniquet (Round Robin).  Cela aide les utilisateurs à commencer à créer leurs tables sans devoir déterminer comment celles-ci doivent être distribuées.  Des tables round robin peuvent être suffisamment performantes pour certaines charges de travail. Toutefois, dans la plupart des cas, la sélection d’une colonne de distribution sera bien plus performante.  

L’exemple le plus courant de meilleures performances observées avec une table distribuée par une colonne par rapport à une table Round Robin est lorsque deux grandes tables de faits sont jointes.  Par exemple, si vous avez une table de commandes distribuée par order_id, et une table de transactions également distribuée par order_id, lorsque vous joignez votre table de commandes à votre table de transactions sur order_id, cette requête devient une requête directe. Cela signifie que nous éliminons les opérations de déplacement de données.  Moins d’étapes signifie une requête plus rapide.  Moins de déplacement des données permet également d’obtenir des requêtes plus rapides.

Lors du chargement d’une table distribuée, assurez-vous que vos données entrantes ne sont pas triées sur la clé de distribution car cela ralentit vos charges.  Consultez les liens ci-dessous pour des explications plus détaillées sur la façon dont la sélection d’une colonne de distribution peut améliorer les performances, et pour apprendre à définir une table distribuée dans la clause WITH de l’instruction CREATE TABLES.

Voir aussi [Vue d’ensemble des tables][Table overview], [Distribution de tables][Table distribution], [Sélection d’une distribution de tables][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT].

### <a name="do-not-over-partition"></a>Ne pas créer trop de partitions
Bien que le partitionnement des données peut être efficace pour mettre à jour vos données grâce au basculement de partitions ou à l’optimisation des analyses avec élimination des partitions, avoir un trop grand nombre de partitions peut ralentir vos requêtes.  Souvent une stratégie de partitionnement à granularité élevée qui peut fonctionner correctement sur SQL Server peut poser des problèmes sur un pool SQL.  

Un trop grand nombre de partitions peut également réduire l’efficacité des index columnstore en cluster si chaque partition possède moins d’1 million de lignes. Le pool SQL partitionne vos données pour vous en 60 bases de données. Ainsi, si vous créez une table contenant 100 partitions, vous obtenez 6000 bases de données.  Chaque charge de travail est différente ; par conséquent, le meilleur conseil serait d’expérimenter le partitionnement pour voir ce qui fonctionne le mieux pour votre charge de travail.  

L’une des options à prendre en compte est l’utilisation d’une granularité inférieure à celle qui a peut-être fonctionné pour vous dans SQL Server.  Par exemple, utilisez plutôt des partitions hebdomadaires ou mensuelles plutôt que des partitions quotidiennes.

Voir aussi [Partitionnement de table][Table partitioning].

### <a name="minimize-transaction-sizes"></a>Minimiser la taille des transactions

Les instructions INSERT, UPDATE et DELETE s’exécutent dans une transaction, et en cas d’échec elles doivent être restaurées.  Pour minimiser le risque d’une restauration longue, réduisez si possible les tailles de transactions.  Pour ce faire, vous pouvez diviser les instructions INSERT, UPDATE et DELETE en plusieurs parties.  Par exemple, si vous avez une instruction INSERT dont l’exécution devrait prendre une heure, vous pouvez la fractionner en quatre parties de 15 minutes.

Exploitez des cas spéciaux de journalisation minimale, tels que CTAS, TRUNCATE, DROP TABLE ou INSERT, dans des tables vides, afin de réduire le risque de restauration.  Un autre moyen d’éliminer les restaurations consiste à utiliser des opérations de métadonnées uniquement comme le basculement de partitions pour la gestion des données.  Par exemple, plutôt que d’exécuter une instruction DELETE pour supprimer toutes les lignes d’une table où order_date était octobre 2001, vous pouvez partitionner vos données tous les mois et ensuite extraire la partition contenant les données vers une partition vide à partir d’une autre table (voir les exemples ALTER TABLE).  

Pour les tables non partitionnées, utilisez une instruction CTAS pour écrire les données que vous souhaitez conserver dans une table plutôt que l’instruction DELETE.  Si une instruction CTAS prend le même laps de temps, elle permet une opération beaucoup plus sûre car elle offre une journalisation des transactions très minime et peut être annulée rapidement si nécessaire.

Voir aussi [Transactions][Understanding transactions], [Optimisation des transactions][Optimizing transactions], [Partitionnement de table][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [Create table as select (CTAS)][Create table as select (CTAS)].

### <a name="use-the-smallest-possible-column-size"></a>Utiliser la plus petite taille de colonne possible

Lorsque vous définissez votre commande DDL, l’utilisation du plus petit type de données prenant en charge vos données améliore les performances de requête.  Ceci est particulièrement important pour les colonnes CHAR et VARCHAR.  Si la valeur la plus longue dans une colonne est de 25 caractères, définissez la colonne en tant que VARCHAR(25).  Évitez de définir toutes les colonnes de caractères sur une grande longueur par défaut.  En outre, définissez des colonnes VARCHAR lorsque cela suffit, au lieu d’utiliser NVARCHAR.

Voir aussi [Vue d’ensemble des tables][Table overview], [Types de données des tables](develop-tables-data-types.md), [CREATE TABLE][CREATE TABLE]

### <a name="optimize-clustered-columnstore-tables"></a>Optimiser les tables columnstore en clusters

Les index columnstore en cluster sont l’une des méthodes les plus efficaces pour stocker vos données dans le pool SQL.  Par défaut, les tables dans le pool SQL sont créées en tant que ColumnStore en cluster.  Pour obtenir les meilleures performances pour les requêtes sur les tables columnstore, la qualité du segment est importante.  Lorsque les lignes sont écrites dans les tables columnstore avec une mémoire insuffisante, la qualité du segment columnstore peut être affectée.  

La qualité du segment peut être mesurée par le nombre de lignes dans un groupe de lignes compressé.  Consultez la section [Causes de la qualité médiocre des index columnstore][Causes of poor columnstore index quality] dans l’article [Index de table][Table indexes] pour obtenir des instructions étape par étape sur la détection et l’amélioration de la qualité de segment pour les tables columnstore en cluster.  Étant donné que la qualité des segments columnstore est importante, nous vous conseillons d’utiliser des ID d’utilisateurs qui se trouvent dans la classe de ressource de moyenne ou grande taille pour le chargement des données. L’utilisation d’[unités DWU (Data Warehouse Unit)](azure -synapse-resource-consumption-models.md) inférieures signifie que vous souhaitez assigner une classe de ressource plus grande à votre utilisateur de chargement.

Étant donné que les tables columnstore ne transmettent généralement pas de données dans un segment columnstore compressé s’il existe moins de 1 million de lignes par table et si chaque table du pool SQL est partitionnée en 60 tables, les tables columnstore ne tireront aucun avantage d’une requête, sauf si la table comporte plus de 60 millions de lignes.  Pour les tables contenant moins de 60 millions lignes, il se peut qu’un index columnstore ne soit pas la solution optimale.  

En outre, si vous partitionnez vos données, vous souhaiterez peut-être estimer que chaque partition nécessitera 1 million de lignes pour bénéficier d’un index columnstore en cluster.  Si une table possède 100 partitions, elle devra avoir au moins 6 milliards de lignes pour bénéficier d’une banque de colonnes en cluster (60 distributions *100 partitions* 1 million de lignes).  

Si votre table ne possède pas 6 milliards de lignes, réduisez le nombre de partitions ou envisagez d’utiliser une table de segments de mémoire.  Il peut également être intéressant d’expérimenter pour voir si de meilleures performances peuvent être obtenues en utilisant une table de segments de mémoire avec des index secondaires plutôt qu’avec une table columnstore.

Lorsque vous interrogez une table columnstore, les requêtes s’exécutent plus vite si vous sélectionnez uniquement les colonnes dont vous avez besoin.  

Consultez également [Index de table][Table indexes], [Guide des index columnstore][Columnstore indexes guide] et [Reconstruction des index columnstore][Rebuilding columnstore indexes].

### <a name="next-steps"></a>Étapes suivantes

Si vous ne trouvez pas ce que vous cherchez dans cet article, essayez d’utiliser la fonction de recherche de documents située sur le côté gauche de cette page pour rechercher tous les documents relatifs au pool SQL Azure.  Le [Forum sur le pool SQL Azure][Azure SQL pool MSDN Forum] vous permet de poser des questions à d’autres utilisateurs et au groupe du produit pool SQL.  

Nous suivons activement ce forum pour vous assurer que vos questions sont traitées par un autre utilisateur ou un membre de notre équipe.  Si vous préférez poser vos questions sur Stack Overflow, nous avons également un [Forum Stack Overflow sur le pool SQL Azure][Azure SQL pool Stack Overflow Forum].

Utilisez la page des [commentaires relatifs au pool SQL Azure][Azure SQL pool Feedback] pour demander des fonctionnalités.  L’ajout de vos demandes ou la confirmation des autres demandes nous permet vraiment de hiérarchiser les fonctions.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Create table as select (CTAS)]: ../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Table overview]:develop-tables-overview.md
[Table distribution]: ../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Table indexes]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Causes of poor columnstore index quality]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Temporary tables]:develop-tables-temporary.md
[Guide for using PolyBase]:../sql-analytics/data-loading-best-practices.md
[Load data]: ../sql-data-warehouse/esign-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Move data with Azure Data Factory]: ../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Load data with Azure Data Factory]: ../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Monitor your workload using DMVs]: ../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Pause compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#pause-compute-bk?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Resume compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#resume-compute-bk?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Scale compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scale-compute?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Understanding transactions]:develop-transactions.md
[Optimizing transactions]: ../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Troubleshooting]: ../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[LABEL]:develop-label.md

<!--MSDN references-->
[ALTER TABLE]: /sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE EXTERNAL FILE FORMAT]: /sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE STATISTICS]: /sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE TABLE]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE TABLE AS SELECT]: /sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[DBCC PDW_SHOWEXECUTIONPLAN]: /sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[INSERT]: /sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[OPTION]: /sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[TRUNCATE TABLE]: /sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[UPDATE STATISTICS]: /sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_exec_sessions]: /sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_exec_requests]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_request_steps]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_sql_requests]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_dms_workers]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_waits]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[Columnstore indexes guide]: /sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL pool Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL pool MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL pool Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL pool loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/

## <a name="development-best-practices-for-sql-on-demand-preview"></a>Meilleures pratiques de développement pour SQL à la demande (préversion)

### <a name="general-considerations"></a>Considérations d’ordre général

SQL à la demande vous permet d’interroger des fichiers dans vos comptes de stockage Azure. Il ne dispose pas de capacités de stockage ou d’ingestion locales, ce qui signifie que tous les fichiers que la requête cible sont externes à SQL à la demande. Par conséquent, tout ce qui est lié à la lecture de fichiers à partir du stockage peut avoir un impact sur les performances des requêtes.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Colocaliser le compte Stockage Azure et SQL à la demande

 
Pour réduire la latence, colocalisez votre compte de stockage Azure et votre point de terminaison SQL à la demande. Les comptes de stockage et les points de terminaison approvisionnés lors de la création de l’espace de travail se trouvent dans la même région. 
 
Pour optimiser les performances, si vous accédez à d’autres comptes de stockage avec SQL à la demande, assurez-vous qu’ils se trouvent dans la même région. Autrement, vous obtiendrez une latence accrue pour le transfert réseau des données de la région distante vers la région du point de terminaison.

### <a name="azure-storage-throttling"></a>Limitation de Stockage Azure

Plusieurs applications et services peuvent accéder à votre compte de stockage. Lorsque les IOPS ou le débit combinés générés par des applications, services et charges de travail SQL à la demande dépassent les limites du compte de stockage, une limitation du stockage se produit. Celle-ci a un impact négatif substantiel sur les performances des requêtes. 
 
Quand une limitation est détectée, SQL à la demande dispose d’une fonction intégrée pour la gestion de ce scénario. SQL à la demande ralentit la fréquence des demandes adressées au stockage jusqu’à ce que la limitation soit résolue. Toutefois, pour optimiser l’exécution des requêtes, il est recommandé de ne pas solliciter le compte de stockage avec d’autres charges de travail lors de l’exécution de la requête.

### <a name="prepare-files-for-querying"></a>Préparer les fichiers pour l’interrogation

Si possible, vous pouvez préparer les fichiers pour améliorer les performances :

- Convertir CSV en Parquet – Parquet est un format en colonnes. Dans la mesure où il est compressé, les fichiers sont de plus petite taille que des fichiers CSV contenant les mêmes données, et SQL à la demande a besoin de moins de temps et de demandes de stockage pour le lire.
- Si une requête cible un seul fichier volumineux, il est avantageux de fractionner celui-ci en fichiers plus petits.
- Essayez de conserver une taille de fichier CSV inférieure à 10 Go.
- Il est préférable d’avoir des fichiers de taille identique pour un chemin d’accès OPENROWSET unique ou un emplacement de table externe.
- Partitionnez vos données en stockant des partitions dans différents dossiers ou noms de fichiers. Consultez [Utiliser les fonctions filename et filepath pour cibler des partitions spécifiques](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Utiliser les fonctions filename et filepath pour cibler des partitions spécifiques

Les données sont souvent organisées en partitions. Vous pouvez donner pour instruction à SQL à la demande d’interroger des dossiers et fichiers particuliers. Cela permet de réduire le nombre de fichiers et la quantité de données que la requête doit lire et traiter. Par conséquent, vous obtiendrez de meilleures performances. Pour plus d’informations, consultez les fonctions [filename](develop-storage-files-overview.md#filename-function) et [filepath](develop-storage-files-overview.md#filepath-function), ainsi que les exemples montrant comment [interroger des fichiers spécifiques](query-specific-files.md).

Si vos données dans le stockage ne sont pas partitionnées, envisagez de les partitionner afin de pouvoir utiliser ces fonctions pour optimiser les requêtes ciblant ces fichiers.

Lorsque de l’[interrogation de tables Spark partitionnées](develop-storage-files-spark-tables.md) à partir de SQL à la demande, la requête cible automatiquement uniquement les fichiers nécessaires.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Utiliser CETAS pour améliorer les performances des requêtes et les jointures

[CETAS](develop-tables-cetas.md) est l’une des fonctionnalités les plus importantes disponibles dans SQL à la demande. CETAS est une opération parallèle qui crée des métadonnées de table externe et exporte le résultat de la requête SELECT vers un ensemble de fichiers dans votre compte de stockage.

Vous pouvez utiliser CETAS pour stocker dans un nouveau jeu de fichiers des parties de requêtes souvent utilisées, telles des tables de référence jointes. Par la suite, vous pouvez joindre une telle table externe au lieu de répéter des jointures communes dans plusieurs requêtes. Comme CETAS génère des fichiers Parquet, les statistiques sont automatiquement créées lorsque la première requête cible cette table externe, et les performances sont améliorées.
