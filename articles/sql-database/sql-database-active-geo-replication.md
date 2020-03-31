---
title: La géoréplication active
description: Utilisez la géoréplication active pour créer des bases de données secondaires de bases de données individuelles accessibles en lecture dans un centre de données identique ou différent (région).
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/17/2020
ms.openlocfilehash: fe006cebe9aab30a6aaa0bdf2bf3362a494f64d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77426271"
---
# <a name="creating-and-using-active-geo-replication"></a>Création et utilisation de la géoréplication active

La géoréplication active est une fonctionnalité Azure SQL Database qui vous permet de créer des bases de données secondaires lisibles à partir de bases de données individuelles sur un serveur SQL Database situé dans le même centre de données (région) ou dans un centre de données distinct.

> [!NOTE]
> La géoréplication active n’est pas prise en charge par Managed Instance. Pour le basculement géographique d’instances gérées, utilisez des [groupes de basculement automatique](sql-database-auto-failover-group.md).

La géoréplication active est conçue comme une solution de continuité d’activité qui permet à l’application d’effectuer une reprise d’activité rapide au niveau des bases de données individuelles en cas de panne régionale ou à plus grande échelle. Si la géoréplication est activée, l’application peut lancer le basculement vers une base de données secondaire dans une autre région Azure. Jusqu’à quatre bases de données secondaires sont prises en charge dans des régions identiques ou différentes, et les bases de données secondaires peuvent également servir pour les requêtes d’accès en lecture seule. Le basculement doit être lancé manuellement par l’application ou l’utilisateur. Après le basculement, la nouvelle base de données primaire présente un point de terminaison de connexion différent. Le diagramme suivant illustre la configuration standard d’une application cloud géoredondante avec la géoréplication active.

