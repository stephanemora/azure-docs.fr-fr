---
title: Guide du développeur - Préversion d’IoT Plug-and-Play | Microsoft Docs
description: Description de la modélisation des appareils pour les développeurs IoT Plug-and-Play
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5fda51e6d2f62b9cbef0fcac22d5bb2ea0df905b
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605224"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>Guide du développeur pour la modélisation avec la préversion d’IoT Plug-and-Play

La préversion d’IoT Plug-and-Play vous permet de créer des appareils qui publient leurs fonctionnalités sur les applications Azure IoT. Les appareils IoT Plug-and-Play n’ont pas besoin de configuration manuelle quand un client les connecte à des applications compatibles avec IoT Plug-and-Play. IoT Central est un exemple d’application compatible avec IoT Plug-and-Play.

Pour créer un appareil IoT Plug-and-Play, vous devez créer une description de l’appareil. La description est effectuée à l’aide d’un langage de définition simple appelé DTDL (langage de définition de jumeau numérique).

## <a name="device-capability-model"></a>Modèle de capacité d’appareil

Avec DTDL, vous créez un _modèle de capacité d’appareil_ pour décrire les parties de votre appareil. Un appareil IoT classique est constitué des éléments suivants :

- Des parties personnalisées, qui rendent votre appareil unique.
- Des parties standard, communes à tous les appareils.

Ces parties sont appelés _interfaces_ dans un modèle de capacité d’appareil. Les interfaces définissent les détails de chaque partie implémentée par votre appareil.

L’exemple suivant montre le modèle de capacité d’appareil pour un appareil à thermostat :

