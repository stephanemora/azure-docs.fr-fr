---
title: Résoudre les problèmes liés aux codes d’erreur Azure Stream Analytics
description: Résolvez les problèmes Azure Stream Analytics liés aux codes d'erreur interne.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: c8c792d548fc953cb5d059f322ad1f3139c91562
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595118"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Codes d’erreur interne Azure Stream Analytics

Vous pouvez utiliser les journaux d’activité et les journaux de ressources pour déboguer les comportements inattendus à partir de votre tâche Azure Stream Analytics. Cet article répertorie la description de chaque code d’erreur interne. Les erreurs internes sont des erreurs génériques déclenchées au sein de la plateforme Stream Analytics lorsque Stream Analytics ne peut déterminer s'il s'agit d'une erreur de disponibilité interne ou d'un bogue dans le système.

## <a name="kafkainvalidrequest"></a>KafkaInvalidRequest

* **Cause** : La requête envoyée au serveur Kafka n'est pas valide.

## <a name="kafkainputerror"></a>KafkaInputError

* **Cause** : L'entrée Kafka rencontre un problème.

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **Cause** : La taille de lot utilisée pour écrire dans Cosmos DB est trop grande. 
* **Recommandation** : Réessayez en utilisant une taille de lot plus petite.

## <a name="next-steps"></a>Étapes suivantes

* [Troubleshoot input connections](stream-analytics-troubleshoot-input.md) (Résoudre les problèmes liés aux connexions d’entrée)
* [Résoudre les problèmes liés aux sorties Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Résoudre les problèmes liés aux requêtes Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Résoudre les problèmes liés à Azure Stream Analytics à l’aide des journaux de ressources](stream-analytics-job-diagnostic-logs.md)
* [Erreurs de données Azure Stream Analytics](data-errors.md)