---
title: Exporter vos données vers Azure Event Hubs et Azure Service Bus | Microsoft Docs
description: Comment exporter des données depuis votre application Azure IoT Central vers Azure Event Hubs et Azure Service Bus
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: fed9c924274cb66671e233a7dc6d431d81e0dbfb
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973211"
---
# <a name="export-your-data-in-azure-iot-central-preview-features"></a>Exporter vos données dans Azure IoT Central (fonctionnalités de préversion)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Cette rubrique s’applique aux administrateurs.*

Cet article décrit la façon d’utiliser la fonctionnalité d’exportation continue de données d’Azure IoT Central afin d’exporter vos données vers vos instances **Azure Event Hubs** et **Azure Service Bus**. Vous pouvez exporter des **données de télémétrie**, des **appareils** et des **modèles d’appareil** vers votre propre destination pour obtenir des insights et des données d’analytique sur le chemin à chaud. Cela inclut le déclenchement de règles personnalisées dans Azure Stream Analytics, le déclenchement de workflows personnalisés dans Azure Logic Apps ou la transmission des données à transformer via Azure Functions.

> [!Note]
> Une fois encore, quand vous activez l’exportation de données continue, vous obtenez seulement les données à partir de ce moment. Pour le moment, vous ne pouvez pas récupérer les données d’une période pendant laquelle l’exportation de données continue est désactivée. Pour conserver un historique des données plus étendu, activez l’exportation de données continue tôt dans le processus.

## <a name="prerequisites"></a>Prérequis

- Vous devez être administrateur dans votre application IoT Central

## <a name="set-up-export-destination"></a>Définir la destination de l’exportation

En l’absence d’un hub d’événements ou d’un Service Bus vers lequel exporter des données, suivez ces étapes pour en créer un :

### <a name="create-event-hubs-namespace"></a>Créer un espace de noms Event Hubs

