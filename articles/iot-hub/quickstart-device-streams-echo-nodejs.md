---
title: Communiquer avec une application d’appareil dans Node.js par le biais de flux d’appareil IoT Hub (préversion) | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous allez exécuter une application côté service Node.js qui communique avec un appareil IoT par le biais d’un flux d’appareil.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 9a123c35620cd82059eb08d370939761f7c2fe69
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834042"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Démarrage rapide : Communiquer avec une application d’appareil dans Node.js par le biais de flux d’appareil IoT Hub (préversion)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT Hub prend actuellement en charge les flux d’appareil en tant que [fonctionnalité d’évaluation](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les [flux d’appareil IoT Hub](./iot-hub-device-streams-overview.md) permettent aux applications de service et d’appareil de communiquer de manière sécurisée à travers des pare-feux. Dans la préversion publique, le SDK Node.js prend uniquement en charge des flux d’appareil côté service. Par conséquent, le présent guide de démarrage rapide couvre uniquement les instructions permettant d’exécuter l’application côté service. Vous devez exécuter une application côté appareil complémentaire à partir d’un des guides de démarrage rapide suivants :

* [Communiquer avec des applications d’appareil en C via les flux d'appareils IoT Hub](./quickstart-device-streams-echo-c.md)

* [Communiquer avec des applications d’appareil en C# via les flux d'appareils IoT Hub](./quickstart-device-streams-echo-csharp.md).

L’application Node.js côté service utilisée dans ce guide de démarrage rapide présente les fonctionnalités suivantes :

* Crée un flux d’appareil vers un appareil IoT.

* Lit une entrée à partir de la ligne de commande et l’envoie à l’application d’appareil, qui la renvoie à son tour.

Le code illustre le processus de lancement d’un flux d’appareil ainsi que la façon de l’utiliser pour envoyer et recevoir des données.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour le moment, la préversion des flux d’appareil est uniquement prise en charge pour les hubs IoT créés dans les régions suivantes :

*  **USA Centre**

*  **EUAP USA Centre**

Pour exécuter l’application côté service dans le cadre du présent guide de démarrage rapide, vous avez besoin de Node.js version 10.x.x ou ultérieure sur votre ordinateur de développement.

Vous pouvez télécharger Node.js pour plusieurs plateformes sur [Nodejs.org](https://nodejs.org).

Vous pouvez vérifier la version actuelle de Node.js sur votre machine de développement à l’aide de la commande suivante :

```
node --version
```

Exécutez la commande suivante afin d’ajouter l’extension Microsoft Azure IoT pour Azure CLI à votre instance Cloud Shell. L’extension IoT ajoute des commandes IoT Hub, IoT Edge et IoT Device Provisioning Service (DPS) à Azure CLI.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Si ce n’est déjà fait, téléchargez l’exemple de projet Node.js à partir de https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip et extrayez l’archive ZIP.

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Si vous avez terminé le précédent [Démarrage rapide : Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-node.md), vous pouvez ignorer cette étape.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Inscrire un appareil

Si vous avez terminé le précédent [Démarrage rapide : Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-node.md), vous pouvez ignorer cette étape.

Un appareil doit être inscrit dans votre hub IoT pour pouvoir se connecter. Dans ce démarrage rapide, vous utilisez Azure Cloud Shell pour inscrire un appareil simulé.

1. Exécutez les commandes suivantes dans Azure Cloud Shell pour créer l’identité d’appareil.

   **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

   **MyDevice** : il s’agit du nom donné à l’appareil inscrit. Utilisez MyDevice comme indiqué. Si vous choisissez un autre nom pour votre appareil, vous devez également utiliser ce nom pour l’ensemble de cet article et mettre à jour le nom de l’appareil dans les exemples d’application avant de les exécuter.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Vous avez également besoin d’une *chaîne de connexion de service* pour activer l’application back-end afin de vous connecter à votre IoT Hub et récupérer les messages. La commande suivante récupère la chaîne de connexion de service correspondant à votre hub IoT :

    **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Notez la valeur retournée, qui ressemble à ceci :

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Communiquer entre appareil et service par le biais de flux d’appareil

Dans cette section, vous exécutez l’application côté appareil et l’application côté service et communiquez entre les deux.

### <a name="run-the-device-side-application"></a>Exécuter l’application côté appareil

Comme mentionné précédemment, le SDK Node.js IoT Hub prend uniquement en charge des flux d’appareil côté service. Pour l’application côté serveur, utilisez les programmes d’appareil complémentaires, disponibles dans l’un de ces guides de démarrage rapide :

   * [Communiquer avec des applications d’appareil en C via les flux d'appareils IoT Hub](./quickstart-device-streams-echo-c.md)

   * [Communiquer avec des applications d’appareil en C# via les flux d'appareils IoT Hub](./quickstart-device-streams-echo-csharp.md)

Vérifiez que l’application côté appareil est en cours d’exécution avant de passer à l’étape suivante.

### <a name="run-the-service-side-application"></a>Exécuter l’application côté service

Si l’application côté appareil est en cours d’exécution, suivez les étapes ci-dessous pour exécuter l’application côté service dans Node.js :

* Indiquez les informations d’identification de votre service et l’ID de l’appareil sous forme de variables d’environnement.
 
   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Remplacez `MyDevice` par l’ID d’appareil que vous avez choisi pour votre appareil.

* Accédez à `Quickstarts/device-streams-service` dans votre dossier de projet décompressé, puis exécutez l’exemple en utilisant Node.

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

À la fin de la dernière étape, le programme côté service lance un flux à votre appareil et, une fois établi, envoie un tampon de chaîne au service sur le flux. Dans cet exemple, le programme côté service lit simplement l’`stdin` sur le terminal et l’envoie à l’appareil, qui la renvoie à son tour. Cet exemple montre une communication bidirectionnelle réussie entre les deux applications.

![Sortie de la console côté service](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Vous pouvez ensuite mettre fin au programme en appuyant de nouveau sur Entrée.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez configuré un hub IoT, inscrit un appareil, établi un flux d’appareil entre des applications côté appareil et service, puis utilisé le flux pour envoyer des données dans les deux sens entre les applications.

Utilisez les liens ci-dessous pour en savoir plus sur les flux d’appareil :

> [!div class="nextstepaction"]
> [Vue d’ensemble des flux d’appareil](./iot-hub-device-streams-overview.md)