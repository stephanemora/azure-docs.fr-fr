---
title: Envoyer des événements à un environnement Azure Time Series Insights | Microsoft Docs
description: Apprenez à configurer un hub d'événements et à exécuter un exemple d'application pour envoyer (push) des événements consultables dans Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: ae59e8115ca2d1ba69c8a3a099216eb3d98e2658
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237692"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Envoyer des événements à un environnement Time Series Insights à l'aide d'un hub d'événements

Cet article explique comment créer et configurer un concentrateur d’événements dans Azure Event Hubs. Il décrit également comment exécuter un exemple d’application pour envoyer des événements à Azure Time Series Insights à partir d’Event Hubs. Si vous avez un event hub existant avec des événements au format JSON, ignorez ce didacticiel et affichez votre environnement dans [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Configurer un concentrateur d’événements

1. Pour en savoir plus sur la création d'un hub d'événements, consultez la [documentation Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
1. Dans la zone de recherche, recherchez **Event Hubs**. Dans la liste retournée, sélectionnez **Event Hubs**.
1. Sélectionnez votre hub d'événements.
1. Lorsque vous créez un concentrateur d’événements, vous créez un espace de noms event hub. Si vous n’avez pas encore créé un hub d’événements au sein de l’espace de noms, dans le menu, sous **entités**, créer un concentrateur d’événements.  

    [![Liste des concentrateurs d’événements](media/send-events/updated.png)](media/send-events/updated.png#lightbox)

1. Une fois votre hub d'événements créé, sélectionnez-le dans la liste.
1. Dans le menu, sous **entités**, sélectionnez **Event Hubs**.
1. Sélectionnez le nom du hub d'événements pour le configurer.
1. Sous **Entités**, sélectionnez **Groupes de consommateurs**, puis **Groupe de consommateurs**.

    [![Créer un groupe de consommateurs](media/send-events/consumer-group.png)](media/send-events/consumer-group.png#lightbox)

1. Vérifiez que vous créez un groupe de consommateurs est utilisé exclusivement par votre source d’événement Time Series Insights.

    > [!IMPORTANT]
    > Assurez-vous que ce groupe de consommateurs n’est pas utilisé par un autre service, comme un travail Azure Stream Analytique ou un autre environnement Time Series Insights. Si le groupe de consommateurs est utilisé par les autres services, les opérations de lecture sont affectées pour cet environnement et pour les autres services. Si vous utilisez le groupe de consommateurs **$Default**, d'autres lecteurs risquent de pouvoir réutiliser votre groupe de consommateurs.

1. Dans le menu, sous **paramètres**, sélectionnez **stratégies d’accès partagé**, puis sélectionnez **ajouter**.

    [![Sélectionnez les stratégies d’accès partagé, puis sélectionnez le bouton Ajouter](media/send-events/shared-access-policy.png)](media/send-events/shared-access-policy.png#lightbox)

1. Dans le volet **Ajouter une nouvelle stratégie d'accès partagé**, créez un accès partagé intitulé **MySendPolicy**. Cette stratégie d’accès partagé vous permet d’envoyer des événements le C# exemples plus loin dans cet article.

    [![Dans la zone Nom de la stratégie, entrez MySendPolicy](media/send-events/shared-access-policy-2.png)](media/send-events/shared-access-policy-2.png#lightbox)

1. Sous **revendication**, sélectionnez le **envoyer** case à cocher.

## <a name="add-a-time-series-insights-instance"></a>Ajouter une instance de Time Series Insights

La mise à jour de Time Series Insights utilise des instances pour ajouter des données contextuelles aux données de télémétrie entrantes. Les données sont jointes au moment de la requête à l'aide d'un **ID de série chronologique**. Le **ID de série de temps** pour les procès exemple projet que nous utilisons plus loin dans cet article est `id`. Pour en savoir plus sur les instances de Time Series Insight et sur l'**ID de série chronologique**, consultez [Modèles de séries chronologiques](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Créer une source d'événement Time Series Insights

1. Si vous n'avez créé aucune source d'événement, suivez la procédure de [création d'une source d'événement](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Définissez une valeur pour `timeSeriesId`. Pour en savoir plus sur l'**ID de série chronologique**, consultez [Modèles de séries chronologiques](./time-series-insights-update-tsm.md).

### <a name="push-events"></a>Envoyer (push) des événements (exemple de projet d'éoliennes)

1. Sur la barre de recherche, recherchez **Event Hubs**. Dans la liste retournée, sélectionnez **Event Hubs**.

1. Sélectionnez votre hub d'événements.

1. Accédez à **Stratégies d'accès partagé** > **RootManageSharedAccessKey**. Copiez la valeur de **la clé primaire de la chaîne de connexion**.

    [![Copiez la valeur de la chaîne de connexion de clé primaire](media/send-events/sample-code-connection-string.png)](media/send-events/sample-code-connection-string.png#lightbox)

1. Accédez à https://tsiclientsample.azurewebsites.net/windFarmGen.html L'URL exécute des éoliennes simulées.
1. Dans la zone **Chaîne de connexion Event Hub** de la page web, collez la chaîne de connexion que vous avez copiée dans [Envoyer (push) des événements](#push-events).
  
    [![Collez la chaîne de connexion de clé primaire dans la zone de chaîne de connexion Event Hub](media/send-events/updated_two.png)](media/send-events/updated_two.png#lightbox)

1. Sélectionnez **Cliquez pour démarrer**. Le simulateur génère une instance JSON que vous pouvez utiliser directement.

1. Revenez à votre hub d'événements sur le portail Azure. Sur le **vue d’ensemble** page, vous voyez les nouveaux événements sont reçus par le concentrateur d’événements.

    [![Une page de présentation event hub qui affiche des mesures pour le concentrateur d’événements](media/send-events/telemetry.png)](media/send-events/telemetry.png#lightbox)

## <a name="json"></a>Structures JSON prises en charge

### <a name="example-one"></a>Exemple 1

* **Entrée**: Un objet JSON simple.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Sortie**: Un seul événement.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Le deuxième exemple

* **Entrée**: Un tableau JSON avec deux objets JSON. Chaque objet JSON est converti en événement.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **Sortie**: Deux événements.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Exemple trois

* **Entrée**: Objet JSON avec tableau JSON imbriqué contenant deux objets JSON.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **Sortie**: Deux événements. La propriété **location** est copiée dans chaque événement.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Exemple quatre

* **Entrée**: Objet JSON avec tableau JSON imbriqué contenant deux objets JSON. Cette entrée montre que les propriétés globales peuvent être représentées par l'objet JSON complexe.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **Sortie**: Deux événements.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Étapes suivantes

- [Affichez votre environnement](https://insights.timeseries.azure.com) dans l’Explorateur Time Series Insights.
