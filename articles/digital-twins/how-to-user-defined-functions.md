---
title: Guide pratique pour utiliser des fonctions définies par l’utilisateur dans Azure Digital Twins | Microsoft Docs
description: Indication sur la création de fonctions définies par l’utilisateur, de matchers et d’attributions de rôles avec Azure Digital Twins.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 7fbaff5ed1b60a4434ba2eb0c78c6aa1f3fd6645
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323859"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Guide pratique pour utiliser des fonctions définies par l’utilisateur dans Azure Digital Twins

Les [fonctions définies par l’utilisateur](./concepts-user-defined-functions.md) permettent à l’utilisateur d’exécuter une logique personnalisée sur les messages de télémétrie entrants et sur les métadonnées du graphe spatial, avant d’envoyer des événements à des points de terminaison prédéfinis. Dans ce guide, nous allons étudier un exemple qui montre comment exploiter des événements de température pour détecter une lecture qui dépasse une certaine température et émettre une alerte à son sujet.

Dans les exemples ci-dessous, `https://yourManagementApiUrl` fait référence à l’URI des API Digital Twins :

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management
```

| Nom de l’attribut personnalisé | Remplacer par |
| --- | --- |
| `yourInstanceName` | Nom de votre instance Azure Digital Twins |
| `yourLocation` | Région de serveur dans laquelle votre instance est hébergée |

## <a name="client-library-reference"></a>Informations de référence sur la bibliothèque cliente

Les fonctions qui sont disponibles en tant que méthodes d’assistance dans le runtime des fonctions définies par l’utilisateur sont énumérées dans les [informations de référence sur le client](#Client-Reference) suivantes.

## <a name="create-a-matcher"></a>Créer un matcher

Les matchers sont des objets de graphe, qui déterminent ce que doivent exécuter les fonctions définies par l’utilisateur pour un message de télémétrie donné.

Comparaisons valides pour les conditions du matcher :

- `Equals`
- `NotEquals`
- `Contains`

Cibles valides pour les conditions du matcher :

- `Sensor`
- `SensorDevice`
- `SensorSpace`

L’exemple de matcher suivant est évalué comme vrai sur n’importe quel événement de télémétrie de capteur avec `Temperature` comme valeur de type de données. Vous pouvez créer plusieurs matchers sur une fonction définie par l’utilisateur.

```text
POST https://yourManagementApiUrl/api/v1.0/matchers
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "yourSpaceIdentifier"
}
```

| Nom de l’attribut personnalisé | Remplacer par |
| --- | --- |
| `yourManagementApiUrl` | Chemin d’URL complet pour votre API de gestion  |
| `yourSpaceIdentifier` | Région de serveur dans laquelle votre instance est hébergée |

## <a name="create-a-user-defined-function-udf"></a>Création d’une fonction définie par l’utilisateur

Une fois les matchers créés, chargez l’extrait de code de fonction avec l’appel POST suivant :

> [!IMPORTANT]
> - Dans les en-têtes, définissez l’élément `Content-Type: multipart/form-data; boundary="userDefinedBoundary"` suivant.
> - Le corps comprend deux parties :
>   - La première partie concerne les métadonnées nécessaires pour la fonction définie par l’utilisateur.
>   - La seconde partie est la logique de calcul javascript.
> - Remplacez dans la section `userDefinedBoundary` les GUID `SpaceId` et `Machers`.

```plaintext
POST https://yourManagementApiUrl/api/v1.0/userdefinedfunctions with Content-Type: multipart/form-data; boundary="userDefinedBoundary"
```

| Nom de l’attribut personnalisé | Remplacer par |
| --- | --- |
| `yourManagementApiUrl` | Chemin d’URL complet pour votre API de gestion  |

Corps :

```plaintext
--userDefinedBoundary
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "yourSpaceIdentifier",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["yourMatcherIdentifier"]
}
--userDefinedBoundary
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--userDefinedBoundary--
```

| Nom de l’attribut personnalisé | Remplacer par |
| --- | --- |
| `yourSpaceIdentifier` | Identificateur d’espace  |
| `yourMatcherIdentifier` | ID du matcher que vous souhaitez utiliser |

### <a name="example-functions"></a>Exemples de fonctions

Définissez la lecture des données de télémétrie de capteur directement pour le capteur ayant le type de données `Temperature`, en l’occurrence sensor.DataType :

```javascript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

