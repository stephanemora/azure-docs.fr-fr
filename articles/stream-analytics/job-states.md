---
title: États des tâches Azure Stream Analytics
description: 'Cet article décrit les quatre états différents d’une tâche Stream Analytics : Exécution, Arrêté, Détérioré et Échec.'
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: fc46b4f24c3c21cda190f80556373991f6c711c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82130938"
---
# <a name="azure-stream-analytics-job-states"></a>États des tâches Azure Stream Analytics

Une tâche Stream Analytics peut être dans un de ces quatre états à un moment donné : Exécution, Arrêté, Détérioré et Échec. Vous pouvez trouver l’état de votre tâche sur la page Vue d’ensemble de la tâche Stream Analytics dans le portail Azure. 

| State | Description | Actions recommandées |
| --- | --- | --- |
| **Exécution** | Votre tâche est en cours d’exécution sur des événements de lecture Azure provenant des sources d’entrée définies, elle les traite et écrit les résultats dans les récepteurs de sortie configurés. | Il est recommandé de suivre les performances de votre tâche en surveillant les [mesures clés](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Arrêté** | Votre tâche est arrêtée et ne traite pas les événements. | N/D | 
| **Détérioré** | Il peut y avoir des problèmes intermittents avec les connexions entrantes et sortantes. Ces erreurs sont appelées des erreurs temporaires ; elles peuvent faire entrer votre travail dans un état Détérioré. Stream Analytics va immédiatement tenter de récupérer de ces erreurs pour revenir à un état Exécution (en l’espace de quelques minutes). Ces erreurs peuvent se produire en raison de problèmes réseau, de la disponibilité d’autres ressources Azure, d’erreurs de désérialisation etc. Les performances de votre tâche peuvent être affectées lorsque la tâche est en état Détérioré.| Vous pouvez examiner les [journaux de diagnostic ou d’activité](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) pour en savoir plus sur la cause de ces erreurs temporaires. Dans le cas des erreurs de désérialisation, il est recommandé d’entreprendre une action corrective pour garantir que les événements ne soient pas mal formés. Si le tâche continue d’atteindre la limite d’utilisation des ressources, essayez d’augmenter le nombre d’unités de recherche ou [parallélisez votre tâche](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). Dans d’autres cas où vous ne pouvez pas intervenir, Stream Analytics va tenter de récupérer un état *Exécution*. <br> Vous pouvez utiliser la métrique du [délai en filigrane](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) pour savoir si ces erreurs temporaires ont une incidence sur les performances de votre travail.|
| **Échec** | Votre tâche a rencontré une erreur critique résultant en un état d’échec. Les événements ne sont pas lus ni traités. Les erreurs de runtime sont une cause courante entraînant l’état d’échec des tâches. | Vous pouvez [configurer des alertes](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) afin d’être averti lorsque la tâche entre en état d’échec. <br> <br>Vous pouvez déboguer à l’aide des [journaux d’activité et de ressources](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) pour identifier la cause et résoudre le problème.|

## <a name="next-steps"></a>Étapes suivantes
* [Configurer des alertes pour les travaux Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Mesures disponibles dans Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Résoudre des problèmes à l’aide des journaux d’activité et de ressources](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
