---
title: Démarrage rapide Node.js sur les flux d’appareil Azure IoT Hub pour SSH et RDP (préversion) | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous exécutez un exemple d’application Node.js qui joue le rôle de proxy pour activer des scénarios SSH et RDP sur des flux d’appareil IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 1d77f3a10e286246e23ae522e95aa23c487cca2f
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735109"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Démarrage rapide : Activer SSH et RDP sur un flux d’appareil IoT Hub à l’aide d’une application de proxy Node.js (préversion)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub prend actuellement en charge les flux d’appareil en tant que [fonctionnalité d’évaluation](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les [flux d’appareil IoT Hub](./iot-hub-device-streams-overview.md) permettent aux applications de service et d’appareil de communiquer de manière sécurisée à travers des pare-feux. 

Ce guide de démarrage rapide décrit l’exécution d’une application de proxy Node.js côté service pour permettre d’envoyer du trafic Secure Shell (SSH) et de protocole RDP (Remote Desktop Protocol) à un appareil par le biais d’un flux d’appareil. Pour obtenir une vue d’ensemble de la configuration, consultez [lExemple de proxy local](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp). 

Dans la préversion publique, le SDK Node.js prend uniquement en charge des flux d’appareil côté service. Ainsi, le présent guide de démarrage rapide aborde uniquement les instructions permettant d’exécuter l’application de proxy locale de service. Pour exécuter l’application de proxy locale d’appareil, consultez :  

   * [Activer SSH et RDP sur des flux d’appareil IoT Hub à l’aide d’une application de proxy C](./quickstart-device-streams-proxy-c.md)
   * [Activer SSH et RDP sur des flux d’appareil IoT Hub à l’aide d’une application de proxy C#](./quickstart-device-streams-proxy-csharp.md)

Cet article décrit la configuration pour SSH (à l’aide du port 22), puis la manière de modifier la configuration pour RDP (qui utilise le port 3389). Étant donné que les flux d’appareil ne dépendent pas des applications et des protocoles, vous pouvez modifier ce même exemple pour prendre en charge d’autres types de trafic d’application client-serveur, généralement en modifiant le port de communication.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Pour le moment, la préversion des flux d’appareil est uniquement prise en charge pour les hubs IoT créés dans les régions suivantes :

  * USA Centre
  * EUAP USA Centre

* Pour exécuter l’application locale de service dans le cadre du présent démarrage rapide, vous avez besoin de Node.js version 10.x.x ou ultérieure sur votre ordinateur de développement.
  * Téléchargez [Node.js](https://nodejs.org) pour plusieurs plateformes.
  * Vérifiez la version actuelle de Node.js sur votre machine de développement à l’aide de la commande suivante :

   ```
   node --version
   ```

* Ajoutez l’extension Azure IoT pour Azure CLI à votre instance Cloud Shell en exécutant la commande suivante. L’extension IoT ajoute des commandes IoT Hub, IoT Edge et IoT Device Provisioning Service (DPS) à Azure CLI.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* Si ce n’est déjà fait, [téléchargez l’exemple de projet Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip) et extrayez l’archive ZIP.

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Si vous avez terminé le précédent [Démarrage rapide : Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-node.md), vous pouvez ignorer cette étape.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Inscrire un appareil

Si vous avez terminé [Démarrage rapide : Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-node.md), vous pouvez ignorer cette étape.

Un appareil doit être inscrit dans votre hub IoT pour pouvoir se connecter. Dans cette section, vous utilisez Azure Cloud Shell pour inscrire un appareil simulé.

1. Pour créer l’identité d’appareil, exécutez la commande suivante dans Cloud Shell :

   > [!NOTE]
   > * Remplacez l’espace réservé *YourIoTHubName* par le nom que vous avez choisi pour votre hub IoT.
   > * Utilisez *MyDevice* comme indiqué. Il s’agit du nom donné à l’appareil inscrit. Si vous choisissez un autre nom pour votre appareil, utilisez-le pour l’ensemble de cet article et mettez à jour le nom de l’appareil dans les exemples d’application avant de les exécuter.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Pour activer l’application back-end afin de vous connecter à votre hub IoT et récupérer les messages, vous avez également besoin d’une *chaîne de connexion de service*. La commande suivante récupère la chaîne de votre hub IoT :

   > [!NOTE]
   > Remplacez l’espace réservé *YourIoTHubName* par le nom que vous avez choisi pour votre hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Notez la valeur renvoyée pour l’utiliser plus tard dans le cadre du présent démarrage rapide. Cela ressemble à l’exemple suivant :

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Liaison SSH à un appareil par le biais de flux d’appareil

Dans cette section, vous allez établir un flux de bout en bout pour tunneliser le trafic SSH.

### <a name="run-the-device-local-proxy-application"></a>Exécuter l’application proxy locale de l’appareil

Comme mentionné précédemment, le SDK Node.js IoT Hub prend uniquement en charge des flux d’appareil côté service. Pour l’application locale d’appareil, utilisez une application de proxy d’appareil disponible dans un des guides de démarrage rapide suivants :

   * [Activer SSH et RDP sur des flux d’appareil IoT Hub à l’aide d’une application de proxy C](./quickstart-device-streams-proxy-c.md)
   * [Activer SSH et RDP sur des flux d’appareil IoT Hub à l’aide d’une application de proxy C#](./quickstart-device-streams-proxy-csharp.md) 

Avant de passer à l’étape suivante, vérifiez que l’application de proxy locale d’appareil est en cours d’exécution.

### <a name="run-the-service-local-proxy-application"></a>Exécuter l’application proxy locale du service

Avec l’application de proxy locale d’appareil en cours d’exécution, exécutez l’application de proxy locale de service écrite en Node.js en effectuant les étapes suivantes :

1. Pour les variables d’environnement, fournissez les informations d’identification de votre service, l’ID de l’appareil cible sur lequel s’exécute le démon SSH et le numéro de port du proxy en cours d’exécution sur l’appareil.

   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
   export STREAMING_TARGET_DEVICE="MyDevice"
   export PROXY_PORT=2222

   # In Windows
   SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
   SET STREAMING_TARGET_DEVICE=MyDevice
   SET PROXY_PORT=2222
   ```

   Remplacez les valeurs précédentes par votre ID d’appareil et votre chaîne de connexion.

1. Accédez au répertoire *Quickstarts/device-streams-service* de votre dossier de projet décompressé, puis exécutez l’application de proxy locale de service.

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>Établir une liaison SSH à votre appareil par le biais de flux d’appareil

Sur Linux, exécutez SSH en utilisant `ssh $USER@localhost -p 2222` sur un terminal. Sur Windows, utilisez le client SSH de votre choix (par exemple, PuTTY).

Sortie de console au niveau local du service une fois la session SSH établie (l’application de proxy locale de service écoute le port 2222) :

![Sortie de terminal SSH](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Sortie de console de l’application cliente SSH (le client SSH communique avec le démon SSH en se connectant au port 22 qu’écoute l’application de proxy locale de service) :

![Sortie de client SSH](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>Établir une liaison RDP à votre appareil par le biais de flux d’appareil

Utilisez maintenant votre application cliente RDP pour vous connecter au proxy de service sur le port 2222, un port arbitraire que vous avez choisi préalablement.

> [!NOTE]
> Vérifiez que votre proxy d’appareil est correctement configuré pour RDP avec le port RDP 3389.

![Le client RDP se connecte à l’application de proxy locale de service](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez configuré un hub IoT, inscrit un appareil et déployé une application de proxy de service pour autoriser le trafic RDP et SSH sur un appareil IoT. Le trafic RDP et SSH est traité par tunnel par le biais d’un flux d’appareil via le hub IoT. Ainsi, la nécessité d’une connectivité directe à l’appareil est supprimée.

Pour en savoir plus sur les flux d’appareil, consultez :

> [!div class="nextstepaction"]
> [Vue d’ensemble des flux d’appareil](./iot-hub-device-streams-overview.md)
