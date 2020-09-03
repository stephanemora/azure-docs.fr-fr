---
title: Ingérer la télémétrie depuis IoT Hub
titleSuffix: Azure Digital Twins
description: Découvrez comment ingérer les messages de télémétrie des appareils provenant d’IoT Hub.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 8/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8d720d77773e506a13f176723ab4583613f1e625
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291753"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Ingérer des données de télémétrie IoT Hub dans Azure Digital Twins

Azure Digital Twins se base sur les données des appareils IoT et d’autres sources. Pour les données d’appareil, il est courant d’utiliser la source [IoT Hub](../iot-hub/about-iot-hub.md) dans Azure Digital Twins.

Le processus d’ingestion des données dans Azure Digital Twins consiste à configurer une ressource de calcul externe, par exemple une [fonction Azure](../azure-functions/functions-overview.md), qui reçoit les données et utilise les [API DigitalTwins](how-to-use-apis-sdks.md) pour définir des propriétés ou déclencher des événements de télémétrie en conséquence pour les [jumeaux numériques](concepts-twins-graph.md). 

Ce document explique le processus d’écriture d’une fonction Azure pouvant ingérer des données de télémétrie à partir d’IoT Hub.

## <a name="prerequisites"></a>Prérequis

Avant de poursuivre avec cet exemple, vous devez mettre en place les ressources suivantes en guise de conditions préalables :
* **Un hub IoT**. Pour obtenir des instructions, consultez la section *Créer un hub IoT* de [ce démarrage rapide d’IoT Hub](../iot-hub/quickstart-send-telemetry-cli.md).
* **Une fonction Azure** avec les autorisations appropriées pour appeler votre instance de jumeau numérique. Pour obtenir des instructions, consultez [*Procédure : Configurer une fonction Azure pour le traitement des données*](how-to-create-azure-function.md). 
* **Une instance Azure Digital Twins** qui recevra la télémétrie de votre appareil. Pour obtenir des instructions, consultez [*Procédure : Configurer une instance Azure Digital Twins et l’authentification*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Exemple de scénario de télémétrie

Cet article explique comment envoyer des messages entre IoT Hub et Azure Digital Twins à l’aide d’une fonction Azure. Pour cela, il existe de nombreuses configurations et stratégies possibles. Cependant, l’exemple de cet article contient les éléments suivants :
* Un thermomètre situé dans IoT Hub, ayant un ID d’appareil connu
* Un jumeau numérique représentant l’appareil, avec un ID correspondant

> [!NOTE]
> Cet exemple utilise une correspondance d’ID simple entre l’ID de l’appareil et celui d’un jumeau numérique. Toutefois, il est possible de fournir des mappages plus sophistiqués entre l’appareil et son jumeau (par exemple, avec une table de mappage).

Chaque fois qu’un événement de télémétrie de température est envoyé par le thermomètre, la propriété *temperature* du jumeau numérique doit être mise à jour. Ce scénario est présenté dans un diagramme ci-dessous :

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Diagramme montrant un organigramme. Dans le graphique, un appareil IoT Hub envoie des données de télémétrie de température via IoT Hub à une fonction Azure, qui met à jour une propriété de température pour un jumeau dans Azure Digital Twins." border="false":::

## <a name="add-a-model-and-twin"></a>Ajouter un modèle et un jumeau

Vous aurez besoin d’un jumeau à mettre à jour avec les informations IoT Hub.

Le modèle se présente ainsi :
```JSON
{
  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "contents": [
    {
      "@type": "Property",
      "name": "Temperature",
      "schema": "double"
    }
  ]
}
```

Pour **télécharger ce modèle sur votre instance de jumeaux**, ouvrez Azure CLI et exécutez la commande suivante :

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

Vous devez ensuite **créer un jumeau à l’aide de ce modèle**. Utilisez la commande suivante pour créer un jumeau et définissez 0,0 comme valeur initiale de température.

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

La sortie d’une commande de création de jumeau réussie doit se présenter comme suit :
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-an-azure-function"></a>Création d’une fonction Azure

Cette section utilise les mêmes étapes de démarrage Visual Studio et le même squelette de fonction Azure que ceux de [*Procédure : Configurer une fonction Azure pour le traitement des données*](how-to-create-azure-function.md). Le squelette gère l’authentification et crée un client de service, prêt à traiter les données et à appeler les API Azure Digital Twins. 

Dans les étapes qui suivent, vous y ajouterez du code pour le traitement des événements de télémétrie IoT provenant d’IoT Hub.  

### <a name="add-telemetry-processing"></a>Ajouter le traitement des données de télémétrie
    
Les événements de télémétrie sont fournis sous la forme de messages provenant de l’appareil. Lorsque vous ajoutez du code de traitement des données de télémétrie, la première étape consiste à extraire la partie utile de ce message d’appareil à partir de l’événement Event Grid. 

Les différents types d’appareils peuvent structurer leurs messages différemment. Le code de **cette étape dépend donc de l’appareil connecté.** 

Le code suivant illustre un exemple d’appareil simple qui envoie des données de télémétrie au format JSON. Cet exemple est entièrement abordé dans le [Tutoriel  *: Connecter une solution de bout en bout*](./tutorial-end-to-end.md). Le code suivant trouve l’ID de l’appareil qui a envoyé le message, ainsi que la valeur de température.

```csharp
JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
var temperature = deviceMessage["body"]["Temperature"];
```

L’exemple de code suivant prend la valeur d’ID et de température et les utilise pour « corriger » (mettre à jour) ce jumeau.

```csharp
//Update twin using device temperature
var uou = new UpdateOperationsUtility();
uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
...
```

### <a name="update-your-azure-function-code"></a>Mettre à jour le code de votre fonction Azure

Maintenant que vous comprenez le code des exemples précédents, ouvrez Visual Studio et remplacez le code de votre fonction Azure par cet exemple de code.

```csharp
using System;
using System.Net.Http;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace IotHubtoTwins
{
    public class IoTHubtoTwins
    {
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("IoTHubtoTwins")]
        public async void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");

            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(
                    new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions 
                    { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
            
                if (eventGridEvent != null && eventGridEvent.Data != null)
                {
                    log.LogInformation(eventGridEvent.Data.ToString());

                    // Reading deviceId and temperature for IoT Hub JSON
                    JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                    string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
                    var temperature = deviceMessage["body"]["Temperature"];
                    
                    log.LogInformation($"Device:{deviceId} Temperature is:{temperature}");

                    //Update twin using device temperature
                    var uou = new UpdateOperationsUtility();
                    uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
                    await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
                }
            }
            catch (Exception e)
            {
                log.LogError($"Error in ingest function: {e.Message}");
            }
        }
    }
}
```

## <a name="connect-your-function-to-iot-hub"></a>Connecter votre fonction à IoT Hub

1. Configurez une destination d’événement pour les données de concentrateur. Dans le [portail Azure](https://portal.azure.com/), accédez à votre instance IoT Hub. Sous **Événements**, créez un abonnement pour votre fonction Azure. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Capture d’écran du Portail Azure qui montre l’ajout d’un abonnement aux événements.":::

2. Dans la page **Créer un abonnement aux événements**, renseignez les champs de la façon suivante :
    1. Sous **Nom**, nommez l’abonnement que vous souhaitez.
    2. Sous **Schéma d’événement**, choisissez **Schéma Event Grid**.
    3. Sous **Nom de la rubrique système**, choisissez un nom unique.
    4. Sous **Types d’événements**, choisissez **Télémétrie d’appareil** comme type d’événements à filtrer.
    5. Sous **Détails des points de terminaison**, sélectionnez votre fonction Azure comme point de terminaison.

    :::image type="content" source="media/how-to-ingest-iot-hub-data/event-subscription-2.png" alt-text="Capture d’écran du Portail Azure qui montre les détails de l’abonnement aux événements":::

## <a name="send-simulated-iot-data"></a>Envoyer des données IoT simulées

Pour tester votre nouvelle fonction d’entrée, utilisez le simulateur d’appareil à partir du [*Tutoriel : Connecter une solution de bout en bout*](./tutorial-end-to-end.md). Ce tutoriel est piloté par un exemple de projet écrit en C#. L’exemple de code se trouve ici : [Exemples Azure Digital Twins](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples). Vous utiliserez le projet **DeviceSimulator** dans ce référentiel.

Dans ce tutoriel intégral, vous allez effectuer les étapes suivantes :
1. [*Inscrire l’appareil simulé auprès d’IoT Hub*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*Configurer et démarrer la simulation*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Valider vos résultats

Lors de l’exécution du simulateur d’appareil ci-dessus, la valeur de température de votre jumeau numérique sera modifiée. Dans Azure CLI, exécutez la commande suivante pour afficher la valeur de la température.

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

Votre sortie doit contenir une valeur de température comme ceci :

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Pour voir la modification de la valeur, exécutez à plusieurs reprises la commande de requête ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’entrée et la sortie de données avec Azure Digital Twins :
* [*Concepts : Intégration à d’autres services*](concepts-integration.md)
