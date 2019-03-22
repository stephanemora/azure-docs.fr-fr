---
title: Configuration des stratégies de l’organisation pour Azure Stream Analytique des événements
description: Cet article explique comment configurer le même ordre des paramètres dans Stream Analytique
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 970eeb871775e24abb87c8b977e214645e514d3b
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190532"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Configuration des stratégies de l’organisation pour Azure Stream Analytique des événements

Cet article décrit comment configurer et utiliser d’arrivée tardive et stratégies de désordre événements dans Azure Stream Analytique. Ces stratégies sont appliquées uniquement lorsque vous utilisez le [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) clause dans votre requête.

## <a name="event-time-and-arrival-time"></a>Heure de l’événement et l’heure d’arrivée

Votre travail Stream Analytique peut traiter les événements en fonction *heure de l’événement* ou *heure d’arrivée*. **Heure de l’événement/application** est l’horodatage présent dans la charge utile d’événement (lorsque l’événement a été généré). **Heure d’arrivée** est l’horodatage lorsque l’événement a été reçu à la source d’entrée (stockage Event Hubs/IoT Hub/Blob). 

Par défaut, Stream Analytique traite les événements par *heure d’arrivée*, mais vous pouvez choisir de traiter les événements par *heure de l’événement* à l’aide de [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) clause dans votre requête. Les stratégies d’arrivée et de désordre tardive sont appliquent uniquement si vous traitez des événements par heure de l’événement. Considérez les spécifications de latence et l’exactitude pour votre scénario lorsque vous configurez ces paramètres. 

## <a name="what-is-late-arrival-policy"></a>Qu’est la stratégie d’arrivée tardive ?

Parfois, les événements arrivent en retard pour diverses raisons. Par exemple, un événement qui arrivent en retard de 40 secondes aura heure de l’événement = heure 00:10:00 et d’arrivée = 00:10:40. Si vous définissez la stratégie d’arrivée tardive sur 15 secondes, n’importe quel événement arrive plus tard que les 15 secondes est soit supprimé (ne pas traitées par Stream Analytique) ou ont leur heure de l’événement ajustées. Dans l’exemple ci-dessus, comme l’événement est arrivé à 40 secondes plus tard (plus de jeu de stratégie), son heure de l’événement sera ajusté à la valeur maximale de retard arrivée stratégie 00:10:25 (heure d’arrivée - valeur de la stratégie d’arrivée tardive). Stratégie d’arrivée tardive par défaut est 5 secondes.

## <a name="what-is-out-of-order-policy"></a>Qu’est-ordonnés stratégie ? 

Événement peut-être arriver en désordre. Une fois que l’heure de l’événement est ajustée en fonction de stratégie d’arrivée tardive, vous pouvez également choisir Supprimer automatiquement ou d’ajuster les événements en désordre. Si vous définissez cette stratégie sur 8 secondes, tous les événements qui arrivent en désordre, mais dans la fenêtre de 8 secondes sont réorganisés selon l’heure de l’événement. Les événements qui arrivent plus tard seront être supprimés ou ajustés à la valeur maximale des stratégies de désordre. Stratégie de désordre par défaut est 0 seconde. 

## <a name="adjust-or-drop-events"></a>Modifier ou supprimer d’événements

Si des événements arrivent en retard ou ordonnés en fonction des stratégies que vous avez configuré, vous pouvez supprimer ces événements (ne pas traitées par Stream Analytique) ou de leur temps événement ajustée.

Nous voir un exemple de ces stratégies en action.
<br> **Stratégie d’arrivée tardive :** 15 secondes
<br> **Stratégie de la sortie de commande :** 8 secondes

| Épreuve n° | Heure de l'événement | Heure d’arrivée | System.Timestamp | Explication |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Niveau de la liaison tardive et en dehors de la tolérance d’arrivée de l’événement. Par conséquent, heure de l’événement est réglé à la tolérance d’arrivée tardive maximale.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Événement arrivé au plus tard, mais dans le niveau de tolérance. Heure de l’événement ne pas obtenir réglé.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | L’arrivée de l’événement sur l’heure. Aucun ajustement n’est nécessité.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Arrivée de l’événement de désordre, mais dans la tolérance de 8 secondes. Par conséquent, heure de l’événement ne pas ajustée. À des fins d’analytique, cet événement sera considéré comme événement numéro 4 précédent.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Tolérance de désordre et en dehors de 8 secondes d’arrivée de l’événement. Par conséquent, heure de l’événement est ajustée à un nombre maximum de la tolérance de désordre. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Ces paramètres susceptibles de retarder la sortie de mon travail ? 

Oui. Par défaut,-ordonnés stratégie a la valeur zéro (00 minutes et 00 secondes). Si vous modifiez la valeur par défaut, la première sortie de votre travail est retardée par cette valeur (ou version ultérieure). 

Si une des partitions de vos entrées ne reçoit pas les événements, attendez-vous à votre sortie peut être différé par la valeur de stratégie d’arrivée tardive. Pour savoir pourquoi, lisez la section d’erreur InputPartition ci-dessous. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Je vois les messages LateInputEvents dans mon journal d’activité

Ces messages sont affichées pour vous informer que les événements ont arrivée tardive et sont supprimés ou ajustés en fonction de votre configuration. Vous pouvez ignorer ces messages si vous avez configuré des stratégies d’arrivée tardive en conséquence. 

Exemple de ce message est : <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Je vois InputPartitionNotProgressing dans mon journal d’activité

Votre source d’entrée (Event Hub/IoT Hub) probablement comporte plusieurs partitions. Azure Stream Analytique produit une sortie pour l’horodateur t1 uniquement une fois que toutes les partitions qui sont combinées sont au moins à t1. Par exemple, supposons que la requête lit une partition de hub d’événements qui a deux partitions. Une des partitions, P1, a des événements jusqu’au temps t1. L’autre partition, P2, a des événements jusqu'à l’instant t1 + x. La sortie est produite jusqu'à l’instant t1. Mais, s’il existe une Partition explicite par clause PartitionId, les deux partitions progressent indépendamment. 

Lorsque plusieurs partitions du même flux d’entrée sont combinées, la tolérance d’arrivée tardive est la quantité maximale de temps d’attente de chaque partition pour les nouvelles données. S’il existe une partition dans votre concentrateur d’événements, ou si l’IoT Hub ne reçoivent des entrées, la chronologie pour cette partition ne progresse pas tant qu’atteint le seuil de tolérance d’arrivée tardive. Cela retarde la sortie par le seuil de tolérance d’arrivée tardive. Dans ce cas, vous pouvez voir le message suivant :
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Ce message vous informe qu’au moins une partition de votre entrée est vide et retardera votre sortie par le seuil d’arrivée tardive. Pour pallier ce problème, il est recommandé de vous soit : 1. Vérifiez que toutes les partitions de votre Event Hub/IoT Hub recevoir l’entrée. 2. Utilisation de la Partition par clause PartitionID dans votre requête. 

## <a name="next-steps"></a>Étapes suivantes
* [Considérations relatives à la gestion des temps](stream-analytics-time-handling.md)
* [Mesures disponibles dans Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
