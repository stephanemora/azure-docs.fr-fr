---
title: Notes de publication Azure SQL Database | Microsoft Docs
description: En savoir plus sur les nouvelles fonctionnalités et améliorations dans le service Azure SQL Database et la documentation associée
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: 04c19c9a8603fee46914eb94b50c4f97e34e9c42
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070209"
---
# <a name="sql-database-release-notes"></a>Notes de publication SQL Database

Cet article liste les nouvelles fonctionnalités et améliorations dans le service SQL Database et la documentation associée. Pour les améliorations du service SQL Database, consultez également [Mises à jour du service SQL Database](https://azure.microsoft.com/updates/?product=sql-database). Pour connaître les améliorations apportées aux autres services Azure, consultez [Mises à jour des services](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Fonctionnalités en préversion publique

| Fonctionnalité | Détails |
| ---| --- |
| Récupération de base de données accélérée à l’aide de bases de données uniques et de pools élastiques | Pour plus d’informations, consultez [Récupération de base de données accélérée](sql-database-accelerated-database-recovery.md).|
|Comptage approximatif des valeurs distinctes|Pour plus d’informations, consultez [Comptage approximatif des valeurs distinctes](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Mode Batch sur rowstore (sous le niveau de compatibilité 150)|Pour plus d’informations, consultez [Mode Batch sur rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Découverte et classification des données  |Pour plus d’informations, consultez [Découverte et classification des données pour Azure SQL Database et SQL Data Warehouse](sql-database-data-discovery-and-classification.md).|
| Tâches de base de données élastiques | Pour plus d’informations, consultez [Créer, configurer et gérer des travaux élastiques](elastic-jobs-overview.md). |
| Requêtes élastiques | Pour plus d’informations, consultez [Vue d’ensemble de la requête élastique](sql-database-elastic-query-overview.md). |
| Transactions élastiques | [Transactions distribuées entre bases de données cloud](sql-database-elastic-transactions-overview.md). |
|Rétroaction d’allocation de mémoire (Mode ligne) (sous le niveau de compatibilité 150)|Pour plus d’informations, consultez [Rétroaction d’allocation de mémoire (Mode ligne)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Éditeur de requête dans le portail Azure |Pour plus d’informations, consultez [Utiliser l’éditeur de requête SQL du portail Azure pour se connecter aux données et les interroger](sql-database-connect-query-portal.md).|
| Services R / machine learning avec des bases de données uniques et des pools élastiques |Pour plus d’informations, consultez [Machine Learning Services dans Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
| Recréer les bases de données supprimées avec les instances managées |Pour plus d’informations, consultez [Recréer les bases de données supprimées dans Azure SQL Managed Instance](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| Réplication avec les instances managées |Pour plus d’informations, consultez, [Configurer la réplication dans une base de données d’instance managée Azure SQL Database](replication-with-sql-database-managed-instance.md).|
| Niveau de calcul serverless | Pour plus d’informations, consultez [SQL Database serverless (préversion)](sql-database-serverless.md).|
|SQL Analytics|Pour plus d’informations, consultez [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Compilation différée des variables de table (sous le niveau de compatibilité 150)|Pour plus d’informations, consultez [Compilation différée des variables de table](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| Détection des menaces avec les instances managées |Pour plus d’informations, consultez [Configurer la détection des menaces dans Azure SQL Database Managed Instance](sql-database-managed-instance-threat-detection.md).|
| Chiffrement transparent des données (TDE) avec le service Bring Your Own Key (BYOK) et les instances managées |Pour plus d’informations, consultez [Azure SQL Transparent Data Encryption avec les clés managées par le client dans Azure Key Vault : prise en charge de Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).|
| &nbsp; |

## <a name="june-2019"></a>Juin 2019

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
| Instance managée 4 vCores sur la génération de matériel Gen5| Pour plus d’informations, consultez https://aka.ms/four-cores-sql-mi-update. |
| Managed Instance - Prise en charge des abonnements avec le crédit mensuel Azure pour les abonnés Visual Studio | Pour plus d’informations, consultez https://aka.ms/sql-mi-visual-studio-subscribers. |
|Limites régionales supérieures pour Managed Instance| Pour plus d’informations, consultez [Limitations de ressources régionales](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
| &nbsp; |


## <a name="may-2019"></a>Mai 2019

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
|Disponibilité générale du niveau de service Hyperscale| Pour plus d’informations, consultez [Niveau de service Hyperscale jusqu’à 100 To](sql-database-service-tier-hyperscale.md) et [Obtenir une mise à l’échelle haute performance pour vos charges de travail de base de données Azure avec Hyperscale](https://azure.microsoft.com/blog/get-high-performance-scaling-for-your-azure-database-workloads-with-hyperscale/).|
|Niveau de calcul serverless publié en préversion publique | Pour plus d’informations, consultez [SQL Database serverless (préversion)](sql-database-serverless.md).|
|Disponibilité générale de la prise en charge des fuseaux horaires pour les instances managées |Pour plus d’informations, consultez [Fuseau horaire dans Azure SQL Database Managed Instance](sql-database-managed-instance-timezone.md).|
|Disponibilité générale du classement d’instance avec les instances managées |Pour plus d’informations, consultez, [Utiliser PowerShell avec un modèle Azure Resource Manager pour créer une instance managée dans Azure SQL Database](./scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md).|
| Augmentation du taux de journalisation des transactions et des IOPS cibles pour les bases de données utilisant le modèle d’achat vCore| Pour plus d’informations, consultez [Limites de ressources pour des bases de données uniques suivant le modèle d’achat vCore](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases) et [Limites de ressources pour des bases de données uniques suivant le modèle d’achat DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases).
| &nbsp; |

### <a name="documentation-improvements"></a>Améliorations de la documentation

| Améliorations de la documentation | Détails |
| --- | --- |
| Documentation du niveau de service Hyperscale mise à jour pour la version en disponibilité générale| Pour plus d’informations, consultez [Niveau de service Hyperscale jusqu’à 100 To](sql-database-service-tier-hyperscale.md).|
|Documentation du niveau de calcul serverless publiée avec la préversion publique| Pour plus d’informations, consultez [SQL Database serverless (préversion)](sql-database-serverless.md).|
| &nbsp; |

## <a name="april-2019"></a>Avril 2019

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
| Points de terminaison publics pour Managed Instance en préversion publique| Pour plus d’informations, consultez [Utiliser Azure SQL Database Managed Instance en toute sécurité avec un point de terminaison public](sql-database-managed-instance-public-endpoint-securely.md).|
| Prise en charge des fuseaux horaires pour Managed Instance en préversion publique| Pour plus d’informations, consultez [Fuseau horaire dans Azure SQL Database Managed Instance](sql-database-managed-instance-timezone.md).|
| Sécurisation des bases de données Azure SQL avec les identités managées dans la seconde préversion publique| Consultez [Sécurisation des bases de données Azure SQL avec les identités managées aujourd’hui simplifiée](https://azure.microsoft.com/blog/securing-azure-sql-databases-with-managed-identities-just-got-easier/).|
| &nbsp; |

### <a name="documentation-improvements"></a>Améliorations de la documentation

| Améliorations de la documentation | Détails |
| --- | --- |
| Points de terminaison publics pour Managed Instance en préversion publique| Pour plus d’informations, consultez [Utiliser Azure SQL Database Managed Instance en toute sécurité avec un point de terminaison public](sql-database-managed-instance-public-endpoint-securely.md).|
| Prise en charge des fuseaux horaires pour Managed Instance en préversion publique| Pour plus d’informations, consultez [Fuseau horaire dans Azure SQL Database Managed Instance](sql-database-managed-instance-timezone.md). |
| Gouvernance des ressources dans Azure SQL Database | Pour plus d’informations, consultez [Gouvernance des ressources dans Azure SQL Database](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). || &nbsp; |

## <a name="march-2019"></a>Mars 2019

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
| Disponibilité générale de la prise en charge du scale-out en lecture d’Azure SQL Database | Pour plus d’informations, consultez [Scale-out en lecture](sql-database-read-scale-out.md).|
| &nbsp; |

### <a name="documentation-improvements"></a>Améliorations de la documentation

| Améliorations de la documentation | Détails |
| --- | --- |
| Ajout de limites de journal pour les bases de données uniques|Pour plus d’informations, consultez [Limites de ressources vCore des bases de données uniques](sql-database-vcore-resource-limits-single-databases.md).|
| Ajout de limites de journal pour les pools élastiques et les bases de données mises en pool|Pour plus d’informations, consultez [Limites de ressources vCore des pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md).|
| Ajout de la gouvernance du taux de journalisation des transactions| Ajout d’un nouveau contenu pour la [Gouvernance du taux de journalisation des transactions](sql-database-resource-limits-database-server.md#transaction-log-rate-governance).|
| Mise à jour des exemples PowerShell pour l’utilisation du module az.sql par les bases de données uniques et les pools élastiques | Pour plus d’informations, consultez [Exemples PowerShell pour les bases de données uniques et les pools élastiques](sql-database-powershell-samples.md#single-database-and-elastic-pools).|
| &nbsp; |

## <a name="february-2019"></a>Février 2019

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
|Création d’un index en ligne pouvant être repris désormais mise à la disposition générale| Pour plus d’informations, consultez [Créer un index](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql).|
|Amélioration de la prise en charge des tables de routage par Managed Instance| Pour plus d’informations, consultez [Configuration exigée pour le réseau](sql-database-managed-instance-connectivity-architecture.md#network-requirements).|
|Prise en charge du changement de nom dans Managed Instance | Pour plus d’informations, consultez la syntaxe de [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) et de [sp_rename](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql).|
|SQL Database comme source de données de référence pour Stream Analytics. | Pour plus d’informations, consultez [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) et [Azure Stream Analytics prend désormais en charge Azure SQL Database en tant qu’entrée de données de référence](https://azure.microsoft.com/blog/azure-stream-analytics-now-supports-azure-sql-database-as-reference-data-input/).|
|L’assistance de migration de données ajoute une prise en charge pour Managed Instance. |Pour plus d’informations, consultez [Nouveautés dans l’Assistant Migration de données](https://docs.microsoft.com/sql/dma/dma-whatsnew).|
|L’Assistant Migration SQL Server ajoute une prise en charge dans le cadre de l’évaluation de la disponibilité des cibles pour Managed Instance. | Pour plus d’informations, consultez [Assistant Migration SQL Server](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant).
|Le service de migration des données prend en charge la migration depuis Amazon RDS vers Managed Instance | Pour plus d’informations, consultez [Tutoriel : Migrer en ligne RDS SQL Server vers Azure SQL Database ou une instance managée Azure SQL Database à l’aide de DMS](../dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md).|
| &nbsp; |

### <a name="documentation-improvements"></a>Améliorations de la documentation

| Améliorations de la documentation | Détails |
| --- | --- |
|Ajout d’éclaircissements concernant l’option de déploiement Managed Instance|Mise à jour de nombreux articles pour clarifier la mise en application aux options de déploiement d’instance managée, de pool élastique et de base de données unique. |
|Mise à jour des tailles de tempdb pour le modèle d’achat DTU | Pour plus d’informations, consultez [Base de données Tempdb dans SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
|Mise à jour de l’importation et de l’exportation à l’aide du fichier bacpac pour la prise en charge de Managed Instance| Pour plus d’informations, consultez [Importer depuis BACPAC](sql-database-import.md) et [Exporter vers BACPAC](sql-database-export.md). |
| &nbsp; |

## <a name="january-2019"></a>Janvier 2019

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
| Options de granularité supplémentaires pour les ressources de calcul | Les niveaux de service Usage général et Critique pour l’entreprise des [bases de données uniques](sql-database-vcore-resource-limits-single-databases.md) et des [pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md) disposent désormais d’autres options de calcul plus précises.|
| Affichage des enregistrements d’audit pour l’instance managée dans le portail Azure | L’affichage des [enregistrements d’audit des instances managées](sql-database-managed-instance-auditing.md) dans le portail Azure est désormais prise en charge.|
| Changement de nom de la fonctionnalité de détection avancée des menaces pour « Advanced Data Security » | La fonctionnalité de détection avancée des menaces a été renommée [Advanced Data Security](sql-advanced-threat-protection.md) pour les bases de données uniques, les pools élastiques et les instances managées. |
| &nbsp; |

### <a name="documentation-improvements"></a>Améliorations de la documentation

| Améliorations de la documentation | Détails |
| --- | --- |
| Instances managées et réplication transactionnelle | Ajout de l’article sur l’utilisation de [la réplication transactionnelle avec les instances managées](replication-with-sql-database-managed-instance.md). |
| Ajout du tutoriel Azure AD avec Managed Instance | Ce tutoriel [Azure AD avec Managed Instance](sql-database-managed-instance-aad-security-tutorial.md) montre que vous devez configurer et tester la sécurité Managed Instance à l’aide de connexions Azure AD. |
| Mise à jour de contenu pour l’automatisation des travaux au moyen de scripts Transact-SQL | Mise à jour et clarification de contenu pour l’utilisation de l’[automation des travaux au moyen de scripts Transact-SQL](sql-database-job-automation-overview.md) des bases de données uniques, des pools élastiques et des instances managées. |
| Mise à jour de contenu de sécurité pour les instances managées | Mise à jour et clarification de contenu pour le [modèle de sécurité des instances managées](sql-database-security-overview.md), et différences établies avec le modèle de sécurité des bases de données uniques et des pools élastiques. |
| Actualisation de tous les tutoriels et guides de démarrage rapide | Tous les tutoriels et guides de démarrage rapide dans la [documentation](https://docs.microsoft.com/azure/sql-database) ont été mis à jour et actualisés pour refléter les modifications apportées sur le portail Azure. |
| Ajout de guides de présentation du démarrage rapide | Ajout d’un guide de présentation du démarrage rapide pour les [bases de données uniques](sql-database-quickstart-guide.md) et les [instances managées](sql-database-managed-instance-quickstart-guide.md). |
| Ajout du lexique SQL Database | Cet article sur le [lexique](sql-database-glossary-terms.md) fournit une liste définitive de la terminologie SQL Database ainsi que des liens vers la page conceptuelle principale qui explique le terme en contexte. |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>Contribuer à l’amélioration du contenu

L’ensemble de la documentation Azure SQL est open source. Travailler en code source ouvert présente plusieurs avantages :

- Planifier les référentiels open source en développement libre pour obtenir des commentaires permettant de savoir quels documents sont les plus nécessaires.
- Réviser les référentiels open source en développement libre pour publier le contenu le plus utile de notre première version.
- Mettre à jour les référentiels open source en développement libre pour faciliter l’amélioration en permanence du contenu.

Pour collaborer au contenu de la documentation Azure SQL Database, consultez la [Vue d’ensemble du guide du contributeur Microsoft Docs](https://docs.microsoft.com/contribute/). L’expérience utilisateur sur [docs.microsoft.com](https://docs.microsoft.com/) intègre les workflows [GitHub](https://github.com/) directement pour que l’utilisation soit encore plus simple. Commencez par [modifier le document que vous affichez](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents). Vous pouvez aussi aider en [révisant les nouvelles rubriques](https://docs.microsoft.com/contribute/#review-open-prs) ou [créer des problèmes de qualité](https://docs.microsoft.com/contribute/#create-quality-issues).
