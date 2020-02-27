---
title: Modèles de requête courants - Azure Digital Twins | Microsoft Docs
description: Découvrez plusieurs modèles d’interrogation d’API courants pour les API de gestion Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 133c0e0dcc07afb85a0f3af9ae51d2207abac293
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589111"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Guide pratique pour interroger les API Azure Digital Twins pour les tâches courantes

Cet article présente des modèles de requête afin de vous aider à exécuter des scénarios courants pour votre instance Azure Digital Twins. Nous partons du principe que votre instance Digital Twins est déjà en cours d’exécution. Vous pouvez utiliser n’importe quel client REST, tel que Postman. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Requêtes concernant les espaces et les types

Cette section présente des exemples de requêtes pour obtenir plus d’informations sur vos espaces provisionnés. Exécutez des requêtes HTTP GET authentifiées avec les exemples de requêtes, en remplaçant les espaces réservés par les valeurs de votre installation. 

- Obtenir les espaces qui sont des nœuds racines.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Obtenir un espace par nom et inclure les appareils, capteurs, valeurs calculées et valeurs de capteur 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Obtenir des espaces et leurs informations d’appareil/de capteur, dont le parent est l’ID de l’espace spécifié et qui sont au niveau deux à cinq [par rapport à l’espace spécifié](how-to-navigate-apis.md#api-navigation). 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Obtenir l’espace avec l’ID donné, et inclure les valeurs calculées et les valeurs de capteur

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Obtenir les clés de propriétés pour un espace particulier

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Obtenir les espaces avec la clé de propriété nommée *AreaInSqMeters* et la valeur 30. Vous pouvez également effectuer des opérations de chaîne, par exemple obtenir les espaces contenant la clé de propriété avec `name = X contains Y`

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Obtenir tous les noms avec le nom *Temperature* et les dépendances et ontologies associées

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Requêtes concernant les rôles et les attributions de rôle

Cette section présente quelques requêtes permettant d’obtenir plus d’informations sur les rôles et leurs attributions. 

- Obtenir tous les rôles pris en charge par Azure Digital Twins

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Obtenir toutes les attributions de rôles dans votre instance Digital Twins 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Obtenir les attributions de rôles sur un chemin particulier

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Requêtes concernant les appareils

Cette section présente quelques exemples illustrant comment utiliser les API de gestion pour obtenir des informations spécifiques sur vos appareils. Tous les appels d’API doivent être des requêtes HTTP GET authentifiées.

- Obtenir tous les appareils

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Rechercher tous les états des appareils

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Obtenir un appareil spécifique

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Obtenir tous les appareils connectés à l’espace racine

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Obtenir tous les appareils connectés aux espaces de niveaux 2 à 4

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Obtenir tous les appareils connectés directement à un ID d’espace particulier

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Obtenir tous les appareils connectés à un espace particulier et ses descendants

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Obtenir tous les appareils connectés à des descendants d’un espace, à l’exclusion de cet espace

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Obtenir tous les appareils connectés à des enfants directs d’un espace

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Obtenir tous les appareils connectés à l’un des ancêtres d’un espace

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Obtenir tous les appareils connectés à des descendants d’un espace situés à un niveau inférieur ou égal à 5

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Obtenir tous les appareils connectés à des espaces situés au même niveau que l’espace avec l’ID *YOUR_SPACE_ID*

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Obtenir la chaîne de connexion IoT Hub pour votre appareil.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Obtenir l’appareil avec l’ID de matériel donné, y compris les capteurs attachés

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Obtenir les capteurs pour des types de données particuliers, dans le cas présent *Motion* et *Temperature*

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Requêtes concernant les matchers et les fonctions définies par l’utilisateur 

- Obtenir tous les matchers provisionnés et leurs ID

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Obtenir des détails sur un matcher spécifique, notamment les espaces et la fonction définie par l’utilisateur associée

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Évaluer un matcher par rapport à un capteur et activer la journalisation pour le débogage. Le retour de ce message HTTP GET vous indique si le matcher et le capteur appartiennent au type de données 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Obtenir l’ID des fonctions définies par l’utilisateur 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Obtenir le contenu d’une fonction définie par l’utilisateur particulière 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Requêtes concernant les utilisateurs

Cette section présente quelques exemples de requêtes d’API pour la gestion des utilisateurs dans Azure Digital Twins. Effectuez une requête HTTP GET en remplaçant les espaces réservés par les valeurs de votre installation. 

- Obtenir tous les utilisateurs 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Obtenir un utilisateur spécifique

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment vous authentifier auprès de votre API de gestion, consultez [Authentification auprès des API](./security-authenticating-apis.md).

Pour en savoir plus sur vos points de terminaison d’API, consultez [Comment utiliser Digital Twins Swagger](./how-to-use-swagger.md).
