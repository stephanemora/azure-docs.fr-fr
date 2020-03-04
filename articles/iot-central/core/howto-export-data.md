---
title: Exporter vos données Azure IoT Central | Microsoft Docs
description: Comment exporter des données depuis votre application Azure IoT Central vers Azure Event Hubs, Azure Service Bus et Stockage Blob Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 0386897b6cecc27781626cfecd6f1f5f8a3752e4
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524381"
---
# <a name="export-iot-data-to-destinations-in-azure"></a>Exporter des données IoT vers des destinations dans Azure

*Cette rubrique s’applique aux administrateurs.*

Cet article décrit la façon d’utiliser la fonctionnalité d’exportation continue de données d’Azure IoT Central afin d’exporter vos données vers vos instances **Azure Event Hubs**, **Azure Service Bus** ou **Stockage Blob Azure**. Les données sont exportées au format JSON et peuvent inclure des informations de télémétrie, ainsi que des informations sur les appareils et les modèles d’appareil. Utilisez les données exportées pour :

- Insights et analytiques à chaud. Cette option inclut le déclenchement de règles personnalisées dans Azure Stream Analytics, le déclenchement de workflows personnalisés dans Azure Logic Apps ou la transmission des données à transformer via Azure Functions.
- Analytique des chemins à froid, comme les modèles d’apprentissage dans Azure Machine Learning ou l’analyse de tendances à long terme dans Microsoft Power BI.

> [!Note]
> Quand vous activez l’exportation de données continue, vous obtenez seulement les données à partir de ce moment. Pour le moment, vous ne pouvez pas récupérer les données d’une période pendant laquelle l’exportation de données continue est désactivée. Pour conserver un historique des données plus étendu, activez l’exportation de données continue tôt dans le processus.

## <a name="prerequisites"></a>Prérequis

Vous devez être administrateur de votre application IoT Central, ou disposer d’autorisations d’exportation de données.

## <a name="set-up-export-destination"></a>Définir la destination de l’exportation

Votre destination d’exportation doit exister avant la configuration de l’exportation de données continue.

### <a name="create-event-hubs-namespace"></a>Créer un espace de noms Event Hubs

En l’absence d’espace de noms Event Hubs existant vers lequel exporter, suivez ces étapes :

