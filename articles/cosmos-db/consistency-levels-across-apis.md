---
title: Niveaux de cohérence et API Azure Cosmos DB
description: Comprendre le mappage des niveaux de cohérence entre les différentes API dans Azure Cosmos DB, Apache Cassandra et MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 8bfff7790fa4984241e1c3dda19a20c8b13591fb
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872177"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Niveaux de cohérence et API Azure Cosmos DB

Azure Cosmos DB fournit la prise en charge native des API compatibles avec les protocoles filaires pour les bases de données les plus courantes. Ces bases de données incluent le stockage Table Azure, MongoDB, Apache Cassandra et Gremlin. Elles n’offrent pas de modèle de cohérence défini avec précision, ni de garantie reposant sur un contrat SLA pour les niveaux de cohérence. Elles ne fournissent généralement qu’un sous-ensemble des cinq modèles de cohérence offerts par Azure Cosmos DB. 

Pour les API SQL, Gremlin et Table, le niveau de cohérence configuré par défaut sur le compte Azure Cosmos est utilisé. 

Lorsque vous utilisez l’API Cassandra ou l’API Azure Cosmos DB pour MongoDB, les applications obtiennent un ensemble complet de niveaux de cohérence proposés par Apache Cassandra et MongoDB, respectivement, avec des garanties encore plus fortes de cohérence et de durabilité. Ce document présente les niveaux de cohérence Azure Cosmos DB correspondants pour les niveaux de cohérence Apache Cassandra et MongoDB.


## <a id="cassandra-mapping"></a>Mappage entre les niveaux de cohérence Apache Cassandra et Azure Cosmos DB

Contrairement à Azure Cosmos DB, Apache Cassandra ne fournit pas en mode natif des garanties de cohérence précises.  Au lieu de cela, Apache Cassandra fournit un niveau de cohérence d’écriture et un niveau de cohérence de lecture, pour permettre les compromis de haute disponibilité, de cohérence et de latence. Lors de l’utilisation de l’API Cassandra Azure Cosmos DB : 

* Le niveau de cohérence d’écriture d’Apache Cassandra est mappé au niveau de cohérence par défaut configuré sur votre compte Azure Cosmos. 

* Azure Cosmos DB mappe de manière dynamique le niveau de cohérence de lecture spécifié par le pilote client Cassandra à l’un des niveaux de cohérence Azure Cosmos DB configurés dynamiquement sur une requête de lecture. 

Le tableau suivant illustre le mappage des niveaux de cohérence Cassandra en mode natif aux niveaux de cohérence d’Azure Cosmos DB lors de l’utilisation de l’API Cassandra :  

[![Mappage du modèle de cohérence Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>Mappage entre les niveaux de cohérence MongoDB et Azure Cosmos DB

Contrairement à Azure Cosmos DB, MongoDB en mode natif ne fournit pas de garanties de cohérence précises. Au lieu de cela, MongoDB en mode natif permet aux utilisateurs de configurer les garanties de cohérence suivantes : problème d’écriture, problème de lecture et directive isMaster - pour diriger les opérations de lecture vers les réplicas principaux ou secondaires dans l’optique d’atteindre le niveau de cohérence souhaité. 

Lorsque vous utilisez l’API Azure Cosmos DB pour MongoDB, le pilote MongoDB traite votre région d’écriture en tant que réplica principal, et toutes les autres régions sont des réplicas en lecture. Vous pouvez choisir la région associée à votre compte Azure Cosmos en tant que réplica principal. 

Lors de l’utilisation de l’API Azure Cosmos DB pour MongoDB :

* Le problème d’écriture est mappé au niveau de cohérence par défaut configuré sur votre compte Azure Cosmos.
 
* Azure Cosmos DB mappe de manière dynamique le problème de lecture spécifié par le pilote client MongoDB à l’un des niveaux de cohérence Azure Cosmos DB configurés dynamiquement sur une requête de lecture. 

* Vous pouvez annoter une région spécifique associée à votre compte Azure Cosmos en tant que « Master » en désignant la région comme première région accessible en écriture. 

Le tableau suivant illustre le mappage des problèmes d’écriture/lecture Cassandra en mode natif aux niveaux de cohérence d’Azure Cosmos lors de l’utilisation de l’API Azure Cosmos DB pour MongoDB :

[![Mappage du modèle de cohérence MongoDB](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les niveaux de cohérence et la compatibilité entre les API Cosmos DB et les API open source. Consultez les articles suivants :

* [Compromis entre disponibilité et performance pour différents niveaux de cohérence](consistency-levels-tradeoffs.md)
* [Fonctionnalités MongoDB prises en charge par l’API d’Azure Cosmos DB pour MongoDB](mongodb-feature-support.md)
* [Fonctionnalités Apache Cassandra prises en charge par l’API Cassandra pour Azure Cosmos DB](cassandra-support.md)
