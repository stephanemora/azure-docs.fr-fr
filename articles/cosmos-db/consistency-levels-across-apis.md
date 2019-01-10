---
title: Niveaux de cohérence et API Azure Cosmos DB
description: Comprendre les niveaux de cohérence entre les API dans Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: 76ebbc8cc8dbea4b7f8f8226cf1d8570a421e8cf
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034333"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Niveaux de cohérence et API Azure Cosmos DB

Cinq modèles de cohérence offerts par Azure Cosmos DB sont pris en charge nativement par l’API SQL Azure Cosmos DB. Lorsque vous utilisez Azure Cosmos DB, l’API SQL est la valeur par défaut. 

Azure Cosmos DB fournit également la prise en charge native des API compatibles avec les protocoles filaires pour les bases de données les plus courantes. Les bases de données incluent le stockage Table Azure, MongoDB, Apache Cassandra et Gremlin. Celles-ci n’offrent pas de modèle de cohérence défini avec précision, ni de garantie reposant sur un contrat SLA pour les niveaux de cohérence. Elles ne fournissent généralement qu’un sous-ensemble des cinq modèles de cohérence offerts par Azure Cosmos DB. Pour les API SQL, Gremlin et Table, le niveau de cohérence configuré par défaut sur le compte Azure Cosmos DB est utilisé. 

Les sections suivantes montrent le mappage entre la cohérence des données demandée par un pilote de client OSS pour Apache Cassandra 4.x, et MongoDB 3.4. Ce document présente également les niveaux de cohérence Azure Cosmos DB correspondants pour Apache Cassandra et MongoDB.

## <a id="cassandra-mapping"></a>Mappage entre les niveaux de cohérence Apache Cassandra et Azure Cosmos DB

Ce tableau ci-dessous montre le mappage des « cohérences de lecture » entre le client Apache Cassandra 4.x et le niveau de cohérence par défaut dans Azure Cosmos DB. Le tableau renseigne sur les déploiements dans plusieurs régions et dans une région unique.

| **Apache Cassandra 4.x** | **Azure Cosmos DB (multi-région)** | **Azure Cosmos DB (région unique)** |
| - | - | - |
| ONE, TWO, THREE | Préfixe cohérent | Préfixe cohérent |
| LOCAL_ONE | Préfixe cohérent | Préfixe cohérent |
| QUORUM, ALL, SERIAL | L’obsolescence limitée est la valeur par défaut. Le niveau de cohérence Forte est disponible en préversion privée. | Remarque |
| LOCAL_QUORUM | Bounded staleness (En fonction de l'obsolescence) | Remarque |
| LOCAL_SERIAL | Bounded staleness (En fonction de l'obsolescence) | Remarque |

## <a id="mongo-mapping"></a>Mappage entre les niveaux de cohérence MongoDB 3.4 et Azure Cosmos DB

Le tableau ci-dessous montre le mappage des « problèmes de lecture » entre MongoDB 3.4 et le niveau de cohérence par défaut dans Azure Cosmos DB. Le tableau renseigne sur les déploiements dans plusieurs régions et dans une région unique.

| **MongoDB 3.4** | **Azure Cosmos DB (multi-région)** | **Azure Cosmos DB (région unique)** |
| - | - | - |
| Linéarisable | Remarque | Remarque |
| Majorité | Bounded staleness (En fonction de l'obsolescence) | Remarque |
| Local | Préfixe cohérent | Préfixe cohérent |

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les niveaux de cohérence et la compatibilité entre les API Cosmos DB et les API open source. Consultez les articles suivants :

* [Compromis entre disponibilité et performance pour différents niveaux de cohérence](consistency-levels-tradeoffs.md)
* [Fonctionnalités MongoDB prises en charge par l’API d’Azure Cosmos DB pour MongoDB](mongodb-feature-support.md)
* [Fonctionnalités Apache Cassandra prises en charge par l’API Cassandra pour Azure Cosmos DB](cassandra-support.md)