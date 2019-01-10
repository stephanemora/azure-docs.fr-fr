---
title: Diagnostiquer et résoudre les problèmes dans Azure Time Series Insights | Microsoft Docs
description: Cet article explique comment diagnostiquer, dépanner et résoudre les problèmes courants que vous pouvez rencontrer dans votre environnement Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 04/09/2018
ms.custom: seodec18
ms.openlocfilehash: 36ea2b8d3649fbda5a5cd6cc5f2cd05cdc095902
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555810"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnostiquer et résoudre les problèmes dans votre environnement Time Series Insights

Cet article décrit certains problèmes que vous pouvez rencontrer dans votre environnement Azure Time Series Insights. Il présente les causes éventuelles et les solutions pour les résoudre.

## <a name="video"></a>Vidéo : 

Dans cette vidéo, nous abordons des défis mais aussi des cas d’atténuation des risques rencontrés par les clients Time Series Insights :</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-1-no-data-is-shown"></a>Problème 1 : Aucune donnée n’est affichée

Il existe plusieurs raisons pour lesquelles vous ne pouvez pas voir vos données dans l’[explorateur Azure Time Series Insights](https://insights.timeseries.azure.com) :

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Cause A : Les données sources de l’événement ne sont pas au format JSON

Azure Time Series Insights prend uniquement en charge les données JSON. Pour obtenir des exemples de données JSON, consultez [Structures JSON prises en charge](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Cause B : Il manque une autorisation requise pour la clé de source de l’événement

* Pour un hub IoT dans Azure IoT Hub, vous devez fournir la clé qui dispose des autorisations **Connexion de service**. Les stratégies **iothubowner** et **service** fonctionnent, car elles disposent toutes deux d’autorisations **Connexion de service**.

   ![Autorisations de connexion de service IoT Hub](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)


* Pour Event Hub dans Azure Event Hub, vous devez fournir la clé disposant des autorisations **Écouter**. Les stratégies **read** et **manage** fonctionnent, car elles disposent toutes deux d’autorisations **Écouter**.

   ![Autorisations d’écoute Event Hub](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Cause C : Le groupe de consommateurs fourni n’est pas exclusif à Time Series Insights

Lorsque vous inscrivez un hub IoT ou un Event Hub, il est important de définir le groupe de consommateurs que vous souhaitez utiliser pour lire les données. Ce groupe de consommateurs *ne peut pas être partagé*. Si le groupe de consommateurs est partagé, le hub IoT ou l’Event Hub sous-jacent déconnecte de manière automatique et aléatoire l’un des lecteurs. Fournissez un groupe de consommateurs unique auprès duquel Time Series Insights lira les informations.

## <a name="problem-2-some-data-is-shown-but-data-is-missing"></a>Problème 2 : Certaines données sont affichées, mais d’autres manquent

Lorsque les données n’apparaissent que partiellement et semblent être en décalage, vous devez envisager plusieurs possibilités.

### <a name="cause-a-your-environment-is-being-throttled"></a>Cause A : Votre environnement est sujet à des limitations

La limitation est un problème courant lors de la configuration des environnements après la création d’une source de l’événement qui comporte des données. Azure IoT Hub et Azure Events Hubs stockent les données jusqu’à sept jours. Time Series Insights commence toujours par l’événement le plus ancien dans la source de l’événement (premier entré, premier sorti, ou *FIFO*). 

Par exemple, si 5 millions d’événements se trouvent dans une source de l’événement lorsque vous vous connectez à un S1 (environnement Time Series Insights à une unité), Time Series Insights lit environ 1 million d’événements par jour. Time Series Insights peut donc subir cinq jours de latence. En réalité, l’environnement est limité. 

Si votre source de l’événement comporte des événements antérieurs, vous pouvez aborder la limitation de l’une des deux façons suivantes :

- Modifiez les limites de rétention de votre source de l’événement pour vous débarrasser des anciens événements que vous ne souhaitez pas voir apparaître dans Time Series Insights.
- Configurez une plus grande taille d’environnement (nombre d’unités) pour augmenter le débit des anciens événements. Dans l’exemple précédent, si vous augmentez l’environnement S1 à cinq unités pendant un jour, l’environnement doit rattraper le retard en un seul jour. Si votre production d’événement stable compte 1 million d’événements par jour maximum, vous pouvez réduire la capacité de l’événement à une unité après le rattrapage.

Les limitations sont appliquées en fonction de la capacité et du type de référence de l’environnement. Cette capacité est répartie entre les différentes sources d’événements de l’environnement. Si la source de l’événement pour votre hub IoT/Event Hub envoie des données au-delà des limites définies, cela génère des limitations et un décalage.

La figure suivante illustre un environnement Time Series Insights ayant une référence SKU S1 et une capacité de 3 unités. Cet environnement peut recevoir 3 millions d’événements par jour.

![Capacité actuelle et référence de l’environnement](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Par exemple, supposons que cet environnement ingère les messages à partir d’un Event Hub. La figure suivante illustre le taux d’entrée :

![Exemple de taux d’entrée pour un concentrateur d’événements](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Le taux d’entrée quotidien est d’environ 67 000 messages. Cela représente environ 46 messages par minute. Si chaque message de l’Event Hub est aplati dans un seul événement Time Series Insights, aucune limitation ne se produit. Si chaque message de l’Event Hub est aplati dans 100 événements Time Series Insights, 4 600 événements devraient être ingérés toutes les minutes. Un environnement de référence SKU S1 qui a une capacité de 3 unités peut seulement ingérer 2 100 événements toutes les minutes (1 million d’événements par jour = 700 événements par minute à 3 unités = 2 100 événements par minute). Pour cette configuration, les limitations qui s’appliquent provoquent un décalage. 

Pour en savoir plus sur la logique de mise à plat, consultez [Structures JSON prises en charge](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Résolutions recommandées en cas de limitation excessive

Pour éviter tout décalage, augmentez la capacité de votre environnement. Pour plus d’informations, consultez [Mise à l’échelle de votre environnement Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Cause B : L’ingestion initiale de données historiques ralentit l’entrée

Si vous vous connectez à une source de l’événement existante, il est probable que votre hub IoT ou votre Event Hub comporte déjà des données. L’environnement démarre l’extraction des données depuis le début de la période de rétention des messages de la source d’événement. Cela correspond au traitement par défaut et ne peut pas être substitué. Vous pouvez appliquer des limitations. La limitation peut prendre un certain temps en ce qui concerne le rattrapage, car elle ingère les données d’historique.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Résolutions recommandées en cas d’ingestion initiale volumineuse

Pour corriger le décalage :

1. Définissez la capacité de référence SKU sur la valeur maximale autorisée (10 unités dans ce cas). Après augmentation de la capacité, le processus d’entrée commence à rattraper le retard beaucoup plus rapidement. L’augmentation de capacité occasionne des frais supplémentaires. Pour suivre sa progression rapide, consultez le graphique de disponibilité dans l’[explorateur Time Series Insights](https://insights.timeseries.azure.com). 

2. Une fois le retard rattrapé, rétablissez la capacité de référence SKU sur votre taux d’entrée normal.

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problème 3 : Le paramètre Nom de la propriété timestamp de ma source d’événement ne fonctionne pas

Vérifiez que le nom et la valeur de la propriété timestamp répondent aux critères suivants :
* Le nom de la propriété timestamp est sensible à la casse.
* La valeur de la propriété timestamp provenant de votre source d’événement, comme une chaîne JSON, doit être au format _aaaa-MM-jjTHH:mm:ss.FFFFFFFK_. Par exemple : **2008-04-12T12:53Z**.

La méthode la plus simple pour vous assurer que votre nom de propriété timestamp est capturé et fonctionne correctement consiste à utiliser l’explorateur Time Series Insights. Dans l’explorateur Time Series Insights, à l’aide du graphique, sélectionnez une période de temps spécifique après avoir indiqué le nom de la propriété timestamp. Cliquez avec le bouton droit sur la sélection, puis sélectionnez l’option **Explorer les événements**. 

Le premier en-tête de colonne doit correspondre au nom de votre propriété timestamp. En regard du mot **Timestamp**, vous devez voir **($ts)**. 

Vous ne devez pas voir les valeurs suivantes :
- *(abc)* : indique que Time Series Insights lit les valeurs de données sous forme de chaînes.
- *Icône de calendrier* : indique que Time Series Insights lit la valeur de données sous la forme *dateHeure*.
- *#* : indique que Time Series Insights lit les valeurs de données sous forme d’entier.


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une assistance, entamez une conversation sur le [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) ou sur [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). 
- Vous pouvez également utiliser le [support Azure](https://azure.microsoft.com/support/options/) pour les options de support assisté.
