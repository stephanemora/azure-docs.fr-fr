---
title: Exporter vos données Azure IoT Central | Microsoft Docs
description: Comment exporter des données depuis votre application Azure IoT Central vers Azure Event Hubs, Azure Service Bus et Stockage Blob Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/06/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 1aac5af916e414178676a1caf42fead41109de68
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974459"
---
# <a name="export-your-azure-iot-central-data-preview-features"></a>Exporter vos données Azure IoT Central (fonctionnalités de préversion)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Cette rubrique s’applique aux administrateurs.*

Cet article décrit la façon d’utiliser la fonctionnalité d’exportation continue de données d’Azure IoT Central afin d’exporter vos données vers vos instances **Azure Event Hubs**, **Azure Service Bus** ou **Stockage Blob Azure**. Les données sont exportées au format JSON et peuvent inclure des informations de télémétrie, ainsi que des informations sur les appareils et les modèles d’appareil. Utilisez les données exportées pour :

- Insights et analytiques à chaud. Cette option inclut le déclenchement de règles personnalisées dans Azure Stream Analytics, le déclenchement de workflows personnalisés dans Azure Logic Apps ou la transmission des données à transformer via Azure Functions.
- Analytique des chemins à froid, comme les modèles d’apprentissage dans Azure Machine Learning ou l’analyse de tendances à long terme dans Microsoft Power BI.

> [!Note]
> Quand vous activez l’exportation de données continue, vous obtenez seulement les données à partir de ce moment. Pour le moment, vous ne pouvez pas récupérer les données d’une période pendant laquelle l’exportation de données continue est désactivée. Pour conserver un historique des données plus étendu, activez l’exportation de données continue tôt dans le processus.

## <a name="prerequisites"></a>Prérequis

Vous devez être administrateur dans votre application IoT Central

## <a name="set-up-export-destination"></a>Définir la destination de l’exportation

Votre destination d’exportation doit exister avant la configuration de l’exportation de données continue.

### <a name="create-event-hubs-namespace"></a>Créer un espace de noms Event Hubs

En l’absence d’espace de noms Event Hubs existant vers lequel exporter, suivez ces étapes :

