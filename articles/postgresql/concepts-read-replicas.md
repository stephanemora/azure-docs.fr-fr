---
title: Réplicas en lecture dans Azure Database pour PostgreSQL
description: Cet article décrit les réplicas en lecture pour Azure Database pour PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: 9270c3290bd7be0bbb79d30aff8becc04dcfc603
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903993"
---
# <a name="read-replicas-in-azure-database-for-postgresql"></a>Réplicas en lecture dans Azure Database pour PostgreSQL

> [!IMPORTANT]
> La fonctionnalité de réplica en lecture est en préversion publique.

La fonctionnalité de réplica en lecture vous permet de répliquer des données d’un serveur Azure Database pour PostgreSQL (maître) sur jusqu’à cinq serveurs en lecture seule (réplicas en lecture) dans la même région Azure. Les réplicas en lecture sont mis à jour de manière asynchrone à l’aide de la technologie de réplication native du moteur MySQL.

Les réplicas sont de nouveaux serveurs qui peuvent être gérés de la même manière que les serveurs autonomes Azure Database pour PostgreSQL. Pour chaque réplica en lecture, vous êtes facturé pour la capacité de calcul approvisionnée dans les vCores et le stockage approvisionné en Go/mois.

## <a name="when-to-use-read-replicas"></a>Quand utiliser des réplicas en lecture
La fonction de réplica en lecture vise à améliorer les performances et l'échelle des charges de travail nécessitant beaucoup de lecture. Par exemple, les charges de travail de lecture pourraient être isolées sur les réplicas, alors que les charges de travail d’écriture peuvent être dirigées vers le serveur maître.

Un scénario courant est d’avoir les charges de travail décisionnelles et analytiques qui utilisent le réplica en lecture comme source de données pour la création de rapports.

Comme les réplicas sont en lecture seule, ils n'allègent pas directement les charges de capacité d'écriture sur le maître, et cette fonctionnalité n'est donc pas destinée aux charges de travail gourmandes en écriture.

La fonction de réplica en lecture utilise la réplication asynchrone de PostgreSQL et n’est donc pas destinée aux scénarios de réplication synchrone. Il y aura un délai mesurable entre le maître et le réplica. Les données de la réplique deviennent cohérentes avec les données du maître. Utilisez cette fonctionnalité pour les charges de travail pouvant s’adapter à ce délai.

## <a name="creating-a-replica"></a>Création d’un réplica
**azure.replication_support** doit être défini sur REPLICA sur le serveur maître. La modification de ce paramètre nécessite un redémarrage du serveur pour prendre effet. (Le paramètre **Azure.replication_support** s’applique aux niveaux De base, Usage général et À mémoire optimisée uniquement).

Lorsque vous lancez le flux de travail de création de réplica, une base de données vide Azure Database pour PostgreSQL est créée. Le nouveau serveur est rempli avec les données qui se trouvaient sur le serveur maître. La durée nécessaire à la création du nouveau réplica dépend de la quantité de données sur le maître et de la durée écoulée depuis la dernière sauvegarde hebdomadaire complète. Cela peut prendre de plusieurs minutes à plusieurs heures.

La fonction de réplica en lecture utilise la réplication physique de PostgreSQL (et non pas la réplication logique). La réplication en continu utilisant des emplacements de réplication est le mode de fonctionnement par défaut. Au besoin, la copie des journaux de transaction est utilisée pour le rattrapage.

> [!NOTE]
> Si vous n’avez pas encore configuré une alerte de stockage sur vos serveurs, nous vous recommandons de le faire de façon à être informé lorsqu’un serveur approche de sa limite de stockage, puisque cela affectera la réplication.

[Découvrez comment créer un réplica en lecture dans le portail Azure](howto-read-replicas-portal.md).

## <a name="connecting-to-a-replica"></a>Connexion à un réplica
Lorsque vous créez un réplica, il n’hérite pas des règles de pare-feu ou du point de terminaison de service VNet du serveur maître. Ces règles doivent être configurés indépendamment pour le réplica.

Le réplica hérite de son compte administrateur à partir du serveur maître. Tous les comptes d’utilisateur sur le serveur maître sont répliqués sur les réplicas en lecture. Vous ne pouvez vous connecter à un réplica en lecture qu’avec des comptes d’utilisateur disponibles sur le serveur maître.

