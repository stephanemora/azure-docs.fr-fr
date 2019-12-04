---
title: Informations de référence sur la bibliothèque cliente des fonctions définies par l’utilisateur - Azure Digital Twins | Microsoft Docs
description: Documentation de référence sur la bibliothèque cliente des fonctions définies par l’utilisateur Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 41baacd4485e6702ec29057f5d539724b74e353b
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383240"
---
# <a name="user-defined-functions-client-library-reference"></a>Informations de référence sur la bibliothèque cliente des fonctions définies par l’utilisateur

Ce document fournit des informations de référence pour la bibliothèque clients des fonctions définies par l’utilisateur Azure Digital Twins.

## <a name="helper-methods"></a>Méthodes d’assistance

La bibliothèque cliente définit les méthodes d’assistance pour les opérations couramment utilisées.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

À l’aide d’un identificateur d’espace, cette fonction récupère l’espace à partir du graphe.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Identificateur d’espace |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

À l’aide d’un identificateur de capteur, cette fonction récupère le capteur à partir du graphe.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Identificateur de capteur |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

À l’aide d’un identificateur d’appareil, cette fonction récupère l’appareil à partir du graphe.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Identificateur d’appareil |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

À l’aide d’un identificateur de capteur et de son type de données, cette fonction récupère la valeur actuelle de ce capteur.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Identificateur de capteur |
| *dataType*  | `string` | Type de données du capteur |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

À l’aide d’un identificateur d’espace et du nom de la valeur, cette fonction récupère la valeur actuelle de cette propriété d’espace.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Identificateur d’espace |
| *valueName* | `string` | Nom de la propriété d’espace |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>de getSensorHistoryValues (sensorId, type de données) `value[]`

À l’aide d’un identificateur de capteur et de son type de données, cette fonction récupère les valeurs historiques de ce capteur.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificateur de capteur |
| *dataType* | `string` | Type de données du capteur |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

À l’aide d’un identificateur d’espace et du nom de la valeur, cette fonction récupère les valeurs historiques de cette propriété de l’espace.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificateur d’espace |
| *valueName* | `string` | Nom de la propriété d’espace |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

À l’aide d’un identificateur d’espace, cette fonction récupère les espaces enfants de cet espace parent.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificateur d’espace |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

À l’aide d’un identificateur d’espace, cette fonction récupère les capteurs enfants de cet espace parent.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificateur d’espace |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

À l’aide d’un identificateur d’espace, cette fonction récupère les appareils enfants de cet espace parent.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificateur d’espace |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

À l’aide d’un identificateur d’appareil, cette fonction récupère les capteurs enfants de cet appareil parent.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identificateur d’appareil |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

À l’aide d’un identificateur d’espace, cette fonction récupère son espace parent.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Identificateur d’espace |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

À l’aide d’un identificateur de capteur, cette fonction récupère son espace parent.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identificateur de capteur |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

À l’aide d’un identificateur d’appareil, cette fonction récupère son espace parent.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Identificateur d’appareil |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

À l’aide d’un identificateur de capteur, cette fonction récupère son appareil parent.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identificateur de capteur |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

À l’aide d’un identificateur d’espace, cette fonction récupère la propriété et sa valeur à partir de l’espace.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificateur d’espace |
| *propertyName* | `string` | Nom de la propriété d’espace |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

À l’aide d’un identificateur de capteur, cette fonction récupère la propriété et sa valeur à partir du capteur.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificateur de capteur |
| *propertyName* | `string` | Nom de propriété de capteur |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

À l’aide d’un identificateur d’appareil, cette fonction récupère la propriété et sa valeur à partir de l’appareil.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identificateur d’appareil |
| *propertyName* | `string` | Nom de propriété d’appareil |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

Cette fonction définit une valeur pour l’objet de capteur avec le type de données indiqué.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificateur de capteur |
| *dataType*  | `string` | Type de données du capteur |
| *value*  | `string` | Valeur |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

Cette fonction définit une valeur pour l’objet d’espace avec le type de données indiqué.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificateur d’espace |
| *dataType* | `string` | Type de données |
| *value* | `string` | Valeur |

### <a name="logmessage"></a>log(message)

Cette fonction journalise le message suivant dans la fonction définie par l’utilisateur.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *message* | `string` | Message à journaliser |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

