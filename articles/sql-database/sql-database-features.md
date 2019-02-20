---
title: Comparaison des fonctionnalités d’Azure SQL Database | Microsoft Docs
description: Cet article compare les fonctionnalités de SQL Server disponibles dans différentes versions d’Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: c2a1cbd75bb3ff0b936d8dd801572e5783ab5260
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100915"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Comparaison des fonctionnalités : Azure SQL Database et SQL Server

Azure SQL Database partage une base de code commune avec SQL Server. Les fonctionnalités de SQL Server prises en charge par Azure SQL Database varient selon le type de base de données Azure SQL que vous créez. Avec Azure SQL Database, vous pouvez créer une base de données au sein d'une [instance gérée](sql-database-managed-instance.md), en tant que base de données unique, ou au sein d'un pool élastique.

Microsoft continue d’ajouter des fonctionnalités à Azure SQL Database. Visitez la page web de mises à jour de service pour Azure afin d’obtenir les dernières mises à jour avec ces filtres :

- Filtrez sur [Service SQL Database](https://azure.microsoft.com/updates/?service=sql-database).
- Filtrez sur [annonces](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) de disponibilité générale pour les fonctionnalités SQL Database.

## <a name="sql-server-feature-support-in-azure-sql-database"></a>Prise en charge des fonctionnalités SQL Server dans Aure SQL Database

Le tableau suivant répertorie les principales fonctionnalités de SQL Server et fournit des informations sur la prise en charge partielle ou complète de chaque fonctionnalité, ainsi qu’un lien vers davantage d’informations sur la fonctionnalité.

| **Fonctionnalité SQL** | **Prise en charge dans les bases de données uniques/pools élastiques** | **Prise en charge dans Azure SQL Database/Managed Instance** |
| --- | --- | --- |
| [Géo-réplication active](sql-database-active-geo-replication.md) | Oui - Niveaux de service Usage général et Critique pour l’entreprise uniquement| Non, voir [Groupes de basculement automatique](sql-database-auto-failover-group.md) |
| [Groupes de basculement automatique](sql-database-auto-failover-group.md) | Oui - Niveaux de service Usage général et Critique pour l’entreprise uniquement| Oui, en [préversion publique](sql-database-auto-failover-group.md)|
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Oui - voir [Magasin de certificats](sql-database-always-encrypted.md) et [Coffre de clés](sql-database-always-encrypted-azure-key-vault.md) | Oui - voir [Magasin de certificats](sql-database-always-encrypted.md) et [Coffre de clés](sql-database-always-encrypted-azure-key-vault.md) |
| [Groupes de disponibilité AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | La [haute disponibilité](sql-database-high-availability.md) est incluse dans chaque base de données. La récupération d’urgence est abordée dans [Vue d’ensemble de la continuité de l’activité avec Azure SQL Database](sql-database-business-continuity.md) | La [haute disponibilité](sql-database-high-availability.md) est incluse dans chaque base de données. La récupération d’urgence est abordée dans [Vue d’ensemble de la continuité de l’activité avec Azure SQL Database](sql-database-business-continuity.md) |
| [Attacher une base de données](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Non  | Non  |
| [Rôles d’application](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | OUI | OUI |
|[Audit](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Oui](sql-database-auditing.md)| [Oui](sql-database-managed-instance-auditing.md) |
| [Sauvegardes automatiques](sql-database-automated-backups.md) | OUI | OUI |
| [Réglage automatique (forçage du plan)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Oui](sql-database-automatic-tuning.md)| [Oui](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [Réglage automatique (index)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Oui](sql-database-automatic-tuning.md)| Non  |
| [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) | OUI | OUI |
| [Fichier BACPAC (exporter)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Oui - voir [Exportation de base de données SQL](sql-database-export.md) | OUI |
| [Fichier BACPAC (importer)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Oui - voir [Importation de base de données SQL](sql-database-import.md) | OUI |
| [Commande de sauvegarde](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Non, uniquement les sauvegardes automatiques générées par le système - voir [Sauvegardes automatiques](sql-database-automated-backups.md) | Sauvegardes automatiques initiées par le système et sauvegardes en copie seule initiées par l’utilisateur - voir [Différences entre les sauvegardes](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Fonctions intégrées](https://docs.microsoft.com/sql/t-sql/functions/functions) | La plupart - voir Fonctions individuelles | Oui - voir [Procédures stockées, déclencheurs et fonctions définies par l’utilisateur](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Modifier la capture de données](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Non  | OUI |
| [Suivi des modifications](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | OUI |OUI |
| [Classement - base de données](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation) | OUI | OUI |
| [Classement - serveur/instance](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) | Non  | Oui, en [préversion publique](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)|
| [Index Columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Oui - [niveau Premium, niveau Standard - S3 et supérieur, niveau Usage général et niveaux Critique pour l’entreprise](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |OUI |
| [Common Language Runtime (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Non  | Oui - voir [Différences CLR](sql-database-managed-instance-transact-sql-information.md#clr) |
| [Bases de données à relation contenant-contenu](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | OUI | OUI |
| [Utilisateurs contenus](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | OUI | OUI |
| [Contrôle des mots clés de langage de flux](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | OUI | OUI |
| [Requêtes entre plusieurs bases de données](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Non - voir [Requêtes élastiques](sql-database-elastic-query-overview.md) | Oui, plus [Requêtes élastiques](sql-database-elastic-query-overview.md) |
| [Transactions entre bases de données](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Non  | Oui - voir les [Différences entre serveurs liés](sql-database-managed-instance-transact-sql-information.md#linked-servers) |
| [Curseurs](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | OUI |OUI |
| [Compression des données](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | OUI |OUI |
| [Messagerie de base de données](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Non  | OUI |
| [Service de migration de données (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | OUI | OUI |
| [Mise en miroir de bases de données](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Non  | Non  |
| [Paramètres de configuration de base de données](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | OUI | OUI |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Non  | Non  |
| [Instantanés de base de données](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Non  | Non  |
| [Types de données](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | OUI |OUI |
| [Instructions DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | La plupart - voir Instructions individuelles | Oui - voir [Différences DBCC](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [Instructions DDL](https://docs.microsoft.com/sql/t-sql/statements/statements) | La plupart - voir Instructions individuelles | Oui - voir [Différences de T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Déclencheurs DDL](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Base de données uniquement |  OUI |
| [Vues partitionnées distribuées](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | Non  | OUI |
| [Transactions distribuées - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Non - voir [Transactions élastiques](sql-database-elastic-transactions-overview.md) |  Non - voir [Transactions élastiques](sql-database-elastic-transactions-overview.md) |
| [Instructions DML](https://docs.microsoft.com/sql/t-sql/queries/queries) | OUI | OUI |
| [Déclencheurs DML](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | La plupart - voir Instructions individuelles |  OUI |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | La plupart - voir DMV individuels |  Oui - voir [Différences de T-SQL](sql-database-managed-instance-transact-sql-information.md) |
|[Masquage des données dynamiques](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[Oui](sql-database-dynamic-data-masking-get-started.md)| [Oui](sql-database-dynamic-data-masking-get-started.md) |
| [Pools élastiques](sql-database-elastic-pool.md) | OUI | Intégré - une seule instance gérée peut avoir plusieurs bases de données qui partagent le même pool de ressources |
| [Notifications d’événement](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Non - voir [Alertes](sql-database-insights-alerts-portal.md) | Non  |
| [Expressions](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |OUI | OUI |
| [Événements étendus](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Certains - voir [Événements étendus dans SQL Database](sql-database-xevent-db-diff-from-svr.md) | Oui - voir [Différences des événements étendus](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [Procédures stockées étendues](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Non  | Non  |
[Fichiers et groupes de fichiers](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Groupe de fichiers principal uniquement | OUI |
| [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Non  | Non  |
| [Recherche en texte intégral](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  Analyseurs lexicaux tiers non pris en charge |Analyseurs lexicaux tiers non pris en charge |
| [Fonctions](https://docs.microsoft.com/sql/t-sql/functions/functions) | La plupart - voir Fonctions individuelles | Oui - voir [Procédures stockées, déclencheurs et fonctions définies par l’utilisateur](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Géorestauration](sql-database-recovery-using-backups.md#geo-restore) | Oui - Niveaux de service Usage général et Critique pour l’entreprise uniquement | Non, vous pouvez restaurer les sauvegardes complètes COPY_ONLY que vous effectuez régulièrement - consultez [Différences entre sauvegardes](sql-database-managed-instance-transact-sql-information.md#backup) et [Restaurer les différences](sql-database-managed-instance-transact-sql-information.md#restore-statement). |
| [Traitement de graphe](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | OUI | OUI |
| [Optimisation en mémoire](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Oui - [niveaux Premium et Critique pour l’entreprise uniquement](sql-database-in-memory.md) | Oui - [niveau Critique pour entreprise uniquement](sql-database-managed-instance.md) |
| [Prise en charge des données JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Oui](sql-database-json-features.md) | [Oui](sql-database-json-features.md) |
| [Éléments de langage](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | La plupart - voir Éléments individuels |  Oui - voir [Différences de T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Serveurs liés](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Non - voir [Requête élastique](sql-database-elastic-query-horizontal-partitioning.md) | Uniquement sur SQL Server et SQL Database |
| [Copie des journaux de transaction](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | La [haute disponibilité](sql-database-high-availability.md) est incluse dans chaque base de données. La récupération d’urgence est abordée dans [Vue d’ensemble de la continuité de l’activité avec Azure SQL Database](sql-database-business-continuity.md) |La [haute disponibilité](sql-database-high-availability.md) est incluse dans chaque base de données. La récupération d’urgence est abordée dans [Vue d’ensemble de la continuité de l’activité avec Azure SQL Database](sql-database-business-continuity.md) |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Non  | Non  |
| [Journalisation minimale dans l’importation en bloc](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Non  | Non  |
| [Modification des données système](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Non  | OUI |
| [Opérations d’index en ligne](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | OUI | OUI |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|Non |Oui - voir [Différences de T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|OUI|OUI|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|Non |Oui - voir [Différences de T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|Non |Oui - voir [Différences de T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|OUI|OUI|
| [Opérateurs](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | La plupart - voir Opérateurs individuels |Oui - voir [Différences de T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Partitionnement](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | OUI | OUI |
| [Limite de restauration dans le temps d’une base de données](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Oui - Niveaux de service Usage général et Critique pour l’entreprise uniquement - voir [Récupération SQL Database](sql-database-recovery-using-backups.md#point-in-time-restore) | Oui - voir [Récupération de base de données SQL](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Non  | Non  |
| [Gestion basée sur des stratégies](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Non  | Non  |
| [Prédicats](https://docs.microsoft.com/sql/t-sql/queries/predicates) | OUI | OUI |
| [Notifications de requête](https://docs.microsoft.com/sql/relational-databases/native-client/features/working-with-query-notifications) | Non  | OUI |
| [R Services](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Oui, en [préversion publique](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | Non  |
| [Gouverneur de ressources](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Non  | OUI |
| [Instructions RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Non  | Oui - voir [Restaurer les différences](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [Restauration de la base de données à partir de la sauvegarde](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | À partir des sauvegardes automatisées uniquement - voir [Récupération de base de données SQL](sql-database-recovery-using-backups.md) | À partir des sauvegardes automatisées - consultez [Récupération de SQL Database](sql-database-recovery-using-backups.md) et à partir des sauvegardes complètes - voir [Différences entres sauvegardes](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Sécurité au niveau des lignes](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | OUI | OUI |
| [Recherche sémantique](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Non  | Non  |
| [Numéros de séquence](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | OUI | OUI |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Non  | Oui - voir [Différences de Service Broker](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [Paramètres de configuration du serveur](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Non  | Oui - voir [Différences de T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Instructions SET](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | La plupart - voir Instructions individuelles | Oui - voir [Différences de T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | OUI | OUI |
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | OUI | OUI |
| [Synchronisation des données SQL](sql-database-get-started-sql-data-sync.md) | OUI | Non  |
| [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Non. Voir [Tâches élastiques](sql-database-elastic-jobs-getting-started.md). | Oui - voir [Différences entre agents SQL Server](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Non - voir [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | Non - voir [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [Audit SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Non - voir [Audit de base de données SQL](sql-database-auditing.md) | Oui - voir [Vérification des différences](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [Outils SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | OUI | OUI |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Oui, avec les services SSIS managés dans un environnement Azure Data Factory (ADF), dans lequel les packages sont stockés dans la base de données SSISDB hébergée par Azure SQL Database et exécutée sur Azure SSIS IR (Integration Runtime). Consultez [Créer Azure-SSIS IR dans ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Pour comparer les fonctionnalités SSIS de SQL Database Server et Managed Instance, consultez [Comparer des bases de données uniques/pools élastiques Azure SQL Database et Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). | Oui, avec les services SSIS managés dans un environnement Azure Data Factory (ADF), dans lequel les packages sont stockés dans la base de données SSISDB hébergée par Managed Instance et exécutée sur Azure SSIS IR (Integration Runtime). Consultez [Créer Azure-SSIS IR dans ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Pour comparer les fonctionnalités SSIS de SQL Database et Managed Instance, consultez [Comparer des bases de données uniques/pools élastiques Azure SQL Database et Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | OUI | OUI |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | OUI | OUI |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Non - voir [Événements étendus](sql-database-xevent-db-diff-from-svr.md) | OUI |
| [Réplication SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Abonné à la réplication transactionnelle et de capture instantanée uniquement](sql-database-single-database-migrate.md) | Oui, en [préversion publique](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Non - [voir Power BI](https://docs.microsoft.com/power-bi/) | Non - [voir Power BI](https://docs.microsoft.com/power-bi/) |
| [procédures stockées](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | OUI | OUI |
| [Fonctions stockées sur système](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | La plupart - voir Fonctions individuelles | Oui - voir [Procédures stockées, déclencheurs et fonctions définies par l’utilisateur](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Procédures stockées sur système](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Certaines - voir Procédures stockées individuelles | Oui - voir [Procédures stockées, déclencheurs et fonctions définies par l’utilisateur](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Tables système](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Certaines - voir Tables individuelles | Oui - voir [Différences de T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Vues catalogue système](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Certaines - voir Vues individuelles | Oui - voir [Différences de T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Tables temporaires](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | Tables temporaires globales niveau base de données ou local | Tables temporaires globales locales et limitées à une instance |
| [Tables temporelles](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Oui](sql-database-temporal-tables.md) | [Oui](sql-database-temporal-tables.md) |
|Détection de menaces|  [Oui](sql-database-threat-detection.md)|[Oui](sql-database-managed-instance-threat-detection.md)|
| [Indicateurs de trace](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | Non  | Non  |
| [Variables](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | OUI | OUI |
| [Chiffrement transparent des données (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Oui - Niveaux de service Usage général et Critique pour l’entreprise uniquement| [Oui](transparent-data-encryption-azure-sql.md) |
[Réseau virtuel](../virtual-network/virtual-networks-overview.md) | Partielle – Voir [Points de terminaison de réseau virtuel](sql-database-vnet-service-endpoint-rule-overview.md) | Oui, modèle Resource Manager uniquement |
| [Clustering de basculement Windows Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | La [haute disponibilité](sql-database-high-availability.md) est incluse dans chaque base de données. La récupération d’urgence est abordée dans [Vue d’ensemble de la continuité de l’activité avec Azure SQL Database](sql-database-business-continuity.md) | La [haute disponibilité](sql-database-high-availability.md) est incluse dans chaque base de données. La récupération d’urgence est abordée dans [Vue d’ensemble de la continuité de l’activité avec Azure SQL Database](sql-database-business-continuity.md) |
| [Index XML](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | OUI | OUI |

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le service Azure SQL Database, consultez [What is SQL Database?](sql-database-technical-overview.md) (Qu’est-ce qu’une base de données SQL ?)
- Pour plus d’informations sur Managed Instance, consultez [What is a Managed Instance (preview)?](sql-database-managed-instance.md) (Présentation de l’option Managed Instance (préversion)).