```json
{
  "@id": "urn:example:Thermostat_T_1000:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "name": "thermostat",
      "schema": "urn:example:Thermostat:1"
    },
    {
      "name": "urn_azureiot_deviceManagement_DeviceInformation",
      "schema": "urn:azureiot:deviceManagement:DeviceInformation:1"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Un modèle de capacité comporte certains champs obligatoires :

- `@id` : ID unique sous la forme d’un nom de ressource uniforme simple.
- `@type` : déclare que cet objet est un modèle de capacité.
- `@context` : spécifie la version DTDL utilisée pour le modèle de capacité.
- `implements` : liste les interfaces implémentées par votre appareil.

Chaque entrée de la liste d’interfaces dans la section implements comprend :

- `name` : nom de programmation de l’interface.
- `schema` : interface implémentée par le modèle de capacité.

Il existe des champs facultatifs supplémentaires que vous pouvez utiliser pour ajouter des détails au modèle de capacité, tels que le nom d’affichage et la description. Les interfaces déclarées dans un modèle de capacité peuvent être considérées comme des composants de l’appareil. Pour la préversion publique, la liste d’interfaces peut n’avoir qu’une seule entrée par schéma.

## <a name="interface"></a>Interface

Avec DTDL, vous décrivez les fonctionnalités de votre appareil à l’aide d’interfaces. Les interfaces décrivent les _propriétés_, _données de télémétrie_ et _commandes_ qu’une partie de votre appareil implémente :

- `Properties`. Les propriétés sont des champs de données qui représentent l’état de votre appareil. Utilisez les propriétés pour représenter l’état durable de l’appareil, par exemple l’état marche/arrêt d’une pompe de refroidissement. Les propriétés peuvent également représenter des propriétés d’appareil de base, telles que la version du microprogramme de l’appareil. Vous pouvez déclarer des propriétés comme étant accessibles en lecture seule ou en écriture.
- `Telemetry`. Les champs de télémétrie représentent les mesures des capteurs. Chaque fois que votre appareil prend une mesure de capteur, il doit envoyer un événement de télémétrie contenant les données de capteur.
- `Commands`. Les commandes représentent les méthodes que les utilisateurs de votre appareil peuvent exécuter sur l’appareil. Par exemple, une commande de réinitialisation ou une commande pour allumer ou éteindre un ventilateur.

L’exemple suivant montre l’interface pour un appareil à thermostat :

```json
{
  "@id": "urn:example:Thermostat:1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Telemetry",
      "name": "temperature",
      "schema": "double"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Une interface contient certains champs obligatoires :

- `@id` : ID unique sous la forme d’un nom de ressource uniforme simple.
- `@type` : déclare que cet objet est une interface.
- `@context` : spécifie la version DTDL utilisée pour l’interface.
- `contents` : liste les propriétés, données de télémétrie et commandes qui composent votre appareil.

Dans cet exemple simple, il n’y a qu’un seul champ de télémétrie. Une description de champ minimale comprend :

- `@type` : spécifie le type de fonctionnalité : `Telemetry`, `Property` ou `Command`.
- `name` : fournit le nom de la valeur de télémétrie.
- `schema` : spécifie le type des données de télémétrie. Cette valeur peut être un type primitif, tel que double, entier, valeur booléenne ou chaîne. Les types d’objets complexes, les tableaux et les mappages sont également pris en charge.

D’autres champs facultatifs, tels que le nom d’affichage et la description, vous permettent d’ajouter des détails à l’interface et aux fonctionnalités.

### <a name="properties"></a>Propriétés

Par défaut, les propriétés sont en lecture seule. Ainsi, l’appareil signale les mises à jour de valeurs de propriété à votre hub IoT. Votre hub IoT ne peut pas définir la valeur d’une propriété en lecture seule.

Vous pouvez également marquer une propriété comme étant accessible en écriture sur une interface. Un appareil peut recevoir de votre hub IoT une mise à jour d’une propriété accessible en écriture ainsi que signaler les mises à jour de valeurs de propriété à votre hub.

Les appareils n’ont pas besoin d’être connectés pour définir des valeurs de propriété. Les valeurs mises à jour sont transférées quand l’appareil se connecte ensuite au hub. Ce comportement s’applique aux propriétés en lecture seule et en écriture.

N’utilisez pas de propriétés pour envoyer des données de télémétrie à partir de votre appareil. Par exemple, une propriété en lecture seule telle que `temperatureSetting=80` doit signifier que la température de l’appareil a été définie sur 80 et que l’appareil tente d’atteindre cette température ou d’y rester.

Pour les propriétés accessibles en écriture, l’application d’appareil retourne un code d’état, une version et une description de l’état souhaité pour indiquer si elle a reçu et appliqué la valeur de la propriété.

### <a name="telemetry"></a>Télémétrie

Par défaut, IoT Hub route tous les messages de télémétrie des appareils vers son [point de terminaison intégré côté service (**messages/events**)](../iot-hub/iot-hub-devguide-messages-read-builtin.md) qui est compatible avec [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/).

Vous pouvez utiliser [des règles de routage et des points de terminaison personnalisés d’IoT Hub](../iot-hub/iot-hub-devguide-messages-d2c.md) pour envoyer des données de télémétrie à d’autres destinations, telles que le stockage d’objets blob ou d’autres hubs d’événements. Les règles de routage utilisent des propriétés de message pour sélectionner des messages.

### <a name="commands"></a>Commandes

Les commandes sont synchrones ou asynchrones. Une commande synchrone doit être exécutée dans un délai de 30 secondes par défaut, et l’appareil doit être connecté quand la commande arrive. Si l’appareil ne répond pas à temps ou qu’il n’est pas connecté, la commande échoue.

Utilisez des commandes asynchrones pour les opérations de longue durée. L’appareil envoie des informations de progression à l’aide de messages de télémétrie. Ces messages de progression ont les propriétés d’en-tête suivantes :

- `iothub-command-name` : nom de la commande, par exemple `UpdateFirmware`.
- `iothub-command-request-id` : ID de la demande généré côté serveur et envoyé à l’appareil lors de l’appel initial.
- `iothub-interface-id`:  ID de l’interface sur laquelle cette commande est définie, par exemple `urn:example:AssetTracker:1`.
 `iothub-interface-name` : nom de l’instance de cette interface, par exemple `myAssetTracker`.
- `iothub-command-statuscode` : code d’état retourné par l’appareil, par exemple `202`.

## <a name="register-a-device"></a>Inscrire un appareil

IoT Plug-and-Play facilite la publication des fonctionnalités de votre appareil. Avec IoT Plug-and-Play, une fois que votre appareil se connecte à IoT Hub, vous devez inscrire votre modèle de capacité d’appareil. L’inscription permet aux clients d’utiliser les fonctionnalités IoT Plug-and-Play de votre appareil.

Ce guide vous montre comment inscrire un appareil à l’aide d’Azure IoT device SDK pour C.

Pour chaque interface que votre appareil implémente, vous devez créer une interface et la connecter à son implémentation.

Concernant l’interface de thermostat indiquée plus haut, à l’aide du kit SDK C, vous la créez et la connectez à son implémentation :

```c
DIGITALTWIN_INTERFACE_HANDLE thermostatInterfaceHandle;

DIGITALTWIN_CLIENT_RESULT result = DigitalTwin_InterfaceClient_Create(
    "thermostat",
    "urn:example:Thermostat:1",
    null, null,
    &thermostatInterfaceHandle);

result = DigitalTwin_Interface_SetCommandsCallbacks(
    thermostatInterfaceHandle,
    commandsCallbackTable);

result = DigitalTwin_Interface_SetPropertiesUpdatedCallbacks(
    thermostatInterfaceHandle,
    propertiesCallbackTable);

```

Répétez ce code pour chaque interface implémentée par votre appareil.

Après avoir créé une interface, inscrivez le modèle de capacité d’appareil et les interfaces auprès de votre hub IoT :

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaces, 2,
    null, null);
