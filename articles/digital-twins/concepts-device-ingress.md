---
title: Entrée de télémétrie et connectivité des appareils avec Azure Digital Twins | Microsoft Docs
description: Vue d’ensemble de l’intégration d’un appareil à Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: 7eddea7e0d57b89318232da6f086bbe2f649ee77
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211925"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Entrée de télémétrie et connectivité des appareils

Les données de télémétrie envoyées par les appareils et les capteurs constituent l’armature de toute solution IoT. Ainsi, la représentation de ces ressources disparates et leur gestion dans le contexte d’un emplacement sont une préoccupation primordiale pendant le développement d’applications IoT. Azure Digital Twins simplifie le développement de solutions IoT en assemblant des appareils et des capteurs avec un graphe d’intelligence spatiale.

Au départ, une ressource IoT Hub doit être créée à la racine du graphe spatial, ce qui permet à tous les appareils sous l’espace racine d’envoyer des messages. Une fois que le hub IoT a été créé et que des appareils avec des capteurs ont été inscrits dans l’instance Digital Twins, les appareils peuvent commencer à envoyer des données à un service Digital Twins par le biais du [kit Azure IoT device SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-device-sdks).

Un guide pas à pas sur l’intégration des appareils est à votre disposition dans le [tutoriel montrant comment déployer et configurer Azure Digital Twins](tutorial-facilities-setup.md). Voici un récapitulatif des étapes :

- Déployer une instance Azure Digital Twins à partir du [portail Azure](https://portal.azure.com)
- Créer des espaces dans votre graphe
- Créer une ressource IoT Hub et l’affecter à un espace dans votre graphe
- Créer des appareils et des capteurs dans votre graphe, puis les affecter aux espaces créés au cours des étapes ci-dessus
- Créer un matcher pour filtrer les messages de télémétrie en fonction de conditions
- Créer une [fonction définie par l’utilisateur](concepts-user-defined-functions.md) et l’affecter à un espace dans le graphe en vue d’un traitement personnalisé de vos messages de télémétrie
- Attribuer un rôle pour autoriser la fonction définie par l’utilisateur à accéder aux données du graphe
- Obtenir la chaîne de connexion d’appareil IoT Hub à partir des API de gestion Digital Twins
- Configurer la chaîne de connexion d’appareil sur l’appareil avec le kit Azure IoT device SDK

Ci-après, vous allez apprendre à obtenir la chaîne de connexion d’appareil IoT Hub à partir de l’API de gestion Digital Twins et à utiliser le format de message de télémétrie IoT Hub pour envoyer des données de télémétrie basées sur un capteur. Digital Twins nécessite que chaque donnée de télémétrie qu’il reçoit soit associée à un capteur dans le graphe spatial afin que les données soient traitées et routées dans le contexte spatial approprié.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Obtenir la chaîne de connexion d’appareil IoT Hub à partir de l’API de gestion

Effectuez un appel GET sur l’API d’appareil avec un paramètre `includes=ConnectionString` pour obtenir la chaîne de connexion d’appareil IoT Hub. Vous pouvez filtrer par GUID d’appareil ou ID de matériel pour trouver l’appareil donné :

```plaintext
https://yourManagementApiUrl/api/v1.0/devices/yourDeviceGuid?includes=ConnectionString
```

```plaintext
https://yourManagementApiUrl/api/v1.0/devices?hardwareIds=yourDeviceHardwareId&includes=ConnectionString
```

| Nom de l’attribut personnalisé | Remplacer par |
| --- | --- |
| *yourManagementApiUrl* | Chemin d’URL complet pour votre API de gestion |
| *yourDeviceGuid* | ID de l’appareil |
| *yourDeviceHardwareId* | ID matériel de l’appareil |

Dans la charge utile de la réponse, copiez la propriété *connectionString* de l’appareil, que vous utilisez ensuite pour appeler le kit Azure IoT device SDK, afin d’envoyer les données à Azure Digital Twins.

## <a name="device-to-cloud-message"></a>Message appareil-à-cloud

Vous pouvez personnaliser le format et la charge utile du message de votre appareil en fonction des besoins de votre solution. Vous pouvez utiliser n’importe quel contrat de données qui peut être sérialisé en un flux ou tableau d’octets pris en charge par la [classe Message Azure IoT Device Client Message(byte[] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Le message peut être un format binaire personnalisé de votre choix, sous réserve que vous décodiez le contrat de données dans une fonction définie par l’utilisateur correspondante. La seule exigence pour un message appareil-à-cloud consiste à gérer un ensemble de propriétés (voir ci-dessous) garantissant le routage approprié du message vers le moteur de traitement.

### <a name="telemetry-properties"></a>Propriétés de données de télémétrie

Alors que le contenu de la charge utile d’un **Message** peut être constitué de données arbitraires dans la limite de 256 Ko, il existe quelques exigences sur les [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) attendues. Les étapes décrites ci-dessous reflètent les propriétés obligatoires et facultatives prises en charge par le système :

| Nom de la propriété | Valeur | Obligatoire | Description |
|---|---|---|---|
| *DigitalTwins-Telemetry* | 1.0 | Oui | Valeur constante qui identifie un message auprès du système |
| *DigitalTwins-SensorHardwareId* | `string(72)` | Oui | Identificateur unique du capteur envoyant le **Message**. Cette valeur doit correspondre à la propriété **HardwareId** d’un objet pour que le système puisse la traiter. Par exemple, `00FF0643BE88-CO2` |
| *CreationTimeUtc* | `string` | Non  | Chaîne de date au format [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) qui identifie l’heure d’échantillonnage de la charge utile. Par exemple, `2018-09-20T07:35:00.8587882-07:00` |
| *CorrelationId* | `string` | Non  | UUID qui peut être utilisé pour tracer les événements sur le système. Par exemple, `cec16751-ab27-405d-8fe6-c68e1412ce1f`

### <a name="sending-your-message-to-digital-twins"></a>Envoyer votre message à Digital Twins

Utiliser l’appel DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) ou [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) pour envoyer votre message à votre service Digital Twins.

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir les fonctionnalités de traitement des données et de fonctions définies par l’utilisateur d’Azure Digital Twins, consultez [Traitement des données et fonctions définies par l’utilisateur dans Azure Digital Twins](concepts-user-defined-functions.md).

