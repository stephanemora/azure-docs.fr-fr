---
title: En quoi consiste Azure SQL Data Warehouse ? | Microsoft Docs
description: Base de données distribuée dédiée aux entreprises, capable de traiter des pétaoctets de données relationnelles et non relationnelles. Il s’agit du premier entrepôt de données cloud prenant en charge l’augmentation, la réduction et la pause en quelques secondes.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 05/30/2019
ms.author: martinle
ms.reviewer: igorstan
mscustom: sqlfreshmay19
ms.openlocfilehash: a9126e9023091dd8c3df71f2aa2558a01227a8be
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428037"
---
# <a name="what-is-azure-sql-data-warehouse"></a>En quoi consiste Azure SQL Data Warehouse ?

SQL Data Warehouse est un entrepôt de données d’entreprise basé sur le cloud qui utilise le traitement massivement parallèle pour exécuter rapidement des requêtes complexes parmi des pétaoctets de données. Utilisez SQL Data Warehouse comme composant clé d’une solution Big Data. Importez des données volumineuses dans SQL Data Warehouse avec des requêtes T-SQL [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) simples, puis utilisez le traitement massivement parallèle pour exécuter des analyses hautes performances. À mesure de vos intégrations et analyses, l’entrepôt de données deviendra la seule source pertinente sur laquelle votre activité peut compter pour obtenir des informations précises.  

## <a name="key-component-of-big-data-solution"></a>Composant clé de la solution Big Data

SQL Data Warehouse est un composant clé d’une solution Big Data complète dans le cloud.

![Solution d’entrepôt de données](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

Dans une solution de données cloud, les données sont ingérées dans des magasins de données volumineuses à partir de diverses sources. Une fois dans un magasin de données volumineuses, des algorithmes d’apprentissage de données, Spark et Hadoop préparent et forment les données. Lorsque les données sont prêtes pour l’analyse complexe, SQL Data Warehouse utilise PolyBase pour interroger les magasins de données volumineuses. PolyBase utilise des requêtes T-SQL standard pour rassembler les données dans SQL Data Warehouse.
 
SQL Data Warehouse stocke les données dans des tables relationnelles avec stockage en colonnes. Ce format réduit considérablement le coût de stockage des données et améliore les performances de requête. Une fois que les données sont stockées dans SQL Data Warehouse, vous pouvez exécuter des analyses à très grande échelle. Par rapport aux systèmes de base de données classiques, les requêtes d’analyses se terminent en quelques secondes plutôt qu’en quelques minutes, ou en quelques heures plutôt qu’en quelques jours. 

Les résultats d’analyse peuvent s’étendre aux applications ou bases de données de rapports mondiales. Les analystes commerciaux peuvent alors obtenir des informations pour prendre des décisions éclairées pour l’activité.

## <a name="next-steps"></a>Étapes suivantes

- Explorer la section [Architecture Azure SQL Data Warehouse](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- [Créer rapidement une base de données SQL Data Warehouse][create a SQL Data Warehouse]
- [Charger les exemples de données][load sample data].
- Explorer les [vidéos](/azure/sql-data-warehouse/sql-data-warehouse-videos)

Ou bien, consultez ces autres ressources de SQL Data Warehouse.  
* Effectuer des recherches dans les [blogs]
* Soumettre une [Demandes de fonctionnalités]
* Effectuer des recherches dans les [blogs de l’équipe de conseil clientèle]
* [Création d’un ticket de support]
* Effectuer des recherches dans [Forum MSDN]
* Effectuer des recherches dans le [forum Stack Overflow]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Création d’un ticket de support]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs de l’équipe de conseil clientèle]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Demandes de fonctionnalités]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Forum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
