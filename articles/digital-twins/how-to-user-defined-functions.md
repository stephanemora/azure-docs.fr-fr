---
title: Guide pratique pour utiliser des fonctions définies par l’utilisateur dans Azure Digital Twins | Microsoft Docs
description: Recommandations relatives à la création de fonctions définies par l’utilisateur, de matchers et d’attributions de rôles avec Azure Digital Twins.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: alinast
ms.openlocfilehash: 6a757dca48dc3ff41adfe6f8802fad40e7a4ca81
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636830"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Guide pratique pour utiliser des fonctions définies par l’utilisateur dans Azure Digital Twins

Les [fonctions définies par l’utilisateur](./concepts-user-defined-functions.md) (ou fonctions UDF) permettent à l’utilisateur d’exécuter une logique personnalisée sur les messages de télémétrie entrants et les métadonnées d’un graphe spatial. L’utilisateur peut ensuite envoyer des événements à des points de terminaison prédéfinis. Ce guide montre à l’aide d’un exemple comment exploiter des événements de température pour détecter une lecture qui dépasse une certaine température, et émettre une alerte correspondante.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Informations de référence sur la bibliothèque cliente

Les fonctions disponibles en tant que méthodes d’assistance dans le runtime des fonctions définies par l’utilisateur sont listées dans la section [Informations de référence sur le client](#Client-Reference).

## <a name="create-a-matcher"></a>Créer un matcher

Les matchers sont des objets de graphe qui déterminent quelles sont les fonctions définies par l’utilisateur exécutées pour un message de télémétrie donné.

- Comparaisons valides pour les conditions du matcher :

  - `Equals`
  - `NotEquals`
  - `Contains`

- Cibles valides pour les conditions du matcher :

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

L’exemple de matcher suivant a la valeur true pour tout événement de télémétrie de capteur ayant `"Temperature"` comme valeur de type de données. Vous pouvez créer plusieurs matchers sur une fonction définie par l’utilisateur :

```plaintext
POST YOUR_MANAGEMENT_API_URL/matchers
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
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| Valeur | Remplacer par |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Région de serveur dans laquelle votre instance est hébergée |

## <a name="create-a-user-defined-function-udf"></a>Création d’une fonction définie par l’utilisateur

Une fois les matchers créés, chargez l’extrait de fonction avec l’appel **POST** suivant :

> [!IMPORTANT]
> - Dans les en-têtes, définissez le `Content-Type: multipart/form-data; boundary="userDefinedBoundary"` suivant.
> - Le corps comprend plusieurs parties :
>   - La première partie concerne les métadonnées nécessaires à la fonction UDF.
>   - La deuxième partie concerne la logique de calcul JavaScript.
> - Dans la section **USER_DEFINED_BOUNDARY**, remplacez les valeurs de **SpaceId** et **Matchers**.

```plaintext
POST YOUR_MANAGEMENT_API_URL/userdefinedfunctions with Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"
```

| Valeur du paramètre | Remplacer par |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | Nom de la limite de contenu en plusieurs parties |

### <a name="body"></a>body

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "YOUR_SPACE_IDENTIFIER",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| Valeur | Remplacer par |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Identificateur d’espace  |
| YOUR_MATCHER_IDENTIFIER | ID du matcher à utiliser |

### <a name="example-functions"></a>Exemples de fonctions

Définissez la lecture des données de télémétrie de capteur directement pour le capteur ayant le type de données **Température**, en l’occurrence `sensor.DataType` :

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

Le paramètre **telemetry** expose les attributs **SensorId**et **Message**, qui correspondent à un message envoyé par un capteur. Le paramètre **executionContext** expose les attributs suivants :

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

Dans l’exemple suivant, nous journalisons un message si la lecture des données de télémétrie de capteur dépasse un seuil prédéfini. Si vos paramètres de diagnostic sont activés sur l’instance Azure Digital Twins, les journaux issus des fonctions définies par l’utilisateur sont également transférés :

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

Le code suivant déclenche une notification si le niveau de température dépasse la constante prédéfinie :

```JavaScript
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

Pour obtenir un exemple de code de fonction UDF plus complexe, [vérifiez les espaces disponibles avec une fonction UDF analysant les données de mesure de la pureté de l’air](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Création d'une affectation de rôle

Nous devons créer une attribution de rôle permettant à la fonction définie par l’utilisateur de s’exécuter. Si nous ne le faisons pas, elle n’aura pas les autorisations appropriées pour interagir avec l’API de gestion afin d’effectuer des actions sur les objets de graphe. Les actions effectuées par la fonction définie par l’utilisateur ne sont pas exemptées du contrôle d’accès en fonction du rôle (RBAC) au sein des API de gestion Azure Digital Twins. Vous pouvez limiter leur étendue en spécifiant certains rôles ou certains chemins de contrôle d’accès. Pour plus d’informations, consultez la documentation sur le [contrôle d’accès en fonction du rôle (RBAC)](./security-role-based-access-control.md).

1. Recherchez des rôles pour obtenir l’ID du rôle à attribuer à la fonction UDF. Passez-le à **RoleId** :

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/system/roles
    ```

1. **ObjectId** correspond à l’ID de fonction définie par l’utilisateur créé précédemment.
1. Recherchez la valeur de **Chemin d’accès** en interrogeant votre espaces avec `fullpath`.
1. Copiez la valeur `spacePaths` retournée. Vous allez l’utiliser dans le code suivant :

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Valeur du paramètre | Remplacer par |
    | --- | --- |
    | *YOUR_SPACE_NAME* | Nom de l’espace à utiliser |

1. Collez la valeur retournée `spacePaths` dans  **Chemin** pour créer une attribution de rôle de fonction UDF :

    ```plaintext
    POST YOUR_MANAGEMENT_API_URL/roleassignments
    {
      "RoleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "ObjectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "ObjectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "Path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Votre valeur | Remplacer par |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | Identificateur pour le rôle souhaité |
    | YOUR_USER_DEFINED_FUNCTION_ID | ID de la fonction définie par l’utilisateur à utiliser |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | ID spécifiant le type de fonction UDF |
    | YOUR_ACCESS_CONTROL_PATH | Chemin du contrôle d’accès |

## <a name="send-telemetry-to-be-processed"></a>Envoyer les données de télémétrie à traiter

La télémétrie générée par le capteur décrit dans le graphe déclenche l’exécution de la fonction définie par l’utilisateur, qui a été chargée. Le processeur de données collecte la télémétrie. Un plan d’exécution est ensuite créé pour l’appel de la fonction définie par l’utilisateur.

1. Récupérez les matchers du capteur pour lequel la lecture a été générée.
1. Suivant les matchers évalués avec succès, récupérez les fonctions définies par l’utilisateur associées.
1. Exécutez chaque fonction définie par l’utilisateur.

## <a name="client-reference"></a>Informations de référence sur le client

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

Les modèles suivants décrivent les objets retournés à partir de la référence client précédente.

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
  "DataType": "Temperature",
  "Value": "70",
  "CreatedTime": ""
}
```

### <a name="extended-property"></a>Propriété étendue

```JSON
{
  "Name": "OccupancyStatus",
  "Value": "Occupied"
}
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer des points de terminaison Azure Digital Twins](how-to-egress-endpoints.md) auxquels envoyer des événements.

- Pour plus d’informations sur les points de terminaison Azure Digital Twins, consultez [Découvrir les points de terminaison](concepts-events-routing.md).
