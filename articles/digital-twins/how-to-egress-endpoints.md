---
title: Sortie et points de terminaison dans Azure Digital Twins | Microsoft Docs
description: Instructions pour la création de points de terminaison avec Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: c917fab84448684cf29af162ec0781d764605f71
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323853"
---
# <a name="egress-and-endpoints"></a>Sortie et points de terminaison

Azure Digital Twins prend en charge le concept de _point de terminaison_, dans lequel chaque point de terminaison représente un répartiteur de messages ou d’événements dans l’abonnement Azure de l’utilisateur. Les événements et les messages peuvent être envoyés à **Event Hub**, **Event Grid** et aux **rubriques Service Bus**.

Les événements sont envoyés aux points de terminaison selon les préférences de routage prédéfinies : l’utilisateur peut spécifier le point de terminaison qui doit recevoir l’un des événements suivants :`TopologyOperation`, `UdfCustom`, `SensorChange`, `SpaceChange` ou `DeviceMessage`.

Pour connaître les bases du routage d’événements et des types d’événements, reportez-vous à [Routage des événements et des messages](concepts-events-routing.md).

## <a name="event-types-description"></a>Description de types d’événements

Voici le format d’événement de chaque type d’événement :

- `TopologyOperation`

  S’applique aux modifications du graphe. La propriété `subject` spécifie le type d’objet concerné. Types d’objets pouvant déclencher cet événement : `Device, DeviceBlobMetadata`, `DeviceExtendedProperty`, `ExtendedPropertyKey`, `ExtendedType`, `KeyStore`, `Report`, `RoleDefinition`, `Sensor`, `SensorBlobMetadata`, `SensorExtendedProperty`, `Space` ,  `SpaceBlobMetadata`, `SpaceExtendedProperty`, `SpaceResource`, `SpaceRoleAssignment`, `System`, `User`, `UserBlobMetadata`, `UserExtendedProperty`.

  Exemple :

  ```JSON
  {
    "id": "00000000-0000-0000-0000-000000000000",
    "subject": "ExtendedPropertyKey",
    "data": {
      "SpacesToNotify": [
        "3a16d146-ca39-49ee-b803-17a18a12ba36"
      ],
      "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
      "AccessType": "Create"
    },
    "eventType": "TopologyOperation",
    "eventTime": "2018-04-17T17:41:54.9400177Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Nom de l’attribut personnalisé | Remplacer par |
    | --- | --- |
    | `yourTopicName` | Nom de votre rubrique personnalisée |

- `UdfCustom`

  Événement envoyé par une fonction définie par l’utilisateur Notez que cet événement doit être explicitement envoyé par la fonction définie par l’utilisateur.

  Exemple :

  ```JSON
  {
    "id": "568fd394-380b-46fa-925a-ebb96f658cce",
    "subject": "UdfCustom",
    "data": {
      "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "ResourceType": "Space",
      "Payload": "\"Room is not available or air quality is poor\"",
      "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
    },
    "eventType": "UdfCustom",
    "eventTime": "2018-10-02T06:50:15.198Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Nom de l’attribut personnalisé | Remplacer par |
    | --- | --- |
    | `yourTopicName` | Nom de votre rubrique personnalisée |

- `SensorChange`

  Mise à jour de l’état d’un capteur en fonction des modifications des données de télémétrie.

  Exemple :

  ```JSON
  {
    "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "subject": "SensorChange",
    "data": {
      "Type": "Classic",
      "DataType": "Motion",
      "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
      "Value": "False",
      "PreviousValue": "True",
      "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
      "MessageType": "sensor",
      "Properties": {
        "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
        "ms-activity-id": "ct22YwXEGJ5u.605.0"
      }
    },
    "eventType": "SensorChange",
    "eventTime": "2018-04-17T17:46:18.5452993Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Nom de l’attribut personnalisé | Remplacer par |
    | --- | --- |
    | `yourTopicName` | Nom de votre rubrique personnalisée |

- `SpaceChange`

  Mise à jour de l’état d’un espace en fonction des modifications des données de télémétrie.

  Exemple :

  ```JSON
  {
    "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
    "subject": "SpaceChange",
    "data": {
      "Type": null,
      "DataType": "AvailableAndFresh",
      "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "Value": "Room is not available or air quality is poor",
      "PreviousValue": null,
      "RawData": null,
      "transactionId": null,
      "EventTimestamp": null,
      "MessageType": null,
      "Properties": null,
      "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
    },
    "eventType": "SpaceChange",
    "eventTime": "2018-10-02T06:50:20.128Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Nom de l’attribut personnalisé | Remplacer par |
    | --- | --- |
    | `yourTopicName` | Nom de votre rubrique personnalisée |

- `DeviceMessage`

  Vous pouvez spécifier une connexion `EventHub` à laquelle des événements de télémétrie bruts peuvent être acheminés à partir d’Azure Digital Twins.

> [!NOTE]
> - `DeviceMessage` est combinable uniquement avec `EventHub`. Vous ne pouvez pas combiner `DeviceMessage` avec d’autres types d’événements.
> - Vous ne pouvez spécifier qu’un seul point de terminaison dans la combinaison de type `EventHub`/`DeviceMessage`.

## <a name="configuring-endpoints"></a>Configuration de points de terminaison

La gestion des points de terminaison est effectuée via l’API Points de terminaison. Voici quelques exemples de configuration des différents points de terminaison pris en charge. Faites particulièrement attention au tableau de types d’événements, car il définit le routage du point de terminaison :

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- Routez vers les types d’événements **Service Bus** : `SensorChange`, `SpaceChange`, `TopologyOperation`

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourTopicName"
  }
  ```

    | Nom de l’attribut personnalisé | Remplacer par |
    | --- | --- |
    | `yourNamespace` | L’espace de noms de votre point de terminaison |
    | `yourPrimaryKey` | La chaîne de connexion principale utilisée pour l’authentification |
    | `yourSecondaryKey` | La chaîne de connexion secondaire utilisée pour l’authentification |
    | `yourTopicName` | Nom de votre rubrique personnalisée |

- Routez vers les types d’événements **Event Grid** : `SensorChange`, `SpaceChange`, `TopologyOperation`

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "yourPrimaryKey",
    "secondaryConnectionString": "yourSecondaryKey",
    "path": "yourTopicName.westus-1.eventgrid.azure.net"
  }
  ```

    | Nom de l’attribut personnalisé | Remplacer par |
    | --- | --- |
    | `yourPrimaryKey` | La chaîne de connexion principale utilisée pour l’authentification|
    | `yourSecondaryKey` | La chaîne de connexion secondaire utilisée pour l’authentification |
    | `yourTopicName` | Nom de votre rubrique personnalisée |

- Routez vers les types d’événements **Event Hub** : `SensorChange`, `SpaceChange`, `TopologyOperation`

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourEventHubName"
  }
  ```

    | Nom de l’attribut personnalisé | Remplacer par |
    | --- | --- |
    | `yourNamespace` | L’espace de noms de votre point de terminaison |
    | `yourPrimaryKey` | La chaîne de connexion principale utilisée pour l’authentification |
    | `yourSecondaryKey` | La chaîne de connexion secondaire utilisée pour l’authentification |
    | `yourEventHubName` | Nom de votre hub d’événements |

- Routez vers les types d’événements **Event Hub** `DeviceMessage`. Remarquez l’ajout de _EntityPath_ dans `connectionString`, qui est obligatoire.

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey;EntityPath=yourEventHubName",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey;EntityPath=yourEventHubName",
    "path": "yourEventHubName"
  }
  ```

    | Nom de l’attribut personnalisé | Remplacer par |
    | --- | --- |
    | `yourNamespace` | L’espace de noms de votre point de terminaison |
    | `yourPrimaryKey` | La chaîne de connexion principale utilisée pour l’authentification |
    | `yourSecondaryKey` | La chaîne de connexion secondaire utilisée pour l’authentification |
    | `yourEventHubName` | Nom de votre hub d’événements |

