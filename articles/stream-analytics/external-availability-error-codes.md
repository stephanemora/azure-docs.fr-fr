---
title: Codes d’erreur de disponibilité externe - Azure Stream Analytics
description: Résolvez les problèmes Azure Stream Analytics liés aux codes d’erreur de disponibilité externe.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 71625c4b81fc0795c376a89397e98659f4c72ff0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020551"
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
