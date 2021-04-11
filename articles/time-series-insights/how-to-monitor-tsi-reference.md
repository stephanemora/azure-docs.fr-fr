---
title: Supervision des informations de référence sur les données Azure Time Series Insights | Microsoft Docs
description: Documentation de référence pour la supervision d’Azure Time Series Insights.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: bfd0d04313f0b519b4013a43e29d88400c73ea31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591391"
---
# <a name="monitoring-azure-time-series-insights-data-reference"></a>Supervision des informations de référence sur les données Azure Time Series Insights

Découvrez les données et les ressources collectées par Azure Monitor à partir de votre environnement Azure Time Series Insights. Pour plus d’informations sur la collecte et l’analyse des données de supervision, consultez [Supervision d’Azure Time Series Insights]( ./how-to-monitor-tsi.md).

## <a name="metrics"></a>Mesures

Cette section liste toutes les métriques de plateforme collectées automatiquement pour Azure Time Series Insights. Pour obtenir la liste de toutes les métriques de prise en charge d’Azure Monitor (notamment celles d’Azure Time Series Insights), consultez [Métriques prises en charge par Azure Monitor](../azure-monitor/essentials/metrics-supported.md). Le fournisseur de ressources pour ces métriques est [Microsoft.TimeSeriesInsights/environments/eventsources](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironmentseventsources) et [Microsoft.TimeSeriesInsights/environments](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironments).


### <a name="ingress"></a>Entrée
 
|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|IngressReceivedBytes|Octets reçus en entrée|Octets|Total|Nombre d’octets lus à partir de la source de l’événement|
|IngressReceivedInvalidMessages|Messages non valides reçus en entrée|Count|Total|Nombre de messages non valides lus à partir de la source de l’événement|
|IngressReceivedMessages|Messages reçus en entrée|Count|Total|Nombre de messages lus à partir de la source de l’événement|
|IngressReceivedMessagesCountLag|Décalage de nombre des messages reçus en entrée|Count|Average|Différence entre le numéro de séquence du dernier message placé en file d’attente dans la partition source de l’événement et le numéro de séquence des messages traités en entrée|
|IngressReceivedMessagesTimeLag|Retard des messages reçus en entrée|Secondes|Maximale|Différence entre l’heure à laquelle le message est placé en file d’attente dans la source d’événement et l’heure à laquelle il est traité en entrée|
|IngressStoredBytes|Octets stockés en entrée|Octets|Total|Taille totale des événements traités correctement et disponibles pour une requête|
|IngressStoredEvents|Événements stockés en entrée|Count|Total|Nombre d’événements aplatis traités correctement et disponibles pour une requête|

### <a name="storage"></a>Stockage

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|WarmStorageMaxProperties|Propriétés max de stockage chaud|Count|Maximale|Nombre maximal de propriétés utilisées autorisées par l’environnement pour la référence SKU S1/S2 et nombre maximal de propriétés autorisées par le magasin Warm pour la référence SKU PAYG|
|WarmStorageUsedProperties|Propriétés de stockage chaud utilisées |Count|Maximale|Nombre de propriétés utilisées par l’environnement pour la référence SKU S1/S2 et nombre de propriétés utilisées par le magasin Warm pour la référence SKU PAYG|

## <a name="resource-logs"></a>Journaux d’activité de ressources

Cette section liste les types de journaux de ressources que vous pouvez collecter pour votre environnement Azure Time Series Insights.

| Category | Nom d’affichage | Description |
|----- |----- |----- |
| Entrée | TSIIngress | La catégorie Ingress (Entrée) effectue le suivi des erreurs qui se produisent dans le pipeline d’entrée. Cette catégorie comprend les erreurs qui se produisent lors de la réception d’événements (comme les échecs de connexion à une source d’événement) et le traitement d’événements (comme les erreurs survenant lors de l’analyse d’une charge utile d’événement). |

## <a name="schemas"></a>Schémas
Les schémas suivants sont utilisés par Azure Time Series Insights

### <a name="tsiingress-table"></a>Table TSIIngress

| Propriété | Description |
|----- |----- |
| TimeGenerated | Heure (UTC) à laquelle cet événement est généré. |
| Emplacement | Emplacement de la ressource. |
| Category | Catégorie de l’événement de journal. |
| NomOpération | Nom d’opération de l’événement. |
| CorrelationId | ID de corrélation de la requête. |
| Level | Niveau de gravité de l’événement. |
| ResultDescription | Description du résultat de l’opération, par exemple « Réception d’une erreur d’interdiction ». |
| Message | Message associé à l’erreur. Inclut des informations sur la cause du problème et sur la façon de résoudre l’erreur. |
| ErrorCode | Code associé à l’erreur |
| EventSourceType | Type de source d’événements. Il peut s’agir d’un hub d’événements ou d’un hub IoT. |
| EventSourceProperties | Collection de propriétés spécifiques à votre source d’événement. Contient des détails comme le groupe de consommateurs et le nom de la clé d’accès. |