```

## <a name="use-a-device"></a>Utiliser un appareil

IoT Plug-and-Play vous permet d’utiliser des appareils qui ont inscrit leurs fonctionnalités auprès de votre hub IoT. Par exemple, vous pouvez accéder directement aux propriétés et aux commandes d’un appareil.

Pour utiliser un appareil IoT Plug-and-Play connecté à votre hub IoT, utilisez l’API REST IoT Hub ou l’un des kits SDK de langage IoT. Les exemples suivants utilisent l’API REST IoT Hub. La version actuelle de cet API est `2019-07-01-preview`. Ajoutez `?api-version=2019-07-01-preview` à vos appels de l'API REST.

Pour obtenir la valeur d’une propriété d’appareil, telle que la version du microprogramme (`fwVersion`) dans l’interface `DeviceInformation` du thermostat, vous utilisez l’API REST Digital Twins.

Si votre thermostat s'appelle `t-123`, vous pouvez obtenir toutes les propriétés des différentes interfaces implémentées par votre appareil à l'aide d'un appel GET de l'API REST :

```REST
GET /digitalTwins/t-123/interfaces
```

Plus généralement, toutes les propriétés des différentes interfaces sont accessibles à l'aide de ce modèle d'API REST, où `{device-id}` est l'identificateur de l'appareil :

```REST
GET /digitalTwins/{device-id}/interfaces
```

Si vous connaissez le nom de l'interface, par exemple `deviceInformation`, et souhaitez obtenir les propriétés de celle-ci, définissez l'étendue de la demande sur cette interface en indiquant le nom de cette dernière :

```REST
GET /digitalTwins/t-123/interfaces/deviceInformation
```

Plus généralement, les propriétés d’une interface spécifique sont accessibles via ce modèle d’API REST, où `device-id` est l’identificateur de l’appareil et `{interface-name}` le nom de l’interface :

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

Vous pouvez appeler les commandes d’appareil IoT Plug-and-Play directement. Si l’interface `Thermostat` de l’appareil `t-123` a une commande `restart`, vous pouvez l’appeler avec un appel POST de l’API REST :

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

Plus généralement, les commandes peuvent être appelées à l’aide de ce modèle d’API REST :

- `device-id` : identificateur de l’appareil.
- `interface-name` : nom de l’interface dans la section implements du modèle de capacité d’appareil.
- `command-name` : nom de la commande.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert la modélisation d’appareil, voici quelques ressources supplémentaires :

- [Langage de définition de jumeau numérique (DTDL)](https://aka.ms/DTDL)
- [SDK d’appareils C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [API REST IoT](https://docs.microsoft.com/rest/api/iothub/device)
