---
title: Guide pratique pour créer des fonctions définies par l’utilisateur – Azure Digital Twins | Microsoft Docs
description: Guide pratique pour créer des fonctions définies par l’utilisateur, des matchers et des attributions de rôles dans Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 7334d4292db710a32b888d9a3ad4e78872d15227
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863510"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Guide pratique pour créer des fonctions définies par l’utilisateur dans Azure Digital Twins

Les [fonctions définies par l’utilisateur](./concepts-user-defined-functions.md) permettent aux utilisateurs de configurer une logique personnalisée afin de l’exécuter à partir de messages de télémétrie entrants et de métadonnées d’un graphe spatial. Les utilisateurs peuvent également envoyer des événements à des [points de terminaison](./how-to-egress-endpoints.md) prédéfinis.

Ce guide montre, à l’aide d’un exemple, comment détecter une lecture qui dépasse une certaine température, et émettre une alerte correspondante à partir d’événements de température reçus.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Informations de référence sur la bibliothèque cliente

Les fonctions disponibles en tant que méthodes d’assistance dans le runtime des fonctions définies par l’utilisateur sont listées dans le document de [référence sur les bibliothèques de client](./reference-user-defined-functions-client-library.md).

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

L’exemple de matcher suivant a la valeur true pour tout événement de télémétrie de capteur ayant `"Temperature"` comme valeur de type de données. Vous pouvez créer plusieurs matchers sur une fonction définie par l’utilisateur en exécutant une requête HTTP POST authentifiée dans :

```URL
YOUR_MANAGEMENT_API_URL/matchers
```

Avec le corps JSON :

```JSON
{
  "id": "3626464-f39b-46c0-d9b0c-436aysj55",
  "name": "Temperature Matcher",
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "conditions": [
    {
      "id": "ag7gq35cfu3-e15a-4e9c-6437-sj6w68sy44s",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ]
}
```

| Valeur | Remplacer par |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Région de serveur dans laquelle votre instance est hébergée |

## <a name="create-a-user-defined-function"></a>Création d’une fonction définie par l’utilisateur

La création d’une fonction définie par l’utilisateur implique qu’une requête HTTP en plusieurs parties soit envoyée aux API de gestion Azure Digital Twins.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

Une fois les matchers créés, chargez l’extrait de fonction avec la requête HTTP POST en plusieurs parties authentifiée suivante dans :

```URL
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Utilisez le corps suivant :

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "name": "User Defined Function",
  "description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "matchers": ["YOUR_MATCHER_IDENTIFIER"]
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
| USER_DEFINED_BOUNDARY | Nom de la limite de contenu en plusieurs parties |
| YOUR_SPACE_IDENTIFIER | Identificateur d’espace  |
| YOUR_MATCHER_IDENTIFIER | ID du matcher à utiliser |

1. Vérifiez que les en-têtes incluent : `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Vérifiez que le corps comprend plusieurs parties :

   - La première partie contient les métadonnées requises de la fonction définie par l’utilisateur.
   - La deuxième partie contient la logique de calcul JavaScript.

1. Dans la section **USER_DEFINED_BOUNDARY**, remplacez les valeurs de **spaceId** (`YOUR_SPACE_IDENTIFIER`) et **matchers** (`YOUR_MATCHER_IDENTIFIER`).
1. Vérifiez que la fonction JavaScript définie par l’utilisateur est fournie comme suit  : `Content-Type: text/javascript`.

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

Dans l’exemple suivant, nous journalisons un message si la lecture des données de télémétrie de capteur dépasse un seuil prédéfini. Si vos paramètres de diagnostic sont activés sur l’instance Azure Digital Twins, les journaux d’activité issus des fonctions définies par l’utilisateur sont également transférés :

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