1. Créez un [espace de noms Event Hubs dans le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Pour en savoir plus, consultez les [documents sur Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Choisissez un abonnement. 

    > [!Note] 
    > Vous pouvez maintenant exporter des données vers des abonnements autres, **différents** de celui de votre application de paiement à l’utilisation IoT Central. Vous vous connecterez à l’aide d’une chaîne de connexion dans ce cas.
3. Créez un Event Hub dans votre espace de noms Event Hubs. Accédez à votre espace de noms, puis sélectionnez **+ Event Hub**, en haut, pour créer une instance Event Hub.

### <a name="create-service-bus-namespace"></a>Créer un espace de noms Service Bus

1. Créez un [espace de noms Service Bus dans le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Pour en savoir plus, consultez les [documents sur Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Choisissez un abonnement. 

    > [!Note] 
    > Vous pouvez maintenant exporter des données vers des abonnements autres, **différents** de celui de votre application de paiement à l’utilisation IoT Central. Vous vous connecterez à l’aide d’une chaîne de connexion dans ce cas.

3. Accédez à votre espace de noms Service Bus, puis sélectionnez **+ File d’attente** ou **+ Rubrique**, en haut, pour créer une file d’attente ou une rubrique de destination d’exportation.


## <a name="set-up-continuous-data-export"></a>Configurer l’exportation de données en continu

Maintenant que vous avez une destination Event Hubs/Service Bus pour exporter les données, suivez ces étapes pour configurer l’exportation continue des données. 

1. Connectez-vous à votre application IoT Central.

2. Dans le menu de gauche, sélectionnez **Exportation de données**.

    > [!Note]
    > Si vous ne voyez pas l’option d’exportation de données dans le menu de gauche, vous n’êtes pas l’administrateur de votre application. Contactez un administrateur pour configurer l’exportation de données.

3. Sélectionnez le bouton **+ Nouveau** dans le coin supérieur droit. Choisissez **Azure Event Hubs** ou **Azure Service Bus** comme destination de l’exportation. 

    > [!NOTE] 
    > Le nombre maximal d’exportations par application est de cinq. 

    ![Créer une exportation de données continue](media/howto-export-data-pnp/export-new2.png)

4. Dans la zone de liste déroulante, sélectionnez votre **espace de noms Event Hubs/espace de noms Service Bus**. Vous pouvez également choisir la dernière option de la liste : **Entrer une chaîne de connexion**. 

    > [!NOTE] 
    > Vous verrez seulement les comptes de stockage/espaces de noms Event Hubs/espaces de noms Service Bus se trouvant dans le **même abonnement que votre application IoT Central**. Si vous souhaitez exporter les données vers une destination en dehors de cet abonnement, choisissez **Entrer une chaîne de connexion** et reportez-vous à l’étape 5.

    > [!NOTE] 
    > Pour les applications d’évaluation de 7 jours, le seul moyen de configurer l’exportation des données est d’utiliser une chaîne de connexion. En effet, ces applications ne sont associées à aucun abonnement Azure.

    ![Créer un Event Hub d’exportation de données continue](media/howto-export-data-pnp/export-eh.png)

5. (Facultatif) Si vous avez choisi **Entrer une chaîne de connexion**, une nouvelle zone vous permettant de coller votre chaîne de connexion s’affiche. Pour obtenir votre chaîne de connexion :
    - Pour Event Hubs ou Service Bus, accédez à l’espace de noms correspondant dans le Portail Azure.
        - Sous **Paramètres**, sélectionnez **Stratégies d’accès partagé**
        - Choisissez la valeur par défaut **RootManageSharedAccessKey** ou créez-en une
        - Copiez la chaîne de connexion primaire ou secondaire

6. Choisissez un Event Hub/une file d’attente ou une rubrique dans la zone de liste déroulante.

7. Sous **Données à exporter**, spécifiez chaque type de données à exporter en définissant le type avec la valeur **Activé**.

8. Pour activer l’exportation des données continue, vérifiez que le bouton bascule **Exportation de données** est sur **Activé**. Sélectionnez **Enregistrer**.

9. Après quelques minutes, vos données s’affichent à la destination choisie.


## <a name="data-format"></a>Format de données

Les données de télémétrie, d’appareils et de modèles d’appareil sont exportées en temps quasi réel vers votre hub d’événements, votre file d’attente Service Bus ou votre rubrique. Les données de télémétrie exportées contiennent l’intégralité du message que vos appareils ont envoyé à IoT Central, et non uniquement les valeurs de télémétrie. Les données des appareils exportés contiennent les modifications apportées aux propriétés et aux métadonnées de tous les appareils, et les modèles d’appareil exportés contiennent les modifications apportées à tous les modèles d’appareil. Les données exportées résident dans la propriété « body » au format JSON.

> [!NOTE]
> Lors du choix d’un Service Bus comme destination d’exportation, **les sessions et la détection des doublons ne doivent pas être activés** dans les files d’attente et les rubriques. Si l’une de ces options est activée, certains messages n’arriveront pas dans votre file d’attente ou votre rubrique.

### <a name="telemetry"></a>Télémétrie

Un nouveau message est exporté rapidement après que IoT Central a reçu le message d’un appareil. Chaque message exporté dans Event Hubs et Service Bus contient le message complet envoyé par l’appareil dans la propriété « body » au format JSON. 

> [!NOTE]
> Les appareils qui envoient les données de télémétrie sont représentés par leur ID d’appareil (voir les sections suivantes). Pour obtenir les noms des appareils, exportez les données de ceux-ci et corrélez chaque message en utilisant le **connectionDeviceId** qui correspond au **deviceId** du message de l’appareil.

L’exemple suivant montre un message concernant les données de télémétrie reçues dans le hub d’événements, la file d’attente Service Bus ou la rubrique.

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

### <a name="devices"></a>Appareils

Les messages contenant les données d’appareils sont envoyés à votre event Hub ou à la file d’attente ou à la rubrique Service Bus une fois toutes les n minutes. Cela signifie que toutes les quelques minutes, un lot de messages arrivera avec des données sur
- Nouveaux appareils qui ont été ajoutés
- Appareils pour lesquels des valeurs de propriétés ont changé

Chaque message représente un ou plusieurs changements apportés à un appareil depuis le dernier message exporté. Les informations qui seront envoyées dans chaque message incluent :
- `@id` de l’appareil dans IoT Central
- `name` de l’appareil
- `deviceId` issu du [service de provisionnement des appareils](https://aka.ms/iotcentraldocsdps)
- Informations sur le modèle d’appareil
- Valeurs de propriétés

> [!NOTE]
> Les appareils supprimés depuis le dernier lot ne sont pas exportés. Actuellement, il n’y a aucun indicateur dans les messages exportés pour les appareils supprimés.
>
> Le modèle d’appareil auquel chaque appareil appartient est représenté par un ID de modèle d’appareil. Pour obtenir le nom du modèle d’appareil, veillez à exporter les données des modèles d’appareil également.

L’exemple suivant montre un message concernant les données d’appareil dans la file d’attente ou le sujet de l’event Hub ou de Service Bus :


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

### <a name="device-templates"></a>Modèles d’appareil

Les messages contenant les données des modèles d’appareils sont envoyés à votre event Hub ou à la file d’attente ou à la rubrique Service Bus une fois toutes les n minutes. Cela signifie que toutes les quelques minutes, un lot de messages arrivera avec des données sur
- Nouveaux modèles d’appareil qui ont été ajoutés ou versionnés
- Modèles d’appareil où les capabilityModels, les cloudProperties, les remplacements et les valeurs initiales ont été modifiés

Chaque message représente un ou plusieurs changements apportés à un modèle d’appareil depuis le dernier message exporté. Les informations qui seront envoyées dans chaque message incluent :
- `@id` du modèle d’appareil
- `name` du modèle d’appareil
- `version` du modèle d’appareil
- L’appareil `capabilityModel`, y compris ses `interfaces`, et les définitions de télémétrie, de propriétés et de commandes
- Définitions `cloudProperties`
- Remplacements et valeurs initiales, alignés sur le `capabilityModel`

> [!NOTE]
> Les modèles d’appareil supprimés depuis le dernier lot ne sont pas exportés. Actuellement, il n’y a aucun indicateur dans les messages exportés pour les modèles d’appareil supprimé.

L’exemple suivant montre un message de modèle d’appareil dans le hub d’événements, la file d’attente Service Bus ou la rubrique :

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

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment exporter vos données vers Azure Event Hubs et Azure Service Bus, passez à l’étape suivante :

> [!div class="nextstepaction"]
> [Comment déclencher Azure Functions](howto-trigger-azure-functions.md)
