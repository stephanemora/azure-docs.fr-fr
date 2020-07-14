---
title: Partitionnement et mise à l’échelle horizontale dans Azure Cosmos DB
description: Découvrez comment le partitionnement fonctionne dans Azure Cosmos DB, comment configurer le partitionnement et les clés de partition, et comment choisir la clé de partition appropriée pour votre application.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 57417a80ea83005c01b6f2a17206d46e6c049719
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85112776"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partitionnement et mise à l’échelle horizontale dans Azure Cosmos DB

Cet article explique la relation entre les partitions logiques et physiques. Il présente également les bonnes pratiques pour le partitionnement et donne une vue détaillée du fonctionnement de la mise à l’échelle horizontale dans Azure Cosmos DB. Il n’est pas nécessaire de comprendre ces détails internes pour [sélectionner votre clé de partition](partitioning-overview.md#choose-partitionkey), mais nous les avons abordés afin que vous compreniez bien comment Azure Cosmos DB fonctionne.

## <a name="logical-partitions"></a>Partitions logiques

Une partition logique est constituée d’un ensemble d’éléments dotés de la même clé de partition. Par exemple, dans un conteneur qui contient des données sur la nutrition, tous les éléments contiennent une propriété `foodGroup`. Vous pouvez utiliser `foodGroup` comme clé de partition pour le conteneur. Des groupes d’éléments ayant des valeurs spécifiques pour `foodGroup`, telles que `Beef Products`, `Baked Products` et `Sausages and Luncheon Meats`, forment des partitions logiques distinctes. Vous n’avez pas à vous soucier de la suppression d’une partition logique quand les données sous-jacentes sont supprimées.

Une partition logique définit également la portée des transactions de base de données. Vous pouvez mettre à jour les éléments d’une partition logique via une [transaction avec isolement de capture instantanée](database-transactions-optimistic-concurrency.md). Lorsque de nouveaux éléments sont ajoutés à un conteneur, de nouvelles partitions logiques sont créées de façon transparente par le système.

Il n’existe aucune limite au nombre de partitions logiques dans votre conteneur. Chaque partition logique peut stocker jusqu’à 20 Go de données. Les bons choix de clé de partition procurent une large gamme de valeurs possibles. Par exemple, dans un conteneur dont tous les éléments contiennent une propriété `foodGroup`, les données de la partition logique `Beef Products` peuvent croître jusqu’à 20 Go. La [sélection d’une clé de partition](partitioning-overview.md#choose-partitionkey) avec une large gamme de valeurs possibles garantit que le conteneur peut être mis à l’échelle.

## <a name="physical-partitions"></a>Partitions physiques

Un conteneur Azure Cosmos est mis à l’échelle en répartissant les données et le débit sur des partitions physiques. En interne, une ou plusieurs partitions logiques sont mappées à une seule partition physique. La plupart des petits conteneurs Cosmos ont de nombreuses partitions logiques, mais ne nécessitent qu’une seule partition physique. Contrairement aux partitions logiques, les partitions physiques sont une implémentation interne du système et sont entièrement gérées par Azure Cosmos DB.

Le nombre de partitions physiques dans votre conteneur Cosmos dépend des éléments suivants :

- Quantité de débit provisionné (chaque partition physique peut fournir un débit allant jusqu’à 10 000 unités de requête par seconde)
- Stockage des données total (chaque partition physique peut stocker jusqu’à 50 Go)

Il n’existe aucune limite au nombre total de partitions physiques dans votre conteneur. À mesure qu’augmente la taille des données ou du débit provisionné, Azure Cosmos DB crée automatiquement des partitions physiques en divisant celles qui existent déjà. Les divisions des partitions physiques n’impactent pas la disponibilité de votre application. Après la division des partitions physiques, toutes les données d’une seule partition logique sont toujours stockées sur la même partition physique. La division d’une partition physique crée simplement un mappage de partitions logiques sur des partitions physiques.

Le débit approvisionné pour un conteneur est uniformément réparti entre les partitions physiques. Une conception de clé de partition qui ne distribue pas les requêtes de débit de manière uniforme peut créer des partitions « à chaud ». Les partitions à chaud peuvent entraîner une limitation du débit, une utilisation inefficace du débit approvisionné et des coûts plus élevés.

Vous pouvez voir les partitions physiques de votre conteneur dans la section **Stockage** du **panneau Métriques** du portail Azure :

:::image type="content" source="./media/partition-data/view-partitions-zoomed-out.png" alt-text="Affichage du nombre de partitions physiques" lightbox="./media/partition-data/view-partitions-zoomed-in.png" ::: 

Dans cet exemple de conteneur où nous avons choisi `/foodGroup` comme clé de partition, chacun des trois rectangles représente une partition physique. Dans l’image, la **plage de clés de partition** est identique à une partition physique. La partition physique sélectionnée contient trois partitions logiques : `Beef Products`, `Vegetable and Vegetable Products` et `Soups, Sauces, and Gravies`.

Si nous provisionnons un débit de 18 000 unités de requête par seconde (RU/s), chacune des trois partitions physiques peut utiliser 1/3 du débit total provisionné. Au sein de la partition physique sélectionnée, les clés de partition logique `Beef Products`, `Vegetable and Vegetable Products` et `Soups, Sauces, and Gravies` peuvent, collectivement, utiliser les 6 000  RU/s provisionnées de la partition physique. Le débit provisionné étant réparti uniformément entre les partitions physiques de votre conteneur, il est important de choisir une clé de partition qui répartit uniformément la consommation du débit en [choisissant la clé de partition logique appropriée](partitioning-overview.md#choose-partitionkey). Si vous choisissez une clé de partition qui répartit uniformément la consommation du débit entre les partitions logiques, vous vous assurez que la consommation du débit sur les partitions physiques est équilibrée.

## <a name="replica-sets"></a>Jeux de réplicas

Chaque partition physique se compose d’un ensemble de réplicas, également appelé [*jeu de réplicas*](global-dist-under-the-hood.md). Chaque jeu de réplicas héberge une instance du moteur de base de données Azure Cosmos. Grâce au jeu de réplicas, les données stockées dans la partition physique sont durables, hautement disponibles et cohérentes. Chaque réplica qui constitue la partition physique hérite du quota de stockage de la partition. Tous les réplicas d’une partition physique prennent collectivement en charge le débit alloué à la partition physique. Azure Cosmos DB gère automatiquement les jeux de réplicas.

La plupart des petits conteneurs Cosmos nécessitent uniquement une seule partition physique, mais ils ont toujours au moins 4 réplicas.

L’illustration suivante montre comment les partitions logiques sont mappées sur des partitions physiques mondialement distribuées :

:::image type="content" source="./media/partition-data/logical-partitions.png" alt-text="Image illustrant le partitionnement dans Azure Cosmos DB" border="false":::

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [choix d’une clé de partition](partitioning-overview.md#choose-partitionkey).
* Apprenez-en davantage sur le [débit approvisionné dans Azure Cosmos DB](request-units.md).
* Renseignez-vous sur la [distribution globale dans Azure Cosmos DB](distribute-data-globally.md).
* Découvrez comment [approvisionner le débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).
* Découvrez comment [approvisionner le débit sur une base de données Azure Cosmos](how-to-provision-database-throughput.md).
