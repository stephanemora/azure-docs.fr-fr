---
title: Mettre à l’échelle un groupe de serveurs - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Ajustez la mémoire du groupe de serveurs, le disque et les ressources du processeur pour gérer l’augmentation de la charge
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 9/18/2020
ms.openlocfilehash: fef873d5122fefb48c85281f71e206f95f3fbe48
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90986713"
---
# <a name="server-group-size"></a>Taille de groupe de serveurs

L’option de déploiement Hyperscale (Citus) utilise des serveurs de base de données qui coopèrent pour paralléliser l’exécution des requêtes et stocker plus de données. La « taille » de groupe de serveurs fait référence au nombre de serveurs et aux ressources matérielles de chacun d’entre eux.

## <a name="picking-initial-size"></a>Choix de la taille initiale

La taille d’un groupe de serveurs, en termes de nombre de nœuds et leur capacité matérielle, est facile à modifier ([voir ci-dessous](#scale-a-hyperscale-citus-server-group)). Toutefois, vous devez choisir une taille initiale pour un nouveau groupe de serveurs. Voici quelques conseils pour effectuer un choix raisonnable.

### <a name="multi-tenant-saas-use-case"></a>Cas d’utilisation de SaaS multilocataire

Pour les utilisateurs qui migrent vers Hyperscale (Citus) à partir d’une instance de base de données PostgreSQL à nœud unique existante, nous vous recommandons de choisir un cluster dans lequel le total du nombre de vCores Worker et de la mémoire RAM est égal à celui de l’instance d’origine. Dans de tels scénarios, nous avons constaté que les performances ont doublé, voire triplé, car le partitionnement améliore l’utilisation des ressources, ce qui permet des index plus petits, etc.

Le nombre de vCores requis pour le nœud coordinateur dépend de votre charge de travail existante (débit en écriture/lecture). Le nœud coordinateur ne requiert pas autant de RAM que les nœuds Worker, mais l’allocation de mémoire RAM est déterminée en fonction du nombre de vCores (comme décrit dans les [options de configuration Hyperscale](concepts-hyperscale-configuration-options.md)), de sorte que le nombre de vCores constitue la vraie décision.

### <a name="real-time-analytics-use-case"></a>Cas d’utilisation d’analytique en temps réel

Nombre total de vCores : quand les données de travail tiennent dans la mémoire RAM, vous pouvez vous attendre à une amélioration linéaire des performances proportionnelle au nombre de cœurs Worker sur Hyperscale (Citus). Pour déterminer le nombre de vCores adapté à vos besoins, tenez compte de la latence actuelle des requêtes dans votre base de données à nœud unique et de la latence nécessaire dans Hyperscale (Citus). Divisez la latence actuelle par la latence souhaitée, puis arrondissez le résultat.

Mémoire RAM worker : la meilleure des situations serait de fournir suffisamment de mémoire pour que la majorité de la plage de travail tienne dans la mémoire. Le type de requêtes que votre application utilise affecte les besoins en mémoire. Vous pouvez exécuter EXPLAIN ANALYZE sur une requête pour déterminer la quantité de mémoire nécessaire. N’oubliez pas que les vCores et la RAM sont mis à l’échelle ensemble, comme décrit dans l’article sur les [options de configuration Hyperscale](concepts-hyperscale-configuration-options.md).

## <a name="scale-a-hyperscale-citus-server-group"></a>Mettre à l’échelle un groupe de serveurs Hyperscale (Citus)

Azure Database pour PostgreSQL – Hyperscale (Citus) est capable d’effectuer une mise à l’échelle en libre-service pour gérer une charge accrue. Le portail Azure facilite l’ajout de nouveaux nœuds Worker et l’augmentation des vCores des nœuds existants. L’ajout de nœuds n’entraîne pas de temps d’arrêt et même le déplacement de partitions vers les nouveaux nœuds (appelé [rééquilibrage de partition](#rebalance-shards)) se produit sans interrompre les requêtes.

### <a name="add-worker-nodes"></a>Ajouter des nœuds Worker

Pour ajouter des nœuds, accédez à l’onglet **Calcul + stockage** dans votre groupe de serveurs Hyperscale (Citus).  Faites coulisser le curseur **Nombre de nœuds Worker** pour modifier la valeur.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Curseurs de ressources":::

Cliquez sur le bouton **Enregistrer** pour que la valeur modifiée prenne effet.

> [!NOTE]
> Une fois nombre de nœuds worker augmenté et enregistré, le curseur ne permet plus de le réduire.

#### <a name="rebalance-shards"></a>Rééquilibrer des partitions

Pour tirer parti des nœuds nouvellement ajoutés, vous devez rééquilibrer les [partitions](concepts-hyperscale-distributed-data.md#shards) de la table distribuée, ce qui signifie déplacer des partitions de nœuds existants vers les nouveaux nœuds. Vérifiez d’abord que les nouveaux Workers ont bien effectué le provisionnement. Ensuite, démarrez le rééquilibreur de partition, connectez-vous au nœud coordinateur du cluster avec psql, puis exécutez :

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

La fonction `rebalance_table_shards` rééquilibre toutes les tables dans le groupe [colocation](concepts-hyperscale-colocation.md) de la table nommée dans son argument. Vous n’avez donc pas besoin d’appeler la fonction pour chaque table distribuée. Il vous suffit de l’appeler sur une table représentative à partir de chaque groupe de colocation.

### <a name="increase-or-decrease-vcores-on-nodes"></a>Augmenter ou diminuer le nombre de cœurs virtuels sur les nœuds

Outre l’ajout de nouveaux nœuds, vous pouvez augmenter les capacités des nœuds existants. L’augmentation ou la diminution de la capacité de calcul peut être utile pour les expériences de performances, ainsi que pour les modifications à court terme ou à long terme des demandes de trafic.

Pour changer le nombre de cœurs virtuels pour tous les nœuds de travail, ajustez le curseur **vCores** sous **Configuration (par nœud worker)** . Le nombre de cœurs virtuels du nœud coordinateur peut être ajusté indépendamment. Ajustez le curseur **vCores** sous  **Configuration (nœud coordinateur)** .

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur les [options de performances](concepts-hyperscale-configuration-options.md) du groupe de serveurs.