Pour obtenir un exemple de code d’une fonction plus complexe définie par l’utilisateur, lisez le [Guide de démarrage rapide de recherche de salles disponibles](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Création d'une affectation de rôle

Créez une attribution de rôle permettant à la fonction définie par l’utilisateur de s’exécuter. Si aucune attribution de rôle n’existe pour la fonction définie par l’utilisateur, cette dernière n’a pas les autorisations appropriées pour interagir avec l’API de gestion ou avoir l’accès permettant d’effectuer des actions sur les objets de graphe. Les actions susceptibles d’être effectuées par la fonction définie par l’utilisateur sont spécifiées et définies via le contrôle d’accès en fonction du rôle (RBAC) au sein des API de gestion Azure Digital Twins. Par exemple, les fonctions définies par l’utilisateur peuvent avoir une étendue limitée en spécifiant certains rôles ou certains chemins de contrôle d’accès. Pour plus d’informations, lisez la documentation sur le [contrôle d’accès en fonction du rôle](./security-role-based-access-control.md).

1. [Interrogez l’API System](./security-create-manage-role-assignments.md#retrieve-all-roles) pour tous les rôles afin d’obtenir l’ID de rôle que vous souhaitez affecter à votre fonction définie par l’utilisateur. Exécutez une requête HTTP GET authentifiée sur :

    ```URL
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Conserver l’ID de rôle souhaité. Il sera transmis en tant qu’attribut **roleId** (`YOUR_DESIRED_ROLE_IDENTIFIER`) du corps JSON ci-dessous.

1. **objectId** (`YOUR_USER_DEFINED_FUNCTION_ID`) correspond à l’ID de fonction définie par l’utilisateur créé précédemment.
1. Recherchez la valeur de **path** (`YOUR_ACCESS_CONTROL_PATH`) en interrogeant votre espaces avec `fullpath`.
1. Copiez la valeur `spacePaths` retournée. Vous l’utiliserez ci-dessous. Exécutez une requête HTTP GET sur :

    ```URL
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Valeur | Remplacer par |
    | --- | --- |
    | YOUR_SPACE_NAME | Nom de l’espace à utiliser |

1. Collez la valeur `spacePaths` retournée dans **path** pour créer une attribution de rôle de fonction définie par l’utilisateur en envoyant une requête HTTP POST authentifiée vers :

    ```URL
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    Avec le corps JSON :

    ```JSON
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Valeur | Remplacer par |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | Identificateur pour le rôle souhaité |
    | YOUR_USER_DEFINED_FUNCTION_ID | ID de la fonction définie par l’utilisateur que vous souhaitez utiliser |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | ID spécifiant le type de la fonction définie par l’utilisateur (`UserDefinedFunctionId`) |
    | YOUR_ACCESS_CONTROL_PATH | Chemin du contrôle d’accès |

>[!TIP]
> Lisez l’article [Créer et gérer des attributions de rôles](./security-create-manage-role-assignments.md) pour plus d’informations sur les opérations et les points de terminaison de l’API Management qui concernent les fonctions définies par l’utilisateur.

## <a name="send-telemetry-to-be-processed"></a>Envoyer les données de télémétrie à traiter

Le capteur défini dans le graphique d’intelligence spatiale envoie la télémétrie. La télémétrie déclenche à son tour l’exécution de la fonction définie par l’utilisateur qui a été chargée. Le processeur de données collecte la télémétrie. Un plan d’exécution est ensuite créé pour l’appel de la fonction définie par l’utilisateur.

1. Récupérez les matchers du capteur depuis lequel la lecture a été générée.
1. Suivant les matchers évalués avec succès, récupérez les fonctions définies par l’utilisateur associées.
1. Exécutez chaque fonction définie par l’utilisateur.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer des points de terminaison Azure Digital Twins](./how-to-egress-endpoints.md) auxquels envoyer des événements.

- Pour plus d’informations sur le routage dans Azure Digital Twins, consultez [Routage des événements et des messages](./concepts-events-routing.md).

- Consultez la [documentation sur les informations de référence de la bibliothèque cliente](./reference-user-defined-functions-client-library.md).