> [!NOTE]
> Lorsque vous créez un point de terminaison, 5 à 10 minutes peuvent être nécessaires pour que celui-ci commence à recevoir des événements.

## <a name="primary-and-secondary-connection-keys"></a>Clés de connexion primaires et secondaires

Lorsqu’une clé de connexion primaire n’est plus autorisée, le système essaie automatiquement d’utiliser la clé de connexion secondaire. C’est une solution de secours qui permet d’authentifier et de mettre à jour la clé primaire via l’API Points de terminaison.

Si aucune des deux n’est autorisée, le système s’interrompt pendant une période de 30 minutes maximum. Les événements sont supprimés à chaque période d’interruption.

Lorsque le système est à l’état d’interruption, la mise à jour des clés de connexion via l’API Points de terminaison peut prendre jusqu’à 30 minutes.

## <a name="unreachable-endpoints"></a>Points de terminaison inaccessibles

Lorsqu’un point de terminaison devient inaccessible, le système s’interrompt pendant une période de 30 minutes maximum. Les événements sont supprimés à chaque période d’interruption.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment utiliser Azure Digital Twins Swagger, consultez [Azure Digital Twins Swagger](how-to-use-swagger.md).

Pour plus d’informations sur le routage des événements et des messages dans Azure Digital Twins, consultez [Routage des événements et des messages](concepts-events-routing.md).
