---
title: Guide du développeur - Préversion d’IoT Plug-and-Play | Microsoft Docs
description: Description d’IoT Plug-and-Play pour les développeurs.
author: rido-min
ms.author: rmpablos
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9e6d13fedbfa495448164c1354868e12992dd71c
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88856033"
---
# <a name="iot-plug-and-play-preview-developer-guide"></a>Guide du développeur de la préversion d’IoT Plug-and-Play

La préversion d’IoT Plug-and-Play permet de créer des smart devices qui publient leurs fonctionnalités sur les applications Azure IoT. Les appareils IoT Plug-and-Play n’ont pas besoin de configuration manuelle quand un client les connecte à des applications compatibles avec IoT Plug-and-Play.

Ce guide décrit les étapes de base nécessaires pour créer un appareil qui suit les [conventions IoT Plug-and-Play](concepts-convention.md) et les API REST disponibles pour interagir avec l’appareil.

Pour créer un appareil IoT Plug-and-Play, procédez comme suit :

1. Vérifiez que votre appareil utilise le protocole MQTT ou MQTT sur WebSockets pour se connecter à Azure IoT Hub.
1. Créez un modèle [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) pour décrire votre appareil. Pour plus d’informations, consultez [Présentation des composants dans les modèles IoT Plug-and-Play](concepts-components.md).
1. Mettez à jour votre appareil de façon à annoncer `model-id` dans le cadre de la connexion de l’appareil.
1. Implémentez les données de télémétrie, les propriétés et les commandes suivant les [conventions IoT Plug-and-Play](concepts-convention.md).

Une fois que l’implémentation de votre appareil est prête, utilisez [Azure IoT Explorer](howto-use-iot-explorer.md) pour confirmer qu’il suit les conventions IoT Plug-and-Play.

> [!Tip]
> Tous les fragments de code de cet article sont en C# , mais les concepts s’appliquent à tous les kits de développement logiciel (SDK) disponibles pour C, Python, Node et Java.

## <a name="model-id-announcement"></a>Annonce de l’ID de modèle

Pour annoncer l’ID de modèle, l’appareil doit l’inclure dans les informations de connexion :

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

La nouvelle surcharge de `ClientOptions` est disponible dans toutes les méthodes `DeviceClient` utilisées pour initialiser une connexion.

L’annonce de l’ID de modèle a été ajoutée aux versions suivantes des kits SDK.

|Kit SDK|Version|
|---|-------|
|C-SDK|1.3.9|
|.NET|1.27.0|
|Java|1.14.0|
|Nœud|1.17.0|
|Python|2.1.4|

## <a name="dps-payload"></a>Charge utile DPS

Les appareils qui utilisent [Device Provisioning Service (DPS)](/iot-dps/) peuvent inclure le `modelId` à utiliser pendant le processus de provisionnement à l’aide de la charge utile JSON suivante.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implémentation des données de télémétrie, des propriétés et des commandes

Comme le décrit la section [Présentation des composants dans les modèles IoT Plug-and-Play](concepts-components.md), les générateurs d’appareils doivent décider s’ils souhaitent utiliser des composants pour décrire leurs appareils, auquel cas ces derniers devront suivre les règles décrites dans cette section.

### <a name="telemetry"></a>Télémétrie

Aucune propriété spéciale n’est nécessaire pour les modèles dépourvus de composants.

Lorsque vous utilisez des composants, les appareils doivent définir une propriété de message avec le nom du composant :

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.Properties.Add("$.sub", componentName);
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await deviceClient.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Propriétés en lecture seule

Aucune construction spéciale n’est nécessaire pour les modèles dépourvus de composants :

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Le jumeau d’appareil est mis à jour avec la propriété rapportée suivante :

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

Lorsque vous utilisez des composants, les propriétés doivent être créées dans le nom du composant :

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Le jumeau d’appareil est mis à jour avec la propriété rapportée suivante :

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Propriétés accessibles en écriture

Ces propriétés peuvent être définies par l’appareil ou mises à jour par la solution. Si la solution met à jour une propriété, le client reçoit une notification sous la forme d’un rappel dans `DeviceClient`. Pour respecter les conventions IoT Plug-and-Play, l’appareil doit informer le service que la propriété a bien été reçue.

