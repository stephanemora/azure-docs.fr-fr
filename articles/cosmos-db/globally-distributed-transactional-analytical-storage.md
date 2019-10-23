---
title: Stockage transactionnel et analytique distribué à l’échelle mondiale pour les conteneurs Azure Cosmos
description: Apprenez-en davantage sur le stockage transactionnel et analytique et ses options de configuration pour les conteneurs Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: abf222b7a6d6e8fd053fa83c066d2b7850f575ab
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756905"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Stockage transactionnel et analytique distribué à l’échelle mondiale pour les conteneurs Azure Cosmos

Un conteneur Azure Cosmos s’appuie sur deux moteurs de stockage en interne : un moteur de stockage transactionnel et un moteur de stockage analytique qui peut être mis à jour. Ces deux moteurs de stockage sont structurés autour de journaux et optimisés en écriture, ce qui accélère les mises à jour. Toutefois, ils sont encodés différemment :

* **Moteur de stockage transactionnel** : il est encodé dans un format orienté lignes assurant des requêtes et lectures transactionnelles rapides.

* **Moteur de stockage analytique** : il est encodé dans un format orienté colonnes assurant des analyses et des requêtes analytiques rapides.

![Correspondance entre les moteurs de stockage et les API Azure Cosmos DB](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

Le moteur de stockage transactionnel s’appuie sur des disques SSD locaux, tandis que le stockage analytique s’appuie sur des disques SSD hors cluster peu coûteux. Le tableau suivant indique les principales différences entre le stockage transactionnel et le stockage analytique.


|Fonctionnalité  |Stockage transactionnel  |Stockage analytique |
|---------|---------|---------|
|Capacité de stockage maximale par conteneur Azure Cosmos |   Illimité      |    Illimité     |
|Capacité de stockage maximale par clé de partition logique   |   10 Go      |   Illimité      |
|Encodage du stockage  |   Orienté lignes avec format interne   |   Orienté colonnes avec format Apache Parquet |
|Emplacement de stockage |   Stockage répliqué s’appuyant sur des disques SSD locaux/dans un cluster |  Stockage répliqué s’appuyant sur des disques SSD distants/hors cluster peu coûteux       |
|Durabilité  |    99,99999 (7-9 s)     |  99,99999 (7-9 s)       |
|API accédant aux données  |   SQL, MongoDB, Cassandra, Gremlin, Tables et Etcd.       | Apache Spark         |
|Conservation (durée de vie ou TTL)   |  Basée sur des stratégies, configurée sur le conteneur Azure Cosmos à l’aide de la propriété `DefaultTimeToLive`.       |   Basée sur des stratégies, configurée sur le conteneur Azure Cosmos à l’aide de la propriété `ColumnStoreTimeToLive`.      |
|Tarif par Go    |   0,25 $/Go      |  0,02 $/Go       |
|Tarif pour les transactions de stockage    | Le débit provisionné est facturé 0,008 $ pour 100 RU/s selon une facturation horaire.        |  Le débit basé sur la consommation est facturé 0,05 $ pour 10 000 transactions d’écriture et 0,004 $ pour 10 000 transactions de lecture.       |

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

### <a name="on-demand-snapshots-and-time-travel-analytics"></a>Captures instantanées à la demande et analytique en voyage dans le temps

Vous pouvez effectuer des captures instantanées de vos données stockées dans le stockage analytique de vos conteneurs Azure Cosmos à tout moment en appelant la commande `CreateSnapshot (name, timestamp)` sur le conteneur. Les captures instantanées sont nommées « signets » dans l’historique des mises à jour appliquées au conteneur.

![Captures instantanées à la demande et analytique en voyage dans le temps](./media/globally-distributed-transactional-analytical-storage/ondemand-analytical-data-snapshots.png)

Au moment où vous créez la capture instantanée, vous pouvez spécifier, en plus du nom, l’horodatage qui définit l’état de votre conteneur dans l’historique des mises à jour. Vous pouvez ensuite charger les données de capture instantanée dans Spark et exécuter les requêtes.

Actuellement, vous pouvez uniquement effectuer des captures instantanées à la demande sur le conteneur (à tout moment). La réalisation automatique de captures instantanées selon une planification ou une stratégie personnalisée n’est pas encore prise en charge.

### <a name="configure-and-tier-data-between-transactional-and-analytical-storage-independently"></a>Configurer et hiérarchiser les données entre le stockage transactionnel et le stockage analytique de façon indépendante

Selon votre scénario, vous pouvez activer ou désactiver chacun des deux moteurs de stockage de façon indépendante. Voici les configurations pour chaque scénario :

|Scénario |Paramétrage du stockage transactionnel  |Paramétrage du stockage analytique |
|---------|---------|---------|
|Exécution des charges de travail analytiques exclusivement (avec conservation infinie) |  DefaultTimeToLive = 0       |  ColumnStoreTimeToLive = -1       |
|Exécution des charges de travail transactionnelles exclusivement (avec conservation infinie)  |   DefaultTimeToLive = -1      |  ColumnStoreTimeToLive = 0       |
|Exécution des charges de travail transactionnelles et analytiques (avec conservation infinie)   |   DefaultTimeToLive = -1      | ColumnStoreTimeToLive = -1        |
|Exécution des charges de travail transactionnelles et analytiques avec différents intervalles de conservation (ou hiérarchisation du stockage)  |  DefaultTimeToLive = <Value1>       |     ColumnStoreTimeToLive = <Value2>    |

1. **Configurer le conteneur pour les charges de travail analytiques exclusivement (avec conservation infinie)**

   Vous pouvez configurer votre conteneur Azure Cosmos pour les charges de travail analytiques exclusivement. Cette configuration a pour avantage de vous épargner le paiement du stockage transactionnel. Si votre objectif est d’utiliser le conteneur pour des charges de travail analytiques uniquement, vous pouvez désactiver le stockage transactionnel en affectant à `DefaultTimeToLive` la valeur 0 sur le conteneur Cosmos et activer le stockage analytique avec une conservation infinie en affectant à `ColumnStoreTimeToLive` la valeur -1.

   ![Charges de travail analytiques avec conservation infinie](./media/globally-distributed-transactional-analytical-storage/analytical-workload-configuration.png)

1. **Configurer le conteneur pour les charges de travail transactionnelles exclusivement (avec conservation infinie)**

   Vous pouvez configurer votre conteneur Azure Cosmos pour les charges de travail transactionnelles exclusivement. Vous pouvez désactiver le stockage analytique en affectant à `ColumnStoreTimeToLive` la valeur 0 sur le conteneur et activer le stockage transactionnel avec une conservation infinie en affectant à `DefaultTimeToLive` la valeur -1.

   ![Charges de travail transactionnelles avec conservation infinie](./media/globally-distributed-transactional-analytical-storage/transactional-workload-configuration.png)

1. **Configurer le conteneur pour les charges de travail transactionnelles et analytiques (avec conservation infinie)**

   Vous pouvez configurer votre conteneur Azure Cosmos pour les charges de travail transactionnelles et analytiques avec une isolation totale des performances entre les deux. Vous pouvez activer le stockage analytique en affectant à `ColumnStoreTimeToLive` la valeur -1 et activer le stockage transactionnel avec une conservation infinie en affectant à `DefaultTimeToLive ` la valeur -1.

   ![Charges de travail transactionnelles et analytiques avec conservation infinie](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-infinite-retention.png)

1. **Configurer le conteneur pour les charges de travail transactionnelles et analytiques avec hiérarchisation du stockage**

   Vous pouvez configurer votre conteneur Azure Cosmos pour les charges de travail transactionnelles et analytiques avec une isolation totale des performances entre les deux et avec différents intervalles de conservation. Azure Cosmos DB fait en sorte que l’intervalle de conservation du stockage analytique soit toujours plus long que celui du stockage transactionnel.

   Vous pouvez activer le stockage transactionnel avec une conservation infinie en affectant à `DefaultTimeToLive` la valeur <Value 1> et activer le stockage analytique en affectant à `ColumnStoreTimeToLive` la valeur <Value 2>. Azure Cosmos DB fait en sorte que <Value 2> soit toujours supérieure à <Value 1>.

   ![Charges de travail transactionnelles et analytiques avec hiérarchisation du stockage](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-specified-retention.png)

## <a name="next-steps"></a>Étapes suivantes

* [Durée de vie dans Azure Cosmos DB](time-to-live.md)
