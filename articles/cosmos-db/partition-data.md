---
title: Partitionnement et mise à l’échelle horizontale dans Azure Cosmos DB
description: Découvrez le fonctionnement du partitionnement dans Azure Cosmos DB, comment configurer le partitionnement et clés de partition et comment choisir la bonne clé de partition pour votre application.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.openlocfilehash: cf454d6504f0433d7ac7ca883982ae317b14f814
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797821"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partitionnement et mise à l’échelle horizontale dans Azure Cosmos DB

Cet article explique les partitions physiques et logiques dans Azure Cosmos DB. Il aborde également les meilleures pratiques pour la mise à l’échelle et le partitionnement. 

## <a name="logical-partitions"></a>Partitions logiques

Une partition logique se compose d’un ensemble d’éléments qui ont la même clé de partition. Par exemple, dans un conteneur dans lequel tous les éléments contiennent un `City` propriété, vous pouvez utiliser `City` comme clé de partition pour le conteneur. Groupes d’éléments qui ont des valeurs spécifiques pour `City`, tel que `London`, `Paris`, et `NYC`, forment des partitions logiques distinctes. Vous n’avez pas à vous soucier de la suppression d’une partition lorsque les données sous-jacentes sont supprimées.

Dans Azure Cosmos DB, un conteneur est l’unité d’extensibilité de base. Les données qui sont ajoutées au conteneur et le débit que vous devez configurer sur le conteneur sont automatiquement partitionnées (horizontalement) sur un ensemble de partitions logiques. Débit et les données sont partitionnées en fonction de la clé de partition que vous spécifiez pour le conteneur Azure Cosmos. Pour plus d’informations, consultez [créer un conteneur Azure Cosmos](how-to-create-container.md).

Une partition logique définit également la portée des transactions de base de données. Vous pouvez mettre à jour les éléments au sein d’une partition logique en utilisant un [transaction avec isolement de capture instantanée](database-transactions-optimistic-concurrency.md). Lorsque de nouveaux éléments sont ajoutés à un conteneur, les nouvelles partitions logiques sont créées en toute transparence par le système.

## <a name="physical-partitions"></a>Partitions physiques

Un conteneur Azure Cosmos est mis à l’échelle en répartissant les données et le débit sur un grand nombre de partitions logiques. En interne, un ou plusieurs partitions logiques sont mappées à une partition physique qui se compose d’un jeu de réplicas, également appelé un [ *jeu de réplicas*](global-dist-under-the-hood.md). Chaque réplica définies héberge une instance du moteur de base de données Azure Cosmos DB. Un jeu de réplicas rend les données stockées dans la partition physique durable, hautement disponible et cohérente. Une partition physique prend en charge le maximum d’unités de stockage et de la demande (RU). Chaque réplica de la partition physique hérite de quota de stockage de la partition. Tous les réplicas d’une partition physique collectivement prend en charge que le débit est alloué à la partition physique. 

L’illustration suivante montre comment les partitions logiques sont mappées sur des partitions physiques mondialement distribuées :

![Une image qui illustre le partitionnement dans Azure Cosmos DB](./media/partition-data/logical-partitions.png)

Débit approvisionné pour un conteneur est réparti uniformément entre partitions physiques. Une conception de clé de partition qui ne le distribue uniformément les demandes de débit peut créer des partitions « à chaud ». Partitions à chaud peuvent entraîner de limitation du débit et en cours d’utilisation inefficace du débit approvisionné et coûts plus élevés.

Contrairement aux partitions logiques, les partitions physiques sont une implémentation interne du système. Vous ne pouvez pas contrôler la taille, la sélection élective ou le nombre de partitions physiques, et vous ne pouvez pas contrôler le mappage entre les partitions logiques et les partitions physiques. Toutefois, vous pouvez contrôler le nombre de partitions logiques et la distribution des données, la charge de travail et le débit par [choix de la clé de la bonne partition logique](partitioning-overview.md#choose-partitionkey).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [choisir une clé de partition](partitioning-overview.md#choose-partitionkey).
* Apprenez-en davantage sur le [débit approvisionné dans Azure Cosmos DB](request-units.md).
* Renseignez-vous sur la [distribution globale dans Azure Cosmos DB](distribute-data-globally.md).
* Découvrez comment [approvisionner le débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).
* Découvrez comment [approvisionner le débit sur une base de données Azure Cosmos](how-to-provision-database-throughput.md).