1. Créez un [espace de noms Event Hubs dans le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Pour en savoir plus, consultez les [documents sur Azure Event Hubs](../../event-hubs/event-hubs-create.md).

2. Choisissez un abonnement. Vous pouvez exporter des données vers des abonnements autres que celui de votre application IoT Central. Vous vous connecterez à l’aide d’une chaîne de connexion dans ce cas.

3. Créez un Event Hub dans votre espace de noms Event Hubs. Accédez à votre espace de noms, puis sélectionnez **+ Event Hub**, en haut, pour créer une instance Event Hub.

### <a name="create-service-bus-namespace"></a>Créer un espace de noms Service Bus

En l’absence d’espace de noms Service Bus vers lequel exporter, suivez ces étapes :

1. Créez un [espace de noms Service Bus dans le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Pour en savoir plus, consultez les [documents sur Azure Service Bus](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Choisissez un abonnement. Vous pouvez exporter des données vers des abonnements autres que celui de votre application IoT Central. Vous vous connecterez à l’aide d’une chaîne de connexion dans ce cas.

3. Accédez à votre espace de noms Service Bus, puis sélectionnez **+ File d’attente** ou **+ Rubrique**, en haut, pour créer une file d’attente ou une rubrique de destination d’exportation.

Lorsque vous choisissez un Service Bus comme destination d’exportation, les sessions et la détection des doublons ne doivent pas être activées dans les files d’attente et les rubriques. Si l’une de ces options est activée, certains messages n’arriveront pas dans votre file d’attente ou votre rubrique.

### <a name="create-storage-account"></a>Créer un compte de stockage

En l’absence de compte de stockage Azure vers lequel exporter, suivez ces étapes :

1. Créez un [compte de stockage sur le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Découvrez comment créer des [comptes de stockage Blob Azure](https://aka.ms/blobdocscreatestorageaccount) ou des [comptes de stockage Azure Data Lake Storage v2](../../storage/blobs/data-lake-storage-quickstart-create-account.md). L’exportation de données peut uniquement écrire des données dans des comptes de stockage qui prennent en charge les objets blob de blocs. La liste suivante répertorie les types de comptes de stockage compatibles connus : 

    |Niveau de performances|Type de compte|
    |-|-|
    |standard|Usage général v2|
    |standard|Usage général v1|
    |standard|Stockage Blob|
    |Premium|Stockage d’objets blob de blocs|

2. Créez un conteneur dans votre compte de stockage. Accédez à votre compte de stockage. Sous **Service blob**, sélectionnez **Parcourir les objets blob**. Sélectionnez **+ Conteneur**, en haut, pour créer un conteneur.

## <a name="set-up-continuous-data-export"></a>Configurer l’exportation de données en continu

Maintenant que vous avez une destination pour exporter les données, suivez ces étapes pour configurer l’exportation continue des données.

1. Connectez-vous à votre application IoT Central.

2. Dans le volet gauche, sélectionnez **Exportation de données**.

    > [!Note]
    > Si vous ne voyez pas « Exportation de données » dans le volet gauche, cela signifie que vous n’avez pas les autorisations nécessaires pour configurer l’exportation de données dans votre application. Contactez un administrateur pour configurer l’exportation de données.

3. Sélectionnez le bouton **+ Nouveau** dans le coin supérieur droit. Choisissez **Stockage Blob Azure**, **Azure Event Hubs** ou **Azure Service Bus** comme destination de l’exportation. Le nombre maximal d’exportations par application est de cinq.

    ![Créer une exportation de données continue](media/howto-export-data/new-export-definition.png)

4. Dans la zone de liste déroulante, sélectionnez votre **espace de noms Event Hubs **,** espace de noms Service Bus**, **espace de noms du compte de stockage**, ou **saisissez une chaîne de connexion**.

    - Vous verrez seulement les comptes de stockage, espaces de noms Event Hubs et espaces de noms Service Bus se trouvant dans le même abonnement que votre application IoT Central. Si vous souhaitez exporter les données vers une destination en dehors de cet abonnement, choisissez **Entrer une chaîne de connexion** et reportez-vous à l’étape 5.
    - Pour les applications créées à l’aide du plan Gratuit, le seul moyen de configurer l’exportation de données continue est d’utiliser une chaîne de connexion. Les applications du plan Gratuit ne sont associées à aucun abonnement Azure.

    ![Créer un Event Hub](media/howto-export-data/export-event-hub.png)

5. (Facultatif) Si vous avez choisi **Entrer une chaîne de connexion**, une nouvelle zone vous permettant de coller votre chaîne de connexion s’affiche. Pour obtenir votre chaîne de connexion :
    - Pour Event Hubs ou Service Bus, accédez à l’espace de noms correspondant dans le Portail Azure.
        - Sous **Paramètres**, sélectionnez **Stratégies d’accès partagé**
        - Choisissez la valeur par défaut **RootManageSharedAccessKey** ou créez-en une
        - Copiez la chaîne de connexion primaire ou secondaire
    - Pour votre compte de stockage, accédez à ce compte dans le Portail Azure :
        - Sous **Paramètres**, sélectionnez **Clés d’accès**.
        - Copiez soit la chaîne de connexion key1 soit la chaîne de connexion key2

6. Choisissez un conteneur, un Event Hub, une file d’attente ou une rubrique dans la zone de liste déroulante.

7. Sous **Données à exporter**, choisissez les types de données à exporter en définissant le type avec la valeur **Activé**.

8. Pour activer l’exportation de données continue, vérifiez que le bouton bascule sous **Activé** indique **activé**. Sélectionnez **Enregistrer**.

9. Après quelques minutes, vos données s’affichent à la destination choisie.

## <a name="export-contents-and-format"></a>Exporter le contenu et le format

Les données de télémétrie exportées contiennent l’intégralité du message que vos appareils ont envoyé à IoT Central, et non uniquement les valeurs de télémétrie. Les données des appareils exportés contiennent les modifications apportées aux propriétés et aux métadonnées de tous les appareils, et les modèles d’appareil exportés contiennent les modifications apportées à tous les modèles d’appareil.

Pour Event Hubs et Service Bus, les données sont exportées en quasi-temps réel. Les données se trouvent dans la propriété de corps et sont au format JSON (voir les exemples ci-dessous).

Pour le Stockage Blob, les données sont exportées une fois par minute et chaque fichier contient le lot de modifications apportées depuis la dernière exportation de fichier. Les données exportées sont placées dans trois dossiers au format JSON. Les chemins d’accès par défaut dans votre compte de stockage sont les suivants :

- Données de télémétrie : _{conteneur}/{id-app}/telemetry/{AAAA}/{MM}/{jj}/{hh}/{mm}/{nom-fichier}_
- Appareils : _{conteneur}/{id-app}/devices/{AAAA}/{MM}/{jj}/{hh}/{mm}/{nom-fichier}_
- Modèles d’appareil : _{conteneur}/deviceTemplates/{AAAA}/{MM}/{jj}/{hh}/{mm}/{nom-fichier}_

Vous pouvez parcourir les fichiers exportés dans le portail Azure en accédant au fichier puis en choisissant l’onglet **Modifier l’objet blob**.


## <a name="telemetry"></a>Télémétrie

Pour Event Hubs et Service Bus, un nouveau message est exporté rapidement après que IoT Central a reçu le message d’un appareil et chaque message exporté contient le message complet que l’appareil a envoyé dans la propriété de corps au format JSON.

Pour le Stockage Blob, les messages sont regroupés par lot et exportés une fois par minute. Les fichiers exportés utilisent le même format que les fichiers de messages exportés par [le routage des messages IoT Hub](../../iot-hub/tutorial-routing.md) vers Stockage Blob. 

> [!NOTE]
> Pour le Stockage Blob, vérifiez que vos appareils envoient bien des messages avec `contentType: application/JSON` et `contentEncoding:utf-8` (ou `utf-16`, `utf-32`). Pour obtenir un exemple, consultez la [documentation IoT Hub](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body).

L’appareil qui a envoyé les données de télémétrie est représenté par l’ID d’appareil (voir les sections suivantes). Pour obtenir les noms des appareils, exportez les données de ceux-ci et corrélez chaque message en utilisant le **connectionDeviceId** qui correspond au **deviceId** du message de l’appareil.

Il s’agit d’un exemple de message reçu dans une file d’attente ou une rubrique Event Hub ou Service Bus.

```json
{
  "body":{
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

Voici un exemple d’enregistrement exporté dans le Stockage Blob :

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>Appareils

Chaque message ou enregistrement d’instantané représente un ou plusieurs changements apportés à un appareil, ainsi que ses propriétés d’appareil et ses propriétés cloud, depuis le dernier message exporté. notamment :

- `id` de l’appareil dans IoT Central
- `displayName` de l’appareil
- L’ID du modèle d’appareil dans `instanceOf`
- L’indicateur `simulated`, true si l’appareil est un appareil simulé
- L’indicateur `provisioned`, true si l’appareil a bien été provisionné
- L’indicateur `approved`, true si l’appareil a reçu une approbation pour l’envoi de données
- Valeurs de propriétés
- Les `properties`, y compris les valeurs de propriétés d’appareil et de cloud

Les appareils supprimés ne sont pas exportés. Actuellement, il n’y a aucun indicateur dans les messages exportés pour les appareils supprimés.

Pour Event Hubs et Service Bus, les messages contenant les données de l’appareil sont envoyés à la file d’attente ou à la rubrique Event Hub ou Service Bus en quasi-temps réel, comme il apparaît dans IoT Central. 

Pour le Stockage Blob, un nouvel instantané contenant toutes les modifications apportées depuis la dernière écriture est exporté une fois par minute.

Voici un exemple de message concernant les données d’appareils et de propriétés dans la file d’attente ou la rubrique Event Hub ou Service Bus :

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
},
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Voici un exemple d’instantané contenant les données d’appareils et de propriétés dans le Stockage Blob. Les fichiers exportés contiennent une seule ligne par enregistrement.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>Modèles d’appareil

Chaque message ou enregistrement d’instantané représente un ou plusieurs changements apportés à un modèle d’appareil publié depuis le dernier message exporté. Les informations envoyées dans chaque message ou enregistrement incluent :

- `id` du modèle d’appareil qui correspond à `instanceOf` du flux d’appareils ci-dessus
- `displayName` du modèle d’appareil
- L’appareil `capabilityModel`, y compris ses `interfaces`, et les définitions de télémétrie, de propriétés et de commandes
- Définitions `cloudProperties`
- Remplacements et valeurs initiales, alignés sur le `capabilityModel`

Les modèles d’appareils supprimés ne sont pas exportés. Actuellement, il n’y a aucun indicateur dans les messages exportés pour les modèles d’appareil supprimé.

Pour Event Hubs et Service Bus, les messages contenant les données du modèle de l’appareil sont envoyés à la file d’attente ou à la rubrique Event Hub ou Service Bus en quasi-temps réel, comme il apparaît dans IoT Central. 

Pour le Stockage Blob, un nouvel instantané contenant toutes les modifications apportées depuis la dernière écriture est exporté une fois par minute.

Voici un exemple de message concernant les données des modèles d’appareils dans la file d’attente ou la rubrique Event Hub ou Service Bus :

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Voici un exemple d’instantané contenant les données d’appareils et de propriétés dans le Stockage Blob. Les fichiers exportés contiennent une seule ligne par enregistrement.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```
## <a name="data-format-change-notice"></a>Avis de modification du format de données

> [!Note]
> Le format des données des flux de télémétrie n’est pas affecté par cette modification. Seuls les flux de données des appareils et des modèles d’appareils sont affectés.

Si vous avez déjà une exportation de données dans votre application en préversion, dans laquelle les flux *Appareils* et *Modèles d’appareil* sont activés, vous devez mettre à jour votre exportation d’ici le **30 juin 2020**. Cela s’applique aux exportations vers le stockage Blob Azure, Azure Event Hubs et Azure Service Bus.

À partir du 3 février 2020, toutes les nouvelles exportations des applications où sont activés les appareils et les modèles d’appareils auront le format de données décrit plus haut. Toutes les exportations créées avant cette date seront conservées dans l’ancien format de données jusqu’au 30 juin 2020. Après cette date, ces exportations seront automatiquement converties vers le nouveau format de données. Le nouveau format de données correspond aux objets de type [appareil](https://docs.microsoft.com/rest/api/iotcentral/devices/get), [propriété d’appareil](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties), [propriété cloud d’appareil](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties) et [modèle d’appareil](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) dans l’API publique IoT Central. 
 
Pour les **appareils**, les différences notables entre l’ancien format de données et le nouveau sont les suivantes :
- L’`@id` a été supprimé pour les appareils, `deviceId` a été remplacé par `id` 
- L’indicateur `provisioned` a été ajouté pour décrire l’état de provisionnement de l’appareil
- L’indicateur `approved` a été ajouté pour décrire l’état d’approbation de l’appareil
- Les `properties`, y compris les propriétés d’appareil et les propriétés cloud, correspondent à des entités de l’API publique

Pour les **modèles d’appareil**, les différences notables entre l’ancien format de données et le nouveau sont les suivantes :

- L’`@id` des modèles d’appareil a été remplacé par `id`
- Le `@type` des modèles d’appareil a été remplacé par `types` et correspond désormais à un tableau

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Appareils (format déprécié à partir du 3 février 2020)
```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Modèles d’appareil (format déprécié à partir du 3 février 2020)
```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```
## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment exporter vos données vers Azure Event Hubs, Azure Service Bus ou Stockage Blob Azure, passez à l’étape suivante :

> [!div class="nextstepaction"]
> [Création de webhooks](./howto-create-webhooks.md)
