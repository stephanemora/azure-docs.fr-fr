---
title: Réplicas en lecture - Azure Database pour PostgreSQL - Hyperscale (Citus)
description: Cet article décrit la fonctionnalité de réplica en lecture dans Azure Database pour PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 08629ed8ce361f5d7ac4bb4d2e148ae5b20fff02
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108315558"
---
# <a name="read-replicas-in-azure-database-for-postgresql---hyperscale-citus"></a>Réplicas en lecture dans Azure Database pour PostgreSQL - Hyperscale (Citus)

> [!IMPORTANT]
> Les réplicas en lecture dans Hyperscale (Citus) sont actuellement en préversion. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
>
> Vous pouvez consulter la liste complète des nouvelles fonctionnalités dans les [fonctionnalités d’évaluation pour Hyperscale (Citus)](hyperscale-preview-features.md).

La fonctionnalité de réplica en lecture vous permet de répliquer les données d’un groupe de serveurs Hyperscale (Citus) sur un groupe de serveurs en lecture seule. Les réplicas en lecture sont mis à jour de manière **asynchrone** avec la technologie de réplication physique de PostgreSQL. Vous pouvez effectuer la réplication du serveur principal vers un nombre illimité de réplicas.

Les réplicas sont de nouveaux groupes de serveurs que vous gérez comme les groupes de serveurs Hyperscale (Citus). Pour chaque réplica en lecture, vous êtes facturé en fonction de la capacité de calcul provisionnée dans les vCores et du stockage provisionné en Go/mois.

Découvrez comment [créer et gérer des réplicas](howto-hyperscale-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Quand utiliser un réplica en lecture

La fonctionnalité de réplica en lecture contribue à améliorer les performances et la scalabilité des charges de travail nécessitant une lecture intensive. Les charges de travail de lecture peuvent être isolées sur les réplicas, tandis que les charges de travail d’écriture peuvent être dirigées vers le serveur principal.

Un scénario courant est d’avoir les charges de travail décisionnelles et analytiques qui utilisent le réplica en lecture comme source de données pour la création de rapports.

Dans la mesure où les réplicas sont en lecture seule, ils ne réduisent pas directement les charges relatives à la capacité d’écriture sur le serveur principal.

### <a name="considerations"></a>Considérations

Cette fonctionnalité est destinée aux scénarios où la latence est acceptable dans la réplication et vise à décharger les requêtes. Elle n’est pas destinée aux scénarios de réplication synchrone où les données des réplicas sont supposées être à jour. Il y aura un délai mesurable entre le serveur principal et le réplica. Ce délai peut être de quelques minutes, voire de quelques heures, selon la charge de travail et le temps de latence entre la source et le réplica.  Les données du réplica finissent par devenir cohérentes avec les données du serveur principal. Utilisez cette fonctionnalité pour les charges de travail pouvant s’adapter à ce délai. 

## <a name="create-a-replica"></a>Créer un réplica

Quand vous démarrez le workflow de création de réplica, un groupe de serveurs Hyperscale (Citus) est créé. Ce nouveau groupe est rempli avec les données qui se trouvaient sur le groupe de serveurs principal. Le temps de création dépend de la quantité de données présentes sur le serveur principal et du temps écoulé depuis la dernière sauvegarde complète hebdomadaire. Le temps nécessaire peut aller de quelques minutes à plusieurs heures.

La fonctionnalité de réplica en lecture utilise la réplication physique PostgreSQL, et non la réplication logique. Le mode par défaut est la réplication en continu à l’aide d’emplacements de réplication.
Quand cela est nécessaire, la copie des journaux de transaction permet de rattraper le retard.

Découvrez comment [créer un réplica en lecture dans le portail Azure](howto-hyperscale-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Se connecter à un réplica

Lorsque vous créez un réplica, il n’hérite pas des règles de pare-feu du groupe de serveurs principal. Ces règles doivent être configurés indépendamment pour le réplica.

Le réplica hérite du compte administrateur (citus) du groupe de serveurs principal.
Tous les comptes d’utilisateur sont répliqués sur les réplicas en lecture. Vous pouvez uniquement vous connecter à un réplica en lecture à l’aide des comptes d’utilisateur disponibles sur le serveur principal.

Vous pouvez vous connecter au nœud coordinateur du réplica à l’aide de son nom d’hôte et d’un compte d’utilisateur valide, comme vous le feriez sur un groupe de serveurs Hyperscale (Citus).
Pour un serveur nommé **myreplica**, à l’aide du nom d’utilisateur administrateur **citus**, vous pouvez vous connecter au nœud coordinateur du réplica via psql :

```bash
psql -h c.myreplica.postgres.database.azure.com -U citus@myreplica -d postgres
```

À l’invite, entrez le mot de passe du compte d’utilisateur.

## <a name="considerations"></a>Considérations

Cette section résume les considérations relatives à la fonctionnalité de réplica en lecture.

### <a name="new-replicas"></a>Nouveaux réplicas

Un réplica en lecture est créé en tant que nouveau groupe de serveurs Hyperscale (Citus). Un groupe de serveurs existant ne peut pas être transformé en réplica. Vous ne pouvez pas créer un réplica d’un autre réplica en lecture.

### <a name="replica-configuration"></a>Configuration du réplica

Le réplica doit être créé en utilisant les mêmes paramètres de calcul, de stockage et de nœud Worker que le serveur principal. Après la création d’un réplica, plusieurs paramètres sont modifiables, y compris la période de rétention du stockage et de la sauvegarde. D’autres paramètres ne peuvent pas être modifiés dans les réplicas, tels que la taille de stockage et le nombre de nœuds Worker.

N’oubliez pas de faire en sorte que les réplicas soient suffisamment robustes pour conserver les modifications provenant du serveur principal. Par exemple, veillez à mettre à l’échelle la puissance de calcul dans les réplicas si vous la mettez à l’échelle sur le serveur principal.

Les règles de pare-feu et les paramètres de paramétrage sont hérités du serveur principal au réplica lorsque le réplica est créé ou après sa création.

### <a name="regions"></a>Régions

Les groupes de serveurs Hyperscale (Citus) prennent uniquement en charge la réplication dans la même région.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer et gérer des réplicas en lecture dans le portail Azure](howto-hyperscale-read-replicas-portal.md).
