---
title: Ingérer la télémétrie depuis IoT Hub
titleSuffix: Azure Digital Twins
description: Découvrez comment ingérer les messages de télémétrie des appareils provenant d’IoT Hub.
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 351f7ed131d545d2aa83df753cac3f26e76e4ccb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84725849"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Ingérer des données de télémétrie IoT Hub dans Azure Digital Twins

Azure Digital Twins se base sur les données des appareils IoT et d’autres sources. Pour les données d’appareil, il est courant d’utiliser la source [IoT Hub](../iot-hub/about-iot-hub.md) dans Azure Digital Twins.

Dans la préversion, le processus d’ingestion des données dans Azure Digital Twins consiste à configurer une ressource de calcul externe, telle qu’une [fonction Azure](../azure-functions/functions-overview.md), qui reçoit les données et utilise les [API DigitalTwins](how-to-use-apis-sdks.md) pour définir des propriétés ou déclencher des événements de télémétrie en conséquence pour les [jumeaux numériques](concepts-twins-graph.md). 

Ce document explique le processus d’écriture d’une fonction Azure pouvant ingérer des données de télémétrie à partir d’IoT Hub.

## <a name="example-telemetry-scenario"></a>Exemple de scénario de télémétrie

Cet article explique comment envoyer des messages entre IoT Hub et Azure Digital Twins à l’aide d’une fonction Azure. Pour cela, il existe de nombreuses configurations et stratégies possibles. Cependant, l’exemple de cet article contient les éléments suivants :
* Un thermomètre situé dans IoT Hub, ayant un ID d’appareil connu
* Un jumeau numérique représentant l’appareil, avec un ID correspondant
* Un jumeau numérique représentant une pièce

> [!NOTE]
> Cet exemple utilise une correspondance d’ID simple entre l’ID de l’appareil et celui d’un jumeau numérique. Toutefois, il est possible de fournir des mappages plus sophistiqués entre l’appareil et son jumeau (par exemple, avec une table de mappage).

Chaque fois qu’un événement de télémétrie de température est envoyé par le thermomètre, la propriété *temperature* du jumeau *Room* doit être mise à jour. Pour ce faire, vous allez mapper l’événement de télémétrie d’un appareil sur l’accesseur Set de propriété d’un jumeau numérique. Vous allez utiliser les informations de topologie du [graphe de jumeaux](concepts-twins-graph.md) pour trouver le jumeau *Room*, puis vous allez définir la propriété du jumeau. Dans d’autres scénarios, les utilisateurs peuvent souhaiter définir une propriété dans le jumeau correspondant (dans cet exemple, il s’agit du jumeau avec l’ID *123*). Azure Digital Twins offre une grande souplesse quant à la façon de mapper les données de télémétrie dans les jumeaux. 

Ce scénario est présenté dans un diagramme ci-dessous :

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Un appareil IoT Hub envoie des données de télémétrie de température (via IoT Hub, Event Grid ou des rubriques système) à une fonction Azure, qui met à jour une propriété de température pour les jumeaux dans Azure Digital Twins." border="false":::

## <a name="prerequisites"></a>Prérequis