Journalisez un message si la lecture des données de télémétrie de capteur dépasse un seuil prédéfini. Si vos paramètres de diagnostic sont activés sur l’instance Digital Twins, les journaux issus des fonctions définies par l’utilisateur sont transférés :

```javascript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

Le code suivant déclenche une notification si le niveau de température dépasse la constante prédéfinie.

```javascript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

Pour obtenir un exemple de code de fonction définie par l’utilisateur plus complexe, reportez-vous à [Check available spaces with fresh air UDF](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js) (Vérifier les espaces disponibles avec une fonction définie par l’utilisateur traitant des données de mesure d’air frais).

## <a name="create-a-role-assignment"></a>Création d'une affectation de rôle

Nous devons créer une attribution de rôle sous laquelle devra s’exécuter la fonction définie par l’utilisateur. Si nous ne le faisons pas, elle n’aura pas les autorisations appropriées pour interagir avec l’API de gestion afin d’effectuer des actions sur les objets de graphe. Les actions réalisées par la fonction définie par l’utilisateur sont tributaires du contrôle d’accès en fonction du rôle au sein des API de gestion Digital Twins. Vous pouvez limiter leur étendue en spécifiant certains rôles ou certains chemins de contrôle d’accès. Pour plus d’informations, consultez la documentation [Contrôle d’accès en fonction du rôle](./security-role-based-access-control.md).

- Recherchez les rôles afin d’obtenir l’ID du rôle à affecter à la fonction définie par l’utilisateur, puis passez-le à RoleId ci-dessous.

```plaintext
GET https://yourManagementApiUrl/api/v1.0/system/roles
```

| Nom de l’attribut personnalisé | Remplacer par |
| --- | --- |
| `yourManagementApiUrl` | Chemin d’URL complet pour votre API de gestion  |

- ObjectId correspond à l’ID de fonction définie par l’utilisateur créé précédemment.
- Recherchez `Path` en interrogeant les espaces avec leur chemin complet et copiez la valeur `spacePaths`. Collez-le dans le chemin ci-dessous au moment de la création de l’attribution de rôle pour la fonction définie par l’utilisateur.

```plaintext
GET https://yourManagementApiUrl/api/v1.0/spaces?name=yourSpaceName&includes=fullpath
```

| Nom de l’attribut personnalisé | Remplacer par |
| --- | --- |
| `yourManagementApiUrl` | Chemin d’URL complet pour votre API de gestion  |
| `yourSpaceName` | Nom de l’espace à utiliser |

```plaintext
POST https://yourManagementApiUrl/api/v1.0/roleassignments
{
  "RoleId": "yourDesiredRoleIdentifier",
  "ObjectId": "yourUserDefinedFunctionId",
  "ObjectIdType": "UserDefinedFunctionId",
  "Path": "yourAccessControlPath"
}
```

| Nom de l’attribut personnalisé | Remplacer par |
| --- | --- |
| `yourManagementApiUrl` | Chemin d’URL complet pour votre API de gestion  |
| `yourDesiredRoleIdentifier` | Identificateur pour le rôle souhaité |
| `yourUserDefinedFunctionId` | ID de la fonction définie par l’utilisateur à utiliser |
| `yourAccessControlPath` | Chemin du contrôle d’accès |

## <a name="send-telemetry-to-be-processed"></a>Envoyer les données de télémétrie à traiter

Les données de télémétrie générées par le capteur décrit dans le graphe doivent déclencher l’exécution de la fonction définie par l’utilisateur qui a été chargée. Une fois les données de télémétrie récupérées par le processeur de données, un plan d’exécution est créé pour l’appel de la fonction définie par l’utilisateur.

