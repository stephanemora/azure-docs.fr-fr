---
title: Stockage transactionnel et analytique distribué à l’échelle mondiale (en préversion privée) pour les conteneurs Azure Cosmos
description: Apprenez-en davantage sur le stockage transactionnel et analytique et ses options de configuration pour les conteneurs Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 1c2b79f8d0641b1a1386329a2add14ded766bf5a
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623395"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Stockage transactionnel et analytique distribué à l’échelle mondiale pour les conteneurs Azure Cosmos

Un conteneur Azure Cosmos s’appuie sur deux moteurs de stockage en interne : un moteur de stockage transactionnel et un moteur de stockage analytique qui peut être mis à jour (en préversion privée). Ces deux moteurs de stockage sont structurés autour de journaux et optimisés en écriture, ce qui accélère les mises à jour. Toutefois, ils sont encodés différemment :

* **Moteur de stockage transactionnel** : il est encodé dans un format orienté lignes assurant des requêtes et lectures transactionnelles rapides.

* **Moteur de stockage analytique** : il est encodé dans un format orienté colonnes assurant des analyses et des requêtes analytiques rapides.

![Correspondance entre les moteurs de stockage et les API Azure Cosmos DB](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

Le moteur de stockage transactionnel s’appuie sur des disques SSD locaux, tandis que le stockage analytique s’appuie sur des disques SSD hors cluster peu coûteux. Le tableau suivant indique les principales différences entre le stockage transactionnel et le stockage analytique.


|Fonctionnalité  |Stockage transactionnel  |Stockage analytique |
|---------|---------|---------|
|Capacité de stockage maximale par conteneur Azure Cosmos |   Illimité      |    Illimité     |
|Capacité de stockage maximale par clé de partition logique   |   20 Go      |   Illimité      |
|Encodage du stockage  |   Orienté lignes avec format interne   |   Orienté colonnes avec format Apache Parquet |
|Emplacement de stockage |   Stockage répliqué s’appuyant sur des disques SSD locaux/dans un cluster |  Stockage répliqué s’appuyant sur des disques SSD distants/hors cluster peu coûteux       |
|Durabilité  |    99,99999 (7-9 s)     |  99,99999 (7-9 s)       |
|API accédant aux données  |   SQL, MongoDB, Cassandra, Gremlin, Tables et etcd.       | Apache Spark         |
|Conservation (durée de vie ou TTL)   |  Basée sur des stratégies, configurée sur le conteneur Azure Cosmos à l’aide de la propriété `DefaultTimeToLive`.       |   Basée sur des stratégies, configurée sur le conteneur Azure Cosmos à l’aide de la propriété `ColumnStoreTimeToLive`.      |
|Tarif par Go    |   Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|Tarif pour les transactions de stockage    |  Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>Avantages du stockage transactionnel et du stockage analytique

### <a name="no-etl-operations"></a>Absence d’opérations ETL

Les pipelines analytiques classiques sont complexes et impliquent plusieurs étapes nécessitant chacune des opérations d’extraction, transformation et chargement (ETL, Extract-Transform-Load) vers les niveaux de calcul et de stockage et à partir de ces niveaux. Il en résulte des architectures de traitement de données complexes. Ceci engendre des coûts élevés liés aux différentes étapes de stockage et de calcul ainsi qu’une latence importante due aux grands volumes de données transférés lors de ces étapes.  

L’exécution d’opérations ETL avec Azure Cosmos DB n’entraîne aucune surcharge. Chaque conteneur Azure Cosmos s’appuie sur un moteur de stockage transactionnel et un moteur de stockage analytique. Le transfert de données entre les deux moteurs s’effectue dans le moteur de base de données sans tronçon réseau. La latence et le coût correspondants sont nettement inférieurs à ceux des solutions analytiques classiques. De plus, vous bénéficiez d’un unique système de stockage distribué à l’échelle mondiale pour les charges de travail transactionnelles et analytiques.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Historique des versions, mise à jour et interrogation du stockage analytique

Le stockage analytique peut être entièrement mis à jour. Par ailleurs, il contient l’historique complet (avec versions) de toutes les mises à jour transactionnelles effectuées sur le conteneur Azure Cosmos.

Toute mise à jour apportée au stockage transactionnel est visible au niveau du stockage analytique sous un délai garanti de 30 secondes. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Stockage analytique multimaître distribué à l’échelle mondiale

Si votre compte Azure Cosmos est limité à une seule région, les données stockées (dans le stockage transactionnel et analytique) dans les conteneurs sont également limitées à une seule région. En revanche, si le compte Azure Cosmos est distribué à l’échelle mondiale, les données stockées dans les conteneurs sont également distribuées à l’échelle mondiale.

Pour les comptes Azure Cosmos configurés avec plusieurs régions d’écriture, les écritures dans le conteneur (dans le stockage transactionnel et le stockage analytique) sont toujours effectuées dans la région locale et sont donc rapides.

Pour les comptes Azure Cosmos sur une ou plusieurs régions, qu’il existe une seule région d’écriture (maître unique) ou plusieurs (stockage multimaître), les lectures/requêtes transactionnelles et analytiques sont effectuées localement dans la région concernée.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Isolation des performances entre les charges de travail transactionnelles et analytiques

Dans une région donnée, les charges de travail transactionnelles fonctionnent sur le stockage transactionnel/orienté lignes de votre conteneur. En revanche, les charges de travail analytiques fonctionnent sur le stockage analytique/orienté colonnes de votre conteneur. Les deux moteurs de stockage fonctionnent de façon indépendante et assurent une isolation stricte des performances entre les charges de travail.

Les charges de travail transactionnelles consomment le débit provisionné (RU). En revanche, le débit des charges de travail analytiques est basé sur la consommation réelle. Les charges de travail analytiques consomment des ressources à la demande.

## <a name="next-steps"></a>Étapes suivantes

* [Durée de vie dans Azure Cosmos DB](time-to-live.md)
