---
title: Groupes de basculement
description: Les groupes de basculement automatique sont une fonctionnalité de SQL Database qui vous permet de gérer la réplication et le basculement automatique/coordonné d’un groupe de bases de données sur un serveur SQL Database ou de toutes les bases de données d’une instance gérée.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 11/07/2019
ms.openlocfilehash: 470e9a9c36b6b4ec2e40db5dfc47ae03fb6b5aa8
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421374"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Utiliser les groupes de basculement automatique pour permettre le basculement transparent et coordonné de plusieurs bases de données

Les groupes de basculement automatique sont une fonctionnalité de SQL Database qui vous permet de gérer la réplication et le basculement d’un groupe de bases de données sur un serveur SQL Database ou de toutes les bases de données d’une instance gérée vers une autre région. Il s’agit d’une abstraction déclarative sur la fonctionnalité de [géoréplication active](sql-database-active-geo-replication.md) existante, conçue pour simplifier le déploiement et la gestion des bases de données géorépliquées à l’échelle. Vous pouvez déclencher le basculement manuellement ou vous pouvez le déléguer au service SQL Database via une stratégie définie par l’utilisateur. Cette dernière option vous permet de récupérer automatiquement plusieurs bases de données associées dans une région secondaire après une défaillance grave ou un autre événement non planifié qui entraîne une perte totale ou partielle de la disponibilité du service SQL Database dans la région primaire. Un groupe de basculement peut inclure une ou plusieurs bases de données, généralement utilisées par la même application. En outre, vous pouvez utiliser les bases de données secondaires accessibles en lecture pour décharger les charges de travail de requêtes en lecture seule. Comme les groupes de basculement automatique impliquent de nombreuses bases de données, celles-ci doivent être configurées sur le serveur primaire. Les groupes de basculement automatique prennent en charge la réplication de toutes les bases de données du groupe vers un seul serveur secondaire situé dans une autre région,

> [!NOTE]
> Si vous utilisez des bases de données uniques ou mises en pool sur un serveur SQL Database et souhaitez que plusieurs bases de données secondaires se trouvent dans des régions identiques ou différentes, utilisez la [géoréplication active](sql-database-active-geo-replication.md). 

Lorsque vous utilisez des groupes de basculement automatique avec une stratégie de basculement automatique, toute panne qui affecte une ou plusieurs des bases de données du groupe donne lieu à un basculement automatique. En général, il s’agit d’incidents qui ne peuvent pas être corrigés automatiquement par les opérations de haute disponibilité automatiques intégrées. Les déclencheurs de basculement peuvent être, par exemple, un incident provoqué par un anneau de locataire SQL ou un anneau de contrôle en panne en raison d’une fuite de mémoire du noyau du système d’exploitation sur plusieurs nœuds de calcul, ou un incident causé par un ou plusieurs anneaux de locataire en panne, car un câble réseau incorrect a été coupé au cours de la désactivation du matériel de routine.  Pour plus d’informations, consultez [Haute disponibilité de SQL Database](sql-database-high-availability.md).

En outre, les groupes de basculement automatique fournissent des points de terminaison d’écouteur de lecture-écriture et de lecture seule qui restent inchangés pendant les basculements. Que vous utilisiez l’activation manuelle ou automatique du basculement, ce dernier bascule toutes les bases de données secondaires du groupe en bases de données primaires. Une fois le basculement des bases de données terminé, l’enregistrement DNS est automatiquement mis à jour pour rediriger les points de terminaison vers la nouvelle région. Pour en savoir plus sur les données d’objectif de point et de délai de récupération, voir [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).

Lorsque vous utilisez des groupes de basculement automatique avec une stratégie de basculement automatique, toute panne qui affecte les bases de données sur le serveur SQL Database ou l’instance gérée donne lieu à un basculement automatique. Vous pouvez gérer le groupe de basculement automatique à l’aide des méthodes suivantes :

