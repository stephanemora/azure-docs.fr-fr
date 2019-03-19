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
ms.date: 03/05/2019
ms.author: carlrab
ms.openlocfilehash: 6600a578ba9c73c8a2c71466fd0b008f19058b80
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861286"
---
# <a name="sql-database-release-notes"></a>Notes de publication de base de données SQL

Cet article répertorie les nouvelles fonctionnalités et améliorations dans le service de base de données SQL et dans la documentation de base de données SQL. Pour les améliorations du service de base de données SQL, consultez également [mises à jour du service de base de données SQL](https://azure.microsoft.com/updates/?product=sql-database). Améliorations apportées à d’autres services Azure, consultez [mises à jour de Service](https://azure.microsoft.com/updates).

## <a name="march-2019"></a>Mars 2019

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
| Bientôt disponible ||
| &nbsp; |

### <a name="documentation-improvements"></a>Améliorations de la documentation

| Améliorations de la documentation | Détails |
| --- | --- |
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