1. Créez un [espace de noms Event Hubs dans le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Pour en savoir plus, consultez les [documents sur Azure Event Hubs](../../event-hubs/event-hubs-create.md).

2. Choisissez un abonnement. Vous pouvez exporter des données vers des abonnements différents de celui de votre application de paiement à l’utilisation IoT Central. Vous vous connecterez à l’aide d’une chaîne de connexion dans ce cas.

3. Créez un Event Hub dans votre espace de noms Event Hubs. Accédez à votre espace de noms, puis sélectionnez **+ Event Hub**, en haut, pour créer une instance Event Hub.

### <a name="create-service-bus-namespace"></a>Créer un espace de noms Service Bus

En l’absence d’espace de noms Service Bus vers lequel exporter, suivez ces étapes :

1. Créez un [espace de noms Service Bus dans le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Pour en savoir plus, consultez les [documents sur Azure Service Bus](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Choisissez un abonnement. Vous pouvez exporter des données vers des abonnements différents de celui de votre application de paiement à l’utilisation IoT Central. Vous vous connecterez à l’aide d’une chaîne de connexion dans ce cas.

3. Accédez à votre espace de noms Service Bus, puis sélectionnez **+ File d’attente** ou **+ Rubrique**, en haut, pour créer une file d’attente ou une rubrique de destination d’exportation.

Lorsque vous choisissez un Service Bus comme destination d’exportation, les sessions et la détection des doublons ne doivent pas être activées dans les files d’attente et les rubriques. Si l’une de ces options est activée, certains messages n’arriveront pas dans votre file d’attente ou votre rubrique.

### <a name="create-storage-account"></a>Créer un compte de stockage

En l’absence de compte de stockage Azure vers lequel exporter, suivez ces étapes :

1. Créez un [compte de stockage sur le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Découvrez comment créer des [comptes de stockage Blob Azure](https://aka.ms/blobdocscreatestorageaccount) ou des [comptes de stockage Azure Data Lake Storage v2](../../storage/blobs/data-lake-storage-quickstart-create-account.md).

    - Si vous choisissez d’exporter des données vers un compte de stockage Azure Data Lake Storage v2, vous devez définir **Type de compte** sur **BlobStorage**.
    - Vous pouvez exporter des données vers des comptes de stockage compris dans des abonnements autres que celui de votre application de paiement à l’utilisation IoT Central. Vous vous connecterez à l’aide d’une chaîne de connexion dans ce cas.

2. Créez un conteneur dans votre compte de stockage. Accédez à votre compte de stockage. Sous **Service blob**, sélectionnez **Parcourir les objets blob**. Sélectionnez **+ Conteneur**, en haut, pour créer un conteneur.

## <a name="set-up-continuous-data-export"></a>Configurer l’exportation de données en continu

Maintenant que vous avez une destination pour exporter les données, suivez ces étapes pour configurer l’exportation continue des données.

1. Connectez-vous à votre application IoT Central.

2. Dans le volet gauche, sélectionnez **Exportation de données**.

    > [!Note]
    > Si l’option Exportation de données n’apparaît pas dans le volet gauche, la raison en est que vous n’êtes pas l’administrateur de votre application. Contactez un administrateur pour configurer l’exportation de données.

3. Sélectionnez le bouton **+ Nouveau** dans le coin supérieur droit. Choisissez **Stockage Blob Azure**, **Azure Event Hubs** ou **Azure Service Bus** comme destination de l’exportation. Le nombre maximal d’exportations par application est de cinq.

    ![Créer une exportation de données continue](media/howto-export-data/export-new2.png)

4. Dans la zone de liste déroulante, sélectionnez votre **espace de noms Event Hubs **,** espace de noms Service Bus**, **espace de noms du compte de stockage**, ou **saisissez une chaîne de connexion**.

    - Vous verrez seulement les comptes de stockage, espaces de noms Event Hubs et espaces de noms Service Bus se trouvant dans le même abonnement que votre application IoT Central. Si vous souhaitez exporter les données vers une destination en dehors de cet abonnement, choisissez **Entrer une chaîne de connexion** et reportez-vous à l’étape 5.
    - Pour les applications d’évaluation de sept jours, le seul moyen de configurer l’exportation des données est d’utiliser une chaîne de connexion. Ces applications ne sont associées à aucun abonnement Azure.

    ![Créer un Event Hub](media/howto-export-data/export-eh.png)

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

8. Pour activer l’exportation des données continue, vérifiez que le bouton bascule **Exportation de données** est sur **Activé**. Sélectionnez **Enregistrer**.

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

Pour le Stockage Blob, les messages sont regroupés par lot et exportés une fois par minute. Les fichiers exportés utilisent le même format que les fichiers de messages exportés par [le routage des messages IoT Hub](../../iot-hub/iot-hub-csharp-csharp-process-d2c.md) vers Stockage Blob. 

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

Chaque message ou enregistrement d’instantané représente un ou plusieurs changements apportés à un appareil et ses propriétés depuis le dernier message exporté. notamment :

- `@id` de l’appareil dans IoT Central
- `name` de l’appareil
- `deviceId` issu du [service de provisionnement des appareils](../core/howto-connect-nodejs.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
- Informations sur le modèle d’appareil
- Valeurs de propriétés

Le modèle d’appareil auquel chaque appareil appartient est représenté par `instanceOf`. Pour obtenir le nom et des informations supplémentaires sur le modèle d’appareil, veillez à exporter les données des modèles d’appareil également.

Les appareils supprimés ne sont pas exportés. Actuellement, il n’y a aucun indicateur dans les messages exportés pour les appareils supprimés.

Pour Event Hubs et Service Bus, les messages contenant les données de l’appareil sont envoyés à la file d’attente ou à la rubrique Event Hub ou Service Bus en quasi-temps réel, comme il apparaît dans IoT Central. 

Pour le Stockage Blob, un nouvel instantané contenant toutes les modifications apportées depuis la dernière écriture est exporté une fois par minute.

Voici un exemple de message concernant les données d’appareils et de propriétés dans la file d’attente ou la rubrique Event Hub ou Service Bus :

```json
{
  "body":{
    "@id":"<id>",
    "@type":"Device",
    "displayName":"Airbox - 266d30aedn5",
    "data":{
      "$cloudProperties":{
        "Color":"blue"
      },
      "EnvironmentalSensor":{
        "thsensormodel":{
          "reported":{
            "value":"A1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "pm25sensormodel":{
          "reported":{
            "value":"P1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      },
      "urn_azureiot_DeviceManagement_DeviceInformation":{
        "totalStorage":{
          "reported":{
            "value":3088.1959855710156,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "totalMemory":{
          "reported":{
            "value":16005.703586477555,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      }
    },
    "instanceOf":"<templateId>",
    "deviceId":"<deviceId>",
    "simulated":true
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
  "enqueuedTimeUtc":"2019-10-02T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Voici un exemple d’instantané contenant les données d’appareils et de propriétés dans le Stockage Blob. Les fichiers exportés contiennent une seule ligne par enregistrement.

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

## <a name="device-templates"></a>Modèles d’appareil

Chaque message ou enregistrement d’instantané représente un ou plusieurs changements apportés à un modèle d’appareil depuis le dernier message exporté. Les informations envoyées dans chaque message ou enregistrement incluent :

- `@id` du modèle d’appareil qui correspond à `instanceOf` du flux d’appareils ci-dessus
- `name` du modèle d’appareil
- `version` du modèle d’appareil
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
> [Comment déclencher Azure Functions](../core/howto-trigger-azure-functions.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
