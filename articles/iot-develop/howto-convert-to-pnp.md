---
title: Convertir un appareil existant pour utiliser IoT Plug-and-Play | Microsoft Docs
description: Cet article explique comment convertir votre code d’appareil existant pour qu’il fonctionne avec IoT Plug-and-Play en créant un modèle d’appareil et en envoyant l’ID de modèle quand l’appareil se connecte.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2021
ms.topic: how-to
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 225b999053824513e41d29d80a47a596fdedf991
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442566"
---
# <a name="how-to-convert-an-existing-device-to-be-an-iot-plug-and-play-device"></a>Guide pratique pour convertir un appareil existant en appareil IoT Plug-and-Play

Cet article décrit les étapes à effectuer pour convertir un appareil existant en appareil IoT Plug-and-Play. Il explique comment créer le modèle exigé par chaque appareil IoT Plug-and-Play, ainsi que les changements de code nécessaires pour permettre à l’appareil de fonctionner en tant qu’appareil IoT Plug-and-Play.

Pour les exemples de code, cet article montre le code C qui utilise une bibliothèque MQTT pour se connecter à un hub IoT. Vous pouvez appliquer les changements décrits dans cet article aux appareils implémentés avec d’autres langages et SDK.

Pour convertir votre appareil existant en appareil IoT Plug-and-Play :

1. Passez en revue le code de votre appareil pour comprendre la télémétrie, les propriétés et les commandes qu’il implémente.
1. Créez un modèle qui décrit la télémétrie, les propriétés et les commandes implémentées par votre appareil.
1. Modifiez le code de l’appareil pour annoncer l’ID du modèle quand il se connecte à votre service.

## <a name="review-your-device-code"></a>Passer en revue le code de votre appareil

Avant de créer un modèle pour votre appareil, vous devez comprendre les capacités existantes de votre appareil :

- La télémétrie que l’appareil envoie régulièrement.
- Les propriétés en lecture seule et accessibles en écriture que l’appareil synchronise avec votre service.
- Les commandes appelées à partir du service auquel l’appareil répond.

Par exemple, examinez les extraits de code d’appareil suivants qui implémentent différentes capacités d’appareil. Ces exemples sont basés sur l’exemple se trouvant dans [PnPMQTTWin32-Before](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32-Before) :

L’extrait de code suivant montre un appareil envoyant la télémétrie de température :

```c
#define TOPIC "devices/" DEVICEID "/messages/events/"

// ...

void Thermostat_SendCurrentTemperature()
{
  char msg[] = "{\"temperature\":25.6}";
  int msgId = rand();
  int rc = mosquitto_publish(mosq, &msgId, TOPIC, sizeof(msg) - 1, msg, 1, true);
  if (rc != MOSQ_ERR_SUCCESS)
  {
    printf("Error: %s\r\n", mosquitto_strerror(rc));
  }
}
```

