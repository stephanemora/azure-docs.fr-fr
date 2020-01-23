---
title: Entrée de télémétrie et connectivité des appareils - Azure Digital Twins | Microsoft Docs
description: Apprenez à vous connecter, à intégrer et à envoyer des données de télémétrie à partir d’un appareil IoT dans Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 5c2c519ece9806b92c3e455d5f550bc2abfc9f3b
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75862473"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Entrée de télémétrie et connectivité des appareils

Les données de télémétrie envoyées par les appareils et les capteurs constituent l’armature de toute solution IoT. La représentation de ces ressources disparates et leur gestion dans le contexte d’un emplacement sont une préoccupation primordiale pendant le développement d’applications IoT. Azure Digital Twins simplifie le processus de développement de solutions IoT en assemblant des appareils et des capteurs avec un graphe d’intelligence spatiale.

Pour commencer, créez une ressource Azure IoT Hub à la racine du graphique spatial. La ressource IoT Hub permet à tous les appareils sous l’espace racine d’envoyer des messages. Une fois l’IoT Hub créé, enregistrez vos appareils dotés de capteurs dans l’instance Digital Twins. Les appareils peuvent envoyer des données à un service Digital Twins via le [Kit de développement logiciel (SDK) pour appareils Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks).

Un guide pas à pas sur l’intégration des appareils est disponible dans le [tutoriel de déploiement et de configuration de Digital Twins](tutorial-facilities-setup.md). Voici un récapitulatif des étapes :

- Déployez une instance Digital Twins à partir du [portail Azure](https://portal.azure.com).
- Créez des espaces dans votre graphique.
- Créez une ressource IoT Hub et affectez-la à un espace dans votre graphique.
- Créez des appareils et des capteurs dans votre graphique, puis affectez-les aux espaces créés au cours des étapes précédentes.
- Créez un matcher pour filtrer les messages de télémétrie en fonction de conditions.
- Créez une [fonction définie par l’utilisateur](concepts-user-defined-functions.md) et affectez-la à un espace dans le graphique en vue d’un traitement personnalisé de vos messages de télémétrie.
- Attribuez un rôle pour autoriser la fonction définie par l’utilisateur à accéder aux données du graphique.
- Récupérez la chaîne de connexion d’appareil IoT Hub à partir des API de gestion Digital Twins.
- Configurez la chaîne de connexion d’appareil sur l’appareil en utilisant le Kit de développement logiciel (SDK) pour appareils Azure IoT.

Dans les sections suivantes, vous découvrirez comment récupérer la chaîne de connexion d’appareil IoT Hub à partir des API de gestion Digital Twins. Vous apprendrez également à utiliser le format de message de télémétrie IoT Hub pour envoyer des données de télémétrie basées sur un capteur. Digital Twins nécessite l’association de chaque élément de télémétrie qu'il reçoit à un capteur dans le graphique spatial. Ceci permet de s’assurer que les données sont traitées et acheminées dans le contexte spatial approprié.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Obtenir la chaîne de connexion d’appareil IoT Hub à partir de l’API de gestion

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Effectuez un appel GET sur l’API d’appareil avec un paramètre `includes=ConnectionString` pour obtenir la chaîne de connexion d’appareil IoT Hub. Filtrez par GUID d’appareil ou ID de matériel pour trouver l’appareil donné.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Paramètre | Remplacer par |
| --- | --- |
| *LE_GUID_DE_VOTRE_APPAREIL* | ID de l’appareil |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?HardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Valeur du paramètre | Remplacer par |
| --- | --- |
| *ID_MATÉRIEL_DE_VOTRE_APPAREIL* | ID matériel de l’appareil |

Dans la charge utile de réponse, copiez la propriété **connectionString** de l’appareil. Vous l’utiliserez lorsque vous appelerez le Kit de développement logiciel (SDK) pour appareil Azure IoT afin d’envoyer des données à Digital Twins.

## <a name="device-to-cloud-message"></a>Message d’appareil vers le cloud

Vous pouvez personnaliser le format et la charge utile du message de votre appareil en fonction des besoins de votre solution. Utilisez n’importe quel contrat de données qui peut être sérialisé en un flux ou tableau d’octets pris en charge par la [classe Message Azure IoT Device Client Message(byte[] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Le message peut être un format binaire personnalisé de votre choix, sous réserve que vous décodiez le contrat de données dans une fonction définie par l’utilisateur correspondante. Il n’y a qu’une seule condition requise pour le bon fonctionnement d’un message d’appareil vers le cloud. Conservez un ensemble de propriétés pour vous assurer que votre message est acheminé de façon appropriée au moteur de traitement.

### <a name="telemetry-properties"></a>Propriétés de données de télémétrie

 La charge utile d’un **Message** peut être constitué de données arbitraires dans la limite de 256 Ko. Cependant, , il existe quelques exigences sur les propriétés attendues du type [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet). Ce tableau indique les propriétés obligatoires et facultatives prises en charge par le système.

| Nom de la propriété | Valeur | Obligatoire | Description |
|---|---|---|---|
| **DigitalTwins-Telemetry** | 1.0 | Oui | Valeur constante qui identifie un message auprès du système. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Oui | Identificateur unique du capteur envoyant le **Message**. Cette valeur doit correspondre à la propriété **HardwareId** d’un objet pour que le système puisse la traiter. Par exemple : `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Non | Chaîne de date au format [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) qui identifie l’heure d’échantillonnage de la charge utile. Par exemple : `2018-09-20T07:35:00.8587882-07:00`. |
| **CorrelationId** | `string` | Non | UUID qui peut être utilisé pour tracer les événements sur le système. Par exemple : `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Envoyer votre message à Digital Twins

Utilisez l’appel DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) ou [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) pour envoyer votre message à Digital Twins.

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir les fonctionnalités de traitement des données et de fonctions définies par l’utilisateur d’Azure Digital Twins, consultez [Traitement des données et fonctions définies par l’utilisateur dans Azure Digital Twins](concepts-user-defined-functions.md).
