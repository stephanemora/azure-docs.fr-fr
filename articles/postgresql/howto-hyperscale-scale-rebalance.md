---
title: Rééquilibrage des partitions – Hyperscale (Citus) – Azure Database pour PostgreSQL
description: Distribuez les partitions uniformément entre les serveurs pour obtenir un meilleur niveau de performance.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/20/2021
ms.openlocfilehash: 19b44306185dcfcdd3675898b9066019f686dc75
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463554"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Rééquilibrage des partitions dans un groupe de serveurs Hyperscale (Citus)

Pour tirer parti des nœuds nouvellement ajoutés, vous devez rééquilibrer les [partitions](concepts-hyperscale-distributed-data.md#shards) de la table distribuée, ce qui signifie déplacer des partitions de nœuds existants vers les nouveaux nœuds. Hyperscale (Citus) offre un rééquilibrage sans temps d’arrêt, ce qui signifie que les requêtes peuvent s’exécuter sans interruption pendant le rééquilibrage des partitions.

## <a name="determine-if-the-server-group-needs-a-rebalance"></a>Déterminer si le groupe de serveur a besoin d’un rééquilibrage

Le Portail Azure peut vous indiquer si les données sont distribuées de manière égale entre les nœuds Worker dans un groupe de serveur. Pour le voir, accédez à la page **Rééquilibreur de partition** dans le menu **Gestion des groupes de serveurs**. Si les données sont décalées entre les rôles de travail, le message **Rééquilibrage recommandé** s’affiche, ainsi qu’une liste de la taille de chaque nœud.

Si les données sont déjà équilibrées, le message **Aucun rééquilibrage recommandé pour l’instant** s’affiche.

## <a name="run-the-shard-rebalancer"></a>Exécuter le rééquilibreur de partition

Pour démarrer le rééquilibreur de partition, vous devez vous connecter au nœud coordinateur du groupe de serveurs et exécuter la fonction SQL [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) sur des tables distribuées. La fonction rééquilibre toutes les tables dans le groupe [colocation](concepts-hyperscale-colocation.md) de la table nommée dans son argument. Vous n’avez donc pas besoin d’appeler la fonction pour chaque table distribuée. Il vous suffit de l’appeler sur une table représentative à partir de chaque groupe de colocation.

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

## <a name="monitor-rebalance-progress"></a>Progression du rééquilibrage du moniteur

Pour voir le rééquilibreur après l’avoir démarré, revenez au Portail Azure. Ouvrez la page **Rééquilibreur de partition** dans **Gestion des groupes de serveurs**. Le message **Rééquilibrage en cours** s’affiche avec deux tableaux.

Le premier tableau indique le nombre de partitions qui se déplacent à l’intérieur ou à l’extérieur d’un nœud. Par exemple, « 6 sur 24 déplacées ». Le deuxième tableau affiche la progression par table de base de données : nom, nombre de partitions affectées, taille des données affectées et état de rééquilibrage.

Cliquez sur le bouton **Actualiser** pour mettre à jour la page. Lorsque le rééquilibrage est terminé, le message **Aucun rééquilibrage recommandé pour l’instant** s’affiche.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur les [options de performances](concepts-hyperscale-configuration-options.md) du groupe de serveurs.
- [Scale-up ou scale-out d’un groupe de serveurs](howto-hyperscale-scale-grow.md)
- Consultez la documentation de référence [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards)
