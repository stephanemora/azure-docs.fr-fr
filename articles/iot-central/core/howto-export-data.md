---
title: Exporter des données à partir d'Azure IoT Central | Microsoft Docs
description: Comment utiliser la nouvelle exportation de données pour exporter vos données IoT vers Azure et des destinations cloud personnalisées.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 03/24/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1, contperf-fy21q3
ms.openlocfilehash: 7d57f24f8cb4b59ce9b9cd5853be11fb2d104d75
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277893"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>Exporter des données IoT vers des destinations cloud à l'aide des fonctionnalités d'exportation de données

> [!Note]
> Cet article décrit les fonctionnalités d'exportation de données d'IoT Central. Pour plus d'informations sur la fonctionnalité d'exportation de données héritée, consultez la section [Exporter des données IoT vers des destinations cloud à l'aide de la fonctionnalité d'exportation de données (héritée)](./howto-export-data-legacy.md).

Cet article explique comment utiliser la nouvelle fonctionnalité d'exportation de données d'Azure IoT Central. Utilisez cette fonctionnalité pour une exportation continue des données IoT filtrées et enrichies à partir de votre application IoT Central. La fonctionnalité d'exportation de données envoie (push) les modifications en quasi-temps réel vers d'autres parties de votre solution cloud pour vous fournir des insights, des analyses et un stockage à chaud.

Par exemple, vous pouvez :

- Exporter en continu des données de télémétrie, de modifications de propriétés, de cycle de vie d’appareil et de cycle de vie de modèle d’appareil au format JSON en quasi-temps réel.
- filtrer les flux de données pour exporter des données qui remplissent des conditions personnalisées ;
- enrichir les flux de données avec des valeurs personnalisées et des valeurs de propriété à partir de l'appareil ;
- envoyer les données vers des destinations telles qu'Azure Event Hubs, Azure Service Bus, Stockage Blob Azure et des points de terminaison de webhook.

> [!Tip]
> Quand vous activez l’exportation de données, vous obtenez seulement les données à partir de ce moment. Pour le moment, vous ne pouvez pas récupérer les données d’une période pendant laquelle l’exportation de données était désactivée. Pour conserver un historique des données plus étendu, activez l’exportation de données au plus tôt.

## <a name="prerequisites"></a>Prérequis

Pour utiliser les fonctionnalités d'exportation de données, vous devez disposer d'une [application V3](howto-get-app-info.md) ainsi que d'une autorisation d'[exportation de données](howto-manage-users-roles.md).

Si vous avez une application v2, consultez [Migrer votre application IoT Central v2 vers v3](howto-migrate.md).

## <a name="set-up-export-destination"></a>Définir la destination de l’exportation

Votre destination d’exportation doit exister avant la configuration de l’exportation de données. Les types de destination actuellement disponibles sont les suivants :

- Hubs d'événements Azure
- File d’attente Azure Service Bus
- Rubrique Azure Service Bus
- Stockage Blob Azure
- webhook

### <a name="create-an-event-hubs-destination"></a>Créer une destination Event Hubs

En l’absence d’espace de noms Event Hubs existant vers lequel exporter, suivez ces étapes :