Vous pouvez vous connecter au réplica en utilisant son nom d’hôte et un compte utilisateur valide, comme vous le feriez sur un serveur Azure Database pour PostgreSQL normal. Par exemple, si le nom du serveur est myreplica, et que le nom d’utilisateur administrateur est myadmin, vous pouvez vous y connecter depuis psql comme suit :

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```
et quand vous y êtes invité, entrez le mot de passe du compte d’utilisateur.

## <a name="monitoring-replication"></a>Surveillance (réplication)
La métrique **Max Lag across Replicas** (Retard maximum entre réplicas) est disponible dans Azure Monitor. Cette métrique est disponible sur le serveur maître uniquement. La métrique représente le retard entre le master et le réplica le plus en retard. 

Azure Monitor fournit également une métrique **Retard du réplica**. Cette métrique est disponible pour les réplicas uniquement. 

La métrique est calculée à partir de l’affichage pg_stat_wal_receiver :

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())
```
Rappelez-vous que la métrique Retard du réplica indique le temps écoulé depuis la dernière transaction rejouée. S’il n’y a pas de transactions sur votre maître, la métrique reflète ce retard.

Nous vous recommandons de définir une alerte qui vous informe lorsque le délai du réplica atteint une valeur qui n’est pas acceptable pour votre charge de travail. 

Pour plus d’informations, interrogez directement le serveur maître pour connaître le délai de réplication en octets sur tous les réplicas.
Sur PG 10 :
```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

Sur PG 9.6 et versions inférieures :
```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Si un serveur maître ou un serveur réplica redémarre, le temps nécessaire pour redémarrer et rattraper le temps perdu est reflété dans la métrique Retard du réplica.

## <a name="stopping-replication-to-a-replica"></a>Arrêt de la réplication vers un réplica
Vous pouvez choisir d’arrêter la réplication entre un serveur maître et un réplica. Le réplica redémarre pour supprimer ses paramètres de réplication. Une fois la réplication arrêtée entre serveur maître et serveur réplica, ce dernier devient un serveur autonome. Les données sur le serveur autonome sont celles qui était disponibles sur le réplica au moment où la commande Arrêter la réplication a été lancée. Ce serveur autonome ne se met pas au même niveau que le serveur maître.

Ce serveur ne peut pas être à nouveau transformé en réplica.

Assurez-vous que la réplique possède toutes les données dont vous avez besoin avant d'arrêter la réplication.

Vous pouvez [apprendre à arrêter une réplique dans la documentation du guide pratique](howto-read-replicas-portal.md).


## <a name="considerations"></a>Considérations

### <a name="preparing-for-replica"></a>Préparation du réplica
**azure.replication_support** doit être défini sur REPLICA sur le serveur maître avant de pouvoir créer un réplica. La modification de ce paramètre nécessite un redémarrage du serveur pour prendre effet. Ce paramètre s’applique aux niveaux De base, Usage général et À mémoire optimisée uniquement.

### <a name="stopped-replicas"></a>Réplicas arrêtés
Lorsque vous choisissez d’arrêter la réplication entre un maître et un réplica, le réplica redémarre pour appliquer ces modifications. Par la suite, il ne peut pas être à nouveau transformé en réplica.

### <a name="replicas-are-new-servers"></a>Les réplicas sont de nouveaux serveurs
Les réplicas sont créés comme de nouveaux serveurs Azure Database pour PostgreSQL. Les serveurs existants ne peuvent pas être transformés en réplicas.

### <a name="replica-server-configuration"></a>Configuration de serveurs réplicas
Les serveurs réplicas sont créés à l’aide des mêmes configurations de serveur que le serveur maître, ce qui inclut les configurations suivantes :
- Niveau tarifaire
- Génération de calcul
- vCores
- Stockage
- Période de rétention des sauvegardes
- Option de redondance de sauvegarde
- Version du moteur PostgreSQL

Une fois un réplica créé, le niveau tarifaire (sauf vers et à partir du niveau De base), la génération de calcul, les vCores, le stockage et la conservation des sauvegardes peuvent être changés indépendamment du serveur maître.

> [!IMPORTANT]
> Avant de mettre à jour la configuration de serveur d’un maître avec de nouvelles valeurs, la configuration des réplicas doit être mise à jour avec des valeurs égales ou supérieures. Cela permet de s’assurer que les réplicas sont en mesure de suivre les changements apportés au maître.

En particulier, Postgres exige que la valeur du serveur réplica associé au paramètre max_connections soit supérieure ou égale à la valeur du maître, sinon le réplica ne démarre pas. Dans Azure Database pour PostgreSQL, la valeur max_connections est définie en fonction du sku. Pour plus d’informations, lisez le [document Limites](concepts-limits.md). 

Tenter de faire une mise à jour qui enfreint cette règle entraînera une erreur.


### <a name="deleting-the-master"></a>Suppression du maître
Quand un serveur maître est supprimé, tous ses réplicas en lecture deviennent des serveurs autonomes. Les réplicas sont redémarrés pour refléter cette modification.

### <a name="other"></a>Autres
- Les réplicas en lecture ne peuvent être créés que dans la même région Azure que le maître.
- La création d’un réplica d’un réplica n’est pas prise en charge.

## <a name="next-steps"></a>Étapes suivantes
- [Guide pratique de création et de gestion de réplicas en lecture dans le portail Azure](howto-read-replicas-portal.md).
