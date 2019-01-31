---
title: Notes de publication Azure SQL Data Warehouse - Décembre 2018 | Microsoft Docs
description: Notes de publication pour Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: f5060ab054d5b55b4b31ff01b00e30a34e6ebdce
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55463427"
---
# <a name="whats-new-in-azure-sql-data-warehouse-december-2018"></a>Nouveautés dans Azure SQL Data Warehouse Décembre 2018
Azure SQL Data Warehouse reçoit continuellement des améliorations. Cet article décrit les nouvelles fonctionnalités et les modifications qui ont été introduites en décembre 2018.

## <a name="virtual-network-service-endpoints-generally-available"></a>Disponibilité générale des points de terminaison de service de réseau virtuel
Cette version inclut la disponibilité générale des points de terminaison de service de réseau virtuel pour Azure SQL Data Warehouse dans toutes les régions Azure. Les points de terminaison de service de réseau virtuel vous donnent les moyens d’isoler la connectivité à votre serveur logique à partir d’un sous-réseau ou ensemble de sous-réseaux donnés dans votre réseau virtuel. Le trafic entre votre réseau virtuel et Azure SQL Data Warehouse demeure au sein du réseau principal Azure. Cet itinéraire direct est préféré à tous les itinéraires spécifiques qui acheminent le trafic Internet via des appliances virtuelles ou locales. Il n’existe aucune facturation supplémentaire pour l’accès au réseau virtuel par l’intermédiaire de points de terminaison de service. Le modèle de tarification actuel pour [Azure SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/) reste le même.

