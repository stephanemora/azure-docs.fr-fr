---
title: Partitionnement et mise à l’échelle horizontale dans Azure Cosmos DB
description: Découvrez comment le partitionnement fonctionne dans Azure Cosmos DB, comment configurer le partitionnement et les clés de partition, et comment choisir la clé de partition appropriée pour votre application.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cbd171e10cc1a8b27de98d9d4d779f345ac5a3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79225665"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partitionnement et mise à l’échelle horizontale dans Azure Cosmos DB

Cet article explique les partitions physiques et logiques dans Azure Cosmos DB. Il aborde également les meilleures pratiques de mise à l’échelle et de partitionnement. 

## <a name="logical-partitions"></a>Partitions logiques

Une partition logique est constituée d’un ensemble d’éléments dotés de la même clé de partition. Par exemple, si tous les éléments d’un conteneur incluent la propriété `City`, vous pouvez utiliser `City` comme clé de partition pour le conteneur. Des groupes d’éléments ayant des valeurs spécifiques pour `City`, telles que `London`, `Paris` et `NYC`, forment des partitions logiques distinctes. Vous n’avez pas à vous soucier de la suppression d’une partition lorsque les données sous-jacentes sont supprimées.

Dans Azure Cosmos DB, un conteneur est l’unité d’extensibilité de base. Les données qui sont ajoutées au conteneur et le débit que vous approvisionnez sur celui-ci sont automatiquement partitionnés (horizontalement) sur un ensemble de partitions logiques. Les données et le débit sont partitionnés en fonction de la clé de partition que vous spécifiez pour le conteneur Azure Cosmos. Pour plus d’informations, consultez [Créer un conteneur Azure Cosmos](how-to-create-container.md).

Une partition logique définit également la portée des transactions de base de données. Vous pouvez mettre à jour les éléments d’une partition logique via une [transaction avec isolement de capture instantanée](database-transactions-optimistic-concurrency.md). Lorsque de nouveaux éléments sont ajoutés à un conteneur, de nouvelles partitions logiques sont créées de façon transparente par le système.

## <a name="physical-partitions"></a>Partitions physiques

Un conteneur Azure Cosmos est mis à l’échelle en répartissant les données et le débit sur un grand nombre de partitions logiques. En interne, une ou plusieurs partitions logiques sont mappées sur une partition physique composée d’un ensemble de réplicas, également appelé un [*jeu de réplicas*](global-dist-under-the-hood.md). Chaque jeu de réplicas héberge une instance du moteur de base de données Azure Cosmos. Grâce au jeu de réplicas, les données stockées dans la partition physique sont durables, hautement disponibles et cohérentes. Une partition physique prend en charge le volume maximal de stockage et d’unités de requête (RU). Chaque réplica qui constitue la partition physique hérite du quota de stockage de la partition. Tous les réplicas d’une partition physique prennent collectivement en charge le débit alloué à la partition physique. 

L’illustration suivante montre comment les partitions logiques sont mappées sur des partitions physiques mondialement distribuées :

![Image illustrant le partitionnement dans Azure Cosmos DB](./media/partition-data/logical-partitions.png)

Le débit approvisionné pour un conteneur est uniformément réparti entre les partitions physiques. Une conception de clé de partition qui ne distribue pas les requêtes de débit de manière uniforme peut créer des partitions « à chaud ». Les partitions à chaud peuvent entraîner une limitation du débit, une utilisation inefficace du débit approvisionné et des coûts plus élevés.

Contrairement aux partitions logiques, les partitions physiques sont une implémentation interne du système. Vous ne pouvez pas contrôler la taille, le placement ou le nombre de partitions physiques, et vous ne pouvez pas contrôler le mappage entre partitions logiques et partitions physiques. En revanche, vous pouvez contrôler le nombre de partitions logiques, la répartition des données, de la charge de travail et du débit en [choisissant la clé de partition logique appropriée](partitioning-overview.md#choose-partitionkey).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [choix d’une clé de partition](partitioning-overview.md#choose-partitionkey).
* Apprenez-en davantage sur le [débit approvisionné dans Azure Cosmos DB](request-units.md).
* Renseignez-vous sur la [distribution globale dans Azure Cosmos DB](distribute-data-globally.md).
* Découvrez comment [approvisionner le débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).
* Découvrez comment [approvisionner le débit sur une base de données Azure Cosmos](how-to-provision-database-throughput.md).
