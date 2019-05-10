---
title: Réplicas en lecture dans Azure Database pour MySQL.
description: Cet article décrit les réplicas en lecture pour Azure Database pour MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 04/30/2019
ms.openlocfilehash: be592cb6bb7c041fab0a2f96a338f4f4bb0ff00a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510923"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Réplicas en lecture dans Azure Database pour MySQL

La fonctionnalité de réplica en lecture vous permet de répliquer les données d’un serveur Azure Database pour MySQL à un serveur en lecture seule. Vous pouvez répliquer à partir du serveur maître à jusqu'à cinq réplicas. Les réplicas sont mis à jour en mode asynchrone à l’aide de la technologie de réplication basée sur la position de fichier de journal binaire natif (binlog) du moteur MySQL. Pour en savoir plus sur la réplication binlog, consultez la [vue d’ensemble de la réplication binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

> [!IMPORTANT]
> Vous pouvez créer un réplica en lecture dans la même région que votre serveur principal, ou dans n’importe quelle autre région Azure de votre choix. La réplication entre les régions est actuellement en version préliminaire publique.

Les réplicas sont de nouveaux serveurs que vous gérez similaire à régulière de la base de données Azure pour les serveurs MySQL. Pour chaque réplica en lecture, vous êtes facturé en fonction de la capacité de calcul provisionnée dans les vCores et du stockage provisionné en Go/mois.

