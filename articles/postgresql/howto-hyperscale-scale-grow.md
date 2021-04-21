---
title: Mettre à l’échelle un groupe de serveurs - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Ajustez la mémoire du groupe de serveurs, le disque et les ressources du processeur pour gérer l’augmentation de la charge
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 905224119b9df4e4003b1736443406e9548a49e3
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012527"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Mettre à l’échelle un groupe de serveurs Hyperscale (Citus)

Azure Database pour PostgreSQL – Hyperscale (Citus) est capable d’effectuer une mise à l’échelle en libre-service pour gérer une charge accrue. Le portail Azure facilite l’ajout de nouveaux nœuds Worker et l’augmentation des vCores des nœuds existants. L’ajout de nœuds n’entraîne pas de temps d’arrêt et même le déplacement de partitions vers les nouveaux nœuds (appelé [rééquilibrage de partition](howto-hyperscale-scale-rebalance.md)) se produit sans interrompre les requêtes.

## <a name="add-worker-nodes"></a>Ajouter des nœuds Worker

Pour ajouter des nœuds, accédez à l’onglet **Calcul + stockage** dans votre groupe de serveurs Hyperscale (Citus).  Faites coulisser le curseur **Nombre de nœuds Worker** pour modifier la valeur.

> [!NOTE]
>
> Un groupe de serveurs Hyperscale (Citus) créé avec le [niveau de base (préversion)](concepts-hyperscale-tiers.md) n’a aucun Worker. Le fait d’augmenter le nombre de Worker fait passer automatiquement le groupe de serveurs au niveau standard.
> Après avoir fait passer un groupe de serveurs au niveau standard, vous ne pouvez pas revenir au niveau de base.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Curseurs de ressources":::

Cliquez sur le bouton **Enregistrer** pour que la valeur modifiée prenne effet.

> [!NOTE]
> Une fois nombre de nœuds worker augmenté et enregistré, le curseur ne permet plus de le réduire.

> [!NOTE]
> Pour tirer parti des nouveaux nœuds, vous devez rééquilibrer les [partitions de la table distribuée](howto-hyperscale-scale-rebalance.md), ce qui signifie déplacer des [partitions](concepts-hyperscale-distributed-data.md#shards) des nœuds existants vers les nouveaux.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Augmenter ou diminuer le nombre de cœurs virtuels sur les nœuds

Outre l’ajout de nouveaux nœuds, vous pouvez augmenter les capacités des nœuds existants. L’augmentation ou la diminution de la capacité de calcul peut être utile pour les expériences de performances, ainsi que pour les modifications à court terme ou à long terme des demandes de trafic.

Pour changer le nombre de cœurs virtuels pour tous les nœuds de travail, ajustez le curseur **vCores** sous **Configuration (par nœud worker)** . Le nombre de cœurs virtuels du nœud coordinateur peut être ajusté indépendamment. Ajustez le curseur **vCores** sous  **Configuration (nœud coordinateur)** .

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur les [options de performances](concepts-hyperscale-configuration-options.md) du groupe de serveurs.
- [Rééquilibrer les partitions de la table distribuée](howto-hyperscale-scale-rebalance.md) pour que tous les nœuds Worker puissent participer aux requêtes parallèles