![Géoréplication active](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> SQL Database prend également en charge les groupes de basculement automatique. Pour plus d’informations, voir la section sur l’utilisation des [Groupes de basculement automatique](sql-database-auto-failover-group.md). De plus, la géoréplication active n’est pas prise en charge pour les bases de données créées dans Managed Instance. Envisagez d’utiliser des [groupes de basculement](sql-database-auto-failover-group.md) avec les instances managées.

Si, pour une raison quelconque, votre base de données primaire échoue ou doit simplement être mise hors connexion, vous pouvez initier un basculement vers l’une de vos bases de données secondaires. Lorsque le basculement est activé pour l’une des bases de données secondaires, toutes les autres bases de données secondaires sont automatiquement liées à la nouvelle base de données primaire.

Vous pouvez gérer la réplication et le basculement d’une base de données individuelle ou d’un ensemble de bases de données sur un serveur ou dans un pool élastique à l’aide de la géo-réplication active. Pour ce faire, vous pouvez utiliser les éléments suivants :

- [Portail Azure](sql-database-geo-replication-portal.md)
- [PowerShell : base de données unique](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell : pool élastique](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL : base de données unique ou pool élastique](/sql/t-sql/statements/alter-database-azure-sql-database)
- [API REST : base de données unique](https://docs.microsoft.com/rest/api/sql/replicationlinks)


La géoréplication active tire parti de la technologie [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) de SQL Server pour répliquer de manière asynchrone les transactions validées sur la base de données primaire vers une base de données secondaire à l’aide de l’isolement de capture instantanée. Les groupes de basculement automatique fournissent la sémantique de groupe en plus de la géo-réplication active. Cependant, le même mécanisme de réplication asynchrone est utilisé. À un moment donné, la base de données secondaire peut être légèrement en retard sur la base de données primaire, mais les données secondaire ne peuvent jamais contenir de transactions partielles. Grâce à la redondance entre régions, les applications peuvent récupérer rapidement d’une perte permanente de tout ou partie d’un centre de données résultant de catastrophes naturelles, de graves erreurs humaines ou d’actes de malveillance. Vous trouverez les données d’objectif de point de récupération dans [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).

> [!NOTE]
> En cas de défaillance du réseau entre deux régions, nous tentons de rétablir les connexions toutes les 10 secondes.
> [!IMPORTANT]
> Pour garantir qu’une modification critique de la base de données primaire est répliquée sur la base de données secondaire avant le basculement, vous pouvez forcer la synchronisation pour vous assurer que les modifications critiques (par exemple, les mises à jour de mot de passe) sont répliquées. La synchronisation forcée a un impact sur les performances, car elle bloque le thread appelant jusqu’à ce que toutes les transactions validées soient répliquées. Pour plus d’informations, consultez [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Pour superviser le décalage de réplication entre la base de données primaire et la base de données géosecondaire, voir [sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

La figure suivante présente un exemple de géo-réplication active configurée avec une base de données primaire située dans la région USA Centre Nord, ainsi qu’une base de données secondaire située dans la région USA Centre Sud.

![Relation de géo-réplication](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Comme les bases de données secondaires sont accessibles en lecture, elles peuvent être utilisées pour décharger des charges de travail en lecture seule, telles que des travaux de génération de rapports. Si vous utilisez la géo-réplication active, il est possible de créer la base de données secondaire dans la même région que la base de données primaire, mais cela n’augmente pas la résistance de l’application aux défaillances graves. Si vous utilisez des groupes de basculement automatique, votre base de données secondaire est toujours créée dans une autre région.

En plus de la récupération d’urgence, la géo-réplication active peut être utilisée dans les scénarios suivants :

- **Migration de base de données** : vous pouvez vous servir de la géoréplication active pour migrer une base de données d’un serveur vers un autre serveur en ligne avec un temps d’arrêt minimal.
- **Mises à niveau de l’application** : vous pouvez créer une base de données secondaire supplémentaire faisant office de copie de restauration automatique lors des mises à niveau de l’application.

Pour assurer vraiment la continuité des activités, l’ajout d’une redondance de base de données entre les centres de données n’est qu’une partie de la solution. La récupération d’une application (service) de bout en bout après une défaillance catastrophique implique la récupération de tous les composants constituant le service et tous les services dépendants. En voici quelques exemples : logiciel client (il peut s’agir par exemple d’un navigateur avec un code JavaScript personnalisé), serveurs web frontaux, ressources de stockage et DNS. Il est essentiel que tous les composants résistent aux mêmes défaillances et redeviennent disponibles dans l’objectif de délai de récupération (RTO) de votre application. Par conséquent, vous devez identifier tous les services dépendants et comprendre les garanties et les fonctionnalités qu’ils fournissent. Ensuite, vous devez prendre les mesures appropriées pour vous assurer que votre service fonctionne pendant le basculement des services dont il dépend. Pour plus d’informations sur la conception de solutions pour la récupération d’urgence, consultez la page [Designing Cloud Solutions for Disaster Recovery Using active geo-replication](sql-database-designing-cloud-solutions-for-disaster-recovery.md) (Conception de solutions cloud pour la récupération d’urgence à l’aide de la géo-réplication active).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Terminologie et fonctionnalités associées à la géoréplication active

- **Réplication asynchrone automatique**

  Vous pouvez créer une base de données secondaire seulement par ajout à une base de données existante. La base de données secondaire peut être créée sur n’importe quel serveur Azure SQL Database. Une fois créée, la base de données secondaire est remplie avec les données copiées à partir de la base de données primaire. Ce processus est appelé amorçage. Une fois la base de données secondaire créée et amorcée, les mises à jour de la base de données primaire sont automatiquement répliquées de manière asynchrone sur la base de données secondaire. La réplication asynchrone signifie que les transactions sont validées sur la base de données primaire avant leur réplication sur la base de données secondaire.

- **Réplicas secondaires accessibles en lecture**

  Une application peut accéder à une base de données secondaire pour des opérations en lecture seule avec des principaux de sécurité identiques ou différents de ceux utilisés pour accéder à la base de données primaire. Les bases de données secondaires fonctionnent en mode d’isolement d’instantané pour garantir que la réplication des mises à jour de la base de données primaire n’est pas retardée par des requêtes exécutées sur la base de données secondaire.

> [!NOTE]
> La relecture du journal est différée sur la base de données secondaire s’il existe des mises à jour de schéma sur le Principal. Cette dernière nécessite un verrouillage de schéma sur la base de données secondaire.
> [!IMPORTANT]
> Vous pouvez utiliser la géoréplication pour créer une base de données secondaire dans la même région que la base de données primaire. Vous pouvez utiliser cette base de données secondaire pour équilibrer les charges de travail en lecture seule dans la même région. Toutefois, une base de données secondaire dans la même région n'offre pas davantage de résilience aux défaillances et ne constitue donc pas une cible de basculement adapté à des fins de récupération d’urgence. De même, elle ne garantit en rien l’isolation de la zone de disponibilité. Utilisez le niveau de service Critique pour l’entreprise ou Premium avec une [configuration de zone redondante](sql-database-high-availability.md#zone-redundant-configuration) pour isoler la zone de disponibilité.   
>

- **Basculement planifié**

  Le basculement planifié bascule les rôles des bases de données primaires et secondaires une fois la synchronisation complète terminée. Il s’agit d’une opération en ligne qui n’entraîne pas de perte de données. L’heure de l’opération dépend de la taille du journal des transactions sur le serveur principal qui doit être synchronisé. Le basculement planifié est conçu pour les scénarios suivants : (a) pour simuler la récupération d’urgence en production lorsque la perte de données n’est pas acceptable ; (b) pour déplacer la base de données vers une autre région ; et (c) pour renvoyer la base de données vers la région primaire après la résolution de la panne (restauration automatique).

- **Basculement non planifié**

  Un basculement non planifié ou forcé fait immédiatement basculer la base de données secondaire vers le rôle primaire sans synchronisation avec la base de données primaire. Toutes les transactions validées sur le serveur principal qui ne sont pas répliquées sur la base de données secondaire seront perdues. Cette opération est conçue comme une méthode de récupération pendant les pannes lorsque le serveur principal n’est pas accessible, mais que la disponibilité de la base de données doit être restaurée rapidement. Lorsque la base de données primaire d’origine est de nouveau en ligne, elle se reconnecte et devient une nouvelle base de données secondaire. Toutes les transactions non synchronisées avant le basculement sont conservées dans le fichier de sauvegarde, mais ne sont pas synchronisées avec le nouveau réplica principal, pour éviter les conflits. Ces transactions devront être fusionnées manuellement avec la version la plus récente de la base de données primaire.
 
- **Bases de données secondaires accessibles en lecture**

  Jusqu’à 4 bases de données secondaires peuvent être créées pour chaque base primaire. S’il n’existe qu’une seule base de données secondaire, en cas d’échec de celle-ci, l’application est exposée à un risque plus élevé jusqu’à la création d’une nouvelle base de données secondaire. S’il existe plusieurs bases de données secondaires, l’application reste protégée même en cas d’échec de l’une des bases de données secondaires. Les bases de données secondaires supplémentaires peuvent également être utilisées pour effectuer un scale-out des charges de travail en lecture seule

  > [!NOTE]
  > Si vous utilisez la géoréplication active pour créer une application mondialement distribuée et que vous avez besoin de fournir un accès en lecture seule aux données dans plus de quatre régions, vous pouvez créer la base de données secondaire d’une base de données secondaire (processus connu sous le nom de chaînage). De cette manière, vous pouvez assurer une mise à l’échelle quasiment illimitée pour la réplication de base de données. En outre, le chaînage réduit la charge de réplication qui pèse sur la base de données primaire. Le compromis est l’augmentation du décalage de réplication sur les bases de données secondaires situées aux extrémités.

- **Géoréplication des bases de données dans un pool élastique**

  Chaque base de données secondaire peut participer séparément à un pool élastique ou ne faire partie d’aucun pool élastique. Le choix du pool pour chaque base de données secondaire est distinct et ne dépend pas de la configuration des autres bases de données secondaires (primaires comme secondaires). Chaque pool élastique est contenu dans une seule région. Par conséquent plusieurs bases de données secondaires de la même topologie ne peuvent jamais partager un pool élastique.


- **Basculement et restauration automatique contrôlés par l’utilisateur**

  Une base de données secondaire peut être basculée explicitement à tout moment vers le rôle primaire par l’application ou l’utilisateur. Pendant une panne réelle, l’option « non planifiée » doit être utilisée, qui promeut immédiatement une base de données secondaire en base de données primaire. Lorsque la base de données primaire en échec récupère et redevient disponible, le système marque automatiquement la base de données primaire récupérée comme base de données secondaire, et la met à jour par rapport à la nouvelle base de données primaire. En raison de la nature asynchrone de la réplication, une petite quantité de données peut être perdue lors de basculements non planifiés si une base de données primaire échoue avant la réplication des modifications les plus récentes sur la base de données secondaire. Quand une base de données primaire avec plusieurs bases de données secondaires bascule, le système reconfigure automatiquement les relations de réplication et lie les bases de données secondaires restantes à la base de données nouvellement promue comme primaire sans aucune intervention de l’utilisateur. Une fois la panne à l’origine du basculement résolue, il peut être judicieux de rétablir l’application dans la région primaire. Pour ce faire, la commande de basculement doit être appelée avec l’option « planifiée ».

## <a name="preparing-secondary-database-for-failover"></a>Préparation de la base de données secondaire pour le basculement

Pour vous assurer que votre application peut accéder immédiatement au nouveau réplica principal après le basculement, assurez-vous que les exigences d’authentification de votre serveur et de la base de données secondaires sont correctement configurées. Pour plus d’informations, consultez [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md). Pour garantir la conformité après le basculement, vérifiez que la stratégie de rétention de sauvegarde de la base de données secondaire correspond à celle du serveur principal. Ces paramètres ne font pas partie de la base de données et ne sont pas répliqués. Par défaut, le réplica secondaire est configuré avec une période de rétention de récupération jusqu`à une date et heure par défaut de sept jours. Pour plus d’informations, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).

> [!IMPORTANT]
> Si votre base de données est membre d'un groupe de basculement, vous ne pouvez pas lancer son basculement à l'aide de la commande de basculement de la géoréplication. Utilisez la commande de basculement du groupe. Pour basculer une base de données individuelle, vous devez d'abord la supprimer du groupe de basculement. Pour plus d'informations, consultez [Groupes de basculement](sql-database-auto-failover-group.md). 


## <a name="configuring-secondary-database"></a>Configuration d'une base de données secondaire

Les bases de données primaire et secondaire doivent offrir le même niveau de service. Il est également vivement recommandé de créer la base de données secondaire avec la même taille de calcul (DTU ou vCore) que la base de données primaire. En cas de charge de travail d'écriture importante de la base de données primaire, une base de données secondaire dotée d'une taille de calcul inférieure peut ne pas suivre. Cela entraînera le décalage de la phase de restauration par progression sur le réplica secondaire et l’indisponibilité potentielle. Une base de données secondaire en retard par rapport à la base de données primaire risque également d’occasionner une perte de données importante si un basculement forcé est requis. Pour atténuer ces risques, la géoréplication active limite le taux de journalisation de la base de données principale pour permettre à ses secondaires de rattraper le retard. Après un basculement, une configuration de base de données secondaire déséquilibrée peut aussi altérer le niveau de performance de l'application en raison de la capacité de calcul insuffisante de la nouvelle base de données primaire. Elle devra être mise à niveau vers une taille de calcul supérieure, ce qui ne sera possible qu'une fois la panne résolue. 


> [!IMPORTANT]
> Par conséquent, un RPO publié de 5 s ne peut pas être garanti, sauf si la base de données secondaire est configurée avec la même taille de calcul que la base de données primaire. 


Si vous décidez de créer une base de données secondaire avec une taille de calcul inférieure, vous pouvez utiliser le graphique de pourcentage d’E/S du journal sur le portail Azure pour estimer la taille de calcul minimale de la base de données secondaire qui est nécessaire pour supporter la charge de réplication. Par exemple, si votre base de données primaire est P6 (1 000 DTU) et si son pourcentage d’E/S du journal est de 50 %, la base de données secondaire doit être au moins P4 (500 DTU). Vous pouvez également récupérer les données d’E/S du journal à l’aide des vues de base de données [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) ou [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database).  L’étranglement est signalé comme un état d’attente HADR_THROTTLE_LOG_LOG_RATE_MISMATCHED_SLO dans les vues [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) et [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) des bases de données. 

Pour plus d’informations sur les tailles de calcul SQL Database, consultez [Présentation des niveaux de service SQL Database](sql-database-purchase-models.md).

## <a name="cross-subscription-geo-replication"></a>Géoréplication entre abonnements

Pour configurer la géoréplication active entre deux bases de données appartenant à des abonnements différents (que ce soit sous le même locataire ou non), vous devez suivre la procédure spéciale décrite dans cette section.  La procédure repose sur des commandes SQL et requiert ce qui suit : 

- Création d'un ID de connexion privilégié sur les deux serveurs
- Ajout de l'adresse IP à la liste verte du client qui procède à la modification sur les deux serveurs (comme l'adresse IP de l'hôte qui exécute SQL Server Management Studio). 

Le client qui procède aux modifications doit disposer d'un accès réseau au serveur principal. Bien que la même adresse IP client doive être ajoutée à la liste verte sur le serveur secondaire, la connectivité réseau au serveur secondaire n'est pas strictement nécessaire. 

### <a name="on-the-master-of-the-primary-server"></a>Sur le maître du serveur principal

1. Ajoutez l'adresse IP à la liste verte du client qui procède aux modifications (pour plus d'informations, consultez [Configurer le pare-feu](sql-database-firewall-configure.md)). 
1. Créez un ID de connexion dédié à la configuration de la géoréplication active (et, si nécessaire, modifiez les informations d'identification) :

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. Créez un utilisateur et attribuez-lui le rôle dbmanager : 

   ```sql
   create user geodrsetup for login geodrsetup
   alter role geodrsetup dbmanager add member geodrsetup
   ```

1. Prenez note du SID associé au nouvel ID de connexion en utilisant la requête suivante : 

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup'
   ```

### <a name="on-the-source-database-on-the-primary-server"></a>Sur la base de données source du serveur principal

1. Créez un utilisateur pour le même ID de connexion :

   ```sql
   create user geodrsetup for login geodrsetup
   ```

1. Attribuez le rôle db_owner à l'utilisateur :

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>Sur le maître du serveur secondaire 

1. Ajoutez l'adresse IP à la liste verte du client qui procède aux modifications. Il doit s'agir de la même adresse IP que pour le serveur principal. 
1. Créez le même ID de connexion que sur le serveur principal, en utilisant les mêmes nom d'utilisateur/mot de passe et le même SID : 

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. Créez un utilisateur et attribuez-lui le rôle dbmanager :

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>Sur le maître du serveur principal

1. Connectez-vous au maître du serveur principal à l'aide du nouvel ID de connexion. 
1. Créez un réplica secondaire de la base de données source sur le serveur secondaire (si nécessaire, modifiez le nom de la base de données et le nom du serveur) :

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

Au terme de la configuration initiale, les utilisateurs, les ID de connexion et les règles de pare-feu créés peuvent être supprimés. 


## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Synchronisation des informations d’identification et des règles de pare-feu

Nous recommandons d’utiliser des [règles de pare-feu IP au niveau de la base de données](sql-database-firewall-configure.md) pour les bases de données géorépliquées, de façon à ce que ces règles puissent être répliquées avec la base de données, garantissant ainsi que toutes les bases de données secondaires ont les mêmes règles de pare-feu IP que la base de données primaire. Cette approche évite aux clients de devoir configurer et tenir à jour manuellement les règles de pare-feu sur les serveurs hébergeant les bases de données primaire et secondaires. De même, le recours à des [utilisateurs de base de données contenus](sql-database-manage-logins.md) pour l’accès aux données garantit que les bases de données primaires et secondaires ont toujours les mêmes informations d’identification d’utilisateur afin qu’un basculement n’entraîne aucune interruption due à une discordance d’ID de connexion et de mots de passe. Avec l’ajout [d’Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), les clients peuvent gérer l’accès utilisateur aux bases de données primaires et secondaires. Cela élimine également la nécessité de gérer les informations d’identification dans l’ensemble des bases de données.

## <a name="upgrading-or-downgrading-primary-database"></a>Mise à niveau ou rétrogradation de la base de données primaire

Vous pouvez augmenter ou diminuer la taille de calcul d’une base de données primaire (au sein du même niveau de service, mais pas entre les niveaux Usage général et Critique pour l’entreprise) sans déconnecter les bases de données secondaires. Lors d’une mise à niveau, nous vous recommandons de mettre à niveau la base de données secondaire dans un premier temps, avant de mettre à niveau la base de données primaire. Lors d’une rétrogradation, inversez l’ordre : rétrogradez tout d’abord la base de données primaire, puis dans un second temps la base de données secondaire. Lorsque vous passez la base de données à un niveau de service supérieur ou inférieur, cette recommandation est appliquée.

> [!NOTE]
> Si vous avez créé une base de données secondaire dans le cadre de la configuration des groupes de basculement, il n’est pas conseillé de passer la base de données secondaire à un niveau de service inférieur. En effet, votre couche Données pourrait manquer de capacité pour traiter votre charge de travail normale après l’activation du basculement.

> [!IMPORTANT]
> Pour permettre la mise à l'échelle vers le niveau supérieur d'une base de données primaire dans un groupe de basculement, la base de données secondaire doit avoir été préalablement mise à l'échelle vers le niveau supérieur. Si vous tentez de mettre à l’échelle la base de données primaire préalablement à la base de données secondaire, l'erreur suivante peut s'afficher :
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Prévention de la perte de données critiques

En raison de la latence élevée des réseaux étendus, la copie continue utilise un mécanisme de réplication asynchrone. La perte de certaines données reste donc inévitable en cas de défaillance. Or, pour certaines applications, une perte de données est inacceptable. Pour protéger ces mises à jour critiques, un développeur d’applications peut appeler la procédure système [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) immédiatement après la validation de la transaction. L’appel de **sp_wait_for_database_copy_sync** bloque le thread appelant jusqu’à ce que la dernière transaction validée ait été transmise à la base de données secondaire. Toutefois, il n’attend pas que les transactions transmises soient relues et validées sur la base de données secondaire. **sp_wait_for_database_copy_sync** est limité à une relation de copie continue spécifique. Tout utilisateur disposant de droits de connexion à la base de données primaire peut appeler cette procédure.

> [!NOTE]
> **sp_wait_for_database_copy_sync** empêche la perte de données après un basculement, mais il ne garantit pas la synchronisation complète pour l’accès en lecture. Le délai causé par un appel de procédure **sp_wait_for_database_copy_sync** peut être significatif et dépend de la taille du journal des transactions au moment de l’appel.

## <a name="monitoring-geo-replication-lag"></a>Surveiller le décalage de la géoréplication

Pour surveiller le décalage par rapport au RPO, utilisez la colonne *replication_lag_sec* de [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) sur la base de données primaire. Elle affiche le décalage en secondes entre les transactions validées sur la base de données primaire et les transactions persistantes sur la base de données secondaire. Par exemple, une valeur de décalage d'une seconde indique qu'en cas d'interruption de la base de données primaire, à ce moment précis, et de basculement, les transactions les plus récentes ne seront pas sauvegardées à hauteur d'une seconde. 

Pour mesurer le décalage ayant trait aux modifications de la base de données primaire appliquées à la base de données secondaire, c'est-à-dire pour lire à partir de la base de données secondaire, comparez la valeur *last_commit* sur la base de données secondaire avec la même valeur sur la base de données primaire.

> [!NOTE]
> Sur la base de données primaire, *replication_lag_sec* peut présenter une valeur NULL, ce qui signifie que la base de données primaire ne connaît pas précisément l'état de la base de données secondaire.   Cela se produit généralement après le redémarrage du processus et relève d'une situation temporaire. Envisagez d’alerter l’application si *replication_lag_sec* présente une valeur NULL pendant une période prolongée. En effet, la base de données secondaire peut ne pas être en mesure de communiquer avec la base de données primaire en raison d'un échec de connectivité permanent. D'autres cas de figure peuvent aussi être à l'origine d'une importante différence de valeur *last_commit* entre les bases de données primaire et secondaire. Par exemple, si une validation intervient sur la base de données primaire après une longue période sans modifications, la différence augmente considérablement avant de redevenir rapidement nulle. Envisagez un état d'erreur lorsque la différence entre ces deux valeurs demeure importante pendant une période prolongée.


## <a name="programmatically-managing-active-geo-replication"></a>Gestion de la géo-réplication active par programmation

Comme indiqué plus haut, la géoréplication active peut aussi être gérée par programme à l’aide d’Azure PowerShell et de l’API REST. Les tableaux ci-dessous décrivent l’ensemble des commandes disponibles. La géoréplication active comprend un ensemble d’API Azure Resource Manager pour la gestion, notamment [l’API REST Azure SQL Database](https://docs.microsoft.com/rest/api/sql/) et les [applets de commande Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Ces API nécessitent l’utilisation de groupes de ressources et la prise en charge de la sécurité basée sur les rôles (RBAC). Pour plus d’informations sur l’implémentation de rôles d’accès, consultez la page sur le [contrôle d’accès en fonction du rôle Azure](../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL : Gérer le basculement des bases de données uniques et mises en pool

> [!IMPORTANT]
> Ces commandes Transact-SQL s’appliquent uniquement à la géoréplication active et ne s’appliquent pas aux groupes de basculement. Par conséquent, elles ne s’appliquent pas non plus aux instances managées, car elles prennent uniquement en charge les groupes de basculement.

| Commande | Description |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Utilise l’argument ADD SECONDARY ON SERVER afin de créer une base de données secondaire pour une base de données existante puis lance la réplication des données |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Utilise l’argument FAILOVER ou FORCE_FAILOVER_ALLOW_DATA_LOSS pour basculer d’une base de données secondaire à une base de données principale afin de lancer le basculement |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Utilise l’argument REMOVE SECONDARY ON SERVER pour mettre fin à une réplication de données entre une base de données SQL et la base de données secondaire spécifiée. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Retourne des informations concernant tous les liens de réplication existants pour chaque base de données sur le serveur Azure SQL Database. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Obtient l’heure de la dernière réplication, le dernier décalage de la réplication et d’autres informations sur le lien de réplication pour une base de données SQL spécifique. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Affiche l’état de toutes les opérations de base de données, y compris l’état des liens de réplication. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |oblige l’application à attendre que toutes les transactions validées sont répliquées et acceptées par la base de données secondaire active. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell : Gérer le basculement des bases de données uniques et mises en pool

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Pour ces cmdlets, voir [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm.

| Applet de commande | Description |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Obtient une ou plusieurs bases de données. |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Crée une base de données secondaire pour une base de données existante puis lance la réplication des données. |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Bascule d’une base de données secondaire à une base de données principale afin de lancer le basculement. |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Met fin à une réplication de données entre une base de données SQL et la base de données secondaire spécifiée. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Obtient les liens de géo-réplication entre une base de données Azure SQL et un groupe de ressources ou SQL Server. |
|  | |

> [!IMPORTANT]
> Pour plus d’exemples de scripts, consultez [Configurer et basculer une base de données unique à l’aide de la géoréplication active](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) et [Configurer et basculer une base de données mise en pool à l’aide de la géoréplication active](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>API REST : Gérer le basculement des bases de données uniques et mises en pool

| API | Description |
| --- | --- |
| [.Créer ou mettre à jour la base de données (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Crée, met à jour ou restaure une base de données principale ou secondaire. |
| [Créer ou mettre à jour l’état de la base de données](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Retourne l’état durant une opération de création. |
| [Définir la base de données secondaire comme principale (basculement planifié)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Définit la base de données secondaire principale via basculement à partir de la base de données primaire actuelle. **Cette option n’est pas prise en charge avec Managed Instance.**|
| [Définir la base de données secondaire comme principale (basculement non planifié)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Définit la base de données secondaire principale via basculement à partir de la base de données primaire actuelle. Cette opération peut entraîner une perte de données. **Cette option n’est pas prise en charge avec Managed Instance.**|
| [Obtenir un lien de réplication](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Obtient un liens de réplication spécifique pour une base de données SQL particulière dans un partenariat de géo-réplication. Récupère les informations visibles dans la vue de catalogue sys.geo_replication_links. **Cette option n’est pas prise en charge avec Managed Instance.**|
| [Liens de réplication - Liste par base de données](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Obtient tous les liens de réplication pour une base de données SQL donnée dans un partenariat de géo-réplication. Récupère les informations visibles dans la vue de catalogue sys.geo_replication_links. |
| [Supprimer un lien de réplication](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Supprime un lien de réplication de base de données. Opération impossible pendant le basculement. |
|  | |

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des exemples de scripts, consultez :
  - [Configurer et basculer une base de données unique à l’aide de la géoréplication active](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Configurer et basculer une base de données mise en pool à l’aide de la géoréplication active](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- SQL Database prend également en charge les groupes de basculement automatique. Pour plus d’informations, voir la section sur l’utilisation des [Groupes de basculement automatique](sql-database-auto-failover-group.md).
- Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
- Pour en savoir plus sur les sauvegardes automatisées d’une base de données Azure SQL, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md).
- Pour en savoir plus sur les exigences d’authentification pour de nouveaux serveurs et bases de données primaires, consultez [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md).
