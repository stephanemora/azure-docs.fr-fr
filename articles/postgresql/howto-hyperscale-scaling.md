---
title: Mettre à l’échelle Azure Database pour PostgreSQL – Groupe de serveurs Hyperscale (Citus)
description: Ajustez la mémoire du groupe de serveurs, le disque et les ressources du processeur pour gérer l’augmentation de la charge
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: a69b4988a5ee835381de986edaa5899b09aa9e4e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71263016"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Mettre à l’échelle un groupe de serveurs Hyperscale (Citus)

Azure Database pour PostgreSQL – Hyperscale (Citus) est capable d’effectuer une mise à l’échelle en libre-service pour gérer une charge accrue. Le portail Azure facilite l’ajout de nouveaux nœuds worker.

Pour ce faire, accédez à l’onglet **Configurer** dans votre groupe de serveurs Hyperscale (Citus).
Faites coulisser le curseur **Nombre de nœuds worker** pour modifier la valeur.

![Curseurs de ressources](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Cliquez sur le bouton « Enregistrer » pour que la valeur modifiée prenne effet.

> [!NOTE]
> Une fois nombre de nœuds worker augmenté et enregistré, le curseur ne permet plus de le réduire.
>
> Par ailleurs, il n’est plus possible d’ajuster les vCores et le stockage sur le coordinateur ou les nœuds worker via cette interface utilisateur. Si une mise à l’échelle du calcul sur le coordinateur ou les nœuds worker est nécessaire, ouvrez un ticket de support .

Pour tirer parti des nœuds nouvellement ajoutés, vous devez rééquilibrer les [partitions](concepts-hyperscale-distributed-data.md#shards) de la table distribuée, ce qui signifie déplacer des partitions de nœuds existants vers les nouveaux nœuds. Pour démarrer le rééquilibreur de partition, connectez-vous au nœud du coordinateur du cluster avec psql, puis exécutez :

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

La fonction `rebalance_table_shards` rééquilibre toutes les tables dans le groupe [colocation](concepts-hyperscale-colocation.md) de la table nommée dans son argument. Vous n’avez donc pas besoin d’appeler la fonction pour chaque table distribuée. Il vous suffit de l’appeler sur une table représentative à partir de chaque groupe de colocation.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les [options de performances](concepts-hyperscale-configuration-options.md) du groupe de serveurs.