Pour découvrir plus en détail les fonctionnalités et les problèmes de la réplication MySQL, consultez la [documentation de la réplication MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-a-read-replica"></a>Quand utiliser un réplica en lecture

La fonctionnalité de réplica en lecture contribue à améliorer les performances et la scalabilité des charges de travail nécessitant une lecture intensive. Les charges de travail de lecture peuvent être isolées sur les réplicas, tandis que les charges de travail d’écriture peuvent être dirigées vers le serveur maître.

Un scénario courant est d’avoir les charges de travail décisionnelles et analytiques qui utilisent le réplica en lecture comme source de données pour la création de rapports.

Dans la mesure où les réplicas sont en lecture seule, ils ne réduisent pas directement les charges relatives à la capacité d’écriture sur le serveur maître. Cette fonctionnalité ne cible pas les charges de travail nécessitant une écriture intensive.

La fonctionnalité de réplica en lecture utilise la réplication asynchrone MySQL. La fonctionnalité n’est pas destinée aux scénarios de réplication synchrone. Il y aura un délai mesurable entre le maître et le réplica. Les données du réplica finissent par devenir cohérentes avec les données du serveur maître. Utilisez cette fonctionnalité pour les charges de travail pouvant s’adapter à ce délai.

Les réplicas en lecture peuvent améliorer votre plan de récupération d’urgence. S’il existe un sinistre régional et votre serveur principal n’est pas disponible, vous pouvez diriger votre charge de travail vers un réplica dans une autre région. Pour ce faire, tout d’abord que le réplica acceptent les écritures en utilisant la fonction de réplication de mots vides. Vous pouvez ensuite rediriger votre application en mettant à jour la chaîne de connexion. En savoir plus, consultez le [arrêter la réplication](#stop-replication) section.

## <a name="create-a-replica"></a>Créer un réplica

Si un serveur maître n’a aucun serveur de réplica existant, le maître sera tout d’abord redémarrer pour se préparer pour la réplication.

Lorsque vous démarrez le flux de travail de réplica de créer, un vide serveur Azure Database pour MySQL est créé. Le nouveau serveur est rempli avec les données qui se trouvaient sur le serveur maître. Le temps de création dépend de la quantité de données présentes sur le serveur maître et du temps écoulé depuis la dernière sauvegarde complète hebdomadaire. Le temps nécessaire peut aller de quelques minutes à plusieurs heures.

> [!NOTE]
> Si aucune alerte de stockage n’est configurée sur vos serveurs, nous vous recommandons de le faire. L’alerte vous informe quand un serveur approche de sa limite de stockage, ce qui affecte la réplication.

Découvrez comment [créer un réplica en lecture dans le portail Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Se connecter à un réplica

Quand vous créez un réplica, il n’hérite pas des règles de pare-feu ni du point de terminaison de service VNet (réseau virtuel) du serveur maître. Ces règles doivent être configurés indépendamment pour le réplica.

Le réplica hérite du compte Administrateur du serveur maître. Tous les comptes d’utilisateur sur le serveur maître sont répliqués sur les réplicas en lecture. Vous pouvez uniquement vous connecter à un réplica en lecture à l’aide des comptes d’utilisateur disponibles sur le serveur maître.

Vous pouvez vous connecter au réplica à l’aide de son nom d’hôte et un compte d’utilisateur valide, comme vous le feriez sur un régulière serveur Azure Database pour MySQL. Pour un serveur nommé **myreplica** avec le nom d’utilisateur administrateur **myadmin**, vous pouvez vous connecter au réplica à l’aide de l’interface CLI de mysql :

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

À l’invite, entrez le mot de passe du compte d’utilisateur.

## <a name="monitor-replication"></a>Superviser la réplication

Azure Database pour MySQL fournit le **décalage de réplication en secondes** métrique dans Azure Monitor. Cette métrique est disponible pour les réplicas uniquement.

Cette mesure est calculée à l’aide de la `seconds_behind_master` métrique disponible dans de MySQL `SHOW SLAVE STATUS` commande.

Définir une alerte pour vous informer lorsque le décalage de réplication atteint une valeur qui n’est pas acceptable pour votre charge de travail.

## <a name="stop-replication"></a>Arrêter la réplication

Vous pouvez arrêter la réplication entre un serveur maître et un réplica. Une fois la réplication arrêtée entre un serveur maître et un réplica en lecture, celui-ci devient un serveur autonome. Les données du serveur autonome sont les données disponibles sur le réplica au lancement de la commande d’arrêt de la réplication. Le serveur autonome ne se met pas au même niveau que le serveur maître.

Lorsque vous choisissez d’arrêter la réplication vers un réplica, il perd toutes les liaisons à son maître précédente et d’autres réplicas. Il n’existe aucun basculement automatique entre un serveur maître et son réplica.

> [!IMPORTANT]
> Le serveur autonome ne peut pas être retransformé en réplica.
> Avant d’arrêter la réplication sur un réplica en lecture, vérifiez que celui-ci contient toutes les données nécessaires.

Découvrez comment [arrêter la réplication sur un réplica](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Considérations et limitations

### <a name="pricing-tiers"></a>Niveaux de tarification

Les réplicas en lecture ne sont actuellement disponibles que dans les niveaux tarifaires Usage général et Mémoire optimisée.

### <a name="master-server-restart"></a>Redémarrage du serveur maître

Lorsque vous créez un réplica pour un maître qui a des réplicas existants, le maître va redémarrer tout d’abord pour se préparer pour la réplication. Veuillez prendre cela en compte et effectuer ces opérations pendant une période creuse.

### <a name="new-replicas"></a>Nouveaux réplicas

Un réplica en lecture est créé comme un nouveau serveur Azure Database pour MySQL. Un serveur existant ne peut pas être transformé en réplica. Vous ne pouvez pas créer un réplica d’un autre réplica en lecture.

### <a name="replica-configuration"></a>Configuration du réplica

Un réplica est créé à partir de la même configuration que celle du serveur maître. Une fois un réplica est créé, plusieurs paramètres sont modifiables indépendamment à partir du serveur maître : génération de VCORE, stockage, la période de rétention de sauvegarde et de MySQL version du moteur de calcul. Le niveau tarifaire peut également être changé indépendamment, sauf vers ou depuis le niveau De base.

> [!IMPORTANT]
> Avant de mettre à jour une configuration de serveur maître avec de nouvelles valeurs, mettez à jour la configuration du réplica avec des valeurs égales ou supérieures. Ainsi, vous avez la garantie que le réplica peut suivre les changements apportés au maître.

### <a name="stopped-replicas"></a>Réplicas arrêtés

Si vous arrêtez la réplication entre un serveur maître et un réplica en lecture, le réplica arrêté devient un serveur autonome qui accepte les lectures et écritures. Le serveur autonome ne peut pas être retransformé en réplica.

### <a name="deleted-master-and-standalone-servers"></a>Serveurs maîtres et autonomes supprimés

Quand un serveur maître est supprimé, la réplication est arrêtée sur tous les réplicas en lecture. Ces réplicas deviennent des serveurs autonomes. Le serveur maître lui-même est supprimé.

### <a name="user-accounts"></a>Comptes d'utilisateur

Les utilisateurs sur le serveur maître sont répliqués sur les réplicas en lecture. Vous ne pouvez vous connecter à un réplica en lecture qu’avec des comptes d’utilisateur disponibles sur le serveur maître.

### <a name="server-parameters"></a>Paramètres du serveur

Pour empêcher les données de devenir désynchronisés et afin d’éviter le potentiel de perte de données ou de corruption, certains paramètres de serveur sont verrouillés à partir de la mise à jour à l’aide de lire des réplicas.

Les paramètres de serveur suivants sont verrouillés sur les serveurs maîtres et de réplica :
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

Le [ `event_scheduler` ](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) paramètre est verrouillé sur le serveur de réplication. 

### <a name="other"></a>Autres

- Les identificateurs de transaction globaux (GTID) ne sont pas pris en charge.
- La création d’un réplica d’un réplica n’est pas prise en charge.
- Les tables en mémoire peuvent entraîner la désynchronisation des réplicas. Il s’agit d’une limitation de la technologie de réplication MySQL. Pour plus d’informations, lisez la [documentation de référence MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html).
- Vérifiez que les tables du serveur maître possèdent des clés primaires. L’absence de clés primaires peut entraîner une latence de réplication entre le maître et les répliques.
- Passer en revue la liste complète des limitations relatives à la réplication MySQL dans la [documentation MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Étapes suivantes

- Découvrir comment [créer et gérer des réplicas en lecture clusters avec le portail Azure](howto-read-replicas-portal.md)
- Découvrir comment [créer et gérer des réplicas en lecture avec l’interface de ligne de commande Azure](howto-read-replicas-cli.md)
