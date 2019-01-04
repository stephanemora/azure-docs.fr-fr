---
title: Guide pratique pour surveiller et réduire la limitation dans Azure Time Series Insights | Microsoft Docs
description: Cet article explique comment analyser, diagnostiquer et réduire les problèmes de performances qui provoquent la latence et la limitation dans Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/27/2017
ms.custom: seodec18
ms.openlocfilehash: 3a42570b51811cfbdd4329f196b98d75c8cd53f7
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556745"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Surveiller et réduire la limitation afin d'éviter la latence dans Azure Time Series Insights
Lorsque la quantité de données entrantes dépasse la configuration de votre environnement, vous pouvez rencontrer une latence ou une limitation dans Azure Time Series Insights.

Vous pouvez éviter la latence et la limitation en configurant correctement votre environnement pour la quantité de données que vous souhaitez analyser.

Vous êtes susceptible de rencontrer une latence et une limitation lorsque vous :

- Ajoutez une source d’événement qui contient des données anciennes qui peuvent dépasser le taux d’entrée alloué (que Time Series Insights devra rattraper).
- Ajoutez plusieurs sources d’événements dans un environnement, ce qui entraîne un pic d’activité dû aux événements supplémentaires (cela risque de dépasser la capacité de votre environnement).
- Envoyez de grandes quantités d’événements historiques à une source d’événements, ce qui entraîne un décalage (que Time Series Insights devra rattraper).
- Joignez des données de référence avec la télémétrie, ce qui entraîne une plus grande taille d’événement.  En termes de limitation, un paquet de données entré avec une taille de paquet de 32 Ko est considéré comme 32 événements, faisant chacun 1 Ko. La taille maximale autorisée de l’événement est 32 Ko ; les paquets de données d’une taille supérieure à 32 Ko sont tronqués.

## <a name="video"></a>Vidéo : 

### <a name="in-this-video-we-cover-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Dans cette vidéo, nous traitons du comportement d’entrée de données Time Series Insights et comment le planifier.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Analyser la latence et la limitation à l’aide d’alertes

Les alertes peuvent vous aider à diagnostiquer et réduire les problèmes de latence dus à votre environnement. 

1. Dans le portail Azure, cliquez sur **Mesures**. 

   ![Mesures](media/environment-mitigate-latency/add-metrics.png)

2. Cliquez sur **Ajouter une alerte de mesure**.  

    ![Ajouter une alerte de mesure](media/environment-mitigate-latency/add-metric-alert.png)

À partir de là, vous pouvez configurer des alertes à l’aide des mesures suivantes :

|Métrique  |Description  |
|---------|---------|
|**Octets reçus en entrée**     | Nombre d’octets bruts lus à partir des sources d’événements. Le nombre brut inclut généralement le nom de la propriété et la valeur.  |  
|**Messages non valides reçus en entrée**     | Nombre de messages non valides lus à partir de la totalité des Event Hubs Azure ou des sources d’événements Azure IoT Hub.      |
|**Messages reçus en entrée**   | Nombre de messages lus à partir de la totalité des Event Hubs ou des sources d’événements IoT Hub.        |
|**Octets stockés en entrée**     | Taille totale des événements stockés et disponibles pour la requête. La taille est calculée uniquement sur la valeur de propriété.        |
|**Événements stockés en entrée**     |   Nombre d’événements aplatis stockés et disponibles pour la requête.      |
|**Retard des messages reçus en entrée**    |  Différence en secondes entre l’heure à laquelle le message est placé en file d’attente dans la source d’événement et l’heure à laquelle il est traité en entrée.      |
|**Décalage de nombre des messages reçus en entrée**    |  Différence entre le numéro de séquence du dernier message placé en file d’attente dans la partition source de l’événement et le numéro de séquence du message traité en entrée.      |


![Latence](media/environment-mitigate-latency/latency.png)

Si vous êtes limité, une valeur s’affichera pour la mesure *Retard des messages reçus en entrée*, vous informant du décalage en secondes entre le TSI et l’horodatage réel de l’arrivée du message dans la source d’événement (sans tenir compte du délai d’indexation d’environ 30 à 60 secondes).  Le champ *Décalage de nombre des messages reçus en entrée* doit également comporter une valeur, vous octroyant une visibilité sur le nombre de messages de retard que vous accusez.  Le moyen le plus simple de rattraper le retard est d’augmenter la capacité de votre environnement à une taille qui vous permettra de combler la différence.  

Par exemple, si vous disposez d’un environnement S1 à une seule unité et constatez un décalage de 5 millions de messages, vous avez intérêt à augmenter la taille de votre environnement à 6 unités durant environ une journée pour refaire votre retard.  Vous pouvez même augmenter davantage pour rattraper plus vite le retard.  La période de rattrapage se produit souvent lors du provisionnement initial d’un environnement, plus particulièrement lorsque vous le connectez à une source d’événement qui présente déjà des événements ou quand vous chargez de manière groupée de gros volumes de données d’historique.

Une autre technique est de définir une alerte d’**événements stockés en entrée** >= à un seuil légèrement inférieur à la capacité totale de votre environnement pour une période de 2 heures.  Cette alerte peut vous aider à comprendre si vous êtes en permanence à votre capacité maximale, ce qui implique une forte probabilité de latence.  

Par exemple, si vous avez trois unités S1 configurées (ou 2 100 événements de capacité d’entrée par minute), vous pouvez définir une alerte d’**événements stockés en entrée** pour >= 1 900 événements pendant 2 heures. Si vous dépassez en permanence ce seuil et par conséquent, vous déclenchez l’alerte, vous êtes probablement sous-configuré.  

En outre, si vous pensez que vous êtes limité, vous pouvez comparer vos **messages reçus en entrée** avec les messages de sortie de votre source d’événement.  Si l’entrée dans votre Event Hub est supérieure à vos **messages reçus en entrée**, vos informations Time Series Insights sont probablement limitées.

## <a name="improving-performance"></a>Améliorer les performances 
La meilleure façon de réduire la limitation ou la latence est de d’augmenter la capacité de votre environnement. 

Vous pouvez éviter la latence et la limitation en configurant correctement votre environnement pour la quantité de données que vous souhaitez analyser. Pour plus d’informations sur comment augmenter la capacité de votre environnement, consultez l’article [Mettre à l’échelle votre environnement](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Étapes suivantes
- Pour obtenir des étapes de dépannage supplémentaires, consultez l’article [Diagnostiquer et résoudre les problèmes dans votre environnement Time Series Insights](time-series-insights-diagnose-and-solve-problems.md).
- Pour obtenir une assistance supplémentaire, démarrez une conversation sur le [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) ou sur [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). Vous pouvez également contacter le [Support Azure](https://azure.microsoft.com/support/options/) pour les options de support assisté.