Avant de poursuivre avec cet exemple, vous devez respecter les prérequis suivants.
1. Créez un hub IoT. Pour obtenir des instructions, reportez-vous à la section *Créer un hub IoT* de [ce guide de démarrage rapide IoT Hub](../iot-hub/quickstart-send-telemetry-cli.md).
2. Créez au moins une fonction Azure pour traiter les événements provenant d’IoT Hub. Découvrez [comment faire : Configurer une fonction Azure pour le traitement des données](how-to-create-azure-function.md) pour créer une fonction Azure de base capable de se connecter à Azure Digital Twins et d’appeler les fonctions de l’API Azure Digital Twins. Le reste de cette procédure s’appuiera sur cette fonction.
3. Configurez une destination d’événement pour les données de concentrateur. Dans le [portail Azure](https://portal.azure.com/), accédez à votre instance IoT Hub. Sous *Événements*, créez un abonnement pour votre fonction Azure. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Portail Azure : Ajout d’un abonnement à un événement":::

4. Dans la page *Créer un abonnement aux événements*, renseignez les champs de la façon suivante :
   * Sous *Détails de l’abonnement aux événements*, attribuez le nom de votre choix à l’abonnement.
   * Sous *Types d’événements*, choisissez *Télémétrie d’appareil* comme filtre de types d’événements.
      - Vous pouvez ajouter des filtres à d’autres types d’événements si vous le souhaitez.
   * Sous *Détails des points de terminaison*, sélectionnez votre fonction Azure comme point de terminaison.

## <a name="create-an-azure-function-in-visual-studio"></a>Créer une fonction Azure dans Visual Studio

Cette section utilise les mêmes étapes de démarrage Visual Studio et le même squelette de fonction Azure que ceux de [Procédure : Configurer une fonction Azure pour le traitement des données](how-to-create-azure-function.md). Le squelette gère l’authentification et crée un client de service, prêt à traiter les données et à appeler les API Azure Digital Twins. 

Le cœur du squelette de la fonction est le suivant :

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Dans les étapes qui suivent, vous y ajouterez du code pour le traitement des événements de télémétrie IoT provenant d’IoT Hub.  

## <a name="add-telemetry-processing"></a>Ajouter le traitement des données de télémétrie

Les événements de télémétrie sont fournis sous la forme de messages provenant de l’appareil. Lorsque vous ajoutez du code de traitement des données de télémétrie, la première étape consiste à extraire la partie utile de ce message d’appareil à partir de l’événement Event Grid. 

Les différents types d’appareils peuvent structurer leurs messages différemment. Le code de cette étape dépend donc de l’appareil connecté. 

Le code suivant illustre un exemple d’appareil simple qui envoie des données de télémétrie au format JSON. L’exemple extrait l’ID de l’appareil qui a envoyé le message, ainsi que la valeur de température.

```csharp
JObject job = eventGridEvent.Data as JObject;
string devid = (string)job["systemProperties"].ToObject<JObject>().Property("IoT-hub-connection-device-ID").Value;
double temp = (double)job["body"].ToObject<JObject>().Property("temperature").Value;
```

Rappelez-vous que l’objectif de cet exercice est de mettre à jour la température d’une pièce (*Room*) dans le graphe de jumeaux. Pour le message, notre cible n’est donc pas le jumeau numérique associé à cet appareil, mais le jumeau *Room* qui est son parent. Vous pouvez trouver le jumeau parent à l’aide de la valeur d’ID d’appareil que vous avez extraite du message de télémétrie à l’aide du code ci-dessus.

Pour ce faire, utilisez les API Azure Digital Twins pour accéder aux relations entrantes du jumeau d’appareil (qui, dans ce cas, a le même ID que l’appareil). À partir de la relation entrante, vous pouvez trouver l’ID du parent avec l’extrait de code ci-dessous.

L’extrait de code ci-dessous montre comment récupérer les relations entrantes d’un jumeau :

```csharp
AsyncPageable<IncomingRelationship> res = client.GetIncomingRelationshipsAsync(twin_id);
await foreach (IncomingRelationship irel in res)
{
    Log.Ok($"Relationship: {irel.RelationshipName} from {irel.SourceId} | {irel.RelationshipId}");
}
```

Le parent de votre jumeau se trouve dans la propriété *SourceId* de la relation.

Il est relativement courant pour un modèle de jumeau représentant un appareil de n’avoir qu’une seule relation entrante. Dans ce cas, vous pouvez choisir la première (et unique) relation retournée. Si vos modèles autorisent plusieurs types de relations pour ce jumeau, vous devrez peut-être augmenter le nombre de relations entrantes disponibles. Pour ce faire, il est généralement possible de choisir la relation par `RelationshipName`. 

Une fois que vous avez l’ID du jumeau parent qui représente la pièce (*Room*), vous pouvez « corriger » (c’est-à-dire, mettre à jour) ce jumeau. Pour ce faire, utilisez le code suivant :

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", temp);
try
{
    await client.UpdateDigitalTwinAsync(twin_id, uou.Serialize());
    Log.Ok($"Twin '{twin_id}' updated successfully!");
}
...
```

### <a name="full-azure-function-code"></a>Code complet de la fonction Azure

Voici l’intégralité de la fonction Azure en contexte, basée sur le code des exemples précédents :

```csharp
[FunctionName("ProcessHubToDTEvents")]
public async void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
{
    // After this is deployed, in order for this function to be authorized on Azure Digital Twins APIs,
    // you'll need to turn the Managed Identity Status to "On", 
    // grab the Object ID of the function, and assign the "Azure Digital Twins Owner (Preview)" role to this function identity.

    DigitalTwinsClient client = null;
    //log.LogInformation(eventGridEvent.Data.ToString());
    // Authenticate on Azure Digital Twins APIs
    try
    {
        
        ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
        client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
        log.LogInformation($"ADT service client connection created.");
    }
    catch (Exception e)
    {
        log.LogError($"ADT service client connection failed. " + e.ToString());
        return;
    }

    if (client != null)
    {
        try
        {
            if (eventGridEvent != null && eventGridEvent.Data != null)
            {
                #region Open this region for message format information
                // Telemetry message format
                //{
                //  "properties": { },
                //  "systemProperties": 
                // {
                //    "iothub-connection-device-id": "thermostat1",
                //    "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
                //    "iothub-connection-auth-generation-id": "637199981642612179",
                //    "iothub-enqueuedtime": "2020-03-18T18:35:08.269Z",
                //    "iothub-message-source": "Telemetry"
                //  },
                //  "body": "eyJUZW1wZXJhdHVyZSI6NzAuOTI3MjM0MDg3MTA1NDg5fQ=="
                //}
                #endregion

                // Reading deviceId from message headers
                log.LogInformation(eventGridEvent.Data.ToString());
                JObject job = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                string deviceId = (string)job["systemProperties"]["iothub-connection-device-id"];
                log.LogInformation($"Found device: {deviceId}");

                // Extracting temperature from device telemetry
                byte[] body = System.Convert.FromBase64String(job["body"].ToString());
                var value = System.Text.ASCIIEncoding.ASCII.GetString(body);
                var bodyProperty = (JObject)JsonConvert.DeserializeObject(value);
                var temperature = bodyProperty["Temperature"];
                log.LogInformation($"Device Temperature is:{temperature}");

                // Update device Temperature property
                await AdtUtilities.UpdateTwinProperty(client, deviceId, "/Temperature", temperature, log);

                // Find parent using incoming relationships
                string parentId = await AdtUtilities.FindParent(client, deviceId, "contains", log);
                if (parentId != null)
                {
                    await AdtUtilities.UpdateTwinProperty(client, parentId, "/Temperature", temperature, log);
                }

            }
        }
        catch (Exception e)
        {
            log.LogError($"Error in ingest function: {e.Message}");
        }
    }
}
```

Fonction utilitaire permettant de rechercher les relations entrantes :
```csharp
public static async Task<string> FindParent(DigitalTwinsClient client, string child, string relname, ILogger log)
{
    // Find parent using incoming relationships
    try
    {
        AsyncPageable<IncomingRelationship> rels = client.GetIncomingRelationshipsAsync(child);

        await foreach (IncomingRelationship ie in rels)
        {
            if (ie.RelationshipName == relname)
                return (ie.SourceId);
        }
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error in retrieving parent:{exc.Status}:{exc.Message}");
    }
    return null;
}
```

Et la fonction utilitaire permettant de corriger le jumeau :
```csharp
public static async Task UpdateTwinProperty(DigitalTwinsClient client, string twinId, string propertyPath, object value, ILogger log)
{
    // If the twin does not exist, this will log an error
    try
    {
        // Update twin property
        UpdateOperationsUtility uou = new UpdateOperationsUtility();
        uou.AppendAddOp(propertyPath, value);
        await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error:{exc.Status}/{exc.Message}");
    }
}
```

Vous disposez maintenant d’une fonction Azure qui est équipée pour lire et interpréter les données provenant d’IoT Hub.

## <a name="debug-azure-function-apps-locally"></a>Déboguer les applications de fonction Azure localement

Il est possible de déboguer des fonctions Azure localement à l’aide d’un déclencheur Event Grid. Pour plus d’informations à ce sujet, consultez [Déboguer localement un déclencheur Event Grid](../azure-functions/functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’entrée et la sortie de données avec Azure Digital Twins :
* [Concepts : Intégration à d’autres services](concepts-integration.md)
