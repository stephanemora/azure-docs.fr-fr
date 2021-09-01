---
title: Ajouter des connecteurs pour Confluent Cloud - Solutions partenaires Azure
description: Cet article explique comment installer des connecteurs pour le Confluent Cloud que vous utilisez avec les ressources Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: c4a15a54c5b9fb3b73a3fcaf81d63a9e176b49c5
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112534726"
---
# <a name="add-connectors-for-confluent-cloud"></a>Ajouter des connecteurs pour Confluent Cloud

Cet article explique comment installer des connecteurs sur des ressources Azure pour Confluent Cloud.

## <a name="connector-to-azure-cosmos-db"></a>Connecteur à Azure Cosmos DB

À partir du client Confluent Hub, installez le connecteur Cosmos DB comme recommandé dans l’[offre Confluent Hub](https://www.confluent.io/hub/microsoftcorporation/kafka-connect-cosmos). 

Pour installer le connecteur manuellement, commencez par télécharger un uber JAR à partir de la [page des versions de Cosmos DB](https://github.com/microsoft/kafka-connect-cosmosdb/releases). Vous pouvez également [créer votre propre uber JAR directement à partir du code source](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector). Terminez l’installation en suivant les instructions décrites dans la documentation de Confluent relative à l’[installation manuelle des connecteurs](https://docs.confluent.io/home/connect/install.html#install-connector-manually).  

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir de l’aide pour la résolution des problèmes, consultez [Résolution des problèmes des solutions Apache Kafka pour Confluent Cloud](troubleshoot.md).
