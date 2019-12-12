---
title: Interagir avec un appareil IoT Plug-and-Play (préversion) à partir d’une solution Azure IoT | Microsoft Docs
description: Avec ce tutoriel destiné aux développeurs de solutions, vous allez découvrir comment utiliser le SDK de service pour interagir avec des appareils IoT Plug-and-Play.
author: Philmea
ms.author: philmea
ms.date: 07/24/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 543f332087aef4147c9274ca980cb56543a68112
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977588"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>Connectez-vous à un appareil IoT Plug-and-Play (préversion) et interagissez avec celui-ci

Ce guide pratique vous explique comment utiliser les exemples du SDK de service Node, qui montrent comment votre solution IoT peut interagir avec les appareils IoT Plug-and-Play (préversion).

Si vous n’avez pas suivi le guide de démarrage rapide [Connecter un appareil IoT Plug-and-Play à votre solution](quickstart-connect-pnp-device-solution.md), nous vous recommandons de le faire dès à présent. Ce guide de démarrage rapide vous montre comment télécharger et installer le SDK et exécuter certains exemples.

Avant d’exécuter les exemples de service, ouvrez un nouveau terminal, accédez au dossier racine de votre référentiel cloné, accédez au dossier **digitaltwins/quickstarts/service**, puis exécutez la commande suivante pour installer les dépendances :

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>Exécuter les exemples de service

Utilisez les exemples suivants pour explorer les fonctionnalités du SDK de service Node.js. Assurez-vous que la variable d’environnement `IOTHUB_CONNECTION_STRING` est définie dans l’interpréteur de commandes que vous utilisez :

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Récupérer un jumeau numérique et lister les interfaces

**get_digital_twin.js** obtient le jumeau numérique associé à votre appareil et affiche son composant dans la ligne de commande. Il n’est pas nécessaire qu’un exemple d’appareil soit en cours d’exécution pour qu’il réussisse.

**get_digital_twin_interface_instance.js** obtient une instance d’interface unique du jumeau numérique associé à votre appareil et l’affiche dans la ligne de commande. L’exécution de l’exemple d’appareil n’est pas nécessaire.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Récupérer et définir des propriétés à l’aide du SDK de service Node

**update_digital_twin.js** met à jour une propriété accessible en écriture sur le jumeau numérique de l’appareil à l’aide d’un correctif complet. Vous pouvez mettre à jour plusieurs propriétés sur plusieurs interfaces si vous le souhaitez. Il est nécessaire que l’exemple d’appareil soit en cours d’exécution pour qu’il réussisse. En cas de réussite, l’exemple d’appareil affiche des informations sur la mise à jour d’une propriété et l’exemple de service affiche un jumeau numérique mis à jour dans le terminal.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Envoyer une commande et récupérer la réponse à l’aide du SDK de service Node

**invoke_command.js** appelle une commande synchrone sur le jumeau numérique de l’appareil. Il est nécessaire que l’exemple d’appareil soit en cours d’exécution pour qu’il réussisse. En cas de réussite, l’exemple d’appareil affiche des informations sur la reconnaissance de la commande et le client de service affiche le résultat de la commande dans le terminal.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Se connecter au référentiel public et récupérer une définition de modèle à l’aide du SDK de service Node

Vous devez définir la variable d’environnement suivante en suivant les mêmes instructions que celles fournies pour les exemples de service et d’appareil :

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

Vous trouverez cette chaîne de connexion dans le [portail Microsoft Azure Certified pour IoT](https://preview.catalog.azureiotsolutions.com), sous l’onglet **Chaînes de connexion**, pour votre **référentiel d’entreprise**.

La chaîne de connexion ressemble à l’exemple suivant :

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

Une fois que vous avez défini ces quatre variables d’environnement, exécutez l’exemple de la même façon que les exemples précédents :

```cmd/sh
node model_repo.js
```

Cet exemple télécharge l’interface **ModelDiscovery** et affiche ce modèle dans le terminal.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Exécuter des requêtes dans IoT Hub en fonction des modèles de capacité et des interfaces

Le langage de requête IoT Hub prend en charge `HAS_INTERFACE` et `HAS_CAPABILITYMODEL` comme indiqué dans les exemples suivants :

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Créer des routes de jumeau numérique

Votre solution peut recevoir des notifications d’événements de changement de jumeau numérique. Pour vous abonner à ces notifications, utilisez la [fonctionnalité de routage d’IoT Hub](../iot-hub/iot-hub-devguide-endpoints.md) pour envoyer les notifications à un point de terminaison tel que le stockage Blob, Event Hubs ou une file d’attente Service bus.

Pour créer une route de jumeau numérique :

1. Dans le portail Azure, accédez à votre ressource IoT Hub.
1. Sélectionnez **Routage des messages**.
1. Sous l’onglet **Routes**, sélectionnez **Ajouter**.
1. Entrez une valeur dans le champ **Nom** et choisissez un **point de terminaison**. Si vous n’avez pas configuré de point de terminaison, sélectionnez **Ajouter un point de terminaison**.
1. Dans la liste déroulante **Source de données**, sélectionnez **Événements de changement de jumeau numérique**.
1. Sélectionnez **Enregistrer**.

Le code JSON suivant offre un exemple d’événement de changement de jumeau numérique :

```json
{
  "interfaces": {
    "urn_azureiot_ModelDiscovery_DigitalTwin": {
      "name": "urn_azureiot_ModelDiscovery_DigitalTwin",
      "properties": {
        "modelInformation": {
          "reported": {
            "value": {
              "modelId": "urn:domain:capabilitymodel:TestCapability:1",
              "interfaces": {
                "MyInterfaceFoo": "urn:domain:interfaces:FooInterface:1",
                "urn_azureiot_ModelDiscovery_DigitalTwin": "urn:azureiot:ModelDiscovery:DigitalTwin:1"
              }
            }
          }
        }
      }
    },
    "MyInterfaceFoo": {
      "name": "MyInterfaceFoo",
      "properties": {
        "property_1": { "desired": { "value": "value_1" } },
        "property_2": {
          "desired": { "value": 20 },
          "reported": {
            "value": 10,
            "desiredState": {
              "code": 200,
              "version": 22,
              "subCode": 400,
              "description": ""
            }
          }
        },
        "property_3": { "reported": { "value": "value_3" } }
      }
    }
  },
  "version": 4
}
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous en savez plus sur les solutions de service interagissant avec vos appareils IoT Plug-and-Play, nous vous recommandons d’en apprendre davantage sur la [découverte de modèles](concepts-model-discovery.md).