Avec cette version, nous activons également la connectivité PolyBase à [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (ADLS) via le pilote [Azure Blob File System](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) (ABFS). Azure Data Lake Storage Gen2 apporte au Stockage Azure toutes les qualités nécessaires pour le cycle de vie complet des données analytiques. Il rassemble les fonctionnalités de nos deux services de stockage actuels : Stockage Blob Azure et Azure Data Lake Storage Gen1. Les fonctionnalités d’[Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), comme la sémantique des systèmes de fichiers, la sécurité au niveau du fichier et la mise à l'échelle, sont combinées à celles du [Stockage Blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction), comme le stockage hiérarchisé économique et la haute disponibilité/reprise d’activité après sinistre. 

En utilisant PolyBase, vous pouvez importer des données Azure SQL Data Warehouse d’une instance Stockage Azure sécurisée vers votre réseau virtuel. De même, l’exportation des données d’Azure SQL Data Warehouse vers une instance Stockage Azure sécurisée sur votre réseau virtuel est également prise en charge via Polybase. 

Pour plus d’informations sur les points de terminaison de service de réseau virtuel dans Azure SQL Data Warehouse, consultez le [billet de blog](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/) ou la [documentation](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json).

## <a name="automatic-performance-monitoring-preview"></a>Analyse des performances automatique (préversion)
Le [Magasin de données des requêtes](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017) est maintenant disponible en préversion pour Azure SQL Data Warehouse. Le Magasin de données des requêtes est conçu pour faciliter la résolution des problèmes rencontrés en matière de performance des requêtes en effectuant le suivi des requêtes, des plans de requête, des statistiques de runtime et de l’historique des requêtes pour vous aider à surveiller l’activité et les performances de votre entrepôt de données. Le Magasin de données des requêtes est un ensemble de magasins internes et de vues de gestion dynamique (DMV) qui vous permettent d’effectuer les opérations suivantes :

- Identifier et paramétrer les principales requêtes consommatrices de ressources
- Identifier et améliorer les charges de travail ad hoc
- Évaluer les performances des requêtes et l’impact sur le plan en fonction des modifications des statistiques, des index ou de la taille du système (paramètre DWU)
- Voir le texte de requête complet pour toutes les requêtes exécutées

Le Magasin de données des requêtes contient trois magasins réels :  
- Un magasin de plans pour rendre persistantes les informations du plan d’exécution 
- Un magasin des statistiques de runtime pour conserver les informations sur les statistiques d’exécution
- Un magasin des statistiques d’attente pour conserver les informations sur les statistiques d’attente. 

SQL Data Warehouse gère automatiquement ces magasins et fournit un nombre illimité de requêtes stockées sur les sept derniers jours sans frais supplémentaires. L’activation du Magasin de données des requêtes est aussi simple que d’exécuter une instruction T-SQL ALTER DATABASE :

```sql
ALTER DATABASE [Database Name] SET QUERY_STORE = ON;
```
Pour plus d’informations sur le Magasin de données des requêtes dans Azure SQL Data Warehouse, consultez l’article [Analyse des performances à l’aide du Magasin de données des requêtes](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017) et les vues de gestion dynamique (DMV) du Magasin de données des requêtes, telles que [sys.query_store_query](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?view=sql-server-2017). Voici le [billet de blog](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/) annonçant la publication.

## <a name="lower-compute-tiers-for-azure-sql-data-warehouse-gen2"></a>Niveaux inférieurs de calcul pour Azure SQL Data Warehouse Gen2
Azure SQL Data Warehouse Gen2 prend à présent en charge des niveaux inférieurs de calcul. Les clients peuvent bénéficier des fonctionnalités de performances, de flexibilité et de sécurité exceptionnelles d’Azure SQL Data Warehouse à partir de 100 cDWU ([Data Warehouse Units](https://docs.microsoft.com/azure/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu)) et mettre à l’échelle jusqu’à 30 000 cDWU en quelques minutes. Depuis la mi-décembre 2018, les clients bénéficient de performances Gen2 et de niveaux inférieurs de calcul dans [régions](https://docs.microsoft.com/azure/sql-data-warehouse/gen2-lower-tier-regions). Le reste des régions sera disponible en 2019.

En supprimant le point d’entrée pour l’entreposage de données nouvelle génération, Microsoft ouvre les portes aux clients qui souhaitent évaluer tous les avantages offerts par un entrepôt de données sécurisé et à hautes performances sans avoir à deviner quel environnement d’essai est le mieux adapté. Les clients peuvent dorénavant commencer à 100 cDWU, alors que le point d’entrée précédant était de 500 cDWU. SQL Data Warehouse Gen2 continue à prendre en charge les opérations de suspension et de reprise, et va bien au-delà de la simple flexibilité de calcul. Gen2 prend également en charge une capacité illimitée de stockage columnstore, 2,5 fois plus de mémoire par requête, jusqu’à 128 requêtes simultanées et des fonctionnalités de [mise en cache adaptative](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/). Ces fonctionnalités offrent en moyenne des performances multipliées par cinq par rapport à la même instance Data Warehouse Unit sur Gen1 au même prix. Les sauvegardes géoredondantes sont standard pour Gen2, avec protection des données garantie intégrée. Azure SQL Data Warehouse Gen2 est prêt pour une mise à l’échelle à tout moment.

## <a name="columnstore-background-merge"></a>Fusion en arrière-plan de columnstore
Par défaut, Azure SQL Data Warehouse (Azure SQL DW) stocke les données dans un format en colonnes, avec des micro-partitions appelées [rowgroups](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression). Parfois, en raison de contraintes de mémoire au moment de la génération de l’index ou du chargement des données, les rowgroups peuvent être compressés alors qu’ils contiennent moins d’un million de lignes, leur taille optimale. Les rowgroups peuvent également devenir fragmentés en raison de suppressions. Des rowgroups de petite taille ou fragmentés entraînent une consommation de mémoire supérieure ainsi qu’une exécution inefficace des requêtes. Avec cette version d’Azure SQL DW, la tâche de maintenance en arrière-plan de columnstore fusionne les petits rowgroups compressés pour en créer des plus grands afin de mieux utiliser la mémoire et d’accélérer l’exécution des requêtes.

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
[Forum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vidéos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
