---
title: Réplicas en lecture dans Azure Database pour MySQL.
description: Cet article décrit les réplicas en lecture pour Azure Database pour MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 1/23/2019
ms.openlocfilehash: eca67cb70756dd1184bd3a66c2582743c8baa8fd
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903755"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Réplicas en lecture dans Azure Database pour MySQL

> [!IMPORTANT]
> La fonctionnalité de réplica en lecture est en préversion publique.

La fonctionnalité de réplica en lecture vous permet de répliquer des données d’un serveur Azure Database pour MySQL (maître) sur jusqu’à cinq serveurs en lecture seule (réplicas) dans la même région Azure. Les réplicas en lecture seule sont mis à jour de manière asynchrone à l’aide de la technologie de réplication selon la position du fichier journal binaire (binlog) native au moteur MySQL. Pour en savoir plus sur la réplication binlog, consultez la [vue d’ensemble de la réplication binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Les réplicas créés dans le service Azure Database pour MySQL sont de nouveaux serveurs pouvant être gérés de la même façon que les serveurs MySQL normaux/autonomes. Pour chaque réplica en lecture, vous êtes facturé pour la capacité de calcul provisionnée dans les vCores et le stockage provisionné en Go/mois. 


Pour découvrir plus en détail les fonctionnalités et les problèmes de la réplication MySQL, consultez la [documentation de la réplication MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-read-replicas"></a>Quand utiliser des réplicas en lecture

Les applications et les charges de travail qui sont en lecture intensive peuvent être traitées par les réplicas en lecture seule. Les réplicas en lecture permettent d’augmenter la quantité de capacité de lecture disponible par rapport à une situation où vous utiliseriez juste un seul serveur pour la lecture et l’écriture. Les charges de travail de lecture peuvent être isolées sur les réplicas, alors que les charges de travail d’écriture peuvent être dirigées vers le serveur maître.

Un scénario courant est d’avoir les charges de travail décisionnelles et analytiques qui utilisent le réplica en lecture comme source de données pour la création de rapports.

## <a name="considerations-and-limitations"></a>Considérations et limitations

### <a name="pricing-tiers"></a>Niveaux de tarification

Les réplicas en lecture ne sont actuellement disponibles que dans les niveaux tarifaires Usage général et Mémoire optimisée.

### <a name="master-server-restart"></a>Redémarrage du serveur maître

Avec cette préversion, lorsque vous créez un réplica pour un serveur maître qui ne dispose d’aucun réplica existant, le maître commence par redémarrer pour se préparer pour la réplication. Veuillez prendre cela en compte et effectuer ces opérations pendant une période creuse.

### <a name="stopping-replication"></a>Arrêt de la réplication

Vous pouvez choisir d’arrêter la réplication entre un serveur maître et un serveur réplica. L’arrêt de la réplication supprime la relation de réplication entre le serveur maître et le serveur réplica.

Une fois la réplication arrêtée, le serveur réplica devient un serveur autonome. Les données sur le serveur autonome sont celles qui était disponibles sur le réplica au moment où la commande « Arrêter la réplication » a été lancée. Le serveur autonome ne se met pas au même niveau que le serveur maître. Ce serveur ne peut pas être à nouveau transformé en réplica.

### <a name="replicas-are-new-servers"></a>Les réplicas sont de nouveaux serveurs

Les réplicas sont créés comme de nouveaux serveurs Azure Database pour MySQL. Les serveurs existants ne peuvent pas être transformés en réplicas.

### <a name="replica-server-configuration"></a>Configuration de serveurs réplicas

Les serveurs réplicas sont créés à l’aide des mêmes configurations de serveur que le serveur maître, ce qui inclut les configurations suivantes :

- Niveau tarifaire
- Génération de calcul
- vCores
- Stockage
- Période de rétention des sauvegardes
- Option de redondance de sauvegarde
- Version du moteur MySQL
- Règles de pare-feu

Une fois un réplica créé, vous pouvez changer le niveau tarifaire (sauf vers et à partir du niveau De base), la génération de calcul, les vCores, le stockage et la conservation des sauvegardes indépendamment du serveur maître.

### <a name="master-server-configuration"></a>Configuration d’un serveur maître

Si la configuration d’un maître serveur (par exemple, les vCores ou le stockage) est mise à jour, la configuration des réplicas doit également être mise à jour avec des valeurs égales ou supérieures. Sans cela, le serveur de réplication n’est peut-être pas en mesure de s’adapter aux changements apportés au serveur maître et peut, en conséquence, se bloquer.

Les nouvelles règles de pare-feu ajoutées au serveur maître après la création d’un serveur de réplication ne sont pas répliquées sur le serveur réplica. Celui-ci doit également être mis à jour avec cette nouvelle règle de pare-feu.

### <a name="deleting-the-master-server"></a>Suppression du serveur maître

Quand un serveur maître est supprimé, la réplication est arrêtée sur tous les réplicas en lecture. Ces réplicas deviennent des serveurs autonomes. Le serveur maître lui-même est supprimé.

### <a name="user-accounts"></a>Comptes d'utilisateurs

Les utilisateurs sur le serveur maître sont répliqués sur les réplicas en lecture. Vous ne pouvez vous connecter à un réplica en lecture qu’avec des comptes d’utilisateur disponibles sur le serveur maître.

### <a name="other"></a>Autres

- Les identificateurs de transaction globaux (GTID) ne sont pas pris en charge.
- La création d’un réplica d’un réplica n’est pas prise en charge.
- Les tables en mémoire peuvent entraîner la désynchronisation des réplicas. Il s’agit d’une limitation de la technologie de réplication MySQL. Pour plus d’informations, lisez la [documentation de référence MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html).
- Le réglage du paramètre [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) sur un serveur maître après la création d’un serveur réplica peut entraîner la désynchronisation du réplica. Le serveur réplica n’a pas connaissance des différents espaces disques logiques.
- Passer en revue la liste complète des limitations relatives à la réplication MySQL dans la [documentation MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)


## <a name="next-steps"></a>Étapes suivantes

- Découvrir comment [créer et gérer des réplicas en lecture clusters avec le portail Azure](howto-read-replicas-portal.md)
- Découvrir comment [créer et gérer des réplicas en lecture avec l’interface de ligne de commande Azure](howto-read-replicas-cli.md)
