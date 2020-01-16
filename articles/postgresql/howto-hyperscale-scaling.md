---
title: Mettre à l’échelle un groupe de serveurs - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Ajustez la mémoire du groupe de serveurs, le disque et les ressources du processeur pour gérer l’augmentation de la charge
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: bec2a40d8cf5fb178418ec6bb59a52a0bfe3eb8c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453052"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Mettre à l’échelle un groupe de serveurs Hyperscale (Citus)

Azure Database pour PostgreSQL – Hyperscale (Citus) est capable d’effectuer une mise à l’échelle en libre-service pour gérer une charge accrue. Le portail Azure facilite l’ajout de nouveaux nœuds Worker et l’augmentation des vCores des nœuds existants.

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

## <a name="increase-vcores"></a>Augmenter les vCores

Outre l’ajout de nouveaux nœuds, vous pouvez augmenter les capacités des nœuds existants. Cette fonctionnalité est actuellement en préversion : pour demander une augmentation des vCores pour les nœuds de votre groupe de serveurs, [contactez le support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les [options de performances](concepts-hyperscale-configuration-options.md) du groupe de serveurs.
