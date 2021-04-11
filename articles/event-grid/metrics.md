---
title: Métriques prises en charge par Azure Event Grid
description: Cet article liste les métriques Azure Monitor qui sont prises en charge par le service Azure Event Grid.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 321e318f9dab87fde20b33a6a3a906b020ada622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588730"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Métriques prises en charge par Azure Event Grid
Cet article liste les métriques Event Grid classées par espaces de noms. 

## <a name="system-topics"></a>Rubriques sur le système

|Métrique|Exportable par le biais des paramètres de diagnostic ?|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Oui|Évaluations de filtres avancés|Count|Total|Nombre total de filtres avancés évalués entre les abonnements aux événements pour cette rubrique.|EventSubscriptionName|
|DeadLetteredCount|Oui|Événements de lettres mortes|Count|Total|Nombre total d’événements de lettres mortes correspondant à cet abonnement aux événements|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Non|Événements d’échec de la remise|Count|Total|Nombre total d’événements ayant échoué dans la remise à cet abonnement aux événements|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Oui|Événements remis|Count|Total|Nombre total d’événements remis à cet abonnement aux événements|EventSubscriptionName|
|DestinationProcessingDurationInMs|Non|Durée du traitement de la destination|Millisecondes|Average|Durée du traitement de la destination en millisecondes|EventSubscriptionName|
|DroppedEventCount|Oui|Événements annulés|Count|Total|Nombre total d’événements annulés correspondant à cet abonnement aux événements|DropReason, EventSubscriptionName|
|MatchedEventCount|Oui|Événements correspondants|Count|Total|Nombre total d’événements correspondant à cet abonnement aux événements|EventSubscriptionName|
|PublishFailCount|Oui|Événements d'échec de la publication|Count|Total|Nombre total d’événements ayant échoué à publier dans cette rubrique|ErrorType, Error|
|PublishSuccessCount|Oui|Événements publiés|Count|Total|Nombre total d’événements publiés dans cette rubrique|Aucune dimension|
|PublishSuccessLatencyInMs|Oui|Latence de réussite de la publication|Millisecondes|Total|Latence de réussite de la publication en millisecondes|Aucune dimension|
|UnmatchedEventCount|Oui|Événements sans correspondance|Count|Total|Nombre total d’événements ne correspondant à aucun des abonnements aux événements pour cette rubrique|Aucune dimension|


## <a name="custom-topics"></a>Rubriques personnalisées

|Métrique|Exportable par le biais des paramètres de diagnostic ?|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Oui|Évaluations de filtres avancés|Count|Total|Nombre total de filtres avancés évalués entre les abonnements aux événements pour cette rubrique.|EventSubscriptionName|
|DeadLetteredCount|Oui|Événements de lettres mortes|Count|Total|Nombre total d’événements de lettres mortes correspondant à cet abonnement aux événements|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Non|Événements d’échec de la remise|Count|Total|Nombre total d’événements ayant échoué dans la remise à cet abonnement aux événements|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Oui|Événements remis|Count|Total|Nombre total d’événements remis à cet abonnement aux événements|EventSubscriptionName|
|DestinationProcessingDurationInMs|Non|Durée du traitement de la destination|Millisecondes|Average|Durée du traitement de la destination en millisecondes|EventSubscriptionName|
|DroppedEventCount|Oui|Événements annulés|Count|Total|Nombre total d’événements annulés correspondant à cet abonnement aux événements|DropReason, EventSubscriptionName|
|MatchedEventCount|Oui|Événements correspondants|Count|Total|Nombre total d’événements correspondant à cet abonnement aux événements|EventSubscriptionName|
|PublishFailCount|Oui|Événements d'échec de la publication|Count|Total|Nombre total d’événements ayant échoué à publier dans cette rubrique|ErrorType, Error|
|PublishSuccessCount|Oui|Événements publiés|Count|Total|Nombre total d’événements publiés dans cette rubrique|Aucune dimension|
|PublishSuccessLatencyInMs|Oui|Latence de réussite de la publication|Millisecondes|Total|Latence de réussite de la publication en millisecondes|Aucune dimension|
|UnmatchedEventCount|Oui|Événements sans correspondance|Count|Total|Nombre total d’événements ne correspondant à aucun des abonnements aux événements pour cette rubrique|Aucune dimension|

## <a name="domains"></a>Domaines

|Métrique|Exportable par le biais des paramètres de diagnostic ?|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Oui|Évaluations de filtres avancés|Count|Total|Nombre total de filtres avancés évalués entre les abonnements aux événements pour cette rubrique.|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|Oui|Événements de lettres mortes|Count|Total|Nombre total d’événements de lettres mortes correspondant à cet abonnement aux événements|Topic, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|Non|Événements d’échec de la remise|Count|Total|Nombre total d’événements ayant échoué dans la remise à cet abonnement aux événements|Topic, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|Oui|Événements remis|Count|Total|Nombre total d’événements remis à cet abonnement aux événements|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Non|Durée du traitement de la destination|Millisecondes|Average|Durée du traitement de la destination en millisecondes|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Oui|Événements annulés|Count|Total|Nombre total d’événements annulés correspondant à cet abonnement aux événements|Topic, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Oui|Événements correspondants|Count|Total|Nombre total d’événements correspondant à cet abonnement aux événements|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Oui|Événements d'échec de la publication|Count|Total|Nombre total d’événements ayant échoué à publier dans cette rubrique|Topic, ErrorType, Error|
|PublishSuccessCount|Oui|Événements publiés|Count|Total|Nombre total d’événements publiés dans cette rubrique|Rubrique|
|PublishSuccessLatencyInMs|Oui|Latence de réussite de la publication|Millisecondes|Total|Latence de réussite de la publication en millisecondes|Aucune dimension|

