---
title: Continuité des activités cloud - récupération de base de données - SQL Database | Microsoft Docs
description: Découvrez comment Azure SQL Database prend en charge la continuité des activités cloud et la récupération de base de données et vous aide à maintenir les applications cloud critiques pour la mission.
keywords: continuité des activités, continuité des activités cloud, récupération d’urgence de base de données, récupération de base de données
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: mathoma, carlrab
manager: digimobile
origin.date: 04/04/2019
ms.date: 04/15/2019
ms.openlocfilehash: dfa5d4cb2d782f1466329300157a64fd17765460
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61412341"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Vue d’ensemble de la continuité de l’activité avec la base de données Azure SQL

La **continuité d’activité** dans Azure SQL Database fait référence aux mécanismes, stratégies et procédures qui permettent à votre entreprise de continuer à fonctionner en cas d’interruption affectant notamment son infrastructure informatique. Dans la plupart des cas, Azure SQL Database gère les événements d’interruption qui peuvent se produire dans l’environnement de cloud et préservent l’exécution de vos applications et processus d’entreprise. Toutefois, certains de ces événements ne peuvent être gérés par SQL Database, notamment dans les cas suivants :

- Un utilisateur a accidentellement supprimé ou mis à jour une ligne dans une table.
- Un attaquant a réussi à supprimer des données ou une base de données.
- Un tremblement de terre a provoqué une panne d’alimentation et désactivé temporairement le centre de données.

Azure SQL Database n’a aucun contrôle sur ces situations. Par conséquent, vous devez utiliser les fonctionnalités de continuité d’activité de SQL Database, qui vous permettent de récupérer vos données et de préserver l’exécution de vos applications.

Cette vue d’ensemble décrit les fonctionnalités que fournit Azure SQL Database pour la continuité d’activité et la reprise d’activité après sinistre. Découvrez les options, les recommandations et les didacticiels pour la récupération à partir d’événements d’interruption qui pourraient entraîner une perte de données ou l’indisponibilité de votre base de données et de votre application. Connaissez la procédure à suivre lorsqu’un utilisateur ou qu’une erreur d’application affecte l’intégrité des données, lorsqu’une région Azure subit une panne ou que votre application nécessite une maintenance.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Fonctionnalités de la base de données SQL que vous pouvez utiliser pour garantir une continuité d’activité

Du point de vue de la base de données, il existe quatre scénarios d’interruption potentiels :

- Des défaillances matérielles ou logicielles locales telles qu’une panne de lecteur de disque affectent le nœud de base de données.
- Une suppression ou une altération des données se produit, généralement à la suite d’un bogue d’application ou d’une erreur humaine. Ces défaillances sont intrinsèquement spécifiques à l’application et ne peuvent pas toujours être détectées ou résolues par l’infrastructure de façon automatique.
- Une panne de centre de données se produit, à la suite d’une catastrophe naturelle, par exemple. Ce scénario nécessite un certain degré de redondance géographique permettant le basculement d’application vers un autre centre de données.
- Des erreurs de mise à niveau ou de maintenance se sont produites. Ces problèmes imprévus qui se produisent au cours des mises à niveau ou opérations de maintenance planifiées relatives à une application ou une base de données peuvent nécessiter une restauration rapide à un état antérieur de la base de données.

SQL Database fournit plusieurs fonctionnalités de continuité d’activité, notamment des sauvegardes automatisées et une réplication facultative de la base de données, permettant d’atténuer les problèmes posés par ces scénarios. Tout d’abord, vous devez comprendre comment [l’architecture haute disponibilité](sql-database-high-availability.md) de SQL Database assure une disponibilité de 99,99 % et une résilience à certains événements d’interruption pouvant affecter votre processus d’entreprise.
Ensuite, vous pouvez en apprendre davantage sur les mécanismes supplémentaires que vous pouvez utiliser en vue d’une récupération à la suite d’événements d’interruption ne pouvant pas être gérées par l’architecture haute disponibilité de SQL Database, par exemple :