1. Récupérez les matchers du capteur pour lequel la lecture a été générée.
1. Suivant les matchers évalués avec succès, récupérez les fonctions définies par l’utilisateur associées.
1. Exécutez chaque fonction définie par l’utilisateur.

## <a name="client-reference"></a>Informations de référence sur le client

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Suivant un identificateur d’espace, récupère l’espace du graphe.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| id  | `guid` | Identificateur d’espace |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Suivant un identificateur de capteur, récupère le capteur du graphe.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| id  | `guid` | Identificateur de capteur |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Suivant un identificateur d’appareil, récupère l’appareil du graphe.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| id  | `guid` | Identificateur d’appareil |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

Suivant un identificateur de capteur et son type de données, récupère la valeur actuelle de ce capteur.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | Identificateur de capteur |
| dataType  | `string` | Type de données du capteur |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

Suivant un identificateur d’espace et le nom de valeur, récupère la valeur actuelle de cette propriété d’espace.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | Identificateur d’espace |
| valueName  | `string` | Nom de la propriété d’espace |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>de getSensorHistoryValues (sensorId, type de données) `value[]`

Suivant un identificateur de capteur et son type de données, récupère les valeurs historiques de ce capteur.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | Identificateur de capteur |
| dataType  | `string` | Type de données du capteur |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

Suivant un identificateur d’espace et le nom de valeur, récupère les valeurs historiques de cette propriété sur l’espace.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | Identificateur d’espace |
| valueName  | `string` | Nom de la propriété d’espace |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Suivant un identificateur d’espace, récupère les espaces enfants pour cet espace parent.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | Identificateur d’espace |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Suivant un identificateur d’espace, récupère les capteurs enfants pour cet espace parent.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | Identificateur d’espace |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Suivant un identificateur d’espace, récupère les appareils enfants pour cet espace parent.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | Identificateur d’espace |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Suivant un identificateur d’appareil, récupère les capteurs enfants pour cet appareil parent.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| deviceId  | `guid` | Identificateur d’appareil |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Suivant un identificateur d’espace, récupère son espace parent.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| childSpaceId  | `guid` | Identificateur d’espace |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Suivant un identificateur de capteur, récupère son espace parent.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| childSensorId  | `guid` | Identificateur de capteur |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Suivant un identificateur d’appareil, récupère son espace parent.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| childDeviceId  | `guid` | Identificateur d’appareil |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Suivant un identificateur de capteur, récupère son appareil parent.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| childSensorId  | `guid` | Identificateur de capteur |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

Suivant un identificateur d’espace, récupère la propriété et sa valeur de l’espace.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | Identificateur d’espace |
| propertyName  | `string` | Nom de la propriété d’espace |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

Suivant un identificateur de capteur, récupère la propriété et sa valeur du capteur.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | Identificateur de capteur |
| propertyName  | `string` | Nom de propriété de capteur |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

Suivant un identificateur d’appareil, récupère la propriété et sa valeur de l’appareil.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| deviceId  | `guid` | Identificateur d’appareil |
| propertyName  | `string` | Nom de propriété d’appareil |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

Définit une valeur sur l’objet de capteur avec le type de données indiqué.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | Identificateur de capteur |
| dataType  | `string` | Type de données du capteur |
| value  | `string` | value |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

Définit une valeur sur l’objet d’espace avec le type de données indiqué.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | Identificateur d’espace |
| dataType  | `string` | type de données |
| value  | `string` | value |

### <a name="logmessage"></a>log(message)

Journalise le message suivant au sein de la fonction définie par l’utilisateur.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| Message  | `string` | Message à journaliser |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

Envoie une notification personnalisée en vue de sa distribution.

**Genre** : fonction globale

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| topologyObjectId  | `Guid` | Identificateur d’objet de graphe (ex. ID d’appareil, d’espace ou de capteur)|
| topologyObjectType  | `string` | (ex. espace/capteur/appareil)|
| payload  | `string` | Charge utile JSON à envoyer avec la notification |

## <a name="return-types"></a>Types de retour

Voici une série de modèles qui décrivent les objets retournés à partir des informations de référence sur le client ci-dessus.