## <a name="event-subscriptions"></a>Abonnements à des événements

|Métrique|Exportable par le biais des paramètres de diagnostic ?|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Oui|Événements de lettres mortes|Count|Total|Nombre total d’événements de lettres mortes correspondant à cet abonnement aux événements|DeadLetterReason|
|DeliveryAttemptFailCount|Non|Événements d’échec de la remise|Count|Total|Nombre total d’événements ayant échoué dans la remise à cet abonnement aux événements|Error, ErrorType|
|DeliverySuccessCount|Oui|Événements remis|Count|Total|Nombre total d’événements remis à cet abonnement aux événements|Aucune dimension|
|DestinationProcessingDurationInMs|Non|Durée du traitement de la destination|Millisecondes|Average|Durée du traitement de la destination en millisecondes|Aucune dimension|
|DroppedEventCount|Oui|Événements annulés|Count|Total|Nombre total d’événements annulés correspondant à cet abonnement aux événements|DropReason|
|MatchedEventCount|Oui|Événements correspondants|Count|Total|Nombre total d’événements correspondant à cet abonnement aux événements|Aucune dimension|


## <a name="extension-topics"></a>Rubriques relatives à l'extension

|Métrique|Exportable par le biais des paramètres de diagnostic ?|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|---|
|PublishFailCount|Oui|Événements d'échec de la publication|Count|Total|Nombre total d’événements ayant échoué à publier dans cette rubrique|ErrorType, Error|
|PublishSuccessCount|Oui|Événements publiés|Count|Total|Nombre total d’événements publiés dans cette rubrique|Aucune dimension|
|PublishSuccessLatencyInMs|Oui|Latence de réussite de la publication|Millisecondes|Total|Latence de réussite de la publication en millisecondes|Aucune dimension|
|UnmatchedEventCount|Oui|Événements sans correspondance|Count|Total|Nombre total d’événements ne correspondant à aucun des abonnements aux événements pour cette rubrique|Aucune dimension|

## <a name="partner-namespaces"></a>Espaces de noms de partenaires

|Métrique|Exportable par le biais des paramètres de diagnostic ?|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Oui|Événements de lettres mortes|Count|Total|Nombre total d’événements de lettres mortes correspondant à cet abonnement aux événements|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Non|Événements d’échec de la remise|Count|Total|Nombre total d’événements ayant échoué dans la remise à cet abonnement aux événements|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Oui|Événements remis|Count|Total|Nombre total d’événements remis à cet abonnement aux événements|EventSubscriptionName|
|DestinationProcessingDurationInMs|Non|Durée du traitement de la destination|Millisecondes|Average|Durée du traitement de la destination en millisecondes|EventSubscriptionName|
|DroppedEventCount|Oui|Événements annulés|Count|Total|Nombre total d’événements annulés correspondant à cet abonnement aux événements|DropReason, EventSubscriptionName|
|MatchedEventCount|Oui|Événements correspondants|Count|Total|Nombre total d’événements correspondant à cet abonnement aux événements|EventSubscriptionName|
|PublishFailCount|Oui|Événements d'échec de la publication|Count|Total|Nombre total d’événements ayant échoué à publier dans cette rubrique|ErrorType, Error|
|PublishSuccessCount|Oui|Événements publiés|Count|Total|Nombre total d’événements publiés dans cette rubrique|Aucune dimension|
|PublishSuccessLatencyInMs|Oui|Latence de réussite de la publication|Millisecondes|Total|Latence de réussite de la publication en millisecondes|Aucune dimension|
|UnmatchedEventCount|Oui|Événements sans correspondance|Count|Total|Nombre total d’événements ne correspondant à aucun des abonnements aux événements pour cette rubrique|Aucune dimension|


## <a name="partner-topics"></a>Rubriques de partenaire

|Métrique|Exportable par le biais des paramètres de diagnostic ?|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Oui|Évaluations de filtres avancés|Count|Total|Nombre total de filtres avancés évalués entre les abonnements aux événements pour cette rubrique.|EventSubscriptionName|
|DeadLetteredCount|Oui|Événements de lettres mortes|Count|Total|Nombre total d’événements de lettres mortes correspondant à cet abonnement aux événements|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Non|Événements d’échec de la remise|Count|Total|Nombre total d’événements ayant échoué dans la remise à cet abonnement aux événements|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Oui|Événements remis|Count|Total|Nombre total d’événements remis à cet abonnement aux événements|EventSubscriptionName|
|DestinationProcessingDurationInMs|Non|Durée du traitement de la destination|Millisecondes|Average|Durée du traitement de la destination en millisecondes|EventSubscriptionName|
|DroppedEventCount|Oui|Événements annulés|Count|Total|Nombre total d’événements annulés correspondant à cet abonnement aux événements|DropReason, EventSubscriptionName|
|MatchedEventCount|Oui|Événements correspondants|Count|Total|Nombre total d’événements correspondant à cet abonnement aux événements|EventSubscriptionName|
|PublishFailCount|Oui|Événements d'échec de la publication|Count|Total|Nombre total d’événements ayant échoué à publier dans cette rubrique|ErrorType, Error|
|PublishSuccessCount|Oui|Événements publiés|Count|Total|Nombre total d’événements publiés dans cette rubrique|Aucune dimension|
|UnmatchedEventCount|Oui|Événements sans correspondance|Count|Total|Nombre total d’événements ne correspondant à aucun des abonnements aux événements pour cette rubrique|Aucune dimension|


## <a name="next-steps"></a>Étapes suivantes
Consultez l’article suivant : [Journaux de diagnostic](diagnostic-logs.md)
