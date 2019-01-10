---
title: Partitionnement et mise à l’échelle horizontale dans Azure Cosmos DB
description: Découvrez comment le partitionnement fonctionne dans Azure Cosmos DB, comment configurer le partitionnement et les clés de partition, et comment choisir la clé de partition appropriée pour votre application.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: 38f587fc24478beff3ab236207de3ed8a892c915
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998946"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partitionnement et mise à l’échelle horizontale dans Azure Cosmos DB

Cet article fournit une vue d’ensemble des partitions physiques et logiques dans Azure Cosmos DB, et présente les meilleures pratiques de mise à l'échelle et de partitionnement. 

## <a name="logical-partitions"></a>Partitions logiques

Une partition logique est constituée d'un ensemble d'éléments dotés de la même clé de partition. Par exemple, si tous les éléments d'un conteneur incluent la propriété `City`, vous pouvez utiliser `City` comme clé de partition pour le conteneur. Les groupes d'éléments dont la propriété `City` présente des valeurs spécifiques, comme « Londres », « Paris », « New York », etc., formeront une partition logique distincte.

Dans Azure Cosmos DB, un conteneur est l’unité d’extensibilité de base. Les données ajoutées au conteneur et le débit que vous provisionnez sur celui-ci sont automatiquement partitionnés (horizontalement) sur un ensemble de partitions logiques. Elles sont partitionnées en fonction de la clé de partition que vous spécifiez pour le conteneur Cosmos. Pour plus d’informations, consultez l'article [Comment spécifier la clé de partition de votre conteneur Cosmos](how-to-create-container.md).

Une partition logique définit la portée des transactions de base de données. Vous pouvez mettre à jour les éléments d’une partition logique via une transaction avec isolement de capture instantanée.

Lorsque de nouveaux éléments sont ajoutés au conteneur ou que le débit provisionné sur celui-ci est augmenté, de nouvelles partitions logiques sont créées de manière transparente par le système.

## <a name="physical-partitions"></a>Partitions physiques

Un conteneur Cosmos est mis à l’échelle en répartissant les données et le débit sur un grand nombre de partitions logiques. En interne, une ou plusieurs partitions logiques sont mappées sur une **partition physique** composée d’un ensemble de réplicas, également appelé un jeu de réplicas. Chaque jeu de réplicas héberge une instance du moteur de base de données Cosmos. Grâce au jeu de réplicas, les données stockées dans la partition physique sont durables, hautement disponibles et cohérentes. Une partition physique prend en charge un volume maximal fixe de stockage et d’unités de requête. Chaque réplica constituant la partition physique hérite du quota de stockage. Et tous les réplicas d’une partition physique prennent collectivement en charge le débit alloué à la partition physique. L’illustration suivante montre comment les partitions logiques sont mappées sur des partitions physiques mondialement distribuées :

![Partitionnement dans Azure Cosmos DB](./media/partition-data/logical-partitions.png)

Le débit provisionné pour un conteneur est uniformément réparti entre les partitions physiques. Par conséquent, une conception de clé de partition qui ne distribue pas les demandes de débit de manière uniforme peut créer des partitions « à chaud ». Les partitions à chaud peuvent entraîner une limitation du débit et une utilisation inefficace du débit provisionné.

Contrairement aux partitions logiques, les partitions physiques sont une implémentation interne du système. Vous n'avez aucun contrôle sur leur taille, leur positionnement, leur nombre ou sur le mappage entre les partitions logiques et les partitions physiques. En revanche, vous pouvez contrôler le nombre de partitions logiques, la répartition des données et le débit en choisissant la clé de partition appropriée.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, nous vous avons fourni une vue d’ensemble du partitionnement des données et des meilleures pratiques de mise à l’échelle et de partitionnement dans Azure Cosmos DB. 

* Apprenez-en davantage sur le [débit approvisionné dans Azure Cosmos DB](request-units.md)
* En savoir plus sur la [distribution globale dans Azure Cosmos DB](distribute-data-globally.md)
* En savoir plus sur le [choix de la clé de partition](partitioning-overview.md#choose-partitionkey)
* En savoir plus sur le [provisionnement du débit sur un conteneur Cosmos](how-to-provision-container-throughput.md)
* En savoir plus sur le [provisionnement du débit sur une base de données Cosmos](how-to-provision-database-throughput.md)
