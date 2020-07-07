---
title: Codes d’erreur de disponibilité externe - Azure Stream Analytics
description: Résolvez les problèmes Azure Stream Analytics liés aux codes d’erreur de disponibilité externe.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 1ce867d60c53b9befe5b4480693b54d2c9eba018
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "83650054"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Codes d’erreur de disponibilité externe Azure Stream Analytics

Vous pouvez utiliser les journaux d’activité et les journaux de ressources pour déboguer les comportements inattendus à partir de votre tâche Azure Stream Analytics. Cet article répertorie la description de chaque code d’erreur de disponibilité externe. Les erreurs de disponibilité externe se produisent lorsqu’un service dépendant n’est pas disponible.

## <a name="externalserviceunavailable"></a>ExternalServiceUnavailable

* **Cause** : Un service est temporairement indisponible.
* **Recommandation** : Stream Analytics continuera d'essayer d’atteindre le service.

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **Cause** : Stream Analytics a rencontré une erreur pendant la communication avec EventHub. 


## <a name="next-steps"></a>Étapes suivantes

* [Troubleshoot input connections](stream-analytics-troubleshoot-input.md) (Résoudre les problèmes liés aux connexions d’entrée)
* [Résoudre les problèmes liés aux sorties Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Résoudre les problèmes liés aux requêtes Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Résoudre les problèmes liés à Azure Stream Analytics à l’aide des journaux de ressources](stream-analytics-job-diagnostic-logs.md)
* [Erreurs de données Azure Stream Analytics](data-errors.md)