### <a name="space"></a>Espace

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000000",
  "Name": "Space",
  "FriendlyName": "Conference Room",
  "TypeId": 0,
  "ParentSpaceId": "00000000-0000-0000-0000-000000000001",
  "SubtypeId": 0
}
```

### <a name="space-methods"></a>Méthodes d’espace

#### <a name="parent--space"></a>Parent() ⇒ `space`

Retourne l’espace parent de l’espace actuel.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Retourne les capteurs enfants de l’espace actuel.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

Retourne les appareils enfants de l’espace actuel.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Retourne la propriété étendue et sa valeur pour l’espace actuel.

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | Nom de la propriété étendue |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

Retourne la valeur de l’espace actuel.

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| valueName | `string` | Nom de la valeur |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

Retourne les valeurs historiques de l’espace actuel.

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| valueName | `string` | Nom de la valeur |

#### <a name="notifypayload"></a>Notify(payload)

Envoie une notification avec la charge utile spécifiée.

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| payload | `string` | Charge utile JSON à inclure dans la notification |

### <a name="device"></a>Appareil

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000002",
  "Name": "Device",
  "FriendlyName": "Temperature Sensing Device",
  "Description": "This device contains a sensor that captures temperature readings.",
  "Type": "None",
  "Subtype": "None",
  "TypeId": 0,
  "SubtypeId": 0,
  "HardwareId": "ABC123",
  "GatewayId": "ABC",
  "SpaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>Méthodes d’appareil

#### <a name="parent--space"></a>Parent() ⇒ `space`

Retourne l’espace parent de l’appareil actuel.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Retourne les capteurs enfants de l’appareil actuel.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Retourne la propriété étendue et sa valeur pour l’appareil actuel.

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | Nom de la propriété étendue |

#### <a name="notifypayload"></a>Notify(payload)

Envoie une notification avec la charge utile spécifiée.

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| payload | `string` | Charge utile JSON à inclure dans la notification |

### <a name="sensor"></a>Capteur

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000003",
  "Port": "30",
  "PollRate": 3600,
  "DataType": "Temperature",
  "DataSubtype": "None",
  "Type": "Classic",
  "PortType": "None",
  "DataUnitType": "FahrenheitTemperature",
  "SpaceId": "00000000-0000-0000-0000-000000000000",
  "DeviceId": "00000000-0000-0000-0000-000000000001",
  "PortTypeId": 0,
  "DataUnitTypeId": 0,
  "DataTypeId": 0,
  "DataSubtypeId": 0,
  "TypeId": 0  
}
```

### <a name="sensor-methods"></a>Méthodes de capteur

#### <a name="space--space"></a>Space() ⇒ `space`

Retourne l’espace parent du capteur actuel.

#### <a name="device--device"></a>Device() ⇒ `device`

Retourne l’appareil parent du capteur actuel.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Retourne la propriété étendue et sa valeur pour le capteur actuel.

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | Nom de la propriété étendue |

#### <a name="value--value"></a>Value() ⇒ `value`

Retourne la valeur du capteur actuel.

#### <a name="history--value"></a>History() ⇒ `value[]`

Retourne les valeurs historiques du capteur actuel.

#### <a name="notifypayload"></a>Notify(payload)

Envoie une notification avec la charge utile spécifiée.

| Paramètre  | type                | Description  |
| ------ | ------------------- | ------------ |
| payload | `string` | Charge utile JSON à inclure dans la notification |

### <a name="value"></a>Valeur

```JSON
{
  "DataType": "Temperature",
  "Value": "70",
  "CreatedTime": ""
}
```

### <a name="extended-property"></a>Propriété Extended

```JSON
{
  "Name": "OccupancyStatus",
  "Value": "Occupied"
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment créer des points de terminaison Digital Twins auxquels envoyer des événements, consultez [Créer des points de terminaison Digital Twins](how-to-egress-endpoints.md).

Pour plus d’informations sur les points de terminaison Digital Twins, consultez [Découvrir les points de terminaison](concepts-events-routing.md).