1. Créez un [espace de noms Event Hubs dans le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Pour en savoir plus, consultez les [documents sur Azure Event Hubs](../../event-hubs/event-hubs-create.md).

1. Créez un Event Hub dans votre espace de noms Event Hubs. Accédez à votre espace de noms, puis sélectionnez **+ Event Hub**, en haut, pour créer une instance Event Hub.

1. Générez une clé qui vous servira lors de la configuration de votre fonctionnalité d'exportation de données dans IoT Central :

    - Sélectionnez l'instance d'Event Hub que vous avez créée.
    - Sélectionnez **Paramètres > Stratégies d'accès partagé**.
    - Créez une clé, ou choisissez une clé existante qui dispose des autorisations **Envoyer**.
    - Copiez la chaîne de connexion primaire ou secondaire. Cette chaîne de connexion vous permettra de configurer une nouvelle destination dans IoT Central.
    - Vous pouvez également générer une chaîne de connexion pour l'ensemble de l'espace de noms Event Hubs :
        1. Accédez à votre espace de noms Event Hubs sur le portail Azure.
        2. Sous **Paramètres**, sélectionnez **Stratégies d’accès partagé**
        3. Créez une clé, ou choisissez une clé existante qui dispose des autorisations **Envoyer**.
        4. Copiez la chaîne de connexion primaire ou secondaire
        
### <a name="create-a-service-bus-queue-or-topic-destination"></a>Créer une file d’attente Service Bus ou une destination de rubrique

En l’absence d’espace de noms Service Bus vers lequel exporter, suivez ces étapes :

1. Créez un [espace de noms Service Bus dans le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Pour en savoir plus, consultez les [documents sur Azure Service Bus](../../service-bus-messaging/service-bus-create-namespace-portal.md).

1. Pour créer une file d’attente ou une rubrique de destination d’exportation, accédez à votre espace de noms Service Bus, puis sélectionnez **+ File d’attente** ou **+ Rubrique**, en haut.

1. Générez une clé qui vous servira lors de la configuration de votre fonctionnalité d'exportation de données dans IoT Central :

    - Sélectionnez la file d'attente ou la rubrique que vous avez créée.
    - Sélectionnez **Paramètres/Stratégies d'accès partagé**.
    - Créez une clé, ou choisissez une clé existante qui dispose des autorisations **Envoyer**.
    - Copiez la chaîne de connexion primaire ou secondaire. Cette chaîne de connexion vous permettra de configurer une nouvelle destination dans IoT Central.
    - Vous pouvez également générer une chaîne de connexion pour l'ensemble de l'espace de noms Service Bus :
        1. Accédez à votre espace de noms Service Bus sur le Portail Azure.
        2. Sous **Paramètres**, sélectionnez **Stratégies d’accès partagé**
        3. Créez une clé, ou choisissez une clé existante qui dispose des autorisations **Envoyer**.
        4. Copiez la chaîne de connexion primaire ou secondaire

### <a name="create-an-azure-blob-storage-destination"></a>Créer une destination de stockage Blob Azure

En l’absence de compte de stockage Azure vers lequel exporter, suivez ces étapes :

1. Créez un [compte de stockage sur le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Découvrez comment créer des [comptes de stockage Blob Azure](../../storage/blobs/storage-quickstart-blobs-portal.md) ou des [comptes de stockage Azure Data Lake Storage v2](../../storage/common/storage-account-create.md). L’exportation de données peut uniquement écrire des données dans des comptes de stockage qui prennent en charge les objets blob de blocs. La liste suivante répertorie les types de compte de stockage compatibles connus :

    |Niveau de performances|Type de compte|
    |-|-|
    |standard|Usage général v2|
    |standard|Usage général v1|
    |standard|Stockage d'objets blob|
    |Premium|Stockage d’objets blob de blocs|

1. Pour créer un conteneur sur votre compte de stockage, accédez à celui-ci. Sous **Service blob**, sélectionnez **Parcourir les objets blob**. Sélectionnez **+ Conteneur**, en haut, pour créer un conteneur.

1. Générez une chaîne de connexion pour votre compte de stockage en accédant à **Paramètres > Clés d'accès**. Copiez une des deux chaînes de connexion.

### <a name="create-a-webhook-endpoint"></a>Créer un point de terminaison webhook

Vous pouvez exporter des données vers un point de terminaison de webhook HTTP disponible publiquement. Vous pouvez créer un point de terminaison de webhook test à l'aide de [RequestBin](https://requestbin.net/). RequestBin limite la requête lorsque le nombre maximal de requêtes est atteint :

1. Ouvrez [RequestBin](https://requestbin.net/).
2. Créez un point de terminaison RequestBin et copiez **l’URL du fichier Bin**. Cette URL vous servira lors du test de votre fonctionnalité d'exportation de données.

## <a name="set-up-data-export"></a>Configurer l’exportation de données

Maintenant que vous disposez d'une destination vers laquelle exporter vos données, configurez la fonctionnalité d'exportation de données dans votre application IoT Central :

1. Connectez-vous à votre application IoT Central.

1. Dans le volet gauche, sélectionnez **Exportation de données**.

    > [!Tip]
    > Si vous ne voyez pas **Exportation de données** dans le volet gauche, cela signifie que vous n’avez pas les autorisations nécessaires pour configurer l’exportation de données dans votre application. Contactez un administrateur pour configurer l’exportation de données.

1. Sélectionnez **+ Nouvelle exportation**.

1. Entrez un nom d'affichage pour votre nouvelle exportation et assurez-vous que l'exportation de données est **activée**.

1. Choisissez le type de données à exporter. Le tableau suivant répertorie les types d'exportation de données pris en charge :

    | Type de données | Description | Format de données |
    | :------------- | :---------- | :----------- |
    |  Télémétrie | Exportez des messages de télémétrie à partir d’appareils en quasi-temps réel. Chaque message exporté contient le contenu complet du message d'origine de l'appareil, normalisé.   |  [Format du message de télémétrie](#telemetry-format)   |
    | Modifications de la propriété | Exportez les modifications apportées aux propriétés de l’appareil et du cloud en quasi-temps réel. Pour les propriétés en lecture seule de l'appareil, les modifications apportées aux valeurs signalées sont exportées. Pour les propriétés en lecture-écriture, les valeurs signalées et souhaitées sont exportées. | [Format du message de modification de la propriété](#property-changes-format) |
    | Cycle de vie de l’appareil | Exporter des événements inscrits et supprimés d’appareil. | [Format du message relatif aux changements de cycle de vie d’appareil](#device-lifecycle-changes-format) |
    | Cycle de vie de modèle d’appareil | Exportez les changements de modèle d’appareil publiés, dont les créations, les mises à jour et les suppressions. | [Format de message relatif aux changements de cycle de vie d’appareil](#device-template-lifecycle-changes-format) | 

1. Vous pouvez également ajouter des filtres pour réduire la quantité de données exportées. Différents types de filtres sont disponibles pour chaque type d’exportation de données : <a name="DataExportFilters"></a>
    
    | Type de données | Filtres disponibles| 
    |--------------|------------------|
    |Télémétrie|<ul><li>Filtrer par nom d’appareil, ID d’appareil et modèle d’appareil</li><li>Filtrer le flux de façon à ce qu’il contienne uniquement la télémétrie correspondant aux conditions de filtre</li><li>Filtrer le flux de façon à ce qu’il contienne uniquement la télémétrie des appareils dont les propriétés correspondent aux conditions de filtre</li><li>Filtrer le flux de façon à ce qu’il contienne uniquement la télémétrie contenant des *propriétés de message* correspondant à la condition de filtre. Les *propriétés de message* (également appelées *propriétés d’application*) sont envoyées dans un ensemble de paires clé-valeur sur chaque message de télémétrie éventuellement envoyé par des appareils qui utilisent les kits de développement logiciel (SDK) d’appareil. Pour créer un filtre de propriété de message, entrez la clé de la propriété de message que vous recherchez, et spécifiez une condition. Seuls les messages de télémétrie dont les propriétés correspondent à la condition de filtre spécifiée sont exportés. [En savoir plus sur les propriétés d’application dans des documents IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md) </li></ul>|
    |Modifications de la propriété|<ul><li>Filtrer par nom d’appareil, ID d’appareil et modèle d’appareil</li><li>Filtrer le flux de façon à ce qu’il contienne uniquement les changements de propriété correspondant aux conditions de filtre</li></ul>|
    |Cycle de vie de l’appareil|<ul><li>Filtrer par nom d’appareil, ID d’appareil et modèle d’appareil</li><li>Filtrer le flux de façon à ce qu’il contienne uniquement les changements des appareils dont les propriétés correspondent aux conditions de filtre</li></ul>|
    |Cycle de vie de modèle d’appareil|<ul><li>Filtrer par modèle d’appareil</li></ul>|
    
1. Si vous le souhaitez, vous pouvez également enrichir les messages exportés avec des métadonnées supplémentaires dans les paires clé-valeur. Les enrichissements suivants sont disponibles pour les types d’exportations de données de télémétrie et de changement de propriétés : <a name="DataExportEnrichmnents"></a>
    - **Chaîne personnalisée** : Ajoute une chaîne statique personnalisée à chaque message. Entrez n’importe quelle clé, puis entrez une valeur de chaîne.
    - **Property** : Ajoute la valeur actuelle de la propriété rapportée d’appareil ou de la propriété de cloud à chaque message. Entrez une clé, puis choisissez une propriété d’appareil ou de cloud. Si le message exporté provient d'un appareil qui ne dispose pas de la propriété spécifiée, l'enrichissement ne s'applique pas à ce message.

1. Ajoutez une nouvelle destination ou ajoutez une destination que vous avez déjà créée. Sélectionnez le lien **Créer** et ajoutez les informations suivantes :

    - **Nom de la destination** : nom d'affichage de la destination dans IoT Central.
    - **Type de destination** : choisissez le type de destination. Si vous n'avez pas encore configuré votre destination, consultez [Configurer une destination d'exportation](#set-up-export-destination).
    - Pour Azure Event Hubs, ou pour une file d'attente ou une rubrique Azure Service Bus, collez la chaîne de connexion de votre ressource et, si nécessaire, entrez le nom de l'instance d'Event Hubs, de la file d'attente ou de la rubrique en respectant la casse.
    - Pour le Stockage Blob Azure, collez la chaîne de connexion de votre ressource et, si nécessaire, entrez le nom du conteneur en respectant la casse.
    - Pour Webhook, collez l’URL de rappel de votre point de terminaison webhook. Vous pouvez éventuellement configurer une autorisation de webhook (OAuth 2.0 et le jeton d’autorisation) et ajouter des en-têtes personnalisés. 
        - Pour OAuth 2.0, seul le flux d’informations d’identification du client est pris en charge. Une fois la destination enregistrée, IoT Central communique avec votre fournisseur OAuth pour récupérer un jeton d’autorisation. Ce jeton sera attaché à l’en-tête « Authorization » pour chaque message envoyé à cette destination.
        - Pour le jeton d’autorisation, vous pouvez spécifier une valeur de jeton qui sera directement attachée à l’en-tête « Authorization » pour chaque message envoyé à cette destination.
    - Sélectionnez **Create** (Créer).

1. Sélectionnez **+ Destination** et choisissez une destination dans la liste déroulante. Vous pouvez ajouter cinq destinations par exportation.

1. Une fois la configuration de votre exportation terminée, sélectionnez **Enregistrer**. Au bout de quelques minutes, vos données apparaissent dans vos destinations.

## <a name="monitor-your-export"></a>Superviser votre exportation

En plus de voir l’état de vos exportations dans IoT Central, vous pouvez utiliser [Azure Monitor](../../azure-monitor/overview.md) pour voir la quantité de données que vous exportez et les erreurs d’exportation. Vous pouvez accéder aux métriques d’intégrité des appareils et des exportations dans les graphiques du portail Azure, avec une API REST ou avec des requêtes dans PowerShell ou Azure CLI. Vous pouvez superviser les métriques d’exportation de données suivantes dans Azure Monitor :

- Nombre de messages entrants à exporter avant l’application de filtres.
- Nombre de messages qui passent par des filtres.
- Nombre de messages correctement exportés vers les destinations.
- Nombre d'erreurs rencontrées.

Pour plus d’informations, consultez [Surveiller l’intégrité globale des appareils connectés à une application IoT Central](howto-monitor-application-health.md).

## <a name="destinations"></a>Destinations

### <a name="azure-blob-storage-destination"></a>Destination du Stockage Blob Azure

Les données sont exportées une fois par minute et chaque fichier contient le lot de modifications apportées depuis la précédente exportation. Les données exportées sont enregistrées au format JSON. Par défaut, les chemins d'accès aux données exportées vers votre compte de stockage sont les suivants :

- Données de télémétrie : _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Modifications apportées aux propriétés : _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Pour parcourir les fichiers exportés sur le portail Azure, accédez à un fichier et sélectionnez **Modifier le blob**.

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Destinations Azure Event Hubs et Azure Service Bus

Les données sont exportées en quasi-temps réel. Les données se trouvent dans le corps du message, au format JSON encodé au format UTF-8.

Le conteneur d'annotations ou de propriétés système du message contient les champs `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` et `iotcentral-message-type` qui ont les mêmes valeurs que les champs correspondants dans le corps du message.

### <a name="webhook-destination"></a>Destination du webhook

Pour les destinations de webhooks, les données sont également exportées en quasi-temps réel. Les données contenues dans le corps du message sont au même format que pour Event Hubs et Service Bus.

## <a name="telemetry-format"></a>Format des données de télémétrie.

Chaque message exporté contient un formulaire normalisé du message complet que l'appareil a envoyé dans le corps du message. Le message est au format JSON et encodé en UTF-8. Chaque message contient les informations suivantes :

- `applicationId`: ID de l'application IoT Central.
- `messageSource`: source du message - `telemetry`.
- `deviceId`:  ID de l'appareil qui a envoyé le message de télémétrie.
- `schema`: nom et version du schéma de charge utile.
- `templateId`: ID du modèle d'appareil associé à l'appareil.
- `enqueuedTime` : Heure à laquelle IoT Central a reçu ce message.
- `enrichments`: enrichissements configurés lors de l'exportation.
- `messageProperties`: propriétés supplémentaires envoyées par l'appareil avec le message. Ces propriétés sont parfois appelées *propriétés d'application*. [Pour en savoir plus, consultez la documentation d'IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

Pour Event Hubs et Service Bus, IoT Central exporte rapidement un nouveau message après l’avoir reçu d’un appareil. Dans les propriétés utilisateur (également appelées propriétés d'application) de chaque message, les propriétés `iotcentral-device-id`, `iotcentral-application-id` et `iotcentral-message-source` sont automatiquement incluses.

Pour le stockage Blob, les messages sont regroupés par lot et exportés une fois par minute.

L’exemple suivant montre un message de télémétrie exporté :

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
### <a name="message-properties"></a>Propriétés de message

Les messages de télémétrie ont des propriétés pour les métadonnées en plus de la charge utile de télémétrie. L’extrait de code précédent montre des exemples de messages système tels que `deviceId` et `enqueuedTime`. Pour en savoir plus sur les propriétés des messages système, consultez [Propriétés système des messages IoT Hub D2C](../../iot-hub/iot-hub-devguide-messages-construct.md#system-properties-of-d2c-iot-hub-messages).

Vous pouvez ajouter des propriétés aux messages de télémétrie si vous devez ajouter des métadonnées personnalisées à ces derniers. Par exemple, vous devez ajouter un horodatage quand l’appareil crée le message.

L’extrait de code suivant montre comment ajouter la propriété `iothub-creation-time-utc` au message quand vous le créez sur l’appareil :

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.properties.add("iothub-creation-time-utc", new Date().toISOString());
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

# <a name="java"></a>[Java](#tab/java)

```java
private static void sendTemperatureTelemetry() {
  String telemetryName = "temperature";
  String telemetryPayload = String.format("{\"%s\": %f}", telemetryName, temperature);

  Message message = new Message(telemetryPayload);
  message.setContentEncoding(StandardCharsets.UTF_8.name());
  message.setContentTypeFinal("application/json");
  message.setProperty("iothub-creation-time-utc", Instant.now().toString());

  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
  log.debug("My Telemetry: Sent - {\"{}\": {}°C} with message Id {}.", telemetryName, temperature, message.getMessageId());
  temperatureReadings.put(new Date(), temperature);
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
private async Task SendTemperatureTelemetryAsync()
{
  const string telemetryName = "temperature";

  string telemetryPayload = $"{{ \"{telemetryName}\": {_temperature} }}";
  using var message = new Message(Encoding.UTF8.GetBytes(telemetryPayload))
  {
      ContentEncoding = "utf-8",
      ContentType = "application/json",
  };
  message.Properties.Add("iothub-creation-time-utc", DateTime.UtcNow.ToString("yyyy-MM-ddTHH:mm:ssZ"));
  await _deviceClient.SendEventAsync(message);
  _logger.LogDebug($"Telemetry: Sent - {{ \"{telemetryName}\": {_temperature}°C }}.");
}
```

# <a name="python"></a>[Python](#tab/python)

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.custom_properties["iothub-creation-time-utc"] = datetime.now(timezone.utc).isoformat()
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

---

L’extrait de code suivant montre cette propriété dans le message exporté vers le stockage Blob :

```json
{
  "applicationId":"5782ed70-b703-4f13-bda3-1f5f0f5c678e",
  "messageSource":"telemetry",
  "deviceId":"sample-device-01",
  "schema":"default@v1",
  "templateId":"urn:modelDefinition:mkuyqxzgea:e14m1ukpn",
  "enqueuedTime":"2021-01-29T16:45:39.143Z",
  "telemetry":{
    "temperature":8.341033560421833
  },
  "messageProperties":{
    "iothub-creation-time-utc":"2021-01-29T16:45:39.021Z"
  },
  "enrichments":{}
}
```

## <a name="property-changes-format"></a>Format des modifications de propriétés

Chaque message ou enregistrement représente une modification apportée à une propriété de l’appareil ou du cloud. Pour les propriétés de l'appareil, seules les modifications apportées à la valeur signalée sont exportées en tant que message distinct. Les informations incluses dans le message exporté sont les suivantes :

- `applicationId`: ID de l'application IoT Central.
- `messageSource`: source du message - `properties`.
- `messageType`: `cloudPropertyChange`, `devicePropertyDesiredChange` ou `devicePropertyReportedChange`.
- `deviceId`:  ID de l'appareil qui a envoyé le message de télémétrie.
- `schema`: nom et version du schéma de charge utile.
- `enqueuedTime` : Heure à laquelle IoT Central a détecté cette modification.
- `templateId`: ID du modèle d'appareil associé à l'appareil.
- `enrichments`: enrichissements configurés lors de l'exportation.

Pour Event Hubs et Service Bus, IoT Central exporte de nouvelles données de messages à la file d’attente ou à la rubrique de votre Event Hub ou Service Bus en quasi-temps réel. Dans les propriétés utilisateur (également appelées propriétés d'application) de chaque message, les propriétés `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` et `iotcentral-message-type` sont automatiquement incluses.

Pour le stockage Blob, les messages sont regroupés par lot et exportés une fois par minute.

L’exemple suivant présente un message de modification des propriétés exportées reçu dans le stockage Blob Azure.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "name": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="device-lifecycle-changes-format"></a>Format des changements du cycle de vie d’appareil

Chaque message ou enregistrement représente une modification apportée à un appareil. Les informations incluses dans le message exporté sont les suivantes :

- `applicationId`: ID de l'application IoT Central.
- `messageSource`: source du message - `deviceLifecycle`.
- `messageType` : `registered` ou `deleted`.
- `deviceId` : ID de l’appareil modifié.
- `schema`: nom et version du schéma de charge utile.
- `templateId`: ID du modèle d'appareil associé à l'appareil.
- `enqueuedTime` : Heure à laquelle cette modification est intervenue dans IoT Central.
- `enrichments`: enrichissements configurés lors de l'exportation.

Pour Event Hubs et Service Bus, IoT Central exporte de nouvelles données de messages à la file d’attente ou à la rubrique de votre Event Hub ou Service Bus en quasi-temps réel. Dans les propriétés utilisateur (également appelées propriétés d'application) de chaque message, les propriétés `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` et `iotcentral-message-type` sont automatiquement incluses.

Pour le stockage Blob, les messages sont regroupés par lot et exportés une fois par minute.

L’exemple suivant présente un message de cycle de vie d’appareil exporté reçu dans le service Stockage Blob Azure.

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceLifecycle",
  "messageType": "registered",
  "deviceId": "1vzb5ghlsg1",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-01-01T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}
```
## <a name="device-template-lifecycle-changes-format"></a>Format des changements de cycle de vie d’appareil

Chaque message ou enregistrement représente une modification apportée à un modèle d’appareil publié. Les informations incluses dans le message exporté sont les suivantes :

- `applicationId`: ID de l'application IoT Central.
- `messageSource`: source du message - `deviceTemplateLifecycle`.
- `messageType` : `created`, `updated` ou `deleted`.
- `schema`: nom et version du schéma de charge utile.
- `templateId`: ID du modèle d'appareil associé à l'appareil.
- `enqueuedTime` : Heure à laquelle cette modification est intervenue dans IoT Central.
- `enrichments`: enrichissements configurés lors de l'exportation.

Pour Event Hubs et Service Bus, IoT Central exporte de nouvelles données de messages à la file d’attente ou à la rubrique de votre Event Hub ou Service Bus en quasi-temps réel. Dans les propriétés utilisateur (également appelées propriétés d'application) de chaque message, les propriétés `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` et `iotcentral-message-type` sont automatiquement incluses.

Pour le stockage Blob, les messages sont regroupés par lot et exportés une fois par minute.

L’exemple suivant présente un message de cycle de vie d’appareil exporté reçu dans le service Stockage Blob Azure.

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceTemplateLifecycle",
  "messageType": "created",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-01-01T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>Comparaison entre la fonctionnalité d'exportation de données héritée et la nouvelle fonctionnalité d'exportation de données

Le tableau suivant présente les différences entre la fonctionnalité d'[exportation de données héritée](howto-export-data-legacy.md) et la nouvelle fonctionnalité d'exportation de données :

| Fonctionnalités  | Exportation de données héritée | Nouvelle exportation de données |
| :------------- | :---------- | :----------- |
| Types de données disponibles | Télémétrie, appareils, modèles d’appareil | Télémétrie, modifications de propriétés, modifications de cycle de vie d’appareil, modifications de cycle de vie de modèle d’appareil |
| Filtrage | None | Dépend du type de données exporté. Pour la télémétrie, le filtrage par télémétrie, les propriétés de messages, les valeurs de propriété |
| Enrichissements | None | Enrichir avec une chaîne personnalisée ou une valeur de propriété sur l’appareil |
| Destinations | Files d’attente et rubriques d’Azure Event Hubs, Azure Service Bus, Stockage Blob Azure | Les mêmes que pour la fonctionnalité d'exportation de données héritée plus les webhooks|
| Versions d’application prises en charge | V2, V3 | V3 uniquement |
| Limites notables | 5 exportations par application, 1 destination par exportation | 10 exportations-connexions de destination par application |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez utiliser la nouvelle fonctionnalité d'exportation de données, nous vous suggérons d'apprendre à [Utiliser la fonctionnalité analytique d'IoT Central](./howto-create-analytics.md).