Le nom du champ de télémétrie est `temperature` et son type est float ou double. La définition de modèle pour ce type de télémétrie ressemble au code JSON suivant. Pour en savoir plus, consultez la section [Concevoir un modèle](#design-a-model) ci-dessous :

```json
{
  "@type": [
    "Telemetry"
  ],
  "name": "temperature",
  "displayName": "Temperature",
  "description": "Temperature in degrees Celsius.",
  "schema": "double"
}
```

L’extrait de code suivant montre l’appareil signalant une valeur de propriété :

```c
#define DEVICETWIN_MESSAGE_PATCH "$iothub/twin/PATCH/properties/reported/?$rid=patch_temp"

static void SendMaxTemperatureSinceReboot()
{
  char msg[] = "{\"maxTempSinceLastReboot\": 42.500}";
  int msgId = rand();
  int rc = mosquitto_publish(mosq, &msgId, DEVICETWIN_MESSAGE_PATCH, sizeof(msg) - 1, msg, 1, true);
  if (rc != MOSQ_ERR_SUCCESS)
  {
    printf("Error: %s\r\n", mosquitto_strerror(rc));
  }
}
```

Le nom de la propriété est `maxTempSinceLastReboot` et son type est float ou double. Cette propriété est signalée par l’appareil. L’appareil ne reçoit jamais de mise à jour en provenance du service pour cette valeur. La définition de modèle pour cette propriété ressemble au code JSON suivant. Pour en savoir plus, consultez la section [Concevoir un modèle](#design-a-model) ci-dessous :

```json
{
  "@type": [
    "Property"
  ],
  "name": "maxTempSinceLastReboot",
  "schema": "double",
  "displayName": "Max temperature since last reboot.",
  "description": "Returns the max temperature since last device reboot."
}
```

L’extrait de code suivant montre l’appareil qui répond aux messages du service :

```c
void message_callback(struct mosquitto* mosq, void* obj, const struct mosquitto_message* message)
{
  printf("Message received: %s payload: %s \r\n", message->topic, (char*)message->payload);
  
  if (strncmp(message->topic, "$iothub/methods/POST/getMaxMinReport/?$rid=1",37) == 0)
  {
    char* pch;
    char* context;
    int msgId = 0;
    pch = strtok_s((char*)message->topic, "=",&context);
    while (pch != NULL)
    {
      pch = strtok_s(NULL, "=", &context);
      if (pch != NULL) {
        char * pEnd;
        msgId = strtol(pch,&pEnd,16 );
      }
    }
    char topic[64];
    sprintf_s(topic, "$iothub/methods/res/200/?$rid=%d", msgId);
    char msg[] = "{\"maxTemp\":83.51,\"minTemp\":77.68}";
    int rc = mosquitto_publish(mosq, &msgId, topic, sizeof(msg) - 1, msg, 1, true);
    if (rc != MOSQ_ERR_SUCCESS)
    {
      printf("Error: %s\r\n", mosquitto_strerror(rc));
    }
    delete pch;
  }

  if (strncmp(message->topic, "$iothub/twin/PATCH/properties/desired/?$version=1", 38) == 0)
  {
    char* pch;
    char* context;
    int version = 0; 
    pch = strtok_s((char*)message->topic, "=", &context);
    while (pch != NULL)
    {
      pch = strtok_s(NULL, "=", &context);
      if (pch != NULL) {
        char* pEnd;
        version = strtol(pch, &pEnd, 10);
      }
    }
    // To do: Parse payload and extract target value
    char msg[128];
    int value = 46;
    sprintf_s(msg, "{\"targetTemperature\":{\"value\":%d,\"ac\":200,\"av\":%d,\"ad\":\"success\"}}", value, version);
    int rc = mosquitto_publish(mosq, &version, DEVICETWIN_MESSAGE_PATCH, strlen(msg), msg, 1, true);
    if (rc != MOSQ_ERR_SUCCESS)
    {
      printf("Error: %s\r\n", mosquitto_strerror(rc));
    }
    delete pch;
  }
}
```

La rubrique `$iothub/methods/POST/getMaxMinReport/` reçoit du service une demande pour la commande appelée `getMaxMinReport`. Cette demande peut inclure une charge utile avec des paramètres de commande. L’appareil envoie une réponse avec une charge utile qui comprend les valeurs `maxTemp` et `minTemp`.

La rubrique `$iothub/twin/PATCH/properties/desired/` reçoit du service des mises à jour de propriétés. Cet exemple suppose que la mise à jour de propriété est destinée à la propriété `targetTemperature`. Il répond avec un accusé de réception ressemblant à `{\"targetTemperature\":{\"value\":46,\"ac\":200,\"av\":12,\"ad\":\"success\"}}`.

En résumé, l’exemple implémente les capacités suivantes :

| Name                   | Type de fonctionnalité   | Détails |
| ---------------------- | ----------------- | ------- |
| température            | Télémétrie         | Supposer que le type de données est double |
| maxTempSinceLastReboot | Propriété          | Supposer que le type de données est double |
| targetTemperature      | Propriété accessible en écriture | Le type de données est integer |
| getMaxMinReport        | Commande           | Retourne du code JSON avec les champs `maxTemp` et `minTemp` de type double |

## <a name="design-a-model"></a>Concevoir un modèle

Chaque appareil IoT Plug-and-Play a un modèle qui décrit les fonctionnalités et capacités de l’appareil. Le modèle utilise le [langage DTDL (Digital Twin Definition Language) ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) pour décrire les capacités de l’appareil.

Pour un modèle simple qui mappe les capacités existantes de votre appareil, utilisez les éléments *Télémétrie*, *Propriété* et *Commande* du langage DTDL.

Un modèle DTDL utilisé pour l’exemple décrit dans la section précédente ressemble à l’exemple suivant :

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:ConvertSample;1",
  "@type": "Interface",
  "displayName": "Simple device",
  "description": "Example that shows model for simple device converted to act as an IoT Plug and Play device.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max and min temperature.",
      "request": {
      },
      "response": {
        "name": "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            }
          ]
        }
      }
    }
  ]
}
```

Dans ce modèle :

- Les valeurs `name` et `schema` correspondent aux données que l’appareil envoie et reçoit.
- Toutes les capacités sont regroupées dans une seule interface.
- Les champs `@type` identifient les types DTDL, comme **Propriété** et **Commande**.
- Les champs comme `unit`, `displayName` et `description` fournissent des informations supplémentaires que le service doit utiliser. Par exemple, IoT Central utilise ces valeurs quand il affiche des données sur les tableaux de bord des appareils.

Pour en savoir plus, consultez [Conventions IoT Plug-and-Play](concepts-convention.md) et [Guide de modélisation d’IoT Plug-and-Play](concepts-modeling-guide.md).

## <a name="update-the-code"></a>Mettez à jour le code

Si votre appareil utilise déjà IoT Hub ou IoT Central, vous n’avez pas besoin d’apporter des changements à l’implémentation de ses capacités de télémétrie, de propriété et de commande. Pour que l’appareil suive les conventions IoT Plug-and-Play, modifiez la façon dont l’appareil se connecte à votre service afin qu’il annonce l’ID du modèle que vous avez créé. Le service peut ensuite utiliser le modèle pour comprendre les capacités de l’appareil. Par exemple, IoT Central peut utiliser l’ID de modèle pour récupérer automatiquement le modèle à partir d’un dépôt et générer un modèle d’appareil pour votre appareil.

Les appareils IoT se connectent à votre service IoT par le biais du Service de provisionnement des appareils (DPS) ou directement avec une chaîne de connexion.

Si votre appareil utilise le service DPS pour se connecter, incluez l’ID de modèle dans la charge utile que vous envoyez quand vous inscrivez l’appareil. Pour l’exemple de modèle indiqué précédemment, la charge utile ressemble à ceci :

```json
{
  "modelId" : "dtmi:com:example:ConvertSample;1"
}
```

Pour plus d’informations, consultez [Inscription du runtime - Inscrire un appareil](/rest/api/iot-dps/device/runtime-registration/register-device).

Si votre appareil utilise le service DPS pour se connecter ou se connecte directement à une chaîne de connexion, incluez l’ID du modèle quand votre code se connecte à IoT Hub. Par exemple :

```c
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-09-30&model-id=dtmi:com:example:ConvertSample;1"

// ...

mosquitto_username_pw_set(mosq, USERNAME, PWD);

// ...

rc = mosquitto_connect(mosq, HOST, PORT, 10);
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment convertir un appareil existant en appareil IoT Plug-and-Play, nous vous suggérons de lire le [Guide de modélisation IoT Plug-and-Play](concepts-modeling-guide.md).