#### <a name="report-a-writable-property"></a>Signalement d’une propriété accessible en écriture

Quand un appareil rapporte une propriété accessible en écriture, il doit inclure les valeurs `ack` définies dans les conventions.

Pour rapporter une propriété accessible en écriture dépourvue de composants :

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Le jumeau d’appareil est mis à jour avec la propriété rapportée suivante :

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Pour rapporter une propriété accessible en écriture à partir d’un composant, le jumeau doit inclure un marqueur :

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Le jumeau d’appareil est mis à jour avec la propriété rapportée suivante :

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Abonnement aux mises à jour de propriétés souhaitées

Les services peuvent mettre à jour les propriétés souhaitées qui déclenchent une notification sur les appareils connectés. Cette notification inclut les propriétés souhaitées mises à jour, y compris le numéro de version identifiant la mise à jour. Les appareils doivent répondre avec le même message `ack` que les propriétés rapportées.

Les modèles dépourvus de composants voient la propriété unique et créent le message `ack` rapporté avec la version reçue :

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Le jumeau d’appareil montre la propriété dans les sections desired et reported :

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Les modèles comportant des composants reçoivent les propriétés souhaitées encapsulées avec le nom du composant et doivent renvoyer la propriété rapportée `ack` :

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Le jumeau d’appareil des composants montre les sections desired et reported de la façon suivante :

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Commandes

Les modèles dépourvus de composants reçoivent le nom de la commande telle qu’elle a été appelée par le service.

Les modèles comportant des composants reçoivent le nom de la commande préfixé avec le composant et le séparateur `*`.

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>Charge utile de demande et de réponse

Les commandes utilisent des types pour définir leur charge utile de demande et de réponse. Un appareil doit désérialiser le paramètre d’entrée entrant et sérialiser la réponse. L’exemple suivant montre comment implémenter une commande avec des types complexes définis dans les charges utiles :

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

Les extraits de code suivants montrent comment un appareil implémente cette définition de commande, notamment les types utilisés pour permettre la sérialisation et la désérialisation :

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> Le nom des demandes et des réponses n’est pas présent dans les charges utiles sérialisées transmises sur le réseau.

## <a name="interact-with-the-device"></a>Interaction avec l’appareil 

IoT Plug-and-Play vous permet d’utiliser les appareils qui ont annoncé leur ID de modèle auprès de votre hub IoT. Par exemple, vous pouvez accéder directement aux propriétés et aux commandes d’un appareil.

Pour utiliser un appareil IoT Plug-and-Play connecté à votre hub IoT, utilisez l’API REST IoT Hub ou l’un des kits SDK de langage IoT. Les exemples suivants utilisent l’API REST IoT Hub. La version actuelle de cet API est `2020-05-31-preview`. Ajoutez `?api-version=2020-05-31` à vos appels de l'API REST.

Si votre thermostat s'appelle `t-123`, vous pouvez obtenir toutes les propriétés des différentes interfaces implémentées par votre appareil à l'aide d'un appel GET de l'API REST :

```REST
GET /digitalTwins/t-123
```

Cet appel inclura la propriété JSON `$metadata.$model` avec l’ID de modèle annoncé par l’appareil.

Toutes les propriétés des différentes interfaces sont accessibles à l’aide du modèle d’API REST `GET /DigitalTwin/{device-id}`, où `{device-id}` correspond à l’identificateur de l’appareil :

```REST
GET /digitalTwins/{device-id}
```

Vous pouvez appeler les commandes d’appareil IoT Plug-and-Play directement. Si le composant `Thermostat` de l’appareil `t-123` comporte une commande `restart`, vous pouvez l’appeler avec un appel POST de l’API REST :

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

Plus généralement, les commandes peuvent être appelées à l’aide de ce modèle d’API REST :

- `device-id` : identificateur de l’appareil.
- `component-name` : nom de l’interface dans la section implements du modèle de capacité d’appareil.
- `command-name` : nom de la commande.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert la modélisation d’appareil, voici quelques ressources supplémentaires :

- [Langage DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK d’appareils C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [API REST IoT](https://docs.microsoft.com/rest/api/iothub/device)
- [Composants de modèle](./concepts-components.md)
