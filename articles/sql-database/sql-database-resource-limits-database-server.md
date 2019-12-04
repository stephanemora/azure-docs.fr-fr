---
title: Limites des ressources Azure SQL Database | Microsoft Docs
description: Cet article contient une vue d’ensemble des limites de ressources Azure SQL Database pour les bases de données uniques et les pools élastiques. Il fournit également des informations concernant ce qui se passe lorsque ces limites de ressources sont atteintes ou dépassées.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/19/2019
ms.openlocfilehash: 40b277f0b1bfb3501bb246e555d46db5e1ee9f95
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279300"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>Limites de ressources SQL Database et gouvernance des ressources

Cet article contient une vue d’ensemble des limites de ressources SQL Database d’un serveur SQL Database qui gère des bases de données uniques et des pools élastiques. Il fournit des informations sur ce qui se passe lorsque ces limites de ressources sont atteintes ou dépassées et décrit les mécanismes de gouvernance des ressources utilisés pour appliquer ces limites.

> [!NOTE]
> Pour les limites de Managed Instance, consultez [Limites de ressources de SQL Database pour les instances gérées](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Limites maximales de ressources

| Ressource | Limite |
| :--- | :--- |
| Bases de données par serveur | 5 000 |
| Nombre par défaut de serveurs par abonnement dans n’importe quelle région | 20 |
| Nombre maximal de serveurs par abonnement dans n’importe quelle région | 200 |  
| Quota DTU/eDTU par serveur | 54 000 |  
| Quota vCore par serveur/instance | 540 |
| Nombre maximal de pools par serveur | Limité par le nombre de DTU ou de vCores. Par exemple, si chaque pool contient 1 000 unités DTU, alors un serveur peut prendre en charge 54 pools.|
|||

> [!NOTE]
> Pour augmenter le quota DTU/eDTU ou vCore ou pour obtenir une quantité de serveurs supérieure à la quantité par défaut, vous pouvez envoyer une nouvelle demande de support dans le Portail Azure pour l’abonnement en indiquant le type de problème « Quota ». Le quota DTU/eDTU et le nombre maximal de bases de données par serveur limitent le nombre de pools élastiques par serveur.

> [!IMPORTANT]
> Le nombre de bases de données approchant la limite par serveur SQL Database, les éléments suivants peuvent se produire :
>
> - Augmentation de latence dans l’exécution de requêtes sur la base de données master.  Cela inclut les vues de statistiques d’utilisation des ressources telles que sys.resource_stats.
> - Augmentation de latence des opérations de gestion et le rendu des points de vue de portails qui impliquent des bases de données sur le serveur.

### <a name="storage-size"></a>Taille de stockage

Pour connaître les limites de taille de stockage de chaque niveau tarifaire dans le cas de ressources de bases de données uniques, consultez [Limites de ressources par DTU](sql-database-dtu-resource-limits-single-databases.md) ou [Limites de ressources par vCore](sql-database-vcore-resource-limits-single-databases.md).

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Que se passe-t-il lorsque les limites de ressources d’une base de données sont atteintes ?

### <a name="compute-dtus-and-edtus--vcores"></a>Compute (DTU et eDTU / vCore)

Lorsque l’utilisation du calcul de la base de données (exprimée en DTU, eDTU ou vCore) est élevée, la latence de la requête augmente et peut même causer une expiration du délai d’attente des requêtes. Dans ces conditions, les requêtes peuvent être mises en file d’attente par le service et reçoivent des ressources pour leur exécution lorsque des ressources se libèrent.
En cas d’utilisation élevée des calculs, voici certaines des options d’atténuation à votre disposition :

- Augmenter la taille de calcul de la base de données ou du pool élastique pour fournir plus de ressources de calcul à la base de données. Consultez [Mise à l’échelle des ressources d’une base de données unique](sql-database-single-database-scale.md) et [Mise à l'échelle des ressources d’un pool élastique](sql-database-elastic-pool-scale.md).
- Optimiser les requêtes pour réduire l’utilisation des ressources de chaque requête. Pour plus d’informations, consultez la page [Paramétrage/Compréhension de requêtes](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Stockage

Lorsque l’espace de base de données utilisé atteint la limite de taille maximale, les insertions et mises à jour de base de données qui augmentent la taille des données échouent et les clients reçoivent un [message d’erreur](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md). Les instructions SELECT et DELETE continuent de fonctionner correctement.

En cas d’utilisation élevée de l’espace, voici certaines des options d’atténuation à votre disposition :

- Augmenter la taille maximale de la base de données ou du pool élastique, ou ajouter plus de stockage. Consultez [Mise à l’échelle des ressources d’une base de données unique](sql-database-single-database-scale.md) et [Mise à l'échelle des ressources d’un pool élastique](sql-database-elastic-pool-scale.md).
- Si la base de données est dans un pool élastique, vous pouvez également déplacer la base de données en dehors du pool afin que son espace de stockage ne soit pas partagé avec d’autres bases de données.
- Réduire une base de données afin de récupérer l’espace inutilisé. Pour plus d’informations, consultez [Gérer l’espace des fichiers dans Azure SQL Database](sql-database-file-space-management.md).

### <a name="sessions-and-workers-requests"></a>Sessions et workers (requêtes)

Le nombre maximal de sessions et de rôles de travail est déterminé par le niveau de service et la taille de calcul (DTU/eDTU ou vCore). Lorsque les limites de sessions ou de workers sont atteintes, les nouvelles requêtes sont refusées et les clients reçoivent un message d’erreur. Si le nombre de connexions disponibles peut être contrôlé par l’application, le nombre de workers simultanés est souvent plus difficile à estimer et à contrôler. Cela est particulièrement vrai pendant les pics de charge lorsque les limites de ressources de base de données sont atteintes et que les rôles de travail s’accumulent en raison de requêtes durables, de chaînes de blocage volumineuses ou d’un parallélisme excessif de requêtes.

En cas d’utilisation élevée de workers ou de sessions, voici certaines des options d’atténuation à votre disposition :

- Augmenter le niveau de service ou la taille de calcul du pool élastique ou de la base de données. Consultez [Mise à l’échelle des ressources d’une base de données unique](sql-database-single-database-scale.md) et [Mise à l'échelle des ressources d’un pool élastique](sql-database-elastic-pool-scale.md).
- Optimiser les requêtes afin de réduire l’utilisation des ressources de chaque requête si la cause de l’utilisation du travail accrue est un problème de contention des ressources de calcul. Pour plus d’informations, consultez la page [Paramétrage/Compréhension de requêtes](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="resource-governance"></a>Gouvernance des ressources

Pour appliquer des limites de ressources, Azure SQL Database utilise une implémentation de la gouvernance des ressources basée sur [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) SQL Server, modifié et étendu pour exécuter un service de base de données SQL Server dans Azure. Sur chaque instance SQL Server du service, il existe plusieurs [listes de ressources partagées](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) et [groupes de charges de travail](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group), avec des limites de ressources définies au niveau des listes et des groupes pour fournir une [base de données en tant que service équilibrée](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). La charge de travail utilisateur et les charges de travail internes sont classées dans des listes de ressources partagées et des groupes de charges de travail distincts. La charge de travail utilisateur sur les réplicas primaires et secondaires accessibles en lecture, y compris les géoréplications, est classée dans la liste de ressources partagées `SloSharedPool1` et dans le groupe de charges de travail `UserPrimaryGroup.DBId[N]`, où `N` correspond à la valeur de l’ID de base de données. En outre, il existe plusieurs listes de ressources partagées et groupes de charges de travail pour différentes charges de travail internes.

Outre l’utilisation de Resource Governor pour régir les ressources au sein du processus SQL Server, Azure SQL Database utilise également les [objets de traitement](https://docs.microsoft.com/windows/win32/procthread/job-objects) Windows pour la gouvernance des ressources au niveau du processus et les [Outils de gestion de ressources pour serveur de fichiers (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) pour la gestion des quotas de stockage.

La gouvernance des ressources Azure SQL Database est hiérarchique par nature. De haut en bas, les limites sont appliquées au niveau du système d’exploitation et au niveau du volume de stockage à l’aide des mécanismes de gouvernance des ressources du système d’exploitation et de Resource Governor, puis au niveau de la liste de ressources partagées à l’aide de Resource Governor, et enfin au niveau du groupe de charge de travail à l’aide de Resource Governor. Les limites de gouvernance des ressources en vigueur pour la base de données ou le pool élastique actuels sont exposées dans l’affichage [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database). 

### <a name="data-io-governance"></a>Gouvernance des E/S de données

La gouvernance des E/S de données est un processus d’Azure SQL Database utilisé pour limiter les E/S physiques en lecture et en écriture sur les fichiers de données d’une base de données. Les limites IOPS sont définies pour chaque niveau de service afin de réduire l’effet de « voisinage bruyant », d’assurer une allocation des ressources équitable dans le service multilocataire et de rester dans les limites des capacités du matériel et du stockage sous-jacents.

Pour les bases de données uniques, les limites des groupes de charge de travail sont appliquées à toutes les E/S de stockage par rapport à la base de données, tandis que les limites des listes de ressources partagées s’appliquent à toutes les E/S de stockage pour toutes les bases de données sur la même instance SQL Server, notamment la base de données `tempdb`. Pour les pools élastiques, les limites des groupes de charge de travail s’appliquent à chaque base de données dans le pool, tandis que la limite des listes de ressources partagées s’applique à l’ensemble du pool élastique, y compris la base de données `tempdb`, qui est partagée entre toutes les bases de données du pool. En général, les limites des listes de ressources partagées peuvent ne pas être réalisables par la charge de travail sur une base de données (unique ou en pool), car les limites des groupes de charge de travail sont inférieures à celles des listes de ressources partagées et restreignent l’IOPS ou le débit plus tôt. Toutefois, les limites des listes peuvent être atteintes par la charge de travail combinée sur plusieurs bases de données sur la même instance SQL Server.

Par exemple, si une requête génère 1 000 IOPS sans gouvernance des ressources d’E/S, mais que la limite IOPS maximale du groupe de charge de travail est définie sur 900 IOPS, la requête ne peut pas générer plus de 900 IOPS. Toutefois, si la limite IOPS maximale de la liste de ressources partagées est définie sur 1 500 IOPS et que le nombre total d’E/S de tous les groupes de charge de travail associés à la liste de ressources partagées dépasse 1 500 IOPS, les E/S de la même requête peuvent être réduites sous la limite du groupe de travail de 900 IOPS.

Les valeurs minimales et maximales d’IOPS et de débit renvoyées par l’affichage [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) agissent comme limites/seuils, et non comme garanties. En outre, la gouvernance des ressources ne garantit pas une latence de stockage spécifique. Les meilleurs latence, IOPS et débit possible pour une charge de travail utilisateur donnée dépendent non seulement des limites de gouvernance des ressources d’E/S, mais également de la combinaison des tailles d’E/S utilisées et des fonctionnalités du stockage sous-jacent. SQL Server utilise des E/S dont la taille varie entre 512 Ko et 4 Mo. Dans le cadre de l’application des limites d’IOPS, chaque E/S est comptabilisée, quelle que soit sa taille, à l’exception des bases de données ayant des fichiers de données dans Stockage Azure. Dans ce cas, les E/S de plus de 256 Ko sont comptabilisés comme plusieurs E/S de 256 Ko, afin de les aligner sur la comptabilisation des E/S de Stockage Azure.

Pour les bases de données De base, Standard et Usage général, qui utilisent des fichiers de données dans le stockage Azure, la valeur `primary_group_max_io` peut ne pas être réalisable si une base de données ne dispose pas de suffisamment de fichiers de données pour fournir de manière cumulative ce nombre d’IOPS, si les données ne sont pas réparties de manière égale entre les fichiers ou si le niveau de performance des blobs sous-jacents restreint l’IOPS/le débit au-dessous de la limite de gouvernance des ressources. De même, avec de petites E/S de journaux générées par la validation fréquente des transactions, la valeur `primary_max_log_rate` peut ne pas être réalisable par une charge de travail en raison de la limite d’IOPS sur le blob de stockage Azure sous-jacent.

Les valeurs d’utilisation des ressources telles que `avg_data_io_percent` et `avg_log_write_percent`, signalées dans les affichages [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) et [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database), sont calculées sous la forme de pourcentages des limites de gouvernance des ressources maximales. Par conséquent, lorsque des facteurs autres que la gouvernance des ressources limitent l’IOPS/le débit, il est possible de voir les courbes d’IOPS/de débit s’aplatir et les latences qui augmentent à mesure que la charge de travail augmente, même si l’utilisation des ressources signalée reste inférieure à 100 %. Pour connaître l’IOPS, le débit et la latence en lecture et écriture par fichier de base de données, utilisez la fonction [sys.dm_io_virtual_file_stats()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql). Cette fonction couvre toutes les E/S de la base de données, y compris les E/S en arrière-plan qui ne sont pas prises en compte pour `avg_data_io_percent`, mais utilise l’IOPS et le débit du stockage sous-jacent et peut avoir un impact sur la latence de stockage observée.

### <a name="transaction-log-rate-governance"></a>Gouvernance relative au taux de journalisation des transactions

Ce type de gouvernance est un processus utilisé dans Azure SQL Database pour réduire les taux d’ingestions élevés des charges de travail de type bulk Insert, opérations SELECT INTO et création d’index. Ces limites font l’objet d’un suivi et sont appliquées à une vitesse inférieure à la seconde, selon le rythme de génération des enregistrements de journal, ce qui limite le débit quel que soit le nombre d’E/S générées sur les fichiers de données.  Les vitesses de génération des journaux des transactions évoluent actuellement de manière linéaire, en fonction du matériel. La vitesse maximale autorisée est de 96 Mbit/s avec le modèle d’achat vCore. 

> [!NOTE]
> Les E/S physiques réelles pour les fichiers journaux des transactions ne sont pas régies ou limitées.

Les taux de journalisation sont configurés de sorte qu’il soit possible de les obtenir et de les maintenir dans un large éventail de scénarios, tandis que l’ensemble du système peut gérer ses fonctionnalités avec un impact réduit sur la charge utilisateur. La gouvernance des taux de journalisation garantit que les sauvegardes de fichier journal des transactions respectent les SLA publiés en matière de récupérabilité.  Cette gouvernance empêche également tout backlog excessif sur les réplicas secondaires.

Lorsque des enregistrements de journaux sont générés, chaque opération est évaluée, dans le but de déterminer si elle doit être différée afin que le taux de journalisation maximum souhaité soit maintenu (en Mo/s par seconde). Les retards ne sont pas ajoutés lorsque les enregistrements de journaux sont vidés dans le stockage : la gouvernance du taux de journalisation est appliquée lors de la génération du taux de journalisation lui-même.

Les taux de génération de journaux réels imposés lors de l’exécution peuvent également être influencés par des mécanismes de commentaires, ce qui réduit temporairement les taux de journalisation disponibles afin de permettre au système de se stabiliser. La gestion de l’espace des fichiers journaux, l’absence d’exécution en cas de saturation de l’espace de journalisation disponible et les mécanismes de réplication des groupes de disponibilité peuvent réduire temporairement les limites totales du système.

La mise en forme du trafic de l’administrateur des taux de journalisation est présentée par le biais des types d’attente suivants (exposés dans le DMV [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)) :

| Type d’attente | Notes |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Limitation appliquée aux bases de données |
| POOL_LOG_RATE_GOVERNOR | Limitation appliquée aux pools |
| INSTANCE_LOG_RATE_GOVERNOR | Limitation appliquée aux niveaux d’instances |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Contrôle des commentaires, réplication physique des groupes de disponibilité dans la section Critique pour l’entreprise/Premium ne suivant pas |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Contrôle des commentaires, limitation de taux pour éviter une condition de saturation de l’espace de journalisation |
|||

Lorsque vous rencontrez une limite de taux de journalisation qui entrave l’évolutivité du système, envisagez les options suivantes :
- Montez en puissance vers un niveau de service supérieur afin d’obtenir le taux de journalisation maximal, qui est de 96 Mo/s. 
- Si les données en cours de chargement sont temporaires, telles que des données de processus de site dans un processus ETL, vous pouvez les charger dans la base de données tempdb (qui présente une journalisation minime). 
- Pour les scénarios d’analyse, chargez les données dans une table columnstore en cluster couverte. Cela réduit le taux de journalisation requis en raison de la compression. Cette technique augmente l’utilisation de l’UC et s’applique uniquement aux jeux de données qui bénéficient d’index columnstore en cluster. 

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les limites générales d’Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).
- Pour plus d’informations sur les DTU et eDTU, consultez [DTU et eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Pour plus d’informations sur les limites de taille de tempdb, consultez [Base de données tempdb dans SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