- Les [tables temporelles](sql-database-temporal-tables.md) vous permettent de restaurer des versions de ligne à partir de n’importe quel point dans le temps.
- Les [sauvegardes automatisées intégrées](sql-database-automated-backups.md) et la [limite de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore) vous permettent de restaurer l’ensemble de la base de données selon un moment donné au cours des 35 derniers jours.
- Vous pouvez [restaurer une base de données supprimée](sql-database-recovery-using-backups.md#deleted-database-restore) au point où sa suppression s’est produite si le **serveur SQL Database n’a pas été supprimé**.
- La [rétention des sauvegardes à long terme](sql-database-long-term-retention.md) vous permet de conserver les sauvegardes sur une période allant jusqu’à 10 ans.
- La [géoréplication active](sql-database-active-geo-replication.md) vous permet de créer des réplicas lisibles et d’effectuer un basculement manuel vers n’importe quel réplica en cas de panne de centre de données ou de mise à niveau d’application.
- Le [groupe de basculement automatique](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) permet à l’application d’effectuer automatiquement une récupération en cas de panne du centre de données.

Chacune de ces fonctionnalités possède des caractéristiques spécifiques concernant le temps de récupération estimé (ERT) et le risque de perte de données pour les transactions récentes. Une fois que vous avez compris ces options, vous pouvez choisir celles qui vous conviennent et, dans la plupart des scénarios, les utiliser ensemble dans différents scénarios. Au moment d’élaborer votre plan de continuité d’activité, vous devez comprendre le délai maximal acceptable nécessaire à la récupération complète de l’application après l’événement d’interruption. Il s’agit de l’objectif de délai de récupération (RTO, recovery time objective). Vous devez également comprendre sur quelle période maximale l’application peut accepter de perdre les mises à jour de données récentes (intervalle de temps) lors de la récupération après l’événement d’interruption. Il s’agit de l’objectif de point de récupération (RPO, recovery point objective).

Le tableau suivant compare l’ERT et le RPO pour chaque niveau de service pour les scénarios les plus courants.

| Fonctionnalité | De base | standard | Premium | Usage général | Critique pour l’entreprise
| --- | --- | --- | --- |--- |--- |
| Limite de restauration dans le temps à partir de la sauvegarde |Tout point de restauration dans un délai de sept jours |Tout point de restauration dans un délai de 35 jours |Tout point de restauration dans un délai de 35 jours |N’importe quel point de restauration compris dans la période définie (jusqu’à 35 jours)|N’importe quel point de restauration compris dans la période définie (jusqu’à 35 jours)|
| Géo-restauration à partir de sauvegardes répliquées géographiquement |ERT < 12 h<br> RPO < 1 h |ERT < 12 h<br>RPO < 1 h |ERT < 12 h<br>RPO < 1 h |ERT < 12 h<br>RPO < 1 h|ERT < 12 h<br>RPO < 1 h|
| Groupes de basculement automatique |RTO = 1 h<br>RPO < 5 s |RTO = 1 h<br>RPO < 5 s |RTO = 1 h<br>RPO < 5 s |RTO = 1 h<br>RPO < 5 s|RTO = 1 h<br>RPO < 5 s|
| Basculement de base de données manuelle |ERT = 30 s<br>RPO < 5 s |ERT = 30 s<br>RPO < 5 s |ERT = 30 s<br>RPO < 5 s |ERT = 30 s<br>RPO < 5 s|ERT = 30 s<br>RPO < 5 s|

> [!NOTE]
> *Basculement de base de données manuelle* fait référence au basculement d’une base de données à son géo-répliqué secondaire à l’aide du [mode planifié](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).

## <a name="recover-a-database-to-the-existing-server"></a>Récupération d’une base de données sur le serveur existant

SQL Database effectue automatiquement une combinaison de sauvegardes de bases de données complètes (toutes les semaines), de sauvegardes de bases de données différentielles (toutes les 12 heures) et de sauvegardes de fichiers journaux (toutes les 5 à 10 minutes) pour protéger votre entreprise contre la perte de données. Les sauvegardes sont stockées dans le stockage RA-GRS pendant 35 jours pour tous les niveaux de service à l’exception des niveaux de service DTU De base avec lesquels les sauvegardes sont stockées pendant 7 jours. Pour plus d’informations, consultez la rubrique concernant les [sauvegardes de base de données automatiques](sql-database-automated-backups.md). Vous pouvez restaurer une base de données existante à partir des sauvegardes automatisées à un point antérieur dans le temps en tant que nouvelle base de données sur le même serveur SQL Database à l’aide du portail Azure, de PowerShell ou de l’API REST. Pour plus d’informations, consultez [Limite de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore).

Si la période maximale de rétention qui est associée à la limite de restauration dans le temps n’est pas suffisante pour votre application, vous pouvez la rallonger en configurant une stratégie de rétention à long terme (LTR) pour les bases de données. Pour plus d’informations, consultez [Conservation des sauvegardes à long terme](sql-database-long-term-retention.md).

Vous pouvez utiliser ces sauvegardes automatiques pour récupérer une base de données après divers événements d’interruption, à la fois dans votre propre centre de données et dans un autre centre de données. Le délai de récupération est généralement inférieur à 12 heures. La récupération d’une base de données très volumineuse ou active peut prendre un certain temps. Lorsque vous utilisez des sauvegardes automatiques, le délai estimé de récupération dépend de plusieurs facteurs, notamment du nombre total de bases de données à récupérer dans la même région au même moment, de la taille des bases de données, de la taille du journal des transactions et de la bande passante réseau. Pour plus d’informations sur le temps de récupération, voir [Temps de récupération de la base de données](sql-database-recovery-using-backups.md#recovery-time). Lorsque vous effectuez une récupération vers une autre région de données, le risque de perte de données est limité à 1 heure avec l’utilisation des sauvegardes géoredondantes.

Utilisez des sauvegardes automatisées et une [limite de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore) comme mécanisme de continuité d’activité et de récupération si votre application :

- N'est pas essentielle.
- N’a pas de contrat SLA contraignant, une interruption de service de 24 heures ou plus n’entraîne pas la responsabilité financière.
- Affiche un faible taux de modification des données (peu de transactions par heure) et accepte une perte de données correspondant à une heure de modifications.
- Est sensible aux coûts.

Si vous avez besoin d’une récupération plus rapide, utilisez la [géoréplication active](sql-database-active-geo-replication.md) ou [les groupes de basculement automatique](sql-database-auto-failover-group.md). Si vous devez être en mesure de récupérer des données remontant à plus de 35 jours, utilisez la [rétention de sauvegardes à long terme](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region"></a>Récupération d’une base de données sur une autre région

Bien que le fait soit rare, un centre de données Azure peut subir une panne. En cas de panne, il entraîne une interruption de service qui peut durer de quelques minutes à plusieurs heures.

- Vous pouvez attendre que votre base de données redevienne disponible une fois la panne réparée au niveau du centre de données. Cette méthode fonctionne pour les applications qui peuvent se permettre d’avoir la base de données déconnectée. C’est le cas, par exemple, d’un projet de développement ou d’une version d’évaluation gratuite sur lesquels vous n’avez pas à travailler en permanence. Lorsqu’un centre de données subit une panne, vous ne savez pas combien de temps cette panne peut durer. Cette méthode fonctionne donc uniquement si vous n’avez pas à travailler sur votre base de données pendant un certain temps.
- Vous pouvez également restaurer une base de données sur n’importe quel serveur d’une région Azure quelconque à l’aide des [sauvegardes de base de données géo-redondantes](sql-database-recovery-using-backups.md#geo-restore) (ou géorestaurations). La géorestauration utilise une sauvegarde géoredondante en tant que source et peut être mise à profit pour récupérer une base de données même si la base de données ou le centre de données est inaccessible en raison d’une défaillance.
- Enfin, vous pouvez récupérer rapidement en cas de panne si vous avez configuré à l’aide de géo-secondaire [géo-réplication active](sql-database-active-geo-replication.md) ou un [groupe de basculement automatique](sql-database-auto-failover-group.md) pour votre base de données ou les bases de données. Selon la technologie choisie, vous pouvez utiliser soit le basculement manuel, soit le basculement automatique. Le basculement proprement dit ne prend que quelques secondes, mais son activation par le service prend au moins 1 heure. Cela est nécessaire pour s’assurer que le basculement est justifié par l’étendue de la panne. En outre, le basculement peut entraîner une petite perte de données en raison de la nature de la réplication asynchrone. Consultez le tableau précédemment illustré dans cet article pour plus d’informations sur le RTO et le RPO du basculement automatique.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>
> [!IMPORTANT]
> Pour utiliser la géo-réplication active et les groupes de basculement automatique, vous devez être le propriétaire de l’abonnement ou disposer d’autorisations administratives dans SQL Server. Vous pouvez configurer et effectuer un basculement à l’aide du portail Azure, de PowerShell ou de l’API REST, en utilisant les autorisations d’abonnement Azure ou à l’aide de Transact-SQL en utilisant des autorisations dans SQL Server.

Utilisez des groupes de basculement automatique si votre application répond à l’un des critères suivants :

- Est essentielle.
- A un contrat de niveau de service (SLA) qui n’autorise pas plus de 12 heures d’interruption de service.
- Toute interruption de service peut engager la responsabilité financière.
- Affiche un taux élevé de données modifiées et la perte d’une heure de données n’est pas acceptable.
- Le coût supplémentaire lié à l'utilisation de la géoréplication est plus faible que la responsabilité financière potentielle et la perte d'activité associée.

Lorsque vous prenez une décision, le délai de la récupération et la quantité de données perdues dépendent de la façon dont vous décidez d’utiliser ces fonctionnalités de continuité d’activité dans votre application. En effet, vous pouvez choisir d’utiliser une combinaison de sauvegardes de bases de données et de géo-réplication active en fonction des besoins de votre application. Pour plus d’informations sur la conception d’applications pour des bases de données autonomes et des pools élastiques à l’aide de ces fonctionnalités de continuité d’activité, consultez les articles [Conception d’applications pour la récupération d’urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) et [Stratégies de récupération d’urgence de pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).


Les sections suivantes fournissent une vue d’ensemble des étapes de la récupération à l’aide de sauvegardes de bases de données ou de la géo-réplication active. Pour des instructions détaillées, y compris les exigences de planification, les étapes de post-récupération et des informations sur la simulation d’une panne pour effectuer un exercice de récupération d’urgence, voir la section [Récupérer une base de données SQL Azure en cas de défaillance](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Se préparer à une panne

Quelle que soit la fonctionnalité de continuité d’activité que vous utilisez, vous devez :

- Identifier et préparer le serveur cible, y compris les règles de pare-feu IP au niveau du serveur, les connexions et les autorisations au niveau de la base de données MASTER.
- Déterminer comment rediriger les clients et les applications clientes vers le nouveau serveur
- Documenter les autres dépendances, notamment les paramètres d’audit et les alertes

Si la préparation n’est pas effectuée correctement, la mise en ligne de vos applications après un basculement ou une récupération de base de données prend plus de temps et nécessite probablement de résoudre certains problèmes dans une situation de stress, ce qui constitue une combinaison très risquée.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Basculement vers une base de données secondaire géo-répliquée

Si vous utilisez la géo-réplication active ou groupes de basculement automatique comme mécanisme de récupération, vous pouvez configurer une stratégie de basculement automatique ou utiliser [basculement non planifié manuel](sql-database-active-geo-replication-portal.md#initiate-a-failover). Une fois le basculement lancé, la base de données secondaire est promue comme nouvelle base principale et peut alors enregistrer de nouvelles transactions et répondre aux requêtes, avec des pertes de données minimes pour les données qui n’ont pas encore été répliquées. Pour plus d’informations sur la création du processus de basculement, reportez-vous à la section [Conception d’une application pour la récupération d’urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Lorsque le centre de données revient en ligne, les anciennes bases primaires se reconnectent automatiquement à la nouvelle base primaire et deviennent les bases de données secondaires. Si vous devez replacer la base de données primaire dans sa région d’origine, vous pouvez initier un basculement programmé de manière manuelle (restauration automatique).

### <a name="perform-a-geo-restore"></a>Effectuer une restauration géographique

Si vous utilisez les sauvegardes automatisées avec stockage géoredondant (activé par défaut), vous pouvez récupérer la base de données à l’aide de la [géo-restauration](sql-database-disaster-recovery.md#recover-using-geo-restore). Dans la plupart des cas, la récupération intervient sous 12 heures, avec une perte de données de 1 heure maximum, selon le moment où la dernière sauvegarde de fichier journal a été effectuée et répliquée. Tant que la récupération n’est pas terminée, la base de données ne peut pas enregistrer de transactions ou répondre à des requêtes. Remarquez que la géo-restauration permet uniquement de restaurer la base de données au dernier point disponible dans le temps.

> [!NOTE]
> Si le centre de données redevient disponible avant que vous ne transfériez votre application vers la base de données récupérée, vous pouvez annuler la récupération.

### <a name="perform-post-failover--recovery-tasks"></a>Exécution de tâches de post-basculement/récupération

Après la récupération à l’aide d’un de ces mécanismes de récupération, vous devez effectuer les tâches supplémentaires suivantes afin que les utilisateurs et les applications soient de nouveau opérationnels :

- Rediriger les clients et les applications clientes vers le nouveau serveur et la base de données restaurée
- Vérifier que les règles de pare-feu IP appropriées sont en place au niveau du serveur pour permettre aux utilisateurs de se connecter ou d’utiliser les [pare-feu au niveau de la base de données](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-the-azure-portal) afin d’activer les règles qui conviennent.
- Vérifier que les connexions et les autorisations appropriées au niveau de la base de données MASTER sont en place (ou utiliser des [utilisateurs contenus](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- Configurer l’audit, selon les besoins
- Configurer les alertes, selon les besoins

> [!NOTE]
> Si vous utilisez un groupe de basculement et que vous vous connectez aux bases de données à l’aide de l’écouteur en lecture-écriture, la redirection après le basculement se produit automatiquement et en toute transparence pour l’application.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Mettre à niveau une application avec un temps d’arrêt minimal

Parfois, une application doit être déconnectée en raison d’une maintenance planifiée, par exemple une mise à niveau. [Gestion des mises à niveau des applications](sql-database-manage-application-rolling-upgrade.md) explique comment utiliser la géo-réplication active pour activer les mises à niveau propagées de votre application cloud afin de réduire le temps d’arrêt pendant les mises à niveau et de fournir un chemin de récupération en cas de problème.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la conception d’applications pour des bases de données autonomes et des pools élastiques, consultez les articles [Conception d’applications pour la récupération d’urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) et [Stratégies de récupération d’urgence de pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