- [Portail Azure](sql-database-implement-geo-distributed-database.md)
- [PowerShell : Groupe de basculement](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [API REST : Groupe de basculement](https://docs.microsoft.com/rest/api/sql/failovergroups).

Après le basculement, assurez-vous que les exigences d’authentification de votre serveur et de votre base de données sont configurées sur la nouvelle base de données primaire. Pour plus d’informations, consultez [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md).

Pour assurer vraiment la continuité des activités, l’ajout d’une redondance de base de données entre les centres de données n’est qu’une partie de la solution. La récupération d’une application (service) de bout en bout après une défaillance catastrophique implique la récupération de tous les composants constituant le service et tous les services dépendants. En voici quelques exemples : logiciel client (il peut s’agir par exemple d’un navigateur avec un code JavaScript personnalisé), serveurs web frontaux, ressources de stockage et DNS. Il est essentiel que tous les composants résistent aux mêmes défaillances et redeviennent disponibles dans l’objectif de délai de récupération (RTO) de votre application. Par conséquent, vous devez identifier tous les services dépendants et comprendre les garanties et les fonctionnalités qu’ils fournissent. Ensuite, vous devez prendre les mesures appropriées pour vous assurer que votre service fonctionne pendant le basculement des services dont il dépend. Pour plus d’informations sur la conception de solutions pour la récupération d’urgence, consultez la page [Designing Cloud Solutions for Disaster Recovery Using active geo-replication](sql-database-designing-cloud-solutions-for-disaster-recovery.md) (Conception de solutions cloud pour la récupération d’urgence à l’aide de la géo-réplication active).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Terminologie et fonctionnalités relatives aux groupes de basculement automatique

- **Groupe de basculement (FOG)**

  Un groupe de basculement est un groupe nommé de bases de données gérées par un même serveur SQL Database ou dans une même instance gérée, et qui peut basculer entièrement vers une autre région lorsqu’une partie ou la totalité des bases de données primaires devient indisponible en raison d’une panne dans la région primaire. Lorsqu’il est créé dans le cadre d’instances gérées, un groupe de basculement contient toutes les bases de données utilisateur de l’instance. Ainsi, il n’est possible de configurer qu’un seul groupe de basculement par instance.
  
  > [!IMPORTANT]
  > Le nom du groupe de basculement doit être unique à l’échelle globale dans le domaine `.database.windows.net`.

- **Serveurs SQL Database**

     Avec les serveurs SQL Database, une partie ou la totalité des bases de données utilisateur d’un même serveur SQL Database peut être placée dans un groupe de basculement. En outre, un seul et même serveur SQL Database prend en charge plusieurs groupes de basculement.

- **Primaire**

  Serveur SQL Database ou instance gérée qui héberge les bases de données primaires dans le groupe de basculement.

- **Secondaire**

  Serveur SQL Database ou instance gérée qui héberge les bases de données secondaires dans le groupe de basculement. Le serveur logique secondaire ne peut pas être situé dans la même région que le serveur logique primaire.

- **Ajouter des bases de données uniques au groupe de basculement**

  Vous pouvez placer plusieurs bases de données uniques sur le même serveur SQL Database dans le même groupe de basculement. Si vous ajoutez une base de données unique au groupe de basculement, une base de données secondaire de la même édition et de la même taille de calcul est automatiquement créée sur le serveur secondaire  que vous avez spécifié à la création du groupe de basculement. Si vous ajoutez une base de données qui présente déjà une base de données secondaire dans le serveur secondaire, ce lien de géoréplication est hérité par le groupe. Lors de l’ajout d’une base de données qui présente déjà une base de données secondaire sur un serveur qui ne fait pas partie du groupe de basculement, une nouvelle base de données secondaire est créée sur le serveur secondaire.

  > [!IMPORTANT]
  > Assurez-vous que le serveur secondaire ne dispose pas d’une base de données portant le même nom, sauf s’il s’agit d’une base de données secondaire existante. Dans les groupes de basculement d’une instance gérée, toutes les bases de données utilisateur sont répliquées. Vous ne pouvez pas choisir un sous-ensemble de bases de données utilisateur pour la réplication dans le groupe de basculement.

- **Ajouter des bases de données d’un pool élastique au groupe de basculement**

  Vous pouvez placer une partie ou la totalité des bases de données d’un pool élastique dans le même groupe de basculement. Si la base de données primaire se trouve dans un pool élastique, la base de données secondaire est automatiquement créée dans le pool élastique du même nom (pool secondaire). Vérifiez que le serveur secondaire contient un pool élastique portant exactement le même nom et offrant une capacité disponible suffisante pour héberger les bases de données secondaires qui seront créées par le groupe de basculement. Si vous ajoutez une base de données dans le pool qui présente déjà une base de données secondaire dans le pool secondaire, ce lien de géoréplication est hérité par le groupe. Si vous ajoutez une base de données qui présente déjà une base de données secondaire sur un serveur ne faisant pas partie du groupe de basculement, une nouvelle base de données secondaire est créée dans le pool secondaire.
  
- **Zone DNS**

  ID unique qui est généré automatiquement lorsqu’une instance est créée. Un certificat multidomaine (SAN) est approvisionné pour cette instance afin d’authentifier les connexions clientes effectuées auprès de toutes les instances présentes dans la même zone DNS. Les deux instances gérées d’un même groupe de basculement doivent partager la zone DNS.
  
  > [!NOTE]
  > Il n’est pas nécessaire de disposer d’un ID de zone DNS pour les groupes de basculement créés sur des serveurs SQL Database.

- **Écouteur en lecture-écriture du groupe de basculement**

  Un enregistrement DNS CNAME qui pointe vers l’URL du serveur primaire actuel. Il est créé automatiquement lorsque le groupe de basculement est créé et permet à la charge de travail SQL en lecture-écriture de se reconnecter en toute transparence à la base de données primaire lorsqu’elle est modifiée après le basculement. Lorsque le groupe de basculement est créé sur un serveur SQL Database, l’enregistrement DNS CNAME pour l’URL de l’écouteur est formé comme suit : `<fog-name>.database.windows.net`. Lorsque le groupe de basculement est créé sur une instance gérée, l’enregistrement DNS CNAME pour l’URL de l’écouteur est formé comme suit : `<fog-name>.zone_id.database.windows.net`.

- **Écouteur en lecture seule du groupe de basculement**

  Enregistrement DNS CNAME formé pour l’écouteur en lecture seule pointant vers l’URL du serveur secondaire. Il est créé automatiquement lorsque le groupe de basculement est créé et permet à la charge de travail SQL en lecture seule de se connecter en toute transparence à la base de données secondaire à l’aide des règles d’équilibrage de charge spécifiées. Lorsque le groupe de basculement est créé sur un serveur SQL Database, l’enregistrement DNS CNAME pour l’URL de l’écouteur est formé comme suit : `<fog-name>.secondary.database.windows.net`. Lorsque le groupe de basculement est créé sur une instance gérée, l’enregistrement DNS CNAME pour l’URL de l’écouteur est formé comme suit : `<fog-name>.zone_id.secondary.database.windows.net`.

- **Stratégie de basculement automatique**

  Par défaut, un groupe de basculement est configuré avec une stratégie de basculement automatique. Le service SQL Database déclenche le basculement dès que la défaillance est détectée et que la période de grâce a expiré. Le système doit vérifier que la panne ne peut pas être atténuée par l’[infrastructure de haute disponibilité intégrée du service SQL Database](sql-database-high-availability.md) en raison de l’échelle de l’impact. Si vous souhaitez contrôler le flux de travail de basculement à partir de l’application, vous pouvez désactiver le basculement automatique.
  
  > [!NOTE]
  > Compte tenu du fait que la vérification de l’étendue de la panne et que la rapidité avec laquelle elle peut être atténuée impliquent des actions humaines de la part de l’équipe des opérations, la période de grâce ne peut pas être fixée en dessous d’une heure.  Cette limitation s’applique à toutes les bases de données du groupe de basculement, quel que soit l’état de synchronisation des données. 

- **Stratégie de basculement en lecture seule**

  Par défaut, le basculement de l’écouteur en lecture seule est désactivé. Il garantit que les performances du serveur principal ne sont pas affectées lorsque le serveur secondaire est hors connexion. Toutefois, cela signifie également que les sessions en lecture seule ne seront pas en mesure de se connecter tant que le serveur secondaire n’aura pas été récupérée. Si vous ne pouvez pas tolérer des temps d’arrêt pour les sessions en lecture seule et si vous acceptez d’utiliser temporairement le serveur principal pour le trafic en lecture seule et en lecture-écriture au prix d’une dégradation potentielle des performances du serveur principal, vous pouvez activer le basculement pour l’écouteur en lecture seule en configurant la propriété `AllowReadOnlyFailoverToPrimary`. Dans ce cas, le trafic en lecture seule est automatiquement redirigé vers le serveur principal si le serveur secondaire est indisponible.

- **Basculement planifié**

   Le basculement planifié effectue une synchronisation complète entre les bases de données primaire et secondaire avant que la seconde ne bascule dans le rôle primaire. Cela empêche toute perte de données. Le basculement planifié des appareils est utilisé dans les scénarios suivants :

  - Simuler des récupérations d’urgence en production lorsque la perte de données n’est pas acceptable
  - Déplacer les bases de données vers une autre région
  - Renvoyer les bases de données vers la région primaire une fois la panne éliminée (restauration automatique).

- **Basculement non planifié**

   Un basculement non planifié ou forcé fait immédiatement basculer la base de données secondaire vers le rôle primaire sans synchronisation avec la base de données primaire. Cette opération entraîne une perte de données. Le basculement non planifié est utilisé comme méthode de récupération pendant les pannes, lorsque la base de données primaire n’est pas accessible. Lorsque la base de données primaire d’origine est de nouveau en ligne, elle se reconnecte sans synchronisation et devient une nouvelle base de données secondaire.

- **Basculement manuel**

  Vous pouvez lancer manuellement le basculement à tout moment, quelle que soit la configuration du basculement automatique. Si la stratégie de basculement automatique n’est pas configurée, un basculement manuel est nécessaire pour récupérer les bases de données dans le groupe de basculement vers le serveur secondaire. Vous pouvez lancer un basculement forcé ou convivial (avec synchronisation complète des données). Ce dernier peut être utilisé pour déplacer le serveur primaire dans la région secondaire. Une fois le basculement terminé, les enregistrements DNS sont automatiquement mis à jour pour garantir la connexion au nouveau serveur primaire

- **Période de grâce avec perte de données**

  Comme les bases de données primaires et secondaires sont synchronisées avec la réplication asynchrone, le basculement peut entraîner une perte de données. Vous pouvez personnaliser la stratégie de basculement automatique en fonction de la tolérance de votre application aux pertes de données. En configurant `GracePeriodWithDataLossHours`, vous pouvez contrôler le délai observé par le système avant d’initialiser le basculement qui est susceptible d’entraîner une perte de données.

- **Plusieurs groupes de basculement**

  Vous pouvez configurer plusieurs groupes de basculement pour la même paire de serveurs afin de contrôler l’échelle des basculements. Chaque groupe bascule indépendamment. Si votre application mutualisée fait appel à des pools élastiques, vous pouvez utiliser cette fonctionnalité pour combiner des bases de données primaires et secondaires dans chaque pool. De cette manière, vous pouvez réduire l’impact d’une panne à la moitié seulement des locataires.

  > [!NOTE]
  > Managed Instance ne prend pas en charge les groupes de basculement multiples.
  
## <a name="permissions"></a>Autorisations

Les autorisations pour un groupe de basculement sont gérées via un [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md). Le rôle [Contributeur SQL Server](../role-based-access-control/built-in-roles.md#sql-server-contributor) dispose des autorisations nécessaires pour gérer des groupes de basculement.

### <a name="create-failover-group"></a>Créer un groupe de basculement

Pour créer un groupe de basculement, vous devez disposer d’un accès en écriture RBAC aux serveurs principal et secondaire ainsi qu’à toutes les bases de données du groupe de basculement. Pour une instance gérée, vous devez disposer d’un accès en écriture RBAC aux instances gérées principale et secondaire. Toutefois, les autorisations sur les bases de données individuelles ne sont pas pertinentes dans la mesure où les bases de données d’instance gérée individuelles ne peuvent pas être ajoutées ou supprimées d’un groupe de basculement. 

### <a name="update-a-failover-group"></a>Mettre à jour un groupe de basculement

Pour mettre à jour un groupe de basculement, vous devez disposer d’un accès en écriture RBAC au groupe de basculement et à toutes les bases de données du serveur principal ou de l’instance gérée actuels.  

### <a name="failover-a-failover-group"></a>Faire basculer un groupe de basculement

Pour faire basculer un groupe de basculement, vous devez disposer d’un accès en écriture RBAC au groupe de basculement sur le nouveau serveur principal ou la nouvelle instance gérée.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Meilleures pratiques relatives à l’utilisation des groupes de basculement avec des bases de données uniques et des pools élastiques

Le groupe de basculement automatique doit être configuré sur le serveur SQL Database primaire, qu’il connectera au serveur SQL Database secondaire dans une autre région Azure. Les groupes peuvent inclure une partie ou la totalité des bases de données dans ces serveurs. Le diagramme suivant illustre la configuration standard d’une application cloud géoredondante avec plusieurs bases de données et un groupe de basculement automatique.

![basculement automatique](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> Consultez [Ajouter une base de données unique à un groupe de basculement](sql-database-single-database-failover-group-tutorial.md) pour obtenir un didacticiel détaillé sur l’ajout d’une base de données unique à un groupe de basculement.

Quand vous concevez un service en pensant à la continuité d’activité, suivez ces instructions générales :

- **Utiliser un ou plusieurs groupes de basculement pour gérer le basculement de plusieurs bases de données**

  Un ou plusieurs groupes de basculement peuvent être créés entre deux serveurs situés dans des régions différentes (serveurs principal et serveur secondaire). Chaque groupe peut inclure une ou plusieurs bases de données qui sont récupérées ensemble dans le cas où une partie ou la totalité des bases de données primaires deviennent indisponibles en raison d’une panne dans la région principale. Le groupe de basculement crée une base de données géo-secondaire avec le même objectif de service que la base de données primaire. Si vous ajoutez une relation de géoréplication existante au groupe de basculement, vérifiez que la base de données géosecondaire est configurée avec le même niveau de service et la même taille de calcul que la base de données primaire.
  
  > [!IMPORTANT]
  > La création de groupes de basculement entre deux serveurs dans différents abonnements n’est actuellement pas prise en charge pour les bases de données uniques et les pools élastiques. Le fait de déplacer le serveur principal ou secondaire vers un autre abonnement après la création du groupe de basculement peut entraîner des défaillances au niveau des requêtes de basculement et d’autres opérations.

- **Utiliser un écouteur en lecture-écriture pour la charge de travail OLTP**

  Quand vous effectuez des opérations OLTP, utilisez `<fog-name>.database.windows.net`, car l’URL du serveur et les connexions sont automatiquement dirigées vers le serveur principal. L’URL ne change pas après le basculement. Notez que le basculement implique la mise à jour de l’enregistrement DNS de façon à ce que les connexions clients soient redirigées vers le nouveau serveur primaire seulement après l’actualisation du cache DNS.

- **Utiliser un écouteur en lecture seule pour une charge de travail en lecture seule**

  Si vous avez une charge de travail en lecture seule isolée logiquement et qui est tolérante à une certaine obsolescence des données, vous pouvez utiliser la base de données secondaire dans l’application. Pour les sessions en lecture seule, utilisez `<fog-name>.secondary.database.windows.net`, car l’URL du serveur et la connexion sont automatiquement dirigées vers le serveur principal. Il est également recommandé d’indiquer dans la tentative de lecture de la chaîne de connexion à l’aide de `ApplicationIntent=ReadOnly`. Si vous souhaitez vous assurer que la charge de travail en lecture seule peut se reconnecter après le basculement ou si le serveur secondaire est mis hors connexion, veillez à configurer la propriété `AllowReadOnlyFailoverToPrimary` de la stratégie de basculement.

- **Se préparer à une dégradation des performances**

  La décision de basculement de SQL est indépendante du reste de l’application ou des autres services utilisés. L’application peut être mélangée à certains composants dans une région et d’autres composants dans une région différente. Afin d’éviter la dégradation, assurez le déploiement redondant d’applications dans la région de récupération d’urgence et suivez ces [instructions sur la sécurité réseau](#failover-groups-and-network-security).

  > [!NOTE]
  > Il n’est pas obligatoire pour l’application dans la région de récupération d’urgence d’utiliser une chaîne de connexion différente.  

- **Se préparer à une perte de données**

  Si une panne est détectée, SQL attend la période que vous avez spécifiée avec `GracePeriodWithDataLossHours`. La valeur par défaut est 1 heure. Si vous ne pouvez pas vous permettre de perdre des données, veillez à définir dans la commande `GracePeriodWithDataLossHours` un nombre suffisamment grand, par exemple, 24 heures. Utilisez le basculement de groupe manuel pour effectuer une restauration automatique du serveur secondaire au serveur primaire.

  > [!IMPORTANT]
  > Les pools élastiques disposant de 800 DTU au maximum et de plus de 250 bases de données utilisant la géoréplication peuvent rencontrer des problèmes, notamment des basculements planifiés plus longs et une dégradation des performances.  Ces problèmes sont davantage susceptibles de se produire pour les charges de travail intensives en écriture, quand les points de terminaison de géoréplication sont géographiquement très éloignés, ou quand plusieurs points de terminaison secondaires sont utilisés pour chaque base de données.  Les symptômes de ces problèmes sont signalés quand le décalage de la géoréplication augmente au fil du temps.  Ce décalage peut être surveillé avec [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Si ces problèmes se produisent, l’atténuation des risques inclut l’augmentation du nombre de DTU du pool ou la réduction du nombre de bases de données géorépliquées dans ce même pool.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Meilleures pratiques relatives à l’utilisation de groupes de basculement avec les instances managées

Le groupe de basculement automatique doit être configuré sur l’instance primaire, qu’il connectera à l’instance secondaire dans une autre région Azure.  Toutes les bases de données de l’instance seront répliquées sur l’instance secondaire.

Le diagramme suivant illustre la configuration standard d’une application cloud géoredondante avec une instance managée et un groupe de basculement automatique.

![basculement automatique](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> Consultez [Ajouter une instance gérée à un groupe de basculement](sql-database-managed-instance-failover-group-tutorial.md) pour obtenir un didacticiel détaillé sur l’ajout d’une instance gérée afin d’utiliser un groupe de basculement.

Si votre application utilise une instance gérée comme couche Données, suivez ces recommandations générales lors de la conception des éléments en rapport avec la continuité d’activité :

- **Créer l’instance secondaire dans la même zone DNS que l’instance principale**

  Pour garantir une connectivité ininterrompue à l’instance principale après le basculement automatique, les instances principale et secondaire doivent se trouver dans la même zone DNS. Cela garantit que le même certificat multidomaine (SAN) peut être utilisé pour authentifier les connexions clientes avec les deux instances du groupe de basculement. Lorsque votre application est prête pour le déploiement en production, créez une instance secondaire dans une autre région et assurez-vous qu’elle partage la même zone DNS que l’instance principale. Pour cela, vous pouvez spécifier un paramètre `DNS Zone Partner` facultatif à l’aide du portail Azure, de PowerShell ou de l’API REST.

> [!IMPORTANT]
> La première instance créée dans le sous-réseau détermine la zone DNS pour toutes les instances suivantes de ce même sous-réseau. Cela signifie que deux instances d'un même sous-réseau ne peuvent pas appartenir à des zones DNS différentes.

  Pour plus d’informations sur la création de l’instance secondaire dans la même zone DNS que l’instance principale, consultez [Créer une instance managée secondaire](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

- **Activer le trafic de réplication entre deux instances**

  Étant donné que chaque instance est isolée dans son propre réseau virtuel, le trafic bidirectionnel entre ces réseaux virtuels doit être autorisé. Voir [Passerelle VPN Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **Créer un groupe de basculement entre des instances gérées dans différents abonnements**

  Vous pouvez créer un groupe de basculement entre des instances gérées dans deux abonnements différents. Lorsque vous utilisez l’API PowerShell, vous pouvez le faire en spécifiant le paramètre `PartnerSubscriptionId` pour l’instance secondaire. Lors de l’utilisation de l’API REST, chaque ID d’instance inclus dans le paramètre `properties.managedInstancePairs` peut avoir son propre subscriptionID.
  
  > [!IMPORTANT]
  > Le portail Azure ne prend pas en charge les groupes de basculement sur différents abonnements.

- **Configurer un groupe de basculement pour gérer le basculement de l’intégralité de l’instance**

  Le groupe de basculement gère le basculement de toutes les bases de données dans l’instance. Lorsqu’un groupe est créé, chaque base de données dans l’instance est automatiquement géorépliquée sur l’instance secondaire. Vous ne pouvez pas utiliser les groupes de basculement pour initier le basculement partiel d’un sous-ensemble de bases de données.

  > [!IMPORTANT]
  > Si une base de données est supprimée de l’instance principale, elle est également supprimée automatiquement sur l’instance géorépliquée secondaire.

- **Utiliser un écouteur en lecture-écriture pour la charge de travail OLTP**

  Quand vous effectuez des opérations OLTP, utilisez `<fog-name>.zone_id.database.windows.net`, car l’URL du serveur et les connexions sont automatiquement dirigées vers le serveur principal. L’URL ne change pas après le basculement. Le basculement implique la mise à jour de l’enregistrement DNS de façon à ce que les connexions clients soient redirigées vers le nouveau serveur primaire seulement après l’actualisation du cache DNS. Étant donné que l’instance secondaire partage la même zone DNS que l’instance principale, l’application cliente peut se reconnecter à l’aide du même certificat SAN.

- **Se connecter directement à l’instance géorépliquée secondaire pour les requêtes en lecture seule**

  Si vous avez une charge de travail en lecture seule isolée logiquement et qui est tolérante à une certaine obsolescence des données, vous pouvez utiliser la base de données secondaire dans l’application. Pour vous connecter directement à l’instance géorépliquée secondaire, utilisez `server.secondary.zone_id.database.windows.net`, car l’URL du serveur et la connexion sont automatiquement dirigées vers l’instance géorépliquée secondaire.

  > [!NOTE]
  > Dans certains niveaux de service, Azure SQL Database prend en charge l’utilisation de [réplicas en lecture seule](sql-database-read-scale-out.md) pour équilibrer les charges de travail de requêtes en lecture seule en utilisant la capacité d’un réplica en lecture seule et le paramètre `ApplicationIntent=ReadOnly` dans la chaîne de connexion. Lorsque vous avez configuré une instance géorépliquée secondaire, vous pouvez utiliser cette fonction pour vous connecter à un réplica en lecture seule à l’emplacement primaire ou à l’emplacement géorépliqué.
  > - Pour vous connecter à un réplica en lecture seule à l’emplacement primaire, utilisez `<fog-name>.zone_id.database.windows.net`.
  > - Pour vous connecter à un réplica en lecture seule à l’emplacement secondaire, utilisez `<fog-name>.secondary.zone_id.database.windows.net`.

- **Se préparer à une dégradation des performances**

  La décision de basculement de SQL est indépendante du reste de l’application ou des autres services utilisés. L’application peut être mélangée à certains composants dans une région et d’autres composants dans une région différente. Afin d’éviter la dégradation, assurez le déploiement redondant d’applications dans la région de récupération d’urgence et suivez ces [instructions sur la sécurité réseau](#failover-groups-and-network-security).

- **Se préparer à une perte de données**

  Si une panne est détectée, SQL déclenche automatiquement le basculement en lecture-écriture dans le cas où il n’y a à notre connaissance pas de perte de données. Sinon, SQL patiente pendant le temps que vous avez défini via `GracePeriodWithDataLossHours`. Si vous avez spécifié `GracePeriodWithDataLossHours`, attendez-vous à une perte de données. En général, en cas de panne, Azure favorise la disponibilité. Si vous ne pouvez pas vous permettre de perdre des données, veillez à définir dans la commande GracePeriodWithDataLossHours un nombre suffisamment grand, par exemple, 24 heures.

  La mise à jour DNS de l’écouteur en lecture-écriture se produit immédiatement après que le basculement est initié. Cette opération n’entraîne aucune perte de données. Toutefois, le processus de basculement des rôles des bases de données peut prendre jusqu’à 5 minutes dans des conditions normales. En attendant, certaines bases de données de la nouvelle instance principale resteront en lecture seule. Si le basculement est initié à l’aide de PowerShell, l’intégralité de l’opération est effectuée de manière synchrone. S’il est initié à l’aide du portail Azure, l’interface utilisateur indiquera la progression. S’il est démarré à l’aide de l’API REST, utilisez le mécanisme d’interrogation standard d’Azure Resource Manager pour en surveiller la progression.

  > [!IMPORTANT]
  > Utilisez le basculement de groupe manuel pour redéplacer les bases de données primaires à leur emplacement d’origine. Lorsque la panne ayant provoqué le basculement est résolue, vous pouvez déplacer vos bases de données primaires vers leur emplacement d’origine. Pour ce faire, vous devez effectuer le basculement manuel du groupe.

- **Reconnaître les limites connues des groupes de basculement**

  Le renommage d’une base de données n’est pas pris en charge pour les instances situées dans un groupe de basculement. Vous devez supprimer temporairement le groupe de basculement pour pouvoir renommer une base de données.

## <a name="failover-groups-and-network-security"></a>Groupes de basculement et sécurité réseau

Pour certaines applications, les règles de sécurité nécessitent que l’accès réseau à la couche Données soit limité à un ou plusieurs composants comme une machine virtuelle, un service web, etc. Cette exigence présente quelques défis pour la conception de la continuité d’activité et l’utilisation des groupes de basculement. Tenez compte des options suivantes lors de l’implémentation d’un accès restreint.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Utilisation de groupes de basculement et de règles de réseau virtuel

Si vous utilisez des [règles et points de terminaison de service de réseau virtuel](sql-database-vnet-service-endpoint-rule-overview.md) pour restreindre l’accès à votre base de données SQL, n’oubliez pas que chaque point de terminaison de service de réseau virtuel s’applique à une seule région Azure. Le point de terminaison ne permet pas à d’autres régions d’accepter les communications provenant du sous-réseau. Par conséquent, seules les applications client déployées dans la même région peuvent se connecter à la base de données primaire. Comme le basculement amène les sessions du client SQL à être redirigées vers un serveur dans une autre région (secondaire), ces sessions échouent si elles proviennent d’un client en dehors de cette région. Pour cette raison, la stratégie de basculement automatique ne peut pas être activée si les serveurs participants sont inclus dans les règles de réseau virtuel. Pour prendre en charge le basculement manuel, effectuez les étapes suivantes :

1. Provisionnez les copies redondantes des composants front-end de votre application (service web, machines virtuelles, etc.) dans la région secondaire
2. Configurez les [règles de réseau virtuel](sql-database-vnet-service-endpoint-rule-overview.md) individuellement pour les serveurs primaire et secondaire
3. Activez le [basculement frontend à l’aide d’une configuration Traffic Manager](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Lancez un basculement manuel quand la panne est détectée. Cette option est optimisée pour les applications qui requièrent une latence constante entre le frontend et la couche Données, et prend en charge la récupération quand le frontend et/ou la couche Données sont affectés par la panne.

> [!NOTE]
> Si vous utilisez **l’écouteur en lecture seule** pour équilibrer une charge de travail en lecture seule, vérifiez que cette charge de travail est exécutée dans une machine virtuelle ou une autre ressource dans la région secondaire pour qu’elle puisse se connecter à la base de données secondaire.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Utilisation de groupes de basculement et de règles de pare-feu de base de données SQL

Si votre plan de continuité d’activité nécessite un basculement à l’aide de groupes avec basculement automatique, vous pouvez restreindre l’accès à votre base de données SQL à l’aide des règles de pare-feu classiques. Pour prendre en charge le basculement automatique, effectuez les étapes suivantes :

1. [Créez une adresse IP publique](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address).
2. [Créez un équilibreur de charge public](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) et affectez-lui l’adresse IP publique.
3. [Créez un réseau virtuel et les machines virtuelles](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) pour vos composants frontend.
4. [Créez un groupe de sécurité réseau](../virtual-network/security-overview.md) et configurez les connexions entrantes.
5. Vérifiez que les connexions sortantes sont ouvertes pour la base de données Azure SQL à l’aide de la [balise de service](../virtual-network/security-overview.md#service-tags) « Sql ».
6. Créez une [règle de pare-feu de base de données SQL](sql-database-firewall-configure.md) pour autoriser le trafic entrant à partir de l’adresse IP publique que vous créez à l’étape 1.

Pour plus d’informations sur la configuration de l’accès sortant et l’adresse IP à utiliser dans les règles de pare-feu, consultez [Connexions sortantes de l’équilibreur de charge](../load-balancer/load-balancer-outbound-connections.md).

La configuration ci-dessus garantit que le basculement automatique ne bloque pas les connexions à partir des composants frontend et part du principe que l’application peut tolérer la plus longue latence entre le frontend et la couche Données.

> [!IMPORTANT]
> Pour garantir la continuité d’activité en cas de pannes régionales, vous devez vérifier la redondance géographique pour les composants frontend et les bases de données.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Activation de la géoréplication entre les instances gérées et leurs réseaux virtuels

Lorsque vous configurez des groupes de basculement entre les instances gérées primaire et secondaire dans deux régions différentes, chaque instance est isolée et utilise un réseau virtuel indépendant. Pour autoriser le trafic de réplication entre ces réseaux virtuels, vérifiez que les conditions préalables suivantes sont satisfaites :

1. Les deux instances gérées doivent se trouver dans différentes régions Azure.
1. Les deux instances gérées doivent avoir le même niveau de service et la même capacité de stockage.
1. Votre instance gérée secondaire doit être vide (aucune base de données utilisateur).
1. Les réseaux virtuels utilisés par les instances gérées doivent être connectés via une[passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou Express Route. Lorsque deux réseaux virtuels se connectent via un réseau local, assurez-vous qu’il n’existe pas de ports de blocage de règle de pare-feu 5022 et 11000-11999. L’homologation Global VNet Peering n’est pas prise en charge.
1. Les adresses IP des deux réseaux virtuels des instances gérées ne peuvent pas se chevaucher.
1. Vous devez configurer vos groupes de sécurité réseau (NSG) de telle sorte que les ports 5022 et 11000 à 12000 soient ouverts en entrée et en sortie pour les connexions provenant du sous-réseau de l’autre instance gérée. Il s’agit d’autoriser le trafic de réplication entre les instances

   > [!IMPORTANT]
   > La configuration incorrecte des règles de groupes de sécurité réseau bloque les opérations de copie sur les bases de données.

1. L’instance secondaire est configurée avec l’ID de zone DNS approprié. La zone DNS est une propriété d’une instance managée et d’un cluster virtuel, et son ID est inclus dans l’adresse du nom d’hôte. L’ID de zone est généré sous forme de chaîne aléatoire lors de la création de la première instance gérée de chaque réseau virtuel. De plus, le même ID est attribué à toutes les autres instances dans le même sous-réseau. Une fois attribué, la zone DNS ne peut pas être modifiée. Les instances gérées d’un même groupe de basculement doivent partager la zone DNS. Pour cela, vous devez transmettre l’ID de zone de l’instance principale en tant que valeur du paramètre DnsZonePartner lors de la création de l’instance secondaire. 

   > [!NOTE]
   > Pour obtenir un tutoriel détaillé sur la configuration des groupes de basculement avec instance gérée, consultez [Ajouter une instance gérée à un groupe de basculement](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Mise à niveau ou rétrogradation d’une base de données primaire

Vous pouvez augmenter ou diminuer la taille de calcul d’une base de données primaire (au sein du même niveau de service, mais pas entre les niveaux Usage général et Critique pour l’entreprise) sans déconnecter les bases de données secondaires. Lors d’une mise à niveau, nous vous recommandons de mettre à niveau toutes les bases de données secondaires dans un premier temps, avant de mettre à niveau la base de données primaire. Lors du passage à une version antérieure, inversez l’ordre : faites tout d’abord passer la base de données primaire à une version antérieure, puis toutes les bases de données secondaires dans un second temps. Lorsque vous passez la base de données à un niveau de service supérieur ou inférieur, cette recommandation est appliquée.

Cette séquence est recommandée dans le but spécifique d’éviter le problème de surcharge des bases de données secondaires avec une référence SKU inférieure. Celles-ci doivent alors être alimentées à nouveau lors de la mise à niveau ou du passage à une version antérieure. Vous pouvez également éviter le problème en attribuant un accès en lecture seule à la base de données primaire, ce qui peut cependant nuire à toutes les charges de travail en lecture-écriture qui y sont associées.

> [!NOTE]
> Si vous avez créé une base de données secondaire dans le cadre de la configuration des groupes de basculement, il n’est pas conseillé de passer la base de données secondaire à un niveau de service inférieur. En effet, votre couche Données pourrait manquer de capacité pour traiter votre charge de travail normale après l’activation du basculement.

## <a name="preventing-the-loss-of-critical-data"></a>Prévention de la perte de données critiques

En raison de la latence élevée des réseaux étendus, la copie continue utilise un mécanisme de réplication asynchrone. La perte de certaines données reste donc inévitable en cas de défaillance. Or, pour certaines applications, une perte de données est inacceptable. Pour protéger ces mises à jour critiques, un développeur d’applications peut appeler la procédure système [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) immédiatement après la validation de la transaction. L’appel de `sp_wait_for_database_copy_sync` bloque le thread appelant jusqu’à ce que la dernière transaction validée ait été transmise à la base de données secondaire. Toutefois, il n’attend pas que les transactions transmises soient relues et validées sur la base de données secondaire. `sp_wait_for_database_copy_sync` est limité à un lien de copie continue spécifique. Tout utilisateur disposant de droits de connexion à la base de données primaire peut appeler cette procédure.

> [!NOTE]
> `sp_wait_for_database_copy_sync` empêche la perte de données après un basculement, mais il ne garantit pas la synchronisation complète pour l’accès en lecture. Le délai causé par un appel de procédure `sp_wait_for_database_copy_sync` peut être significatif et dépend de la taille du journal des transactions au moment de l’appel.

## <a name="failover-groups-and-point-in-time-restore"></a>Groupes de basculement et limite de restauration dans le temps

Pour plus d’informations sur l’utilisation de la limite de restauration dans le temps avec les groupes de basculement, voir [Limite de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="programmatically-managing-failover-groups"></a>Gestion par programmation des groupes de basculement

Comme indiqué plus haut, les groupes de basculement automatique et la géo-réplication active peuvent aussi être gérés par programme à l’aide d’Azure PowerShell et de l’API REST. Les tableaux ci-dessous décrivent l’ensemble des commandes disponibles. La géoréplication active comprend un ensemble d’API Azure Resource Manager pour la gestion, notamment [l’API REST Azure SQL Database](https://docs.microsoft.com/rest/api/sql/) et les [applets de commande Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Ces API nécessitent l’utilisation de groupes de ressources et la prise en charge de la sécurité basée sur les rôles (RBAC). Pour plus d’informations sur l’implémentation de rôles d’accès, consultez la page sur le [contrôle d’accès en fonction du rôle Azure](../role-based-access-control/overview.md).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Gérer le basculement des bases de données SQL avec des bases de données uniques et des pools élastiques

| Applet de commande | Description |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Cette commande crée un groupe de basculement et l’enregistre dans les serveurs primaire et secondaire|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Supprime le groupe de basculement du serveur et efface toutes les bases de données secondaires incluses dans le groupe. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Récupère la configuration du groupe de basculement. |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modifie la configuration du groupe de basculement. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Déclenche le basculement du groupe de basculement vers le serveur secondaire. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Ajoute une ou plusieurs bases de données à un groupe de basculement Azure SQL Database|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Gérer des groupes de basculement de base de données SQL avec des instances gérées

| Applet de commande | Description |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Cette commande crée un groupe de basculement et l’enregistre dans les serveurs primaire et secondaire|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Modifie la configuration du groupe de basculement.|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Récupère la configuration du groupe de basculement.|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Déclenche le basculement du groupe de basculement vers le serveur secondaire.|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Supprime un groupe de basculement|

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Gérer le basculement des bases de données SQL avec des bases de données uniques et des pools élastiques

| Commande | Description |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Cette commande crée un groupe de basculement et l’enregistre dans les serveurs primaire et secondaire|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Supprime le groupe de basculement du serveur et efface toutes les bases de données secondaires incluses dans le groupe. |
| [az sql failover-group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Récupère la configuration du groupe de basculement. |
| [az sql failover-group update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Modifie la configuration du groupe de basculement et/ou ajoute une ou plusieurs bases de données à un groupe de basculement Azure SQL Database|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Déclenche le basculement du groupe de basculement vers le serveur secondaire. |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Gérer des groupes de basculement de base de données SQL avec des instances gérées

| Commande | Description |
| --- | --- |
| [az sql instance-failover-group create](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | Cette commande crée un groupe de basculement et l’enregistre dans les serveurs primaire et secondaire|
| [az sql instance-failover-group update](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | Modifie la configuration du groupe de basculement.|
| [az sql instance-failover-group show](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | Récupère la configuration du groupe de basculement.|
| [az sql instance-failover-group set-primary](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | Déclenche le basculement du groupe de basculement vers le serveur secondaire.|
| [az sql instance-failover-group delete](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | Supprime un groupe de basculement |

* * *

> [!IMPORTANT]
> Pour un exemple de script, voir [Configurer et basculer un groupe de basculement pour une base de données unique](scripts/sql-database-add-single-db-to-failover-group-powershell.md).

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>API REST : Gérer les groupes de basculement de base de données SQL avec des bases de données uniques et mises en pool

| API | Description |
| --- | --- |
| [Créer ou mettre à jour un groupe de basculement](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Crée ou met à jour un groupe de basculement |
| [Supprimer un groupe de basculement](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Supprime un groupe de basculement du serveur |
| [Basculement (planifié)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Bascule du serveur principal actuel vers ce serveur. |
| [Forcer le basculement et autoriser la perte de données](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |Bascule du serveur principal actuel vers ce serveur. Cette opération peut entraîner une perte de données. |
| [Get Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/get) (Obtenir un groupe de basculement) | Obtient un groupe de basculement. |
| [Répertorier les groupes de basculement par serveur](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Répertorie les groupes de basculement d’un serveur. |
| [Mettre à jour un groupe de basculement](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Met à jour un groupe de basculement. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>API REST : Gérer des groupes de basculement avec des instances managées

| API | Description |
| --- | --- |
| [Créer ou mettre à jour un groupe de basculement](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Crée ou met à jour un groupe de basculement |
| [Supprimer un groupe de basculement](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Supprime un groupe de basculement du serveur |
| [Basculement (planifié)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Bascule du serveur principal actuel vers ce serveur. |
| [Forcer le basculement et autoriser la perte de données](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |Bascule du serveur principal actuel vers ce serveur. Cette opération peut entraîner une perte de données. |
| [Get Failover Group](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) (Obtenir un groupe de basculement) | Obtient un groupe de basculement. |
| [List Failover Groups - List By Location](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) (Répertorier les groupes de basculement - Liste par emplacement) | Répertorie les groupes de basculement d’un emplacement. |

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des didacticiels détaillés, consultez
    - [Ajouter une base de données unique à un groupe de basculement](sql-database-single-database-failover-group-tutorial.md)
    - [Ajouter un pool élastique à un groupe de basculement](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Ajouter une instance gérée à un groupe de basculement](sql-database-managed-instance-failover-group-tutorial.md)
- Pour obtenir des exemples de scripts, consultez :
  - [Utiliser PowerShell afin de configurer la géoréplication active pour avoir une base de données unique dans Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Utiliser PowerShell afin de configurer la géoréplication active pour avoir une base de données mise en pool dans Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Utiliser PowerShell afin d’ajouter une base de données unique Azure SQL Database à un groupe de basculement](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
- Pour en savoir plus sur les sauvegardes automatisées d’une base de données Azure SQL, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md).
- Pour en savoir plus sur les exigences d’authentification pour de nouveaux serveurs et bases de données primaires, consultez [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md).
