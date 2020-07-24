---
title: Réplicas en lecture - Azure Database pour PostgreSQL - Serveur unique
description: Cet article décrit la fonctionnalité de réplica en lecture dans Azure Database pour PostgreSQL, avec un serveur unique.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: f2f752d6435b311c1737d531f5572aed5af223f2
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86276649"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Réplicas en lecture dans Azure Database pour PostgreSQL - Serveur unique

La fonctionnalité de réplica en lecture vous permet de répliquer les données d’un serveur Azure Database pour PostgreSQL sur un serveur en lecture seule. Vous pouvez effectuer la réplication à partir du serveur maître vers cinq réplicas au maximum. Les réplicas sont mis à jour de manière asynchrone à l’aide de la technologie de réplication native du moteur PostgreSQL.

Les réplicas sont de nouveaux serveurs que vous gérez de manière similaire aux serveurs Azure Database pour PostgreSQL classiques. Pour chaque réplica en lecture, vous êtes facturé en fonction de la capacité de calcul provisionnée dans les vCores et du stockage provisionné en Go/mois.

Découvrez comment [créer et gérer des réplicas](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Quand utiliser un réplica en lecture
La fonctionnalité de réplica en lecture contribue à améliorer les performances et la scalabilité des charges de travail nécessitant une lecture intensive. Les charges de travail de lecture peuvent être isolées sur les réplicas, tandis que les charges de travail d’écriture peuvent être dirigées vers le serveur maître.

Un scénario courant est d’avoir les charges de travail décisionnelles et analytiques qui utilisent le réplica en lecture comme source de données pour la création de rapports.

Dans la mesure où les réplicas sont en lecture seule, ils ne réduisent pas directement les charges relatives à la capacité d’écriture sur le serveur maître. Cette fonctionnalité ne cible pas les charges de travail nécessitant une écriture intensive.

La fonctionnalité de réplica en lecture utilise la réplication asynchrone PostgreSQL. La fonctionnalité n’est pas destinée aux scénarios de réplication synchrone. Il y aura un délai mesurable entre le maître et le réplica. Les données du réplica finissent par devenir cohérentes avec les données du serveur maître. Utilisez cette fonctionnalité pour les charges de travail pouvant s’adapter à ce délai.

## <a name="cross-region-replication"></a>Réplication entre régions
Vous pouvez créer un réplica en lecture dans une autre région à partir de votre serveur maître. La réplication entre régions peut être utile pour des scénarios tels que la planification de la récupération d’urgence ou le rapprochement des données de vos utilisateurs.

>[!NOTE]
> Les serveurs du niveau De base prennent uniquement en charge la réplication dans la même région.

Vous pouvez disposer d’un serveur maître dans toute [région Azure Database pour PostgreSQL](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Un serveur maître peut avoir un réplica dans sa région jumelée ou dans les régions de réplica universelles. L’image ci-dessous montre les régions de réplica disponibles en fonction de votre région maître.

[ ![Régions des réplicas en lecture](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Régions de réplica universelles
Vous pouvez toujours créer un réplica en lecture dans les régions suivantes, quel que soit l’emplacement de votre serveur maître. Les régions de réplica universelles sont les suivantes :

Australie Est, Australie Sud-Est, USA Centre, Asie Est, USA Est, USA Est 2, Japon Est, Japon Ouest, Corée Centre, Corée Sud, USA Centre Nord, Europe Nord, USA Centre Sud, Asie Sud-Est, Royaume-Uni Sud, Royaume-Uni Ouest, Europe Ouest, USA Ouest, USA Ouest 2, USA Centre-Ouest.

### <a name="paired-regions"></a>Régions jumelées
Outre les régions de réplica universelles, vous pouvez créer un réplica en lecture dans la région Azure jumelée de votre serveur maître. Si vous ne connaissez pas la région jumelée de votre région, vous trouverez plus d’informations dans l’[article sur les régions jumelées Azure](../best-practices-availability-paired-regions.md).

Si vous utilisez des réplicas entre régions pour la planification de la récupération d’urgence, nous vous recommandons de créer le réplica dans la région jumelée plutôt que dans une autre région. Les régions jumelées évitent les mises à jour simultanées et hiérarchisent l’isolement physique et la résidence des données.  

Il existe quelques limitations à prendre en compte : 

* Disponibilité régionale : Azure Database pour PostgreSQL est disponible dans les régions France Centre, Émirats arabes unis Nord et Allemagne Centre. Toutefois, leurs régions jumelées ne sont pas disponibles.
    
* Paires unidirectionnelles : Certaines régions Azure sont jumelées dans une seule direction. Ces régions incluent Inde Ouest, et Brésil Sud. 
   Cela signifie qu’un serveur maître dans la région Inde Ouest peut créer un réplica dans la région Inde Sud. Toutefois, un serveur maître dans la région Inde Sud ne peut pas créer de réplica dans la région Inde Ouest. En effet, la région secondaire de la région Inde Ouest est Inde Sud, mais la région secondaire de la région Inde Sud n’est pas Inde Ouest.


## <a name="create-a-replica"></a>Créer un réplica
Quand vous démarrez le workflow de création de réplica, un serveur Azure Database pour PostgreSQL vide est créé. Le nouveau serveur est rempli avec les données qui se trouvaient sur le serveur maître. Le temps de création dépend de la quantité de données présentes sur le serveur maître et du temps écoulé depuis la dernière sauvegarde complète hebdomadaire. Le temps nécessaire peut aller de quelques minutes à plusieurs heures.

Chaque réplica est activé pour la [croissance automatique](concepts-pricing-tiers.md#storage-auto-grow) du stockage. La fonctionnalité de croissance automatique permet au réplica de suivre les données qui sont répliquées sur lui et d’empêcher toute interruption à cause d’une erreur de saturation du stockage.

La fonctionnalité de réplica en lecture utilise la réplication physique PostgreSQL, et non la réplication logique. Le streaming de réplication à l’aide des slots de réplication est le mode de fonctionnement par défaut. Quand cela est nécessaire, la copie des journaux de transaction permet de rattraper le retard.

Découvrez comment [créer un réplica en lecture dans le portail Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Se connecter à un réplica
Quand vous créez un réplica, il n’hérite pas des règles de pare-feu ni du point de terminaison de service VNet (réseau virtuel) du serveur maître. Ces règles doivent être configurés indépendamment pour le réplica.

Le réplica hérite du compte Administrateur du serveur maître. Tous les comptes d’utilisateur sur le serveur maître sont répliqués sur les réplicas en lecture. Vous pouvez uniquement vous connecter à un réplica en lecture à l’aide des comptes d’utilisateur disponibles sur le serveur maître.

Vous pouvez vous connecter au réplica à l’aide de son nom d’hôte et d’un compte d’utilisateur valide, comme vous le faites sur un serveur Azure Database pour PostgreSQL classique. Pour un serveur nommé **myreplica**, à l’aide du nom d’utilisateur administrateur **myadmin**, vous pouvez vous connecter au réplica via psql :

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

À l’invite, entrez le mot de passe du compte d’utilisateur.

## <a name="monitor-replication"></a>Superviser la réplication
Azure Database pour PostgreSQL fournit deux métriques de surveillance de la réplication, à savoir **Retard maximum entre réplicas** et **Retard du réplica**. Pour savoir comment afficher ces métriques, consultez la section **Superviser un réplica** section [Créer et gérer des réplicas en lecture dans Azure Database pour PostgreSQL - serveur unique à partir du Portail Azure](howto-read-replicas-portal.md).

La métrique **Retard maximum entre réplicas** représente le retard entre le master et le réplica le plus en retard, en octets. Cette métrique est disponible sur le serveur maître uniquement.

La métrique **Retard du réplica** indique le temps écoulé depuis la dernière transaction réexécutée. S’il n’existe aucune transaction sur votre serveur maître, la métrique reflète ce retard. Cette métrique est disponible pour les serveurs réplicas uniquement. Le retard du réplica est calculé à partir de la vue `pg_stat_wal_receiver` :

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Définissez une alerte qui vous informe quand le retard du réplica atteint une valeur inacceptable pour votre charge de travail. 

Pour plus d’informations, interrogez directement le serveur maître pour connaître le délai de réplication en octets sur tous les réplicas.

Dans PostgreSQL version 10 :

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

Dans PostgreSQL version 9.6 et les versions antérieures :

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Si un serveur maître ou un réplica en lecture redémarre, le temps nécessaire pour redémarrer et rattraper le temps perdu est reflété par la métrique Retard du réplica.

## <a name="stop-replication"></a>Arrêter la réplication
Vous pouvez arrêter la réplication entre un serveur maître et un réplica. L’action d’arrêt provoque le redémarrage du réplica et la suppression de ses paramètres de réplication. Une fois la réplication arrêtée entre un serveur maître et un réplica en lecture, celui-ci devient un serveur autonome. Les données du serveur autonome sont les données disponibles sur le réplica au lancement de la commande d’arrêt de la réplication. Le serveur autonome ne se met pas au même niveau que le serveur maître.

> [!IMPORTANT]
> Le serveur autonome ne peut pas être retransformé en réplica.
> Avant d’arrêter la réplication sur un réplica en lecture, vérifiez que celui-ci contient toutes les données nécessaires.

Lorsque vous arrêtez la réplication, le réplica perd tous les liens avec son maître précédent et d’autres réplicas.

Découvrez comment [arrêter la réplication sur un réplica](howto-read-replicas-portal.md).

## <a name="failover"></a>Basculement
Il n’est pas possible d’effectuer un basculement automatique entre un serveur maître et un réplica. 

Étant donné que la réplication est asynchrone, il existe un décalage entre le serveur maître et le réplica. Le niveau de décalage dépend d’un certain nombre de facteurs, comme la charge de travail exécutée sur le serveur maître et la latence qui existe entre les centres de données. Dans la plupart des cas, le décalage du réplica va de quelques secondes à quelques minutes. Pour connaître le décalage d’un réplica, consultez la métrique *Décalage de la réplication*, qui est disponible pour chaque réplica. Cette métrique indique le temps écoulé depuis la dernière transaction réexécutée. Il est recommandé d’observer votre réplica sur une période donnée afin de déterminer le décalage moyen. Vous pouvez configurer une alerte afin d’être averti lorsque le décalage d’un réplica sort de la plage définie et prendre les mesures nécessaires.

> [!Tip]
> Si vous basculez vers le réplica, le décalage qui existe au moment où vous supprimez la liaison entre le réplica et le serveur maître indiquera la quantité de données perdues.

Une fois que vous avez décidé de basculer vers un réplica : 

1. Arrêtez la réplication vers le réplica<br/>
   Cette étape est nécessaire pour que le serveur de réplication puisse accepter les écritures. Dans le cadre de ce processus, le serveur de réplication est redémarré, puis il est dissocié du serveur maître. En général, une fois que le processus d’arrêt de la réplication est lancé, le processus back-end prend environ 2 minutes. Pour comprendre les implications d’une telle action, consultez la section [Arrêter la réplication](#stop-replication).
    
2. Faites pointer votre application vers l’ancien réplica<br/>
   Chaque serveur est associé à une chaîne de connexion unique. Mettez à jour votre application pour qu’elle pointe vers l’ancien réplica au lieu du serveur maître.
    
Lorsque votre application est en mesure de traiter les lectures et les écritures, le basculement est terminé. Le temps d’arrêt de votre application dépend du moment où vous détectez le problème et où vous effectuez les étapes 1 et 2 ci-dessus.


## <a name="considerations"></a>Considérations

Cette section résume les considérations relatives à la fonctionnalité de réplica en lecture.

### <a name="prerequisites"></a>Prérequis
Les réplicas en lecture et le [décodage logique](concepts-logical.md) dépendent du journal WAL de Postgres pour obtenir des informations. Ces deux fonctionnalités ont besoin de différents niveaux de journalisation à partir de Postgres. Le décodage logique nécessite un niveau de journalisation plus élevé que les réplicas en lecture.

Pour configurer le niveau de journalisation approprié, utilisez le paramètre de prise en charge de la réplication Azure. La prise en charge de la réplication Azure propose trois options de paramétrage :

* **Désactivé** : place le moins d’informations dans le journal WAL. Ce paramètre n’est pas disponible sur la plupart des serveurs Azure Database pour PostgreSQL.  
* **Réplica** : plus de détails que l’option **Désactivé**. Il s’agit du niveau minimal de journalisation nécessaire pour que les [réplicas en lecture](concepts-read-replicas.md) fonctionnent. Il s’agit du paramètre par défaut sur la plupart des serveurs.
* **Logique** : plus de détails que l’option **Réplica**. Il s’agit du niveau minimal de journalisation pour que le décodage logique fonctionne. Les réplicas en lecture fonctionnent également avec ce paramètre.

Le serveur doit être redémarré après une modification de ce paramètre. En interne, ce paramètre définit les paramètres Postgres `wal_level`, `max_replication_slots` et `max_wal_senders`.

### <a name="new-replicas"></a>Nouveaux réplicas
Un réplica en lecture est créé en tant que serveur Azure Database pour PostgreSQL. Un serveur existant ne peut pas être transformé en réplica. Vous ne pouvez pas créer un réplica d’un autre réplica en lecture.

### <a name="replica-configuration"></a>Configuration du réplica
Le réplica doit être créé en utilisant les mêmes paramètres de calcul et de stockage que le serveur maître. Après la création d’un réplica, plusieurs paramètres sont modifiables, y compris la période de rétention du stockage et de la sauvegarde.

Les vCore et le niveau tarifaire peuvent également être modifiés sur le réplica dans les conditions suivantes :
* Avec PostgreSQL, la valeur du paramètre `max_connections` sur le réplica en lecture doit être supérieure ou égale à la valeur du serveur maître. Sinon, le réplica ne démarre pas. Dans Azure Database pour PostgreSQL, la valeur du paramètre `max_connections` dépend de la référence SKU (vCore et niveau tarifaire). Pour plus d’informations, consultez [Limites d’Azure Database pour PostgreSQL](concepts-limits.md). 
* La mise à l’échelle vers le niveau tarifaire De base (ou à partir de celui-ci) n’est pas prise en charge.

> [!IMPORTANT]
> Avant de modifier un paramètre du serveur maître, remplacez la valeur du paramètre du réplica par une valeur supérieure ou égale à celle du serveur maître. Ainsi, vous avez la garantie que le réplica peut suivre les changements apportés au maître.

Si vous tentez de mettre à jour les valeurs du serveur décrites ci-dessus sans respecter les limites, vous recevez un message d’erreur.

Les règles de pare-feu, les règles de réseau virtuel et les paramètres de paramétrage sont hérités du serveur maître au réplica lorsque le réplica est créé ou après sa création.

### <a name="basic-tier"></a>Niveau de base
Les serveurs du niveau De base prennent uniquement en charge la réplication dans la même région.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
Avec [PostgreSQL](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS), la valeur du paramètre `max_prepared_transactions` sur le réplica en lecture doit être supérieure ou égale à la valeur du serveur maître. Sinon, le réplica ne démarre pas. Si vous souhaitez modifier `max_prepared_transactions` sur le serveur maître, modifiez-le d’abord sur les réplicas.

### <a name="stopped-replicas"></a>Réplicas arrêtés
Si vous arrêtez la réplication entre un serveur maître et un réplica en lecture, le réplica redémarre pour permettre l’application du changement. Le réplica arrêté devient un serveur autonome qui accepte les lectures et les écritures. Le serveur autonome ne peut pas être retransformé en réplica.

### <a name="deleted-master-and-standalone-servers"></a>Serveurs maîtres et autonomes supprimés
Quand un serveur maître est supprimé, tous ses réplicas en lecture deviennent des serveurs autonomes. Les réplicas redémarrent pour permettre l’application de ce changement.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [créer et gérer des réplicas en lecture dans le portail Azure](howto-read-replicas-portal.md).
* Découvrez comment [créer et gérer des réplicas en lecture avec l’interface de ligne de commande Azure et l’API REST](howto-read-replicas-cli.md).
