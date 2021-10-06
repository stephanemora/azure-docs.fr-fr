---
title: Ajouter des connecteurs pour Confluent Cloud - Solutions partenaires Azure
description: Cet article explique comment installer des connecteurs pour le Confluent Cloud que vous utilisez avec les ressources Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 09/03/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: a5ef809be7518c4d99ff208e769af918fe579d4f
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480693"
---
# <a name="add-connectors-for-confluent-cloud"></a>Ajouter des connecteurs pour Confluent Cloud

Cet article explique comment installer des connecteurs sur des ressources Azure pour Confluent Cloud.

## <a name="connector-to-azure-cosmos-db"></a>Connecteur à Azure Cosmos DB

Le **connecteur entièrement géré du Connecteur récepteur Azure Cosmos DB** est généralement disponible dans le Cloud Confluent. Le connecteur entièrement géré élimine le besoin de développement et de gestion des intégrations personnalisées, et réduit le fardeau opérationnel global de la connexion de vos données entre le Cloud Confluent et Cosmos DB Azure. Le Connecteur récepteur Cosmos Microsoft Azure pour le Cloud Confluent lit et écrit les données dans une base de données Cosmos Microsoft Azure. Le connecteur interroge les données de Kafka et écrit dans les conteneurs de base de données.

Pour configurer votre connecteur, consultez [Connecteur récepteur Cosmos DB Azure pour le Cloud Confluent](https://docs.confluent.io/cloud/current/connectors/cc-azure-cosmos-sink.html).

Le **connecteur auto-géré Azure Cosmos DB** doit être installé manuellement. Commencez par télécharger un uber JAR à partir de la [page des versions de Cosmos DB](https://github.com/microsoft/kafka-connect-cosmosdb/releases). Ou, vous pouvez également [créer votre propre uber JAR directement à partir du code source](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector). Terminez l’installation en suivant les instructions décrites dans la documentation de Confluent relative à l’[installation manuelle des connecteurs](https://docs.confluent.io/home/connect/install.html#install-connector-manually).  

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir de l’aide pour la résolution des problèmes, consultez [Résolution des problèmes des solutions Apache Kafka pour Confluent Cloud](troubleshoot.md).
