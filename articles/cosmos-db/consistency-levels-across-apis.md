---
title: Niveaux de cohérence et API Azure Cosmos DB | Microsoft Docs
description: Comprendre les niveaux de cohérence entre les API dans Azure Cosmos DB.
keywords: cohérence, azure cosmos db, azure, modèles, mongodb, cassandra, graphe, table, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: 974531cd5907e4f69e7d064125d3e51fa4974949
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50956381"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Niveaux de cohérence et API Azure Cosmos DB

Les cinq modèles de cohérence offerts par Azure Cosmos DB sont pris en charge nativement par l’API Cosmos DB SQL, qui est l’API par défaut lors de l’utilisation de Cosmos DB. Outre l’API SQL, Cosmos DB fournit également une prise en charge native des API compatibles avec le protocole filaire pour les bases de données populaires telles que MongoDB, Apache Cassandra, Gremlin et Azure Tables. Ces bases de données n’offrent ni des modèles de cohérence définis avec précision ni les garanties reposant sur un SLA pour les niveaux de cohérence. Elles fournissent uniquement un sous-ensemble de cinq modèles de cohérence offert par Cosmos DB. Pour les API SQL, Gremlin et Table, le niveau de cohérence par défaut que vous configurez sur le compte Cosmos est utilisé.

Les sections suivants montrent le mappage entre la cohérence de données demandée par un pilote de client OSS pour Apache Cassandra 4.x et MongoDB 3.4 lors de l’utilisation de l’API Cassandra et de l’API MongoDB, respectivement, ainsi que les niveaux de cohérence Cosmos DB correspondants.

## <a id="cassandra-mapping"></a>Mappage entre les niveaux de cohérence Apache Cassandra et Cosmos DB

Le tableau ci-dessous montre le mappage des « cohérences de lecture » entre le client Apache Cassandra 4.x et le niveau de cohérence par défaut dans Cosmos DB, pour des déploiements dans plusieurs régions et dans une région unique.

| **Apache Cassandra 4.x** | **Cosmos DB (multi-région)** | **Cosmos DB (région unique)** |
| - | - | - |
| ONE, TWO, THREE | Préfixe cohérent | Préfixe cohérent |
| LOCAL_ONE | Préfixe cohérent | Préfixe cohérent |
| QUORUM, ALL, SERIAL | Obsolescence limitée (par défaut) ou forte (en préversion privée) | Remarque |
| LOCAL_QUORUM | Obsolescence limitée | Remarque |
| LOCAL_SERIAL | Obsolescence limitée | Remarque |

## <a id="mongo-mapping"></a>Mappage entre les niveaux de cohérence MongoDB 3.4 et Cosmos DB

Le tableau ci-dessous montre le mappage des « problèmes de lecture » entre MongoDB 3.4 et le niveau de cohérence par défaut dans Cosmos DB, pour des déploiements dans plusieurs régions et dans une région unique.

| **MongoDB 3.4** | **Cosmos DB (multi-région)** | **Cosmos DB (région unique)** |
| - | - | - |
| Linéarisable | Remarque | Remarque |
| Majorité | Obsolescence limitée | Remarque |
| Local | Préfixe cohérent | Préfixe cohérent |

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les niveaux de cohérence et la compatibilité entre les API Cosmos DB et les API open source, consultez les articles suivants :

* [Compromis de disponibilité et de performance pour différents niveaux de cohérence](consistency-levels-tradeoffs.md)
* [Fonctionnalités MongoDB prises en charge par l’API MongoDB Cosmos DB](mongodb-feature-support.md)
* [Fonctionnalités Apache Cassandra prises en charge par l’API Cassandra Cosmos DB](cassandra-support.md)