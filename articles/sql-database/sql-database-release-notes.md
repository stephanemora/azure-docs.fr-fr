---
title: Notes de publication de base de données SQL Azure | Microsoft Docs
description: En savoir plus sur les nouvelles fonctionnalités et améliorations dans le service de base de données SQL Azure et dans la documentation de base de données SQL Azure
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: carlrab
ms.openlocfilehash: e6d702c7a3194f07f9d04139acbc9b6101b296ea
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759458"
---
# <a name="sql-database-release-notes"></a>Notes de publication de base de données SQL

Cet article répertorie les nouvelles fonctionnalités et améliorations dans le service de base de données SQL et dans la documentation de base de données SQL. Pour les améliorations du service de base de données SQL, consultez également [mises à jour du service de base de données SQL](https://azure.microsoft.com/updates/?product=sql-database). Améliorations apportées à d’autres services Azure, consultez [mises à jour de Service](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Fonctionnalités en version préliminaire publique

| Fonctionnalité | Détails |
| ---| --- |
| Tâches de base de données élastiques | Pour plus d’informations, consultez [créer, configurer et gérer des tâches élastiques](elastic-jobs-overview.md) |
| Transactions élastiques | [Transactions distribuées entre bases de données cloud](sql-database-elastic-transactions-overview.md) |
| Requêtes élastiques | Pour plus d’informations, consultez [vue d’ensemble de la requête élastique](sql-database-elastic-query-overview.md) |
| Réplication avec des instances managées |Pour plus d’informations, consultez [configurer la réplication dans une base de données Azure SQL Database instance managée](replication-with-sql-database-managed-instance.md)|
| Classement de l’instance avec les instances gérées |Pour plus d’informations, consultez [utiliser PowerShell avec le modèle Azure Resource Manager pour créer une instance gérée dans la base de données SQL Azure](./scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)|
| R services / apprentissage avec des bases de données uniques et les pools élastiques |Pour plus d’informations, consultez [Machine Learning Services dans la base de données SQL Azure](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)|
| Récupération de base de données accélérée avec les bases de données uniques et les pools élastiques | Pour plus d’informations, consultez [accéléré la récupération de base de données](sql-database-accelerated-database-recovery.md)|
| Découverte et classification des données  |Pour plus d’informations, consultez [découverte de données Azure SQL Database et SQL Data Warehouse et classification](sql-database-data-discovery-and-classification.md)|
| Transparent data encryption (TDE) avec Bring Your Own Key (BYOK) avec les instances gérées |Pour plus d’informations, consultez [Azure SQL Transparent Data Encryption avec clés gérées par le client dans Azure Key Vault : Prise en charge Your Own Key](transparent-data-encryption-byok-azure-sql.md)|
| Recréer les bases de données supprimées avec les instances gérées |Pour plus d’informations, consultez [recréer les bases de données supprimées dans Azure SQL Managed Instance](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266)|
| Détection des menaces avec des instances gérées |Pour plus d’informations, consultez [configurer la détection des menaces dans Azure SQL Database managed instance](sql-database-managed-instance-threat-detection.md)|
| Niveaux de service de très grande échelle avec les bases de données uniques |Pour plus d’informations, consultez [le niveau de service Hyperscale jusqu'à 100 To](sql-database-service-tier-hyperscale.md)|
| Éditeur de requête dans le portail Azure |Pour plus d’informations, consultez [utiliser l’éditeur de requête SQL du portail Azure pour vous connecter et interroger des données](sql-database-connect-query-portal.md)|
|Nombre approximatif distincte|Pour plus d’informations, consultez [approximative Count Distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)|
|En Mode Batch Rowstore (sous le niveau de compatibilité 150)|Pour plus d’informations, consultez [Rowstore en Mode Batch](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)|
|Retour d’allocation de mémoire (Mode en ligne) (sous le niveau de compatibilité 150)|Pour plus d’informations, consultez [retour d’allocation de mémoire (Mode en ligne)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)|
|Table Variable différée Compilation (sous le niveau de compatibilité 150)|Pour plus d’informations, consultez [Compilation différée Variable de Table](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)|
|SQL Analytics|Pour plus d’informations, consultez [Analytique de SQL Azure](../azure-monitor/insights/azure-sql.md)|
| Prise en charge de fuseau horaire pour les instances managées|Pour plus d’informations, consultez [fuseau horaire dans Azure SQL Database Managed Instance](sql-database-managed-instance-timezone.md)|
|||

## <a name="april-2019"></a>Avril 2019

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
| Points de terminaison publics pour l’instance managée | Pour plus d’informations, consultez [à l’aide d’Azure SQL Database managed instance en toute sécurité avec le point de terminaison public](sql-database-managed-instance-public-endpoint-securely.md)
| Prise en charge de fuseau horaire pour l’instance managée | Pour plus d’informations, consultez [fuseau horaire dans Azure SQL Database Managed Instance (version préliminaire)](sql-database-managed-instance-timezone.md)

### <a name="documentation-improvements"></a>Améliorations de la documentation

| Améliorations de la documentation | Détails |
| --- | --- |
| Points de terminaison publics pour l’instance managée | Pour plus d’informations, consultez [à l’aide d’Azure SQL Database managed instance en toute sécurité avec le point de terminaison public](sql-database-managed-instance-public-endpoint-securely.md)
| Prise en charge de fuseau horaire pour l’instance managée | Pour plus d’informations, consultez [fuseau horaire dans Azure SQL Database Managed Instance (version préliminaire)](sql-database-managed-instance-timezone.md)

## <a name="march-2019"></a>Mars 2019

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
| Disponibilité générale : Prise en charge de la fonctionnalité d’échelle lecture pour Azure SQL Database | Pour plus d’informations, consultez [lecture du scale-out](sql-database-read-scale-out.md)|
| &nbsp; |

### <a name="documentation-improvements"></a>Améliorations de la documentation

| Améliorations de la documentation | Détails |
| --- | --- |
| Prise en charge de fuseau horaire pour les instances managées|Pour plus d’informations, consultez [fuseau horaire dans Azure SQL Database Managed Instance](sql-database-managed-instance-timezone.md)|
| Limites du journal ajouté des bases de données|Pour plus d’informations, consultez [limites de ressources vCore de base de données unique](sql-database-vcore-resource-limits-single-databases.md).|
| Limites du journal ajouté pour les pools et bases de données regroupées|Pour plus d’informations, consultez [limites de ressources vCore des pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md).|
| Ajout gouvernance de taux de journal de Transaction| Ajouté le nouveau contenu pour [gouvernance de taux de Transaction log](sql-database-resource-limits-database-server.md#transaction-log-rate-governance).|
| Exemples PowerShell mis à jour pour les bases de données uniques et des pools élastiques à utiliser le module de az.sql | Pour plus d’informations, consultez [exemples PowerShell pour les bases de données uniques et les pools élastiques](sql-database-powershell-samples.md#single-database-and-elastic-pools).|
| &nbsp; |

## <a name="february-2019"></a>Février 2019

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
|Création d’un index en ligne pouvant être repris est désormais disponible| Pour plus d’informations, consultez [Create Index](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql).|
|Prise en charge de l’instance managée pour les tables de routage amélioré| Pour plus d’informations, consultez [configuration réseau requise](sql-database-managed-instance-connectivity-architecture.md#network-requirements).|
|Changement de nom de base de données pris en charge dans l’instance managée | Pour plus d’informations, consultez le [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) et [sp_rename](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql) syntaxe.|
|Base de données SQL en tant que source de données de référence pour l’Analytique de Stream. | Pour plus d’informations, consultez [Stream Analytique](https://azure.microsoft.com/services/stream-analytics/).|
|Assistance de Migration de données prend en charge pour l’instance managée. |Pour plus d’informations, consultez [quelles sont les nouveautés dans DMA](https://docs.microsoft.com/sql/dma/dma-whatsnew).|
|Assistant Migration SQL Server ajoute la prise en charge pour l’évaluation de la disponibilité cible pour l’instance managée. | Pour plus d’informations, consultez [Assistant Migration SQL Server](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant).
|Service de migration de données prend en charge la migration à partir d’Amazon RDS vers une instance gérée | Pour plus d’informations, consultez [Tutoriel : Migrer des services Bureau à distance SQL Server vers Azure SQL Database ou une instance managée de base de données SQL Azure en ligne à l’aide de DMS](../dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md).|
| &nbsp; |

### <a name="documentation-improvements"></a>Améliorations de la documentation

| Améliorations de la documentation | Détails |
| --- | --- |
|Ajout de gérés précisions d’option de déploiement instance|Mise à jour de nombreux articles pour clarifier la mise en application à la base de données unique, pool élastique et options de déploiement d’instance gérée. |
|Tailles de tempdb mis à jour pour le modèle d’achat DTU | Pour plus d’informations, consultez [base de données Tempdb dans SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
|Importation mis à jour et l’exportation avec le fichier bacpac pour la prise en charge de l’instance managée| Pour plus d’informations, consultez [importation depuis BACPAC](sql-database-import.md) et [exporter vers un fichier BACPAC](sql-database-export.md). |
| &nbsp; |


## <a name="january-2019"></a>Janvier 2019

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
| Options de granularité supplémentaire pour les ressources de calcul | L’usage général et critique pour l’entreprise pour les niveaux de service [bases de données uniques](sql-database-vcore-resource-limits-single-databases.md) et [pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md) ont désormais plusieurs options de calcul précis.|
| Affichage des enregistrements d’audit pour l’instance managée dans le portail Azure | Affichage [enregistrements pour les instances gérées d’audit](sql-database-managed-instance-auditing.md) dans le portail Azure portal est désormais pris en charge.|
| Fonctionnalité de détection de menaces avancée renommée en fonctions avancées de sécurité de données | Fonctionnalité de détection de menaces avancée renommé en [fonctions avancées de sécurité de données](sql-advanced-threat-protection.md) pour les bases de données uniques, les pools élastiques et des instances gérées. |
| &nbsp; |

### <a name="documentation-improvements"></a>Améliorations de la documentation

| Améliorations de la documentation | Détails |
| --- | --- |
| Les instances gérées et la réplication transactionnelle | Ajout de l’article sur l’utilisation de [la réplication transactionnelle avec des instances gérées](replication-with-sql-database-managed-instance.md) |
| Ajout d’Azure AD avec le didacticiel de l’instance managée | Cela [Azure AD avec l’instance managée](sql-database-managed-instance-aad-security-tutorial.md) didacticiel montre que vous devez configurer et tester gérés sécurité des instances à l’aide de connexions d’Azure AD. |
| Contenu mis à jour pour l’automatisation du travail à l’aide de scripts Transact-SQL | Mise à jour et clarification de contenu pour l’utilisation de [automation de travail à l’aide de scripts Transact-SQL](sql-database-job-automation-overview.md) pour les bases de données uniques, des pools élastiques et des instances managées |
| Contenu de sécurité pour les instances gérées mis à jour | Mise à jour et clarification de contenu pour le [modèle de sécurité pour les instances gérées](sql-database-security-overview.md)et différences avec le modèle de sécurité pour les bases de données uniques et les pools élastiques |
| Actualisation de tous les Démarrages rapides et didacticiels | Tous les Démarrages rapides et didacticiels dans la [documentation](https://docs.microsoft.com/azure/sql-database) ont été mis à jour et mise à jour pour correspondre à des modifications dans le portail Azure |
| Ajout de démarrage rapide vue d’ensemble des guides | Ajouter un guide de présentation de démarrage rapide pour [bases de données uniques](sql-database-quickstart-guide.md) et pour [instances managées](sql-database-managed-instance-quickstart-guide.md) |
| Glossaire SQL Database ajouté | Cela [glossaire des termes](sql-database-glossary-terms.md) article fournit une liste définitive des termes du contrat de base de données SQL et des liens vers la page principale de conceptuelle qui explique le terme dans le contexte. |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>Contribuer à l’amélioration de contenu

Le jeu de documentation de SQL Azure est open source. Utilisation de l’ouvrir présente plusieurs avantages :

- Plan de référentiels Open source dans l’ouvrir pour obtenir des commentaires sur les documents sont plus nécessaires.
- Les référentiels Open source passez en revue dans l’ouvrir pour publier le contenu plus utile sur notre première version.
- Les référentiels Open source mettre à jour dans l’ouvrir pour faciliter l’améliorer en permanence le contenu.

Pour contribuer au contenu de la documentation Azure SQL Database, consultez la [vue d’ensemble du guide de contributeur Microsoft Docs](https://docs.microsoft.com/contribute/). L’expérience utilisateur sur [docs.microsoft.com](https://docs.microsoft.com/) intègre [GitHub](https://github.com/) des flux de travail directement à faciliter encore davantage. Commencez par [modifier le document que vous affichez](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents). Ou, à l’aide par [examen de nouvelles rubriques](https://docs.microsoft.com/contribute/#review-open-prs), ou [créer des problèmes de qualité](https://docs.microsoft.com/contribute/#create-quality-issues).
