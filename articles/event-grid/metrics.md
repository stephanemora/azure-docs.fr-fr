---
title: Métriques prises en charge par Azure Event Grid
description: Cet article liste les métriques Azure Monitor qui sont prises en charge par le service Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 3b22beafc9f88d2d95b25fd7ad2f2308a4df9097
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116418"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Métriques prises en charge par Azure Event Grid
Cet article liste les métriques Event Grid classées par espaces de noms. 

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|Événements publiés|Count|Total|Nombre total d’événements publiés dans cette rubrique|Rubrique|
|PublishFailCount|Événements d'échec de la publication|Count|Total|Nombre total d’événements ayant échoué à publier dans cette rubrique|Topic,ErrorType,Error|
|PublishSuccessLatencyInMs|Latence de réussite de la publication|Millisecondes|Total|Latence de réussite de la publication en millisecondes|None|
|MatchedEventCount|Événements correspondants|Count|Total|Nombre total d’événements correspondant à cet abonnement aux événements|Topic,EventSubscriptionName,DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Événements d’échec de la remise|Count|Total|Nombre total d’événements ayant échoué dans la remise à cet abonnement aux événements|Topic,EventSubscriptionName,DomainEventSubscriptionName,Error,ErrorType|
|DeliverySuccessCount|Événements remis|Count|Total|Nombre total d’événements remis à cet abonnement aux événements|Topic,EventSubscriptionName,DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Durée du traitement de la destination|Millisecondes|Average|Durée du traitement de la destination en millisecondes|Topic,EventSubscriptionName,DomainEventSubscriptionName|
|DroppedEventCount|Événements annulés|Count|Total|Nombre total d’événements annulés correspondant à cet abonnement aux événements|Topic,EventSubscriptionName,DomainEventSubscriptionName,DropReason|
|DeadLetteredCount|Événements de lettres mortes|Count|Total|Nombre total d’événements de lettres mortes correspondant à cet abonnement aux événements|Topic,EventSubscriptionName,DomainEventSubscriptionName,DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|Événements publiés|Count|Total|Nombre total d’événements publiés dans cette rubrique|None|
|PublishFailCount|Événements d'échec de la publication|Count|Total|Nombre total d’événements ayant échoué à publier dans cette rubrique|ErrorType,Error|
|UnmatchedEventCount|Événements sans correspondance|Count|Total|Nombre total d’événements ne correspondant à aucun des abonnements aux événements pour cette rubrique|None|
|PublishSuccessLatencyInMs|Latence de réussite de la publication|Millisecondes|Total|Latence de réussite de la publication en millisecondes|None|
|MatchedEventCount|Événements correspondants|Count|Total|Nombre total d’événements correspondant à cet abonnement aux événements|EventSubscriptionName|
|DeliveryAttemptFailCount|Événements d’échec de la remise|Count|Total|Nombre total d’événements ayant échoué dans la remise à cet abonnement aux événements|Error,ErrorType,EventSubscriptionName|
|DeliverySuccessCount|Événements remis|Count|Total|Nombre total d’événements remis à cet abonnement aux événements|EventSubscriptionName|
|DestinationProcessingDurationInMs|Durée du traitement de la destination|Millisecondes|Average|Durée du traitement de la destination en millisecondes|EventSubscriptionName|
|DroppedEventCount|Événements annulés|Count|Total|Nombre total d’événements annulés correspondant à cet abonnement aux événements|DropReason, EventSubscriptionName|
|DeadLetteredCount|Événements de lettres mortes|Count|Total|Nombre total d’événements de lettres mortes correspondant à cet abonnement aux événements|DeadLetterReason, EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|Événements publiés|Count|Total|Nombre total d’événements publiés dans cette rubrique|None|
|PublishFailCount|Événements d'échec de la publication|Count|Total|Nombre total d’événements ayant échoué à publier dans cette rubrique|ErrorType, Error|
|UnmatchedEventCount|Événements sans correspondance|Count|Total|Nombre total d’événements ne correspondant à aucun des abonnements aux événements pour cette rubrique|None|
|PublishSuccessLatencyInMs|Latence de réussite de la publication|Millisecondes|Total|Latence de réussite de la publication en millisecondes|None|
|MatchedEventCount|Événements correspondants|Count|Total|Nombre total d’événements correspondant à cet abonnement aux événements|EventSubscriptionName|
|DeliveryAttemptFailCount|Événements d’échec de la remise|Count|Total|Nombre total d’événements ayant échoué dans la remise à cet abonnement aux événements|Error,ErrorType,EventSubscriptionName|
|DeliverySuccessCount|Événements remis|Count|Total|Nombre total d’événements remis à cet abonnement aux événements|EventSubscriptionName|
|DestinationProcessingDurationInMs|Durée du traitement de la destination|Millisecondes|Average|Durée du traitement de la destination en millisecondes|EventSubscriptionName|
|DroppedEventCount|Événements annulés|Count|Total|Nombre total d’événements annulés correspondant à cet abonnement aux événements|DropReason, EventSubscriptionName|
|DeadLetteredCount|Événements de lettres mortes|Count|Total|Nombre total d’événements de lettres mortes correspondant à cet abonnement aux événements|DeadLetterReason, EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|MatchedEventCount|Événements correspondants|Count|Total|Nombre total d’événements correspondant à cet abonnement aux événements|None|
|DeliveryAttemptFailCount|Événements d’échec de la remise|Count|Total|Nombre total d’événements ayant échoué dans la remise à cet abonnement aux événements|Error, ErrorType|
|DeliverySuccessCount|Événements remis|Count|Total|Nombre total d’événements remis à cet abonnement aux événements|None|
|DestinationProcessingDurationInMs|Durée du traitement de la destination|Millisecondes|Average|Durée du traitement de la destination en millisecondes|None|
|DroppedEventCount|Événements annulés|Count|Total|Nombre total d’événements annulés correspondant à cet abonnement aux événements|DropReason|
|DeadLetteredCount|Événements de lettres mortes|Count|Total|Nombre total d’événements de lettres mortes correspondant à cet abonnement aux événements|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|Événements publiés|Count|Total|Nombre total d’événements publiés dans cette rubrique|None|
|PublishFailCount|Événements d'échec de la publication|Count|Total|Nombre total d’événements ayant échoué à publier dans cette rubrique|ErrorType, Error|
|UnmatchedEventCount|Événements sans correspondance|Count|Total|Nombre total d’événements ne correspondant à aucun des abonnements aux événements pour cette rubrique|None|
|PublishSuccessLatencyInMs|Latence de réussite de la publication|Millisecondes|Total|Latence de réussite de la publication en millisecondes|None|

## <a name="next-steps"></a>Étapes suivantes
Consultez l’article suivant : [Journaux de diagnostic](diagnostic-logs.md)
