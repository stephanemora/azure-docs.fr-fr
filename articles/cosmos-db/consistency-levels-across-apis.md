---
title: Niveaux de cohérence et API Azure Cosmos DB
description: Comprendre les niveaux de cohérence entre les API dans Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: b620ca76cfea296e504afffd91852308a01575db
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56001972"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Niveaux de cohérence et API Azure Cosmos DB

Cinq modèles de cohérence offerts par Azure Cosmos DB sont pris en charge nativement par l'API SQL. Lorsque vous utilisez Azure Cosmos DB, l’API SQL est la valeur par défaut. 

Azure Cosmos DB fournit également la prise en charge native des API compatibles avec les protocoles filaires pour les bases de données les plus courantes. Les bases de données incluent le stockage Table Azure, MongoDB, Apache Cassandra et Gremlin. Celles-ci n’offrent pas de modèle de cohérence défini avec précision, ni de garantie reposant sur un contrat SLA pour les niveaux de cohérence. Elles ne fournissent généralement qu’un sous-ensemble des cinq modèles de cohérence offerts par Azure Cosmos DB. Pour les API SQL, Gremlin et Table, le niveau de cohérence configuré par défaut sur le compte Azure Cosmos est utilisé. 

Les sections suivantes présentent le mappage entre la cohérence de données demandée par un pilote client OSS pour Apache Cassandra et MongoDB et les niveaux de cohérence correspondants dans Azure Cosmos DB.

## <a id="cassandra-mapping"></a>Mappage entre les niveaux de cohérence Apache Cassandra et Azure Cosmos DB

Le tableau ci-dessous décrit les différentes combinaisons de cohérence possibles avec l'API Cassandra et le mappage de niveau de cohérence natif équivalent de Cosmos DB. Toutes les combinaisons de modes d'écriture et de lecture Apache Cassandra sont nativement prises en charge par Cosmos DB. Pour toutes les combinaisons de modèles de cohérence en écriture et en lecture Apache Cassandra, Cosmos DB fournit des garanties de cohérence supérieures ou égales à celles d'Apache Cassandra. En outre, Cosmos DB offre des garanties de durabilité supérieures à celles d'Apache Cassandra, même en mode d'écriture minimum.

Le tableau suivant présente le **Mappage de cohérence en écriture** entre Azure Cosmos DB et Cassandra :

| Cassandra | Azure Cosmos DB | Garantie |
| - | - | - |
|ALL|Remarque  | Linéarisabilité |
| EACH_QUORUM   | Remarque    | Linéarisabilité | 
| QUORUM, SERIAL |  Remarque |    Linéarisabilité |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | Préfixe cohérent |Préfixe cohérent global |
| EACH_QUORUM   | Remarque    | Linéarisabilité |
| QUORUM, SERIAL |  Remarque |    Linéarisabilité |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | Préfixe cohérent | Préfixe cohérent global |
| QUORUM, SERIAL | Remarque   | Linéarisabilité |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | Préfixe cohérent | Préfixe cohérent global |
| LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE    | Obsolescence limitée | <ul><li>Obsolescence limitée.</li><li>Au plus des versions K ou d'un retard de temps.</li><li>Consultez la dernière valeur validée dans la région.</li></ul> |
| ONE, LOCAL_ONE, ANY   | Préfixe cohérent | Préfixe cohérent par région |

Le tableau suivant présente le **Mappage de cohérence en lecture** entre Azure Cosmos DB et Cassandra :

| Cassandra | Azure Cosmos DB | Garantie |
| - | - | - |
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO, ONE, LOCAL_ONE | Remarque  | Linéarisabilité|
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO   |Remarque |   Linéarisabilité |
|LOCAL_ONE, ONE | Préfixe cohérent | Préfixe cohérent global |
| ALL, QUORUM, SERIAL   | Remarque    | Linéarisabilité |
| LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE |  Préfixe cohérent   | Préfixe cohérent global |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM |    Préfixe cohérent   | Préfixe cohérent global |
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO   |Remarque |   Linéarisabilité |
| LOCAL_ONE, ONE    | Préfixe cohérent | Préfixe cohérent global|
| ALL, QUORUM, SERIAL   Forte linéarisabilité
LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE  |Préfixe cohérent  | Préfixe cohérent global |
|ALL    |Remarque |Linéarisabilité |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  |Préfixe cohérent  |Préfixe cohérent global|
|ALL, QUORUM, SERIAL    Forte linéarisabilité
LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE  |Préfixe cohérent  |Préfixe cohérent global |
|ALL    |Remarque | Linéarisabilité |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  | Préfixe cohérent | Préfixe cohérent global |
| QUORUM, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE |  Obsolescence limitée   | <ul><li>Obsolescence limitée.</li><li>Au plus des versions K ou d'un retard de temps. </li><li>Consultez la dernière valeur validée dans la région.</li></ul>
| LOCAL_ONE, ONE |Préfixe cohérent | Préfixe cohérent par région |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  | Préfixe cohérent | Préfixe cohérent par région |


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