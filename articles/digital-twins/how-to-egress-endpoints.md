---
title: Sortie et points de terminaison dans Azure Digital Twins | Microsoft Docs
description: Instructions pour la création de points de terminaison avec Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: c94d29f16c011a9ff9951d064d7496d3a87f70ef
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636303"
---
# <a name="egress-and-endpoints"></a>Sortie et points de terminaison

Azure Digital Twins prend en charge le concept de **points de terminaison**. Chaque point de terminaison représente un répartiteur de message ou d’événement dans l’abonnement Azure. Les événements et les messages peuvent être envoyés aux rubriques Azure Event Hubs, Azure Event Grid et Azure Service Bus.

Les événements sont envoyés aux points de terminaison selon les préférences de routage prédéfinies. L’utilisateur peut spécifier quel point de terminaison doit recevoir l’un des événements suivants : 

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

Pour connaître les bases du routage d’événements et des types d’événements, reportez-vous à [Routage des événements et des messages](concepts-events-routing.md).

## <a name="event-types-description"></a>Description de types d’événements

Les formats de chaque type d’événement sont décrits dans les sections suivantes.

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** s’applique au modifications de graphiques. La propriété **subject** spécifie le type d’objet concerné. Les types d’objets suivants peuvent déclencher cet événement : 

- Appareil
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- KeyStore
- Rapport
- RoleDefinition
- Capteur
- SensorBlobMetadata
- SensorExtendedProperty
- Espace
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- SpaceRoleAssignment
- System
- Utilisateur
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>Exemples

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Valeur | Remplacer par |
| --- | --- |
| LE_NOM_DE_VOTRE_RUBRIQUE | Nom de votre rubrique personnalisée |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** est un événement envoyé par une fonction définie par l’utilisateur. 
  
> [!IMPORTANT]  
> Cet événement doit être explicitement envoyé par la fonction définie par l’utilisateur.

#### <a name="example"></a>Exemples

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Valeur | Remplacer par |
| --- | --- |
| LE_NOM_DE_VOTRE_RUBRIQUE | Nom de votre rubrique personnalisée |

### <a name="sensorchange"></a>SensorChange

**SensorChange** est une mise à jour de l’état d’un capteur en fonction des modifications des données de télémétrie.

#### <a name="example"></a>Exemples

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Valeur | Remplacer par |
| --- | --- |
| LE_NOM_DE_VOTRE_RUBRIQUE | Nom de votre rubrique personnalisée |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** est une mise à jour de l’espace d’un capteur en fonction des modifications des données de télémétrie.

#### <a name="example"></a>Exemples

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Valeur | Remplacer par |
| --- | --- |
| LE_NOM_DE_VOTRE_RUBRIQUE | Nom de votre rubrique personnalisée |

### <a name="devicemessage"></a>DeviceMessage

Vous pouvez utiliser **DeviceMessage** pour spécifier une connexion **EventHub** à laquelle des événements de télémétrie bruts peuvent être aussi acheminés à partir d’Azure Digital Twins.

> [!NOTE]
> - Vous pouvez uniquement combiner **DeviceMessage** avec **EventHub**. Vous ne pouvez pas combiner **DeviceMessage** avec un des autres types d’événements.
> - Vous ne pouvez spécifier qu’un seul point de terminaison dans la combinaison de type **EventHub** ou **DeviceMessage**.

## <a name="configure-endpoints"></a>Configuration des points de terminaison

La gestion des points de terminaison est effectuée via l’API Points de terminaison. Les exemples suivants illustrent la configuration des différents points de terminaison pris en charge. Faites particulièrement attention au tableau de types d’événements, car il définit le routage du point de terminaison :

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- Itinéraire vers les types d’événements **SensorChange**, **SpaceChange**, et **TopologyOperation** :

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | Valeur | Remplacer par |
    | --- | --- |
    | VOTRE_ESPACE_DE_NOMS | L’espace de noms de votre point de terminaison |
    | VOTRE_CLÉ_PRIMAIRE | La chaîne de connexion principale utilisée pour l’authentification |
    | VOTRE_CLÉ_SECONDAIRE | La chaîne de connexion secondaire utilisée pour l’authentification |
    | LE_NOM_DE_VOTRE_RUBRIQUE | Nom de votre rubrique personnalisée |

- Itinéraire vers les types d’événements **SensorChange**, **SpaceChange**, et **TopologyOperation** :

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | Valeur | Remplacer par |
    | --- | --- |
    | VOTRE_CLÉ_PRIMAIRE | La chaîne de connexion principale utilisée pour l’authentification|
    | VOTRE_CLÉ_SECONDAIRE | La chaîne de connexion secondaire utilisée pour l’authentification |
    | LE_NOM_DE_VOTRE_RUBRIQUE | Nom de votre rubrique personnalisée |

- Itinéraire vers les types d’événements Event Hubs : **SensorChange**, **SpaceChange**, et **TopologyOperation** :

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Valeur | Remplacer par |
    | --- | --- |
    | VOTRE_ESPACE_DE_NOMS | L’espace de noms de votre point de terminaison |
    | VOTRE_CLÉ_PRIMAIRE | La chaîne de connexion principale utilisée pour l’authentification |
    | VOTRE_CLÉ_SECONDAIRE | La chaîne de connexion secondaire utilisée pour l’authentification |
    | NOM_DE_VOTRE_EVENT_HUB | Le nom de votre Event Hub |

- Itinéraire vers les types d’événements **DeviceMessage**. L’ajout de `EntityPath` dans **connectionString** est obligatoire :

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Valeur | Remplacer par |
    | --- | --- |
    | VOTRE_ESPACE_DE_NOMS | L’espace de noms de votre point de terminaison |
    | VOTRE_CLÉ_PRIMAIRE | La chaîne de connexion principale utilisée pour l’authentification |
    | VOTRE_CLÉ_SECONDAIRE | La chaîne de connexion secondaire utilisée pour l’authentification |
    | NOM_DE_VOTRE_EVENT_HUB | Le nom de votre Event Hub |

> [!NOTE]  
> Lorsque vous créez un point de terminaison, 5 à 10 minutes peuvent être nécessaires pour que celui-ci commence à recevoir des événements.

## <a name="primary-and-secondary-connection-keys"></a>Clés de connexion primaires et secondaires

Lorsqu’une clé de connexion primaire n’est plus autorisée, le système essaie automatiquement d’utiliser la clé de connexion secondaire. C’est une solution de secours qui permet d’authentifier et de mettre à jour la clé primaire via l’API Points de terminaison.

Si aucune des deux n’est autorisée, le système s’interrompt pendant une période de 30 minutes maximum. Les événements sont supprimés à chaque période d’interruption.

Lorsque le système est à l’état d’interruption, la mise à jour des clés de connexion via l’API Points de terminaison peut prendre jusqu’à 30 minutes.

## <a name="unreachable-endpoints"></a>Points de terminaison inaccessibles

Lorsqu’un point de terminaison devient inaccessible, le système s’interrompt pendant une période de 30 minutes maximum. Les événements sont supprimés à chaque période d’interruption.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez à [utiliser Azure Digital Twins Swagger](how-to-use-swagger.md).

- Découvrez plus d’informations sur le [routage des événements et des messages](concepts-events-routing.md) dans Azure Digital Twins.
