---
title: Métriques prises en charge par Azure Event Grid
description: Cet article liste les métriques Azure Monitor qui sont prises en charge par le service Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: spelluru
ms.openlocfilehash: 643df2f4cc6347e0fd56f9124b68f1888ab85e26
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629758"
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
