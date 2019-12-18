---
title: Mettre à l’échelle un groupe de serveurs - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Ajustez la mémoire du groupe de serveurs, le disque et les ressources du processeur pour gérer l’augmentation de la charge
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: 5d8bbe493887c5340f0943a585eb6ff250bd3728
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977554"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Mettre à l’échelle un groupe de serveurs Hyperscale (Citus)

Azure Database pour PostgreSQL – Hyperscale (Citus) est capable d’effectuer une mise à l’échelle en libre-service pour gérer une charge accrue. Le Portail Azure facilite l’ajout de nouveaux nœuds Worker et l’augmentation de la capacité des nœuds existants.

## <a name="add-worker-nodes"></a>Ajouter des nœuds Worker

Pour ajouter des nœuds, accédez à l’onglet **Configurer** dans votre groupe de serveurs Hyperscale (Citus).  Faites coulisser le curseur **Nombre de nœuds Worker** pour modifier la valeur.

![Curseurs de ressources](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Cliquez sur le bouton **Enregistrer** pour que la valeur modifiée prenne effet.

> [!NOTE]
> Une fois nombre de nœuds worker augmenté et enregistré, le curseur ne permet plus de le réduire.

### <a name="rebalance-shards"></a>Rééquilibrer des partitions

Pour tirer parti des nœuds nouvellement ajoutés, vous devez rééquilibrer les [partitions](concepts-hyperscale-distributed-data.md#shards) de la table distribuée, ce qui signifie déplacer des partitions de nœuds existants vers les nouveaux nœuds. Vérifiez d’abord que les nouveaux Workers ont bien effectué le provisionnement. Ensuite, démarrez le rééquilibreur de partition, connectez-vous au nœud coordinateur du cluster avec psql, puis exécutez :

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

La fonction `rebalance_table_shards` rééquilibre toutes les tables dans le groupe [colocation](concepts-hyperscale-colocation.md) de la table nommée dans son argument. Vous n’avez donc pas besoin d’appeler la fonction pour chaque table distribuée. Il vous suffit de l’appeler sur une table représentative à partir de chaque groupe de colocation.

## <a name="increase-vcores-or-storage-space"></a>Augmenter l’espace de stockage ou le nombre de vCores

Outre l’ajout de nouveaux nœuds, vous pouvez augmenter les capacités des nœuds existants. Accédez à l’onglet **Configurer** de votre groupe de serveurs Hyperscale (Citus), puis faites glisser le curseur pour **vCores** et **Stockage** afin de modifier ces valeurs pour tous les nœuds Worker. Veillez à cliquer sur **Enregistrer** pour appliquer les modifications.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les [options de performances](concepts-hyperscale-configuration-options.md) du groupe de serveurs.
