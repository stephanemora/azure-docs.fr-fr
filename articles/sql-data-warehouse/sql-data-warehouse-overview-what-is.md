---
title: Qu’est-ce qu’Azure Synapse Analytics (anciennement SQL DW) ?
description: Azure Synapse Analytics (anciennement SQL DW) est un service d’analytique illimité qui regroupe l’entreposage des données d’entreprise et l’analytique de Big Data.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 07bf3070e42af8a03ce1fd7ea4445fc76557e8a3
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645514"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Qu’est-ce qu’Azure Synapse Analytics (anciennement SQL DW) ?

Azure Synapse est un service d’analytique illimité qui regroupe l’entreposage des données d’entreprise et l’analytique de Big Data. Il vous donne la possibilité d’interroger les données avec votre propre vocabulaire, en utilisant des ressources serverless à la demande ou des ressources provisionnées, le tout à grande échelle. Azure Synapse rassemble ces deux mondes avec une expérience unifiée pour la réception, la préparation, la gestion et la remise de données pour les besoins immédiats d’apprentissage automatique et décisionnels

Azure Synapse comporte quatre composants :
- SQL Analytics : Effectuer des analyses basées sur T-SQL – Disponibilité générale
    - Pool SQL (paiement par DWU configuré) 
    - SQL à la demande (paiement par To traité) – (préversion)
- Spark : Apache Spark profondément intégré (préversion) 
- Intégration des données : Intégration des données hybrides (préversion)
- Studio : Expérience d'utilisateur final unifiée.  (Préversion)

> [!NOTE]
> Pour accéder aux fonctionnalités en préversion d’Azure Synapse, demandez l’accès [ici](https://aka.ms/synapsepreview). Microsoft triera toutes les demandes et y répondra dès que possible.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>SQL Analytics et pool SQL dans Azure Synapse

SQL Analytics fait référence aux fonctionnalités d’entreposage de données d’entreprise qui sont généralement disponibles avec Azure Synapse. 

Le pool SQL représente une collection de ressources analytiques en cours de configuration lors de l’utilisation de SQL Analytics. La taille du pool SQL est déterminée par les unités d’entreposage de données (DWU).

Importez des données volumineuses avec des requêtes T-SQL [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) simples, puis utilisez le traitement massivement parallèle pour exécuter des analyses hautes performances. À mesure de vos intégrations et analyses, SQL Analytics deviendra la seule source pertinente sur laquelle votre activité peut compter pour obtenir des informations plus rapides et robustes.  

## <a name="key-component-of-a-big-data-solution"></a>Composant clé de la solution Big Data

L’entreposage de données est un composant clé d’une solution de Big Data de bout en bout basée sur le cloud.

![Solution d’entrepôt de données](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

Dans une solution de données cloud, les données sont ingérées dans des magasins de données volumineuses à partir de diverses sources. Une fois dans un magasin de données volumineuses, des algorithmes d’apprentissage de données, Spark et Hadoop préparent et forment les données. Lorsque les données sont prêtes pour l’analyse complexe, SQL Analytics utilise PolyBase pour interroger les magasins de données volumineuses. PolyBase utilise des requêtes T-SQL standard pour rassembler les données dans des tables SQL Analytics.
 
SQL Analytics stocke les données dans des tables relationnelles avec un stockage en colonnes. Ce format réduit considérablement le coût de stockage des données et améliore les performances de requête. Une fois les données stockées, vous pouvez exécuter des analyses à grande échelle. Par rapport aux systèmes de base de données classiques, les requêtes d’analyses se terminent en quelques secondes plutôt qu’en quelques minutes, ou en quelques heures plutôt qu’en quelques jours. 

Les résultats d’analyse peuvent s’étendre aux applications ou bases de données de rapports mondiales. Les analystes commerciaux peuvent alors obtenir des informations pour prendre des décisions éclairées pour l’activité.

## <a name="next-steps"></a>Étapes suivantes

- Explorer [l’Architecture d’Azure Synapse](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- [Créer rapidement un pool SQL](create-data-warehouse-portal.md)
- [Charger des exemples de données][load sample data]
- Explorer les [vidéos](/azure/sql-data-warehouse/sql-data-warehouse-videos)

Ou bien, consultez ces autres ressources d’Azure Synapse.  
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
[create a data warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse Analytics solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
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
[SLA for Azure Synapse Analytics]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
