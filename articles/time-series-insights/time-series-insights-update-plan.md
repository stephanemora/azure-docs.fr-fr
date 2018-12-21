---
title: Planification d’Azure Time Series Insights (préversion) - Planifier un environnement Azure Time Series Insights (préversion) | Microsoft Docs
description: Planifier votre environnement Azure Time Series Insights (préversion).
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 1df4847f20329e924352adfe782faa43d10dde98
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53277044"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planifier votre environnement Azure Time Series Insights (préversion)

Cet article décrit les meilleures pratiques pour planifier et commencer à utiliser la préversion d’Azure Time Series Insights.

## <a name="best-practices-for-planning-and-preparation"></a>Meilleures pratiques de planification et de préparation

Pour démarrer avec Time Series Insights, il est important de comprendre les points suivants :

* Ce que vous obtenez lorsque vous approvisionnez un environnement Time Series Insights en préversion.
* Ce que sont vos propriétés ID Time Series et Timestamp.
* Ce qu’est le nouveau modèle Time Series et comment créer le vôtre.
* Comment envoyer des événements de manière efficace dans JSON. 
* Les options de récupération d’urgence de Time Series Insights.

Time Series Insights utilise un modèle de paiement à l’utilisation. Pour plus d’informations sur les frais et sur la capacité, consultez [Tarification de Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-time-series-insights-preview-environment"></a>L’environnement Time Series Insights (préversion)

Lorsque vous approvisionnez un environnement Time Series Insights en préversion, vous créez deux ressources Azure :

* un environnement Time Series Insights préversion
* Un compte Stockage Azure à usage général V1

Pour commencer, vous avez besoin de trois autres éléments :
 
- Un [modèle Time Series](./time-series-insights-update-tsm.md) 
- Une [source d’événements connectée à Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md) 
- Les [événements transférés à la source d’événement](./time-series-insights-send-events.md) qui sont mappés sur le modèle et qui respectent un format JSON valide 

## <a name="configure-your-time-series-ids-and-timestamp-properties"></a>Configurer vos propriétés ID Time Series et Timestamp

Pour créer un environnement Time Series Insights, sélectionnez un ID Time Series. Cette opération fonctionne comme une partition logique pour vos données. Comme indiqué, assurez-vous que votre ID Time Series est prêt.

> [!IMPORTANT]
> Les ID Time Series sont *immuables* et *ne peuvent pas être modifiés ultérieurement*. Vérifiez-les tous avant la sélection finale et leur utilisation.

Vous pouvez sélectionner jusqu’à trois (3) clés pour différencier de manière unique vos ressources. Pour plus d’informations, consultez [Meilleures pratiques pour le choix d’un ID Time Series](./time-series-insights-update-how-to-id.md) et [Stockage et entrée](./time-series-insights-update-storage-ingress.md).

La propriété Timestamp est également importante. Vous pouvez désigner cette propriété lorsque vous ajoutez des sources d’événements. Chaque source d’événement a une propriété Timestamp facultative utilisée pour suivre les sources d’événements. Les valeurs de Timestamp respectent la casse et doivent être mises en forme selon les spécifications propres à chaque source.

> [!TIP]
> Vérifiez les exigences de mise en forme et d’analyse de vos sources d’événements.

Lorsque ce champ est vide, l’heure de mise en file d’attente de l’événement dans la source d’événement est utilisée comme valeur Timestamp de l’événement. Si vous envoyez des données historiques ou des événements par lot, la personnalisation de la propriété Timestamp est plus utile que le temps de file d’attente d’événement par défaut. Pour plus d’informations, consultez l’article [Ajouter des sources d’événements dans IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md). 

## <a name="understand-the-time-series-model"></a>Comprendre le modèle Time Series

Vous pouvez maintenant configurer le modèle Time Series de votre environnement Time Series Insights. Ce nouveau modèle simplifie la recherche et l’analyse des données IoT. Il permet la collecte, la maintenance et l’enrichissement des données de série chronologique et permet de préparer des jeux de données utilisables par le consommateur. Ce modèle utilise les **ID Time Series**, qui sont mappés sur une instance qui associe la ressource unique à des variables, comme les types, et les hiérarchies. En savoir plus sur le nouveau [modèle Time Series](./time-series-insights-update-tsm.md).

Le modèle étant dynamique, il peut être généré à tout moment. Pour démarrer rapidement, générez-le et chargez-le avant d’envoyer les données à Time Series Insights. Pour créer votre modèle, consultez [Utiliser le modèle Time Series](./time-series-insights-update-how-to-tsm.md).

Pour de nombreux clients, le modèle Time Series correspond à un modèle de ressource existant ou à un système ERP déjà en place. Si vous n’avez pas de modèle, une expérience utilisateur prédéfinie est [fournie](https://github.com/Microsoft/tsiclient) pour vous permettre d’être opérationnel rapidement. Pour voir comment un modèle peut vous aider, consultez l’[exemple d’environnement de démonstration](https://insights.timeseries.azure.com/preview/demo). 

## <a name="shape-your-events"></a>Mettre en forme vos événements

Vous pouvez voir la façon dont vous envoyez des événements à Time Series Insights. Dans l’idéal, vos événements sont dénormalisés correctement et efficacement.

Une règle de base :

* stockez les métadonnées dans votre modèle Time Series
* Time Series Mode, les champs d’instance et les événements ne comprennent que les informations nécessaires, comme :
  * ID Time Series
  * Timestamp

Pour plus d’informations, consultez [Événements Shape](./time-series-insights-send-events.md#json).

## <a name="business-disaster-recovery"></a>Récupération d'urgence

Time Series Insights est un service à haute disponibilité qui utilise des redondances au niveau de la région Azure. Aucune configuration n’est nécessaire pour utiliser ces fonctionnalités intégrées. La plateforme Microsoft Azure inclut également des fonctionnalités pour vous aider à générer des solutions offrant des fonctionnalités de récupération d’urgence ou une disponibilité inter-régions. Pour fournir une haute disponibilité globale inter-régions aux appareils et aux utilisateurs, tirez parti de ces fonctionnalités de récupération d’urgence Azure. 

Pour plus d’informations sur les fonctionnalités intégrées Azure permettant la continuité d’activité et la récupération d’urgence, consultez l’article [Guide technique de la résilience Azure](https://docs.microsoft.com/azure/resiliency/resiliency-technical-guidance). Pour bénéficier de recommandations d’architecture concernant les stratégies permettant de mettre en place la haute disponibilité et la récupération d’urgence dans les applications Azure, consultez le document [Récupération d’urgence et haute disponibilité pour les applications Azure](https://docs.microsoft.com/azure/architecture/resiliency/index).

> [!NOTE]

>  Time Series Insights ne comporte pas de fonctionnalités intégrées de continuité d’activité et reprise d’activité.
> Par défaut, Stockage Azure, Azure IoT Hub et Azure Event Hubs disposent de ces fonctionnalités.

Pour plus d’informations, consultez :

* [Redondance de Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Haute disponibilité et récupération d’urgence IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)
* [Stratégies Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)

Si vous en avez besoin, vous pouvez quand même implémenter une stratégie de récupération. Créez un deuxième environnement Time Series Insights dans une région Azure de sauvegarde. Envoyez les événements à cet environnement secondaire à partir de votre source d’événements principale. Utilisez un deuxième groupe de consommateurs dédiés et appliquez les instructions de cette source d’événements BCDR.

Suivez ces étapes pour créer et utiliser un environnement Time Series Insights secondaire.

1. Créez un environnement dans la deuxième région. Pour plus d’informations, consultez [Environnements Time Series Insights](./time-series-insights-get-started.md).
1. Créez un deuxième groupe de consommateurs dédié pour votre source d’événements. Connectez cette source d’événements au nouvel environnement. Faites attention à bien désigner le deuxième groupe de consommateurs dédié. Pour plus d’informations, consultez la [documentation IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) ou la [documentation d’Event Hub](./time-series-insights-data-access.md).
1. Si votre région principale est affectée par un sinistre, basculez les opérations sur l’environnement Time Series Insights de sauvegarde.

> [!IMPORTANT]
> * Notez qu’un délai peut se produire lors du basculement.
> * Le basculement peut également provoquer un pic momentané du traitement des message, puis que les opérations sont redirigées.
> * Pour plus d’informations, consultez [Réduction de la latence dans Azure Time Series Insights](./time-series-insights-environment-mitigate-latency.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez :

- [Azure Time Series Insights (préversion) - Stockage et entrée](./time-series-insights-update-storage-ingress.md)
- [Modélisation des données](./time-series-insights-update-tsm.md)