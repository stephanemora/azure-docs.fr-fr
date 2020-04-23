---
title: Notes de publication
description: En savoir plus sur les nouvelles fonctionnalités et améliorations dans le service Azure SQL Database et la documentation associée
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: sstein
ms.openlocfilehash: 7d922aa0727ad28054d050a29039951d3f04985f
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383368"
---
# <a name="sql-database-release-notes"></a>Notes de publication SQL Database

Cet article répertorie les fonctionnalités SQL Database actuellement en préversion publique. Pour connaître les mises à jour et améliorations de SQL Database, consultez [Mises à jour du service SQL Database](https://azure.microsoft.com/updates/?product=sql-database). Pour connaître les mises à jour et améliorations apportées aux autres services Azure, consultez [Mises à jour des services](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Fonctionnalités en préversion publique

### <a name="single-database"></a>[Base de données unique](#tab/single-database)

| Fonctionnalité | Détails |
| ---| --- |
| Nouvelles générations de matériel de série Fsv2 et de série M| Pour plus d’informations, consultez [Générations de matériel](sql-database-service-tiers-vcore.md#hardware-generations).|
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

### <a name="managed-instance"></a>[Managed Instance](#tab/managed-instance)

| Fonctionnalité | Détails |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Pools d’instances</a> | Moyen pratique et économique de migrer des instances SQL plus petites vers le cloud. |
| <a href="https://aka.ms/managed-instance-aadlogins">Principaux de serveur (connexions) Azure AD au niveau de l’instance</a> | Créer des connexions de niveau serveur avec l’instruction <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a>. |
| [Réplication transactionnelle](sql-database-managed-instance-transactional-replication.md) | Répliquez les changements apportés à vos tables dans d’autres bases de données placées sur des instances managées, des bases de données uniques ou des instances SQL Server, ou mettez à jour vos tables quand certaines lignes sont modifiées dans d’autres instances managées ou dans une instance SQL Server. Pour plus d’informations, consultez, [Configurer la réplication dans une base de données d’instance managée Azure SQL Database](replication-with-sql-database-managed-instance.md). |
| Détection de menaces |Pour plus d’informations, consultez [Configurer la détection des menaces dans Azure SQL Database Managed Instance](sql-database-managed-instance-threat-detection.md).|
| Rétention des sauvegardes à long terme | Pour plus d’informations, consultez [Configurer la rétention des sauvegardes à long terme dans une instance managée Azure SQL Database](sql-database-managed-instance-long-term-backup-retention-configure.md). | 

---

## <a name="managed-instance---new-features-and-known-issues"></a>Instance gérée - Nouvelles fonctionnalités et problèmes connus

### <a name="managed-instance-h2-2019-updates"></a>Mises à jour des instances managées (deuxième partie de l’exercice 2019)

- [Configuration de sous-réseau assistée par le service](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Moyen sécurisé et pratique de gérer la configuration de sous-réseau ; vous contrôlez le trafic de données tandis que l'instance gérée assure le flux ininterrompu du trafic de gestion.
- [Transparent Data Encryption (TDE) avec Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) active le scénario Bring Your Own Key (BYOK) pour la protection des données au repos et permet aux organisations de séparer les tâches de gestion relatives aux clés et aux données.
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

### <a name="known-issues"></a>Problèmes connus

|Problème  |Date de la détection  |Statut  |Date de la résolution  |
|---------|---------|---------|---------|
|[Les autorisations sur le groupe de ressources ne sont pas appliquées à Managed Instance](#permissions-on-resource-group-not-applied-to-managed-instance)|Février 2020|Solution de contournement||
|[Limitation du basculement manuel via le portail pour les groupes de basculement](#limitation-of-manual-failover-via-portal-for-failover-groups)|janvier 2020|Solution de contournement||
|[Les rôles SQL Agent requièrent des autorisations d’exécution explicites pour les connexions non-sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Décembre 2019|Solution de contournement||
|[Les travaux SQL Agent peuvent être interrompus par le redémarrage du processus de l’agent](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Décembre 2019|Aucune solution de contournement|Mars 2020|
|[Les utilisateurs et connexions AAD ne sont pas pris en charge dans SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|Nov 2019|Aucune solution de contournement||
|[Les limites de mémoire OLTP en mémoire ne sont pas appliquées](#in-memory-oltp-memory-limits-are-not-applied)|Octobre 2019|Solution de contournement||
|[Erreur retournée lors de la tentative de suppression d’un fichier qui n’est pas vide](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Octobre 2019|Solution de contournement||
|[Les opérations de changement de niveau de service et de création d’instance sont bloquées par la restauration de base de données en cours](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Septembre 2019|Solution de contournement||
|[Il peut être nécessaire de reconfigurer Resource Governor sur le niveau de service Critique pour l’entreprise après le basculement](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Septembre 2019|Solution de contournement||
|[Les boîtes de dialogue Service Broker utilisées entre plusieurs bases de données doivent être réinitialisées après la mise à niveau du niveau de service](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|août 2019|Solution de contournement||
|[L’emprunt d’identité des types de connexion Azure AD n’est pas pris en charge](#impersonification-of-azure-ad-login-types-is-not-supported)|Juillet 2019|Aucune solution de contournement||
|[Le paramètre @query n’est pas pris en charge dans sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Avril 2019|Aucune solution de contournement||
|[La réplication transactionnelle doit être reconfigurée après un basculement géographique](#transactional-replication-must-be-reconfigured-after-geo-failover)|mars 2019|Aucune solution de contournement||
|[Une base de données temporaire est utilisée d’une opération de restauration](#temporary-database-is-used-during-restore-operation)||Solution de contournement||
|[La structure et le contenu de TEMPDB sont recréés](#tempdb-structure-and-content-is-re-created)||Aucune solution de contournement||
|[Dépassement de l’espace de stockage avec des fichiers de base de données de petite taille](#exceeding-storage-space-with-small-database-files)||Solution de contournement||
|[Affichage des valeurs GUID à la place des noms de base de données](#guid-values-shown-instead-of-database-names)||Solution de contournement||
|[Les journaux des erreurs ne sont pas conservés](#error-logs-arent-persisted)||Aucune solution de contournement||
|[L’utilisation de la même étendue de transaction pour deux bases de données appartenant à une même instance n’est pas prise en charge](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Solution de contournement||
|[Les modules CLR et les serveurs liés parfois ne parviennent pas à référencer une adresse IP locale](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Solution de contournement||
|La cohérence de la base de données est vérifiée à l’aide de DBCC CHECKDB après restauration de la base de données à partir de Stockage Blob Azure.||Résolu|Nov 2019|
|La restauration intégrée de base de données à un point dans le temps du niveau Critique pour l’entreprise vers le niveau Usage général échoue si la base de données source contient des objets OLTP en mémoire.||Résolu|Octobre 2019|
|Fonctionnalité Database Mail avec des serveurs de messagerie externes (non Azure) utilisant une connexion sécurisée||Résolu|Octobre 2019|
|Bases de données autonomes non prises en charge dans une instance managée||Résolu|août 2019|

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>Autorisations sur le groupe de ressources non appliquées à l’instance managée

Si le rôle RBAC Contributeur Managed Instance est appliqué à un groupe de ressources, il n’est pas appliqué à Managed Instance et n’a aucun effet.

**Solution de contournement** : Configurer le rôle Contributeur Managed Instance pour les utilisateurs au niveau de l’abonnement.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Limitation du basculement manuel via le portail pour les groupes de basculement

Si le groupe de basculement s’étend sur plusieurs instances dans différents abonnements ou groupes de ressources Azure, le basculement manuel ne peut pas être initié à partir de l’instance principale dans le groupe de basculement.

**Solution de contournement** : Lancez le basculement via le portail à partir de l’instance géographique secondaire.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Les rôles d’agent SQL requièrent des autorisations d’exécution explicites pour les connexions non-sysadmin

Si des connexions non-sysadmin sont ajoutées à un [rôle de base de données fixe de l’agent SQL](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles) , il y a un problème du fait que des autorisations EXECUTE explicites doivent être accordées aux procédures stockées principales pour que ces connexions fonctionnent. Si ce problème survient, le message d’erreur « L’autorisation EXECUTE a été refusée sur l’objet <object_name> (Microsoft SQL Server, erreur : 229) » s’affiche.

**Solution de contournement** : Une fois que vous ajoutez des connexions à l’un des rôles de base de données fixes de l’agent SQL : SQLAgentUserRole, SQLAgentReaderRole ou SQLAgentOperatorRole, pour chacune des connexions ajoutées à ces rôles, le script T-SQL ci-dessous est exécuté pour accorder explicitement des autorisations EXECUTE aux procédures stockées mentionnées.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>Les travaux de l’agent SQL peuvent être interrompus par le redémarrage du processus de l’agent

L’agent SQL crée une nouvelle session chaque fois que le travail est démarré, ce qui augmente progressivement la consommation de mémoire. Pour éviter d’atteindre la limite de mémoire interne qui bloquerait l’exécution de tâches planifiées, le processus de l’agent sera redémarré une fois que la consommation de mémoire aura atteint le seuil. Cela peut entraîner l’interruption de l’exécution des tâches en cours au moment du redémarrage.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Les limites de mémoire OLTP en mémoire ne sont pas appliquées

Le niveau de service Critique pour l'entreprise n’appliquera pas correctement les [limites max de mémoire pour les objets à mémoire optimisée ](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) dans certains cas. L’instance managée peut permettre à la charge de travail d’utiliser davantage de mémoire pour les opérations OLTP en mémoire, ce qui peut affecter la disponibilité et la stabilité de l’instance. Les requêtes OLTP en mémoire qui atteignent les limites peuvent ne pas échouer immédiatement. Ce problème sera corrigé bientôt. Les requêtes qui utilisent plus de mémoire OLTP en mémoire échoueront plus tôt si elles atteignent les [limites](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Solution de contournement :** [Surveillez l’utilisation du stockage OLTP en mémoire ](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) à l’aide de [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) pour vous assurer que la charge de travail n’utilise pas plus que la mémoire disponible. Augmentez les limites de mémoire qui dépendent du nombre de vCores ou optimisez votre charge de travail pour utiliser moins de mémoire.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Erreur retournée lors de la tentative de suppression d’un fichier qui n’est pas vide

SQL Server/Managed Instance [ne permettent pas à l’utilisateur de supprimer un fichier qui n’est pas vide](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Si vous tentez de supprimer un fichier de données non vide à l’aide d’une instruction `ALTER DATABASE REMOVE FILE`, l’erreur `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` n’est pas retournée immédiatement. Managed Instance continue à essayer de supprimer le fichier et l’opération échoue après 30 minutes avec `Internal server error`.

**Solution de contournement** : Supprimez le contenu du fichier à l’aide de la commande `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)`. S’il s’agit du seul fichier dans le groupe de fichiers, vous devez supprimer les données de la table ou de la partition associées à ce groupe de fichiers avant de réduire le fichier, et éventuellement charger ces données dans une autre table ou partition.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Les opérations de changement de niveau de service et de création d’instance sont bloquées par la restauration de base de données en cours

Les instructions `RESTORE` en cours, le processus de migration Data Migration Service et la limite de restauration dans le temps intégrée bloquent la mise à jour du niveau de service ou le redimensionnement de l’instance existante et la création de nouvelles instances jusqu’à la fin du processus de restauration. Le processus de restauration bloquera ces opérations sur les instances managées et les pools d’instances sur le sous-réseau où le processus de restauration est en cours d’exécution. Les instances dans les pools d’instances ne sont pas affectées. Les opérations de création ou de changement du niveau de service n’échoueront pas et n’expireront pas : elles se poursuivront une fois le processus de restauration terminé ou annulé.

**Solution de contournement** : Attendez la fin du processus de restauration ou annulez-le si l’opération de création ou de mise à jour du niveau de service a une priorité plus élevée.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Il peut être nécessaire de reconfigurer Resource Governor sur le niveau de service Critique pour l’entreprise après le basculement

Il se peut que la fonctionnalité [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) qui permet de limiter les ressources affectées aux charges de travail utilisateur classe erronément une charge de travail utilisateur après un basculement ou une modification du niveau de service apportée par un utilisateur (par exemple, la modification du nombre maximal de vCores ou de la taille maximale de stockage d’instance).

**Solution de contournement** : Exécutez `ALTER RESOURCE GOVERNOR RECONFIGURE` régulièrement ou dans le cadre du travail de l’agent SQL qui exécute la tâche SQL lorsque l’instance démarre si vous utilisez [Resource Governor](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>Les boîtes de dialogue Service Broker utilisées entre plusieurs bases de données doivent être réinitialisées après la mise à niveau du niveau de service

Les boîtes de dialogue Service Broker utilisées entre plusieurs bases de données cessent de transmettre les messages aux services dans d’autres bases de données après un changement du niveau de service. Les messages ne sont **pas perdus** et peuvent être retrouvés dans la file d’attente de l’expéditeur. Toute modification du nombre de vCores ou de la taille de stockage des instances dans Managed Instance entraîne le changement de la valeur `service_broke_guid` affichée dans [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) pour toutes les bases de données. Tout élément `DIALOG` créé à l’aide de l’instruction [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) qui référence des répartiteurs Service Brokers dans une autre base de données cesse de remettre les messages au service cible.

**Solution de contournement :** arrêtez toutes les activités qui utilisent des conversations de boîtes de dialogue Service Broker entre plusieurs bases de données avant de mettre à jour le niveau de service et réinitialisez-les ensuite. S’il reste des messages non transmis après le changement de niveau de service, consultez les messages en question dans la file d’attente source et renvoyez-les à la file d’attente cible.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>L’emprunt d’identité des types de connexion Azure AD n’est pas pris en charge

L’emprunt d’identité avec `EXECUTE AS USER` ou `EXECUTE AS LOGIN` des principaux AAD suivants n’est pas pris en charge :
-    Utilisateurs AAD avec alias. L’erreur suivante est retournée dans ce cas `15517`.
- Connexions et utilisateurs AAD basés sur des applications ou des principaux de service AAD. Les erreurs suivantes sont retournées dans ces cas `15517` et `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>Le paramètre @query n’est pas pris en charge dans sp_send_db_mail

Le paramètre `@query` dans la procédure [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) ne fonctionne pas.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>La réplication transactionnelle doit être reconfigurée après un basculement géographique

Si la réplication transactionnelle est activée sur une base de données dans un groupe de basculement automatique, l’administrateur de l’instance managée doit nettoyer toutes les publications de l’ancien principal et les reconfigurer sur le nouveau principal après un basculement vers une autre région. Pour plus d’informations, consultez [Réplication](sql-database-managed-instance-transact-sql-information.md#replication).

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Les utilisateurs et connexions AAD ne sont pas pris en charge dans SSDT

SQL Server Data Tools ne prend pas complètement en charge les utilisateurs et les connexions Azure Active Directory.

### <a name="temporary-database-is-used-during-restore-operation"></a>Une base de données temporaire est utilisée d’une opération de restauration

Lors de la restauration d’une base de données sur Managed Instance, le service de restauration commence par créer une base de données vide du nom souhaité pour allouer le nom sur l’instance. Après un certain temps, cette base de données est supprimée et la restauration de la base de données réelle commence. Une valeur GUID aléatoire est allouée temporairement, plutôt qu’un nom, à la base de données en état de *restauration*. Le nom temporaire est remplacé par le nom spécifié dans l’instruction `RESTORE` une fois le processus de restauration terminé. Au cours de la phase initiale, l’utilisateur peut accéder à la base de données vide et même créer des tables ou charger des données dans celle-ci. Cette base de données temporaire est supprimée lorsque le service de restauration démarre la deuxième phase.

**Solution de contournement** : N’accédez pas à la base de données que vous restaurez tant que la restauration n’est pas terminée.

### <a name="tempdb-structure-and-content-is-re-created"></a>La structure et le contenu de TEMPDB sont recréés

La base de données `tempdb` est toujours fractionnée en 12 fichiers de données, et cette structure de fichiers ne peut pas être modifiée. La taille maximale par fichier n’est pas modifiable, et il n’est pas possible d’ajouter de nouveaux fichiers dans `tempdb`. `Tempdb` est toujours recréé en tant que base de données vide lorsque l’instance démarre ou bascule, et les modifications apportées dans `tempdb` ne sont pas conservées.

### <a name="exceeding-storage-space-with-small-database-files"></a>Dépassement de l’espace de stockage avec des fichiers de base de données de petite taille

Les instructions `CREATE DATABASE`, `ALTER DATABASE ADD FILE` et `RESTORE DATABASE` risquent d’échouer, car l’instance peut atteindre la limite de Stockage Azure.

Chaque instance managée Usage général dispose de 35 To de stockage réservé pour l’espace disque Premium Azure. Chaque fichier de base de données est placé sur un disque physique distinct. Les tailles de disque peuvent être de 128 Go, 256 Go, 512 Go, 1 To ou 4 To. L’espace non utilisé sur le disque n’est pas facturé, mais la somme des tailles des disques Premium Azure ne peut pas dépasser 35 To. Dans certains cas, une instance managée qui n’a pas besoin de 8 To au total peut dépasser la limite Azure de 35 To en taille de stockage à cause d’une fragmentation interne.

Par exemple, une instance managée à usage général peut avoir un fichier d’une taille de 1,2 To placé sur un disque de 4 To. Elle peut également comporter 248 fichiers, d’une taille de 1 Go chacun, qui sont placés sur des disques distincts de 128 Go. Dans cet exemple :

- La taille totale du stockage de disque alloué est de 1 x 4 To + 248 x 128 Go = 35 To.
- L’espace total réservé pour les bases de données sur l’instance est de 1 x 1,2 To + 248 x 1 Go = 1,4 To.

Cet exemple montre que, dans certaines circonstances, du fait d’une répartition spécifique des fichiers, une instance managée peut atteindre la limite des 35 To réservée pour un disque Premium Azure attaché, sans que vous vous y attendiez.

Dans cet exemple, les bases de données existantes continuent de fonctionner et peuvent croître sans aucun problème du moment que de nouveaux fichiers ne sont pas ajoutés. La création ou la restauration de bases de données est impossible, car il n’y a pas suffisamment d’espace pour les nouveaux lecteurs de disque, même si la taille totale de toutes les bases de données n’atteint pas la limite de taille d’instance. L’erreur qui est retournée dans ce cas n’est pas claire.

Vous pouvez [identifier le nombre de fichiers restants](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) à l’aide de vues système. Si vous atteignez cette limite, essayez de [vider et de supprimer certains fichiers plus petits au moyen de l’instruction DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file), ou basculez vers le [niveau Critique pour l’entreprise, qui ne connaît pas cette limite](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Affichage des valeurs GUID à la place des noms de base de données

Plusieurs vues système, compteurs de performances, messages d’erreur, événements XEvent et entrées du journal des erreurs affichent des identificateurs de base de données GUID au lieu d’afficher les noms des bases de données. Ne prenez pas en compte ces identificateurs GUID, car ils vont être remplacés à l’avenir par les noms des bases de données.

**Solution de contournement** : utilisez la vue sys.databases pour résoudre le nom de la base de données à partir du nom de la base de données physique, spécifié sous forme d’identificateurs de base de données GUID.

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Les journaux des erreurs ne sont pas conservés

Les journaux des erreurs qui sont disponibles dans Managed Instance ne sont pas persistants, et leur taille n’est pas comprise dans la limite de stockage maximale. Les journaux des erreurs peuvent être automatiquement effacés si un basculement se produit. Il peut y avoir des écarts dans l’historique du journal des erreurs, car Managed Instance a été déplacé plusieurs fois sur plusieurs machines virtuelles.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>L’utilisation de la même étendue de transaction pour deux bases de données appartenant à une même instance n’est pas prise en charge

Dans .NET, la classe `TransactionScope` ne fonctionne pas si deux requêtes sont envoyées à deux bases de données appartenant à la même instance et à la même étendue de transaction :

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

Même si ce code fonctionne avec les données d’une même instance, il nécessite MSDTC.

**Solution de contournement :** servez-vous de [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) pour utiliser une autre base de données dans un contexte de connexion au lieu d’utiliser deux connexions.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Les modules CLR et les serveurs liés parfois ne parviennent pas à référencer une adresse IP locale

Il arrive que des modules CLR placés dans une instance managée, et que des serveurs liés ou des requêtes distribuées référençant une instance active, ne parviennent pas quelquefois à résoudre l’adresse IP d’une instance locale. Cette erreur est un problème temporaire.

**Solution de contournement :** utilisez des connexions contextuelles dans un module CLR, si possible.

## <a name="updates"></a>Mises à jour

Pour obtenir la liste des mises à jour et améliorations de SQL Database, consultez [Mises à jour du service SQL Database](https://azure.microsoft.com/updates/?product=sql-database).

Pour connaître les mises à jour et améliorations apportées à tous les services Azure, consultez [Mises à jour des services](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Contribuer au contenu

Pour contribuer à la documentation Azure SQL Database, consultez le [guide du contributeur de documents](https://docs.microsoft.com/contribute/).
