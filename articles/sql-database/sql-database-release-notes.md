---
title: Notes de publication
description: En savoir plus sur les nouvelles fonctionnalités et améliorations dans le service Azure SQL Database et la documentation associée
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: sstein
ms.openlocfilehash: bdea9af04008ef68578ff8c136760f2493a2ae35
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279228"
---
# <a name="sql-database-release-notes"></a>Notes de publication SQL Database

Cet article répertorie les fonctionnalités SQL Database actuellement en préversion publique. Pour connaître les mises à jour et améliorations de SQL Database, consultez [Mises à jour du service SQL Database](https://azure.microsoft.com/updates/?product=sql-database). Pour connaître les mises à jour et améliorations apportées aux autres services Azure, consultez [Mises à jour des services](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Fonctionnalités en préversion publique

### <a name="single-databasetabsingle-database"></a>[Base de données unique](#tab/single-database)

| Fonctionnalité | Détails |
| ---| --- |
| Nouvelles générations de matériel de série Fsv2 et de série M| Pour plus d’informations, consultez [Générations de matériel](sql-database-service-tiers-vcore.md#hardware-generations).|
| [Liaison privée Azure](https://azure.microsoft.com/updates/private-link-now-available-in-preview/)| Le service Liaison privée simplifie l’architecture réseau et sécurise la connexion entre les points de terminaison dans Azure en conservant les données sur le réseau Azure, éliminant ainsi l’exposition à Internet. Liaison privée vous permet également de créer vos propres services et d’assurer leur rendu dans Azure. |
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
|SQL Analytics|Pour plus d’informations, consultez [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Compilation différée des variables de table (sous le niveau de compatibilité 150)|Pour plus d’informations, consultez [Compilation différée des variables de table](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Managed Instance](#tab/managed-instance)

| Fonctionnalité | Détails |
| ---| --- |
| <a href="/azure/sql-database/sql-database-managed-instance-connectivity-architecture#service-aided-subnet-configuration-public-preview-in-east-us-and-west-us">Configuration de sous-réseau assistée par le service</a> | Moyen sécurisé et pratique de gérer la configuration de sous-réseau. |
| <a href="/azure/sql-database/sql-database-instance-pools">Pools d’instances</a> | Moyen pratique et économique de migrer des instances SQL plus petites vers le cloud. |
| <a href="https://aka.ms/managed-instance-tde-byok">Chiffrement transparent des données (TDE) avec prise en charge du service BYOK (Bring Your Own Key)</a> |Pour plus d’informations, consultez [Azure SQL Transparent Data Encryption avec les clés managées par le client dans Azure Key Vault : prise en charge de Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).|
| <a href="https://aka.ms/managed-instance-aadlogins">Principaux de serveur (connexions) Azure AD au niveau de l’instance</a> | Créer des connexions de niveau serveur avec l’instruction <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a>. |
| [Réplication transactionnelle](sql-database-managed-instance-transactional-replication.md) | Répliquez les changements apportés à vos tables dans d’autres bases de données placées sur des instances managées, des bases de données uniques ou des instances SQL Server, ou mettez à jour vos tables quand certaines lignes sont modifiées dans d’autres instances managées ou dans une instance SQL Server. Pour plus d’informations, consultez, [Configurer la réplication dans une base de données d’instance managée Azure SQL Database](replication-with-sql-database-managed-instance.md). |
| Détection de menaces |Pour plus d’informations, consultez [Configurer la détection des menaces dans Azure SQL Database Managed Instance](sql-database-managed-instance-threat-detection.md).|
| Recréer les bases de données supprimées avec les instances managées |Pour plus d’informations, consultez [Recréer les bases de données supprimées dans Azure SQL Managed Instance](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| &nbsp; |

---

## <a name="new-features"></a>Nouvelles fonctionnalités

### <a name="managed-instance-h2-2019-updates"></a>Mises à jour des instances managées (deuxième partie de l’exercice 2019)

- Des [groupes de basculement automatique](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) vous permettent de répliquer toutes les bases de données de l’instance principale vers une instance secondaire dans une autre région.
- Configurez le comportement de votre instance managée avec des [indicateurs de trace globaux](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Mises à jour des instances managées (première partie de l’exercice 2019)

Les fonctionnalités suivantes sont activées dans le modèle de déploiement d’instance managée de la première partie de l’exercice 2019 :
  - Prise en charge des abonnements avec le <a href="https://aka.ms/sql-mi-visual-studio-subscribers">crédit mensuel Azure pour les abonnés Visual Studio</a> et des [limites régionales](sql-database-managed-instance-resource-limits.md#regional-resource-limitations) étendues.
  - Prise en charge de <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 et SharePoint 2019 </a> ainsi que de <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Création d’instances pour lesquelles vous choisissez le <a href="https://aka.ms/managed-instance-collation">classement au niveau du serveur</a> et le <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">fuseau horaire</a>.
  - Les instances managées sont maintenant protégées par un <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">pare-feu intégré</a>.
  - Configurer des instances pour utiliser des [points de terminaison publics](sql-database-managed-instance-public-endpoint-configure.md), [remplacer par proxy](sql-database-connectivity-architecture.md#connection-policy) une connexion afin d’obtenir de meilleures performances réseau, <a href="https://aka.ms/four-cores-sql-mi-update"> 4 vCores sur la génération de matériel Gen5</a> ou <a href="https://aka.ms/managed-instance-configurable-backup-retention">configurer la conservation des sauvegardes jusqu’à 35 jours</a> pour une restauration dans le temps. La conservation des sauvegardes à long terme (jusqu’à 10 ans) n’est toujours pas activée. À la place, vous pouvez utiliser des <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">sauvegardes de copie uniquement</a>.
  - De nouvelles fonctionnalités vous permettent de <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">géo-restaurer votre base de données dans un autre centre de données à l’aide de PowerShell</a>, de [renommer la base de données](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/) et de [supprimer un cluster virtuel](sql-database-managed-instance-delete-virtual-cluster.md).
  - Le nouveau [rôle Contributeur d’instance intégré](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) permet la conformité de la séparation des tâches aux principes de sécurité et le respect des normes d’entreprise.
  - L’instance managée est disponible dans les régions Azure Government suivantes pour la disponibilité générale (US Gov Texas, US Gov Arizona) ainsi que dans les régions Chine Nord 2 et Chine Est 2. Elle est également disponible dans les régions publiques suivantes : Australie Centre, Australie Centre 2, Brésil Sud, France Sud, Émirats arabes unis Centre, Émirats arabes unis Nord, Afrique du Sud Nord, Afrique du Sud Ouest.

## <a name="fixed-known-issues"></a>Problèmes connus résolus

- **Août 2019** : Les bases de données autonomes sont entièrement prises en charge dans une instance managée.
- **Octobre 2019** – La restauration intégrée de base de données à un point dans le temps du niveau Critique pour l’entreprise vers le niveau Usage général échoue si la base de données source contient des objets OLTP en mémoire.
- **Octobre 2019** – Vous pouvez utiliser la fonctionnalité **Database Mail** avec des serveurs de messagerie externes (non Azure) avec une connexion sécurisée.
- **Novembre 2019** – La cohérence de la base de données est vérifiée à l’aide de `DBCC CHECKDB` après la restauration de la base de données à partir de Stockage Blob Azure.

## <a name="updates"></a>Mises à jour

Pour obtenir la liste des mises à jour et améliorations de SQL Database, consultez [Mises à jour du service SQL Database](https://azure.microsoft.com/updates/?product=sql-database).

Pour connaître les mises à jour et améliorations apportées à tous les services Azure, consultez [Mises à jour des services](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Contribuer au contenu

Pour contribuer à la documentation Azure SQL Database, consultez le [guide du contributeur de documents](https://docs.microsoft.com/contribute/).
