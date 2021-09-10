---
title: Regroupement des connexions – Hyperscale (Citus) – Azure Database pour PostgreSQL
description: Mise à l’échelle des connexions de base de données client
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 8331cd2f0fa0df52f550acfdac1d8d3506e415f2
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123318608"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-connection-pooling"></a>Regroupement des connexions Azure Database pour PostgreSQL - Hyperscale (Citus)

L’établissement de nouvelles connexions prend du temps. Ce point est problématique pour la plupart des applications, qui demandent de nombreuses connexions de courte durée. Nous vous recommandons d’utiliser un dispositif de regroupement de connexions pour réduire les transactions inactives et réutiliser les connexions existantes. Pour en savoir plus, consultez notre [billet de blog](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

Vous pouvez exécuter votre propre pool de connexions, ou utiliser PgBouncer géré par Azure.

## <a name="managed-pgbouncer"></a>PgBouncer managé

Les regroupeurs de connexion tels que PgBouncer permettent à un plus grand nombre de clients de se connecter en même temps au nœud coordinateur. Les applications se connectent au regroupement et le regroupement relaie les commandes vers la base de données de destination.

Lorsque les clients se connectent par le biais de PgBouncer, le nombre de connexions qui peuvent s’exécuter activement dans la base de données ne change pas. Au lieu de cela, PgBouncer met en file d’attente les connexions en excès et les exécute lorsque la base de données est prête.

Hyperscale (Citus) offre désormais une instance gérée de PgBouncer pour les groupes de serveurs. Il prend en charge jusqu’à 2 000 connexions clientes simultanées.  Pour vous connecter par le biais de PgBouncer, procédez comme suit :

1. Accédez à la page **Chaînes de connexion** de votre groupe de serveurs dans le portail Azure.
2. Activez la case à cocher **Chaînes de connexion PgBouncer**. (Les chaînes de connexion répertoriées varient.)

   > [!IMPORTANT]
   >
   > Si la case à cocher n’existe pas, PgBouncer n’est pas encore activé pour votre groupe de serveurs. Le PgBouncer géré est déployé dans toutes les [régions prises en charge](concepts-hyperscale-configuration-options.md#regions).  Une fois activée dans une région, il est ajouté aux groupes de serveurs existants dans la région pendant un événement de [maintenance planifiée](concepts-hyperscale-maintenance.md).

3. Mettez à jour les applications clientes pour qu’elles se connectent à la nouvelle chaîne.

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur les [limites et limitations](concepts-hyperscale-limits.md) d’Hyperscale (Citus).
