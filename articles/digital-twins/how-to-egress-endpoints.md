---
title: Sortie et points de terminaison - Azure Digital Twins | Microsoft Docs
description: Découvrez comment créer et sortir des points de terminaison d’événement dans Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3803802a3d81655091d8be543ae9cb17221a98d8
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511567"
---
# <a name="egress-and-endpoints-in-azure-digital-twins"></a>Sortie et points de terminaison dans Azure Digital Twins

Les *points de terminaison* Azure Digital Twins représentent un répartiteur de messages ou d’événements au sein d’un abonnement Azure. Les événements et les messages peuvent être envoyés aux rubriques Azure Event Hubs, Azure Event Grid et Azure Service Bus.

Les événements sont routés aux points de terminaison selon les préférences de routage prédéfinies. Les utilisateurs spécifient les *types d’événements* que chaque point de terminaison peut recevoir.

Pour plus d’informations sur les événements, le routage et les types d’événements consultez [Routage des événements et des messages dans Azure Digital Twins](./concepts-events-routing.md).

## <a name="events"></a>Événements

Les événements sont envoyés par des objets IoT (tels que les appareils et capteurs) pour être traités par des répartiteurs de messages et d’événements Azure. Les événements sont définis par la [référence de schéma d’événement Azure Event Grid](../event-grid/event-schema.md) suivante.

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

| Attribut | Type | Description |
| --- | --- | --- |
| id | string | Identificateur unique de l’événement. |
| subject | string | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| data | object | Données d’événement spécifiques au fournisseur de ressources. |
| eventType | string | Un des types d’événements inscrits pour cette source d’événement. |
| eventTime | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| dataVersion | string | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| metadataVersion | string | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |
| topic | string | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |

Pour plus d’informations sur le schéma d’événement Event Grid :

- Consultez la [référence de schéma d’événement Azure Event Grid](../event-grid/event-schema.md).
- Lisez la [référence EventGridEvent du Kit de développement logiciel (SDK) Azure EventGrid Node.js](https://docs.microsoft.com/javascript/api/@azure/eventgrid/eventgridevent?view=azure-node-latest).

## <a name="event-types"></a>Types d’événements

Les types d’événements déterminent la nature de l’événement et sont définis dans le champ **eventType**. Les types d’événements disponibles sont indiqués dans la liste suivante :

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

Les formats d’événement de chaque type d’événement sont décrits en détail dans les sous-sections suivantes.

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
- Système
- Utilisateur
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>Exemple

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

#### <a name="example"></a>Exemple

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

#### <a name="example"></a>Exemple

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

#### <a name="example"></a>Exemple

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

La gestion des points de terminaison est effectuée via l’API Points de terminaison.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Les exemples suivants illustrent la configuration des différents points de terminaison pris en charge.

>[!IMPORTANT]
> Soyez attentif à l’attribut **eventTypes**. Il définit quels types d’événements sont gérés par le point de terminaison, ce qui détermine leurs routages.

Une requête HTTP POST authentifiée sur :

```URL
YOUR_MANAGEMENT_API_URL/endpoints
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