Cette fonction envoie une notification personnalisée à distribuer.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Identificateur d’objet de graphe. Exemples : espace, capteur et ID d’appareil.|
| *topologyObjectType*  | `string` | Exemples : capteur et appareil.|
| *payload*  | `string` | Charge utile JSON à envoyer avec la notification. |

## <a name="return-types"></a>Types de retour

Les modèles de réponse renvoyés par les méthodes d’assistance de référence du client sont décrits ci-dessous.

### <a name="space"></a>Espace

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "Space",
  "friendlyName": "Conference Room",
  "typeId": 0,
  "parentSpaceId": "00000000-0000-0000-0000-000000000001",
  "subtypeId": 0
}
```

### <a name="space-methods"></a>Méthodes d’espace

#### <a name="parent--space"></a>Parent() ⇒ `space`

Cette fonction retourne l’espace parent de l’espace actuel.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Cette fonction retourne les capteurs enfants de l’espace actuel.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

Cette fonction retourne les appareils enfants de l’espace actuel.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Cette fonction retourne la propriété étendue et sa valeur pour l’espace actuel.

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nom de la propriété étendue |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

Cette fonction retourne la valeur de l’espace actuel.

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nom de la valeur |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

Cette fonction retourne les valeurs historiques de l’espace actuel.

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nom de la valeur |

#### <a name="notifypayload"></a>Notify(payload)

Cette fonction envoie une notification avec la charge utile spécifiée.

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Charge utile JSON à inclure dans la notification |

### <a name="device"></a>Appareil

```JSON
{
  "id": "00000000-0000-0000-0000-000000000002",
  "name": "Device",
  "friendlyName": "Temperature Sensing Device",
  "description": "This device contains a sensor that captures temperature readings.",
  "type": "None",
  "subtype": "None",
  "typeId": 0,
  "subtypeId": 0,
  "hardwareId": "ABC123",
  "gatewayId": "ABC",
  "spaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>Méthodes d’appareil

#### <a name="parent--space"></a>Parent() ⇒ `space`

Cette fonction retourne l’espace parent de l’appareil actuel.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Cette fonction retourne les capteurs enfants de l’appareil actuel.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Cette fonction retourne la propriété étendue et sa valeur pour l’appareil actuel.

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nom de la propriété étendue |

#### <a name="notifypayload"></a>Notify(payload)

Cette fonction envoie une notification avec la charge utile spécifiée.

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Charge utile JSON à inclure dans la notification |

### <a name="sensor"></a>Capteur

```JSON
{
  "id": "00000000-0000-0000-0000-000000000003",
  "port": "30",
  "pollRate": 3600,
  "dataType": "Temperature",
  "dataSubtype": "None",
  "type": "Classic",
  "portType": "None",
  "dataUnitType": "FahrenheitTemperature",
  "spaceId": "00000000-0000-0000-0000-000000000000",
  "deviceId": "00000000-0000-0000-0000-000000000001",
  "portTypeId": 0,
  "dataUnitTypeId": 0,
  "dataTypeId": 0,
  "dataSubtypeId": 0,
  "typeId": 0  
}
```

### <a name="sensor-methods"></a>Méthodes de capteur

#### <a name="space--space"></a>Space() ⇒ `space`

Cette fonction retourne l’espace parent du capteur actuel.

#### <a name="device--device"></a>Device() ⇒ `device`

Cette fonction retourne l’appareil parent du capteur actuel.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Cette fonction retourne la propriété étendue et sa valeur pour le capteur actuel.

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nom de la propriété étendue |

#### <a name="value--value"></a>Value() ⇒ `value`

Cette fonction retourne la valeur du capteur actuel.

#### <a name="history--value"></a>History() ⇒ `value[]`

Cette fonction retourne les valeurs historiques du capteur actuel.

#### <a name="notifypayload"></a>Notify(payload)

Cette fonction envoie une notification avec la charge utile spécifiée.

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Charge utile JSON à inclure dans la notification |

### <a name="value"></a>Valeur

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>Propriété étendue

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur les [fonctions définies par l’utilisateur Azure Digital Twins ](./concepts-user-defined-functions.md).

- Découvrez [comment créer des fonctions définies par l’utilisateur](./how-to-user-defined-functions.md).

- Découvrez [comment déboguer des fonctions définies par l’utilisateur](./how-to-diagnose-user-defined-functions.md).
