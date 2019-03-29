---
title: Niveaux de cohérence et API Azure Cosmos DB
description: Comprendre les niveaux de cohérence entre les API dans Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: 902303a8f55f4494e0cc6c21b0438e41437c0567
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620663"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Niveaux de cohérence et API Azure Cosmos DB

Azure Cosmos DB fournit la prise en charge native pour fil protocole compatible avec les API pour les bases de données les plus courants. Citons notamment MongoDB, Apache Cassandra, Gremlin et stockage Table Azure. Ces bases de données n’offrent pas précisément les modèles de cohérence définis ou les garanties de reposant sur le contrat SLA pour les niveaux de cohérence. Elles ne fournissent généralement qu’un sous-ensemble des cinq modèles de cohérence offerts par Azure Cosmos DB. 

Lorsque vous utilisez l’API SQL, les API Gremlin et les API de Table, le niveau de cohérence par défaut configuré sur le compte Azure Cosmos est utilisé. 

Lorsque vous utilisez les API de l’API Cassandra ou Azure Cosmos DB pour MongoDB, applications obtiennent un ensemble complet de niveaux de cohérence proposés par Apache Cassandra et MongoDB, respectivement, avec encore plus forte garanties de cohérence et durabilité. Ce document présente les niveaux de cohérence Azure Cosmos DB correspondants pour Apache Cassandra et MongoDB les niveaux de cohérence.


## <a id="cassandra-mapping"></a>Mappage entre les niveaux de cohérence Apache Cassandra et Azure Cosmos DB

Contrairement à AzureCosmos DB, Apache Cassandra ne fournit pas en mode natif avec précision les garanties de cohérence définis.  Au lieu de cela, Apache Cassandra fournit un niveau de cohérence d’écriture et un niveau de cohérence de lecture, pour activer les compromis de disponibilité, la cohérence et la latence élevées. Lorsque vous utilisez l’API Cassandra Azure Cosmos DB : 

* Le niveau de cohérence d’écriture de Apache Cassandra est mappé vers le niveau de cohérence par défaut configuré sur votre compte Azure Cosmos. 

* Azure Cosmos DB mappera dynamiquement le niveau de cohérence de lecture spécifié par le pilote du client Cassandra à un des niveaux de cohérence Azure Cosmos DB configurés dynamiquement sur une demande de lecture. 

Le tableau suivant illustre comment les niveaux de cohérence Cassandra natifs sont mappés aux niveaux de cohérence de l’Azure Cosmos DB lors de l’utilisation de l’API Cassandra :  

[ ![Mappage de modèle de cohérence Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png) ](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>Le mappage entre les niveaux de cohérence MongoDB et Azure Cosmos DB

Contrairement à Azure Cosmos DB, MongoDB natif ne fournit pas de garanties de cohérence définis avec précision. Au lieu de cela, MongoDB natif permet aux utilisateurs de configurer les garanties de cohérence suivant : un problème d’écriture, un problème de lecture et la directive isMaster - pour diriger les opérations de lecture aux réplicas principaux ou secondaires pour atteindre le niveau de cohérence souhaité. 

Lorsque vous utilisez l’API d’Azure Cosmos DB pour MongoDB, le pilote MongoDB traite votre région d’écriture en tant que le réplica principal et toutes les autres régions sont lues de réplica. Vous pouvez choisir quelle région associée à votre compte Azure Cosmos en tant qu’un réplica principal. 

Lors de l’utilisation des API d’Azure Cosmos DB pour MongoDB :

* Le problème d’écriture est mappé sur le niveau de cohérence par défaut configuré sur votre compte Azure Cosmos.
 
* Azure Cosmos DB mappera dynamiquement le problème de lecture spécifié par le pilote du client MongoDB à un des niveaux de cohérence Azure Cosmos DB configuré dynamiquement sur une demande de lecture. 

* Vous pouvez annoter une région spécifique associée à votre compte Azure Cosmos en tant que « Master » en rendant la région que la première région accessible en écriture. 

Le tableau suivant illustre comment le MongoDB natif lecture/écriture préoccupations sont mappés aux niveaux de cohérence Azure Cosmos lors de l’utilisation des API d’Azure Cosmos DB pour MongoDB :

[ ![Mappage de modèle de cohérence de MongoDB](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png) ](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les niveaux de cohérence et la compatibilité entre les API Cosmos DB et les API open source. Consultez les articles suivants :

* [Compromis entre disponibilité et performance pour différents niveaux de cohérence](consistency-levels-tradeoffs.md)
* [Fonctionnalités MongoDB prises en charge par l’API d’Azure Cosmos DB pour MongoDB](mongodb-feature-support.md)
* [Fonctionnalités Apache Cassandra prises en charge par l’API Cassandra pour Azure Cosmos DB](cassandra-support.md)