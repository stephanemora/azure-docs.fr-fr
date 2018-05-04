---
title: Diagnostiquer et résoudre les problèmes dans Azure Time Series Insights | Microsoft Docs
description: Cet article décrit comment diagnostiquer, dépanner et résoudre les problèmes courants que vous pouvez rencontrer dans votre environnement Azure Time Series Insights.
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 04/09/2018
ms.openlocfilehash: f0c1b8aa99e9ac9c73f57af17490dd3a465a9cac
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Diagnostiquer et résoudre les problèmes dans votre environnement Time Series Insights

## <a name="problem-1-no-data-is-shown"></a>Problème 1 : Aucune donnée n’est indiquée
Il existe plusieurs raisons pour lesquelles vous ne pouvez pas voir vos données dans [l’explorateur Azure Time Series Insights](https://insights.timeseries.azure.com) :

### <a name="possible-cause-a-event-source-data-is-not-in-json-format"></a>Cause possible A : Les données de la source d’événements ne sont pas au format JSON
Azure Time Series Insights ne prend en charge que les données JSON. Pour obtenir des exemples de données JSON, consultez [Structures JSON prises en charge](time-series-insights-send-events.md#supported-json-shapes).

### <a name="possible-cause-b-event-source-key-is-missing-a-required-permission"></a>Cause possible B : Il manque une autorisation requise pour la clé de source d’événements
* Pour IoT Hub, vous devez fournir la clé avec l’autorisation **Connexion de service**.

   ![Autorisation de connexion de service IoT Hub](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Comme indiqué dans l’illustration précédente, les stratégies **iothubowner** et **service** sont acceptées, car elles disposent toutes deux de l’autorisation **Connexion de service**.
   
* Pour Event Hub, vous devez fournir la clé avec l’autorisation **Écouter**.

   ![Autorisation d’écoute EventHub](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Comme indiqué dans l’illustration précédente, les stratégies **read** et **manage** sont acceptées, car elles disposent toutes deux de l’autorisation **Listen**.

### <a name="possible-cause-c-the-consumer-group-provided-is-not-exclusive-to-time-series-insights"></a>Cause possible C : le groupe de consommateurs spécifié a été créé exclusivement pour Time Series Insights
Lors de l’enregistrement d’un IoT Hub ou Event Hub, spécifiez le groupe de consommateurs utilisé pour la lecture des données. Ce groupe de consommateurs ne doit **pas** être partagé. Si le groupe de consommateurs est partagé, le concentrateur d’événements sous-jacent déconnecte automatiquement l’un des lecteurs au hasard. Fournissez un groupe de consommateurs unique auprès duquel Time Series Insights lira les informations.

## <a name="problem-2-some-data-is-shown-but-some-is-missing"></a>Problème 2 : Certaines données sont affichées, mais certaines manquent
Lorsque vous affichez des données partiellement, mais que les données sont en retard, il existe plusieurs possibilités à envisager :

### <a name="possible-cause-a-your-environment-is-getting-throttled"></a>Cause possible A : Votre environnement est sujet à des limitations
Il s’agit d’un problème courant lors de l’approvisionnement d’environnements après la création d’une source d’événement contenant des données.  Les Azure IoT Hubs et Events Hubs stockent des données jusqu’à sept jours.  TSI commence toujours par l’événement le plus ancien (FIFO) de la source de l’événement.  Par conséquent, si vous avez cinq millions d’événements dans une source d’événements lorsque vous vous connectez à un S1, un environnement TSI à une unité, TSI lira environ un million d’événements par jour.  Cela laisse penser à première vue que TSI rencontre cinq jours de latence.  En réalité, l’environnement est limité.  Si vous avez des événements antérieurs dans votre source de l’événement, vous pouvez utiliser une des deux méthodes suivantes :

- Modifiez les limites de rétention de votre source de l’événement pour pouvoir vous débarrasser des anciens événements que vous ne souhaitez pas voir apparaître dans TSI
- Configurez une plus grande taille d’environnement (en termes de nombre d’unités) pour augmenter le débit des anciens événements.  Dans l’exemple ci-dessus, si vous avez augmenté l’environnement S1 à cinq unités pendant un jour, l’environnement doit intercepter jusqu’à l’heure en cours du jour.  Si votre production d’événement stable est de 1 Mo ou moins d’événements par jour, vous pouvez alors réduire la capacité de l’événement à une unité après l’interception.  

Les limitations sont appliquées en fonction de la capacité et du type de référence de l’environnement. Cette capacité est répartie entre les différentes sources d’événements de l’environnement. Si la source d’événement pour votre Event Hub/IoT Hub envoie des données au-delà des limites définies, cela génère des limitations et un décalage.

Le diagramme suivant illustre un environnement Time Series Insights ayant une référence S1 et une capacité de 3 unités. Cet environnement peut recevoir 3 millions d’événements par jour.

![Capacité actuelle et référence de l’environnement](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Par exemple, supposons que cet environnement reçoit les messages à partir d’un concentrateur d’événements. Observez la vitesse d’entrée indiquée dans le diagramme suivant :

![Exemple de taux d’entrée pour un concentrateur d’événements](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Comme indiqué dans le diagramme, le taux d’entrée quotidien est d’environ 67 000 messages. Cela représente environ 46 messages par minute. Si chaque message du concentrateur d’événements est aplati dans un seul événement Time Series Insights, aucune limitation ne sera appliquée à cet environnement. Si chaque message du concentrateur d’événements est aplati dans 100 événements Time Series Insights, 4 600 événements devraient être reçus toutes les minutes. Un environnement de référence (SKU) S1 qui a une capacité de 3 peut uniquement prendre 2 100 événements d’entrée toutes les minutes (1 million d’événements par jour = 700 événements par minute à 3 unités = 2 100 événements par minute). Par conséquent, les limitations qui s’appliquent provoquent un décalage. 

Pour en savoir plus sur la logique de mise à plat, consultez [Structures JSON prises en charge](time-series-insights-send-events.md#supported-json-shapes).

### <a name="recommended-resolution-steps-for-excessive-throttling"></a>Étapes de résolution recommandées pour une limitation excessive
Pour éviter tout décalage, augmentez la capacité de votre environnement. Pour plus d’informations, consultez [Comment mettre à l’échelle votre environnement Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="possible-cause-b-initial-ingestion-of-historical-data-is-causing-slow-ingress"></a>Cause possible de B: La réception initiale des données d’historique est à l’origine de l’entrée lente
Si vous vous connectez à une source d’événement actuelle, il est probable que votre Event Hub ou IoT Hub comporte déjà des données. L’environnement démarre l’extraction des données depuis le début de la période de rétention des messages de la source d’événement.

Ce comportement est le comportement par défaut et ne peut être modifié. Vous pouvez appliquer des limitations, et l’ingestion des données historiques peut prendre un certain temps.

#### <a name="recommended-resolution-steps-of-large-initial-ingestion"></a>Étapes de résolution recommandées en cas de réception initiale volumineuse
Pour corriger le décalage, suivez les étapes ci-dessous :
1. Définissez la capacité de référence sur la valeur maximale autorisée (10 unités dans ce cas). Une fois que la capacité a été augmentée, le processus d’entrée rattrape le retard beaucoup plus rapidement. Vous pouvez suivre sa progression rapide depuis le graphique de disponibilité dans [l’explorateur Time Series Insights](https://insights.timeseries.azure.com). L’augmentation de capacité occasionne des frais supplémentaires.
2. Une fois le retard rattrapé, rétablissez la capacité de référence sur votre taux d’entrée normal.

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problème 3 : Le paramètre *Nom de la propriété timestamp* de ma source d’événement ne fonctionne pas
Vérifiez que le nom et la valeur répondent aux critères suivants :
* Le nom de la propriété timestamp est _sensible à la casse_.
* La valeur de la propriété timestamp provenant de votre source d’événement, telle qu’une chaîne JSON, doit être au format _aaaa-MM-jjTHH:mm:ss.FFFFFFFK_. Exemple de chaîne : 2008-04-12T12:53Z.

La méthode la plus simple pour vous assurer que votre *nom de propriété timestamp* est capturé et fonctionne correctement consiste à utiliser l’explorateur TSI.  Dans l’explorateur TSI, à l’aide du graphique, sélectionnez une période de temps après avoir indiqué le *nom de la propriété timestamp*.  Cliquez avec le bouton droit sur la sélection et choisissez l’option *Explorer les événements*.  Le premier en-tête de colonne doit être votre *nom de la propriété timestamp* et *($ts)* doit apparaître en regard du mot *Timestamp* au lieu de :
- *(abc)*, ce qui indiquerait que TSI lit les valeurs de données sous forme de chaînes
- *icône de calendrier*, ce qui indiquerait que TSI lit les valeurs de données sous forme de *datetime*
- *#*, ce qui indiquerait que TSI lit les valeurs de données sous forme d’entier


## <a name="next-steps"></a>Étapes suivantes
- Pour obtenir une assistance supplémentaire, démarrez une conversation sur le [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) ou sur [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). 
- Vous pouvez également utiliser le [Support Azure](https://azure.microsoft.com/support/options/) pour les options de support assisté.
