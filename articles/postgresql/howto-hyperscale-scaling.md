---
title: Mettre à l’échelle un groupe de serveurs - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Ajustez la mémoire du groupe de serveurs, le disque et les ressources du processeur pour gérer l’augmentation de la charge
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 3/16/2020
ms.openlocfilehash: 1173defa8bbe66cbeaaf6bd5264b0730160a197b
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116826"
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

## <a name="increase-or-decrease-vcores-on-nodes"></a>Augmenter ou diminuer le nombre de cœurs virtuels sur les nœuds

> [!NOTE]
> Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Pour demander une modification du nombre de cœurs virtuels pour les nœuds de votre groupe de serveurs, veuillez [contacter le support technique Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Outre l’ajout de nouveaux nœuds, vous pouvez augmenter les capacités des nœuds existants. L’augmentation ou la diminution de la capacité de calcul peut être utile pour les expériences de performances, ainsi que pour les modifications à court terme ou à long terme des demandes de trafic.

Pour changer le nombre de cœurs virtuels pour tous les nœuds de travail, ajustez le curseur **vCores** sous **Configuration (par nœud worker)** . Le nombre de cœurs virtuels du nœud coordinateur peut être ajusté indépendamment. Cliquez sur le lien **Changer la configuration** sous le **nœud coordinateur**. Une boîte de dialogue s’affiche avec des curseurs pour le nombre de cœurs virtuels et la capacité de stockage du coordinateur. Changez les curseurs comme vous le souhaitez et sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les [options de performances](concepts-hyperscale-configuration-options.md) du groupe de serveurs.
