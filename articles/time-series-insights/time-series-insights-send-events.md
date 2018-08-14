---
title: Guide pratique pour envoyer des événements dans un environnement Azure Time Series Insights | Microsoft Docs
description: Ce didacticiel explique comment créer et configurer un hub d’événements et exécuter un exemple d’application pour envoyer (push) des événements à afficher dans Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: 30b83c54d314934f1de170955eec22e7b2a264b8
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629750"
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Envoyer des événements à un environnement Time Series Insights à l’aide d’un concentrateur d’événements
Ce didacticiel explique comment créer et configurer un hub d’événements et exécuter un exemple d’application pour envoyer (push) des événements. Si vous disposez d’un concentrateur d’événements existant qui a déjà des événements au format JSON, ignorez ce didacticiel et affichez votre environnement dans [Time Series Insights](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Configurer un concentrateur d’événements
1. Pour créer un concentrateur d’événements, suivez les instructions de la [documentation](../event-hubs/event-hubs-create.md) relative aux concentrateurs d’événements.

2. Recherchez **hub d’événements** dans la barre de recherche. Cliquez sur **Hubs d’événements** dans la liste retournée.

3. Sélectionnez votre hub d’événements en cliquant sur son nom.

4. Sous **Entités** dans la fenêtre de configuration du milieu, cliquez à nouveau sur **Event Hubs**.

5. Sélectionnez le nom du hub d'événements pour le configurer.

  ![Sélectionnez le groupe de consommateurs du concentrateur d’événements](media/send-events/consumer-group.png)

6. Sous **Entités**, sélectionnez **Groupes de consommateurs**.
 
7. Veillez à créer un groupe de consommateurs qui sera utilisé exclusivement par votre source d’événement Time Series Insights.

   > [!IMPORTANT]
   > Assurez-vous que ce groupe de consommateurs n’est pas utilisé par un autre service (par exemple, une tâche Stream Analytics ou un autre environnement Time Series Insights). Si le groupe de consommateurs est utilisé par d’autres services, l’opération de lecture est affectée pour cet environnement et les autres services. Si vous utilisez le groupe de consommateurs « $Default », ceci peut entraîner une réutilisation potentielle par d’autres lecteurs.

8. Sous le titre **Paramètres**, sélectionnez **Stratégies d’accès partagé**.

9. Dans le hub d’événements, créez **MySendPolicy** utilisé pour envoyer des événements dans l’exemple csharp.

  ![Sélectionnez des stratégies d’accès partagé et cliquez sur le bouton Ajouter](media/send-events/shared-access-policy.png)  

  ![Ajoutez une stratégie d’accès partagé](media/send-events/shared-access-policy-2.png)  

## <a name="add-time-series-insights-reference-data-set"></a>Ajoutez un jeu de données de référence Time Series Insights 
L’utilisation de données référence dans TSI contextualise vos données de télémétrie.  Ce contexte ajoute une signification à vos données et simplifie le filtrage et l’agrégation.  TSI joint des données de référence au moment de l’entrée et ne peut pas joindre ces données de manière rétroactive.  Par conséquent, il est essentiel d’ajouter des données de référence avant d’ajouter une source de l’événement contenant des données.  Les données telles que le type d’emplacement ou de capteur sont des dimensions utiles que vous souhaiterez peut-être joindre à un appareil/une balise/un ID de capteur pour faciliter la segmentation et le filtrage.  

> [!IMPORTANT]
> Disposer d’un jeu de données de référence configuré est essentiel lorsque vous chargez des données historiques.

Vérifiez que vous disposez de données de référence lorsque vous chargez en bloc des données historiques dans TSI.  N’oubliez pas que TSI commence à lire immédiatement une source de l’événement jointe si cette source de l’événement comporte des données.  Il est utile d’attendre de disposer de vos données de référence avant de joindre une source de l’événement à TSI, et plus particulièrement si cette source de l’événement comporte des données. Vous pouvez également attendre de disposer d’un jeu de données de référence avant de transmettre des données à cette source de l’événement.

Pour gérer les données de référence, vous disposez d’une interface utilisateur web dans l’explorateur TSI, ainsi que d’une API C# de programmation. L’explorateur TSI propose un expérience utilisateur visuelle pour charger des fichiers ou coller des jeux de données de référence existants au format JSON ou CSV. Grâce à l’API, vous pouvez créer une application personnalisée si nécessaire.

Pour plus d’informations sur la gestion des données de référence dans Time Series Insights, consultez l’[article des données de référence](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="create-time-series-insights-event-source"></a>Créer la source d’événement Time Series Insights
1. Si vous n’avez créé aucune source d’événement, suivez [ces instructions](time-series-insights-how-to-add-an-event-source-eventhub.md) pour créer une source d’événement.

2. Spécifiez **deviceTimestamp** comme nom de la propriété timestamp. Cette propriété définit l’horodatage réel dans l’exemple C#. Le nom de la propriété timestamp est sensible à la casse et les valeurs doivent être au format __aaaa-MM-jjTHH:mm:ss.FFFFFFFK__ lors de l’envoi au format JSON au concentrateur d’événements. Si la propriété n’existe pas dans l’événement, le système utilise l’heure à laquelle l’événement a été placé dans la file d’attente du concentrateur d’événements.

  ![Créez la source d’événement](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>Exemple de code pour envoyer des événements
1. Accédez à la stratégie de hub d’événements nommée **MySendPolicy**. Copiez la **chaîne de connexion** avec la clé de stratégie.

  ![Copiez la chaîne de connexion MySendPolicy](media/send-events/sample-code-connection-string.png)

2. Exécutez le code suivant qui envoie 600 événements pour chacun des trois appareils. Mettez à jour `eventHubConnectionString` avec votre chaîne de connexion.

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.Rdx.DataGenerator
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var from = new DateTime(2017, 4, 20, 15, 0, 0, DateTimeKind.Utc);
            Random r = new Random();
            const int numberOfEvents = 600;

            var deviceIds = new[] { "device1", "device2", "device3" };

            var events = new List<string>();
            for (int i = 0; i < numberOfEvents; ++i)
            {
                for (int device = 0; device < deviceIds.Length; ++device)
                {
                    // Generate event and serialize as JSON object:
                    // { "deviceTimestamp": "utc timestamp", "deviceId": "guid", "value": 123.456 }
                    events.Add(
                        String.Format(
                            CultureInfo.InvariantCulture,
                            @"{{ ""deviceTimestamp"": ""{0}"", ""deviceId"": ""{1}"", ""value"": {2} }}",
                            (from + TimeSpan.FromSeconds(i * 30)).ToString("o"),
                            deviceIds[device],
                            r.NextDouble()));
                }
            }

            // Create event hub connection.
            var eventHubConnectionString = @"Endpoint=sb://...";
            var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubConnectionString);

            using (var ms = new MemoryStream())
            using (var sw = new StreamWriter(ms))
            {
                // Wrap events into JSON array:
                sw.Write("[");
                for (int i = 0; i < events.Count; ++i)
                {
                    if (i > 0)
                    {
                        sw.Write(',');
                    }
                    sw.Write(events[i]);
                }
                sw.Write("]");

                sw.Flush();
                ms.Position = 0;

                // Send JSON to event hub.
                EventData eventData = new EventData(ms);
                eventHubClient.Send(eventData);
            }
        }
    }
}

```
## <a name="supported-json-shapes"></a>Structures JSON prises en charge
### <a name="sample-1"></a>Exemple 1

#### <a name="input"></a>Entrée

Un objet JSON simple.

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---one-event"></a>Sortie - un événement

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>Exemple 2

#### <a name="input"></a>Entrée
Un tableau JSON avec deux objets JSON. Chaque objet JSON sera converti en un événement.
```json
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
#### <a name="output---two-events"></a>Sortie - deux événements

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>Exemple 3

#### <a name="input"></a>Entrée

Un objet JSON avec un tableau JSON imbriqué qui contient deux objets JSON :
```json
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
#### <a name="output---two-events"></a>Sortie - deux événements
Notez que la propriété « location » est copiée dans chacun des événements.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>Exemple 4

#### <a name="input"></a>Entrée

Un objet JSON avec un tableau JSON imbriqué contenant deux objets JSON. Cette entrée montre que les propriétés globales peuvent être représentées par l’objet JSON complexe.

```json
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
#### <a name="output---two-events"></a>Sortie - deux événements

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|



## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Affichez votre environnement dans l’explorateur Time Series Insights](https://insights.timeseries.azure.com).
