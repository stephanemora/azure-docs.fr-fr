---
title: Démarrage rapide Node.js sur les flux d’appareil Azure IoT Hub pour SSH/RDP (préversion) | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous allez exécuter un exemple d’application Node.js qui joue le rôle d’un proxy pour activer des scénarios SSH/RDP sur des flux d’appareil IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: c4f994638420819da41a355d679cb03785b94a3a
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65832317"
---
# <a name="quickstart-sshrdp-over-an-iot-hub-device-stream-using-a-nodejs-proxy-application-preview"></a>Démarrage rapide : SSH/RDP sur un flux d'appareil IoT Hub à l'aide d'une application proxy Node.js (préversion)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub prend actuellement en charge les flux d’appareil en tant que [fonctionnalité d’évaluation](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les [flux d’appareil IoT Hub](./iot-hub-device-streams-overview.md) permettent aux applications de service et d’appareil de communiquer de manière sécurisée à travers des pare-feux. Ce guide de démarrage rapide décrit l’exécution d’une application proxy Node.js côté service pour permettre l’envoi de trafic SSH et RDP à un appareil par le biais d’un flux d’appareil. Consultez [Exemple de proxy local pour SSH ou RDP](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) pour avoir un aperçu de la configuration. Dans la préversion publique, le SDK Node.js prend uniquement en charge des flux d’appareil côté service. Par conséquent, le présent guide de démarrage rapide couvre uniquement les instructions permettant d’exécuter le proxy local de service. Vous devez exécuter l'un des démarrages rapides de proxy local d’appareil suivants :  

   * [SSH/RDP sur des flux d'appareil IoT Hub à l'aide d'une application proxy C](./quickstart-device-streams-proxy-c.md)

   * [SSH/RDP sur des flux d'appareil IoT Hub à l'aide d'une application proxy C#](./quickstart-device-streams-proxy-csharp.md)

Tout d’abord, nous décrivons la configuration pour SSH (qui utilise le port 22). Nous décrivons ensuite comment modifier la configuration pour RDP (qui utilise le port 3389). Étant donné que les flux d’appareil ne dépendent pas des applications et des protocoles, vous pouvez modifier ce même exemple pour prendre en charge d’autres types de trafic d’application client/serveur (généralement en modifiant le port de communication).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Pour le moment, la préversion des flux d’appareil est uniquement prise en charge pour les hubs IoT créés dans les régions suivantes :

   * **USA Centre**

   * **EUAP USA Centre**

Pour exécuter l’application locale de service dans le cadre du présent démarrage rapide, vous avez besoin de Node.js version 10.x.x ou ultérieure sur votre ordinateur de développement.

* Téléchargez Node.js pour plusieurs plateformes sur [nodejs.org](https://nodejs.org).

Vous pouvez vérifier la version actuelle de Node.js sur votre machine de développement à l’aide de la commande suivante :

```
node --version
```

* Exécutez la commande suivante afin d’ajouter l’extension Microsoft Azure IoT pour Azure CLI à votre instance Cloud Shell. L’extension IoT ajoute des commandes IoT Hub, IoT Edge et IoT Device Provisioning Service (DPS) à Azure CLI.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

* Si ce n’est déjà fait, téléchargez l’exemple de projet Node.js à partir de https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip et extrayez l’archive ZIP.

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

## <a name="ssh-to-a-device-via-device-streams"></a>Liaison SSH à un appareil par le biais de flux d’appareil

Dans cette section, vous allez établir un flux de bout en bout pour tunneler le trafic SSH.

### <a name="run-the-device-local-proxy"></a>Exécuter le proxy local d’appareil

Comme mentionné précédemment, le SDK Node.js IoT Hub prend uniquement en charge des flux d’appareil côté service. Pour l’application locale d’appareil, utilisez un des programmes de proxy d’appareil disponibles dans un des guides de démarrage rapide suivants :

   * [SSH/RDP sur des flux d'appareil IoT Hub à l'aide d'applications proxy C](./quickstart-device-streams-proxy-c.md)

   * [SSH/RDP sur des flux d'appareil IoT Hub à l'aide d'applications proxy C#](./quickstart-device-streams-proxy-csharp.md). 

Vérifiez que le proxy local d’appareil est en cours d’exécution avant de passer à l’étape suivante.

### <a name="run-the-service-local-proxy"></a>Exécuter le proxy local de service

Si le [proxy local d’appareil](#run-the-device-local-proxy) est en cours d’exécution, suivez les étapes ci-dessous pour exécuter le proxy local de service écrit dans Node.js.

* Fournissez les informations d’identification de votre service, l’ID de l’appareil cible sur lequel s’exécute le démon SSH et le numéro de port du proxy en cours d’exécution sur l’appareil sous forme de variables d’environnement.

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

  Remplacez les valeurs ci-dessus par votre ID d’appareil et votre chaîne de connexion.

* Accédez à `Quickstarts/device-streams-service` dans votre dossier de projet décompressé et exécutez le proxy local de service.

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

Sortie de console au niveau local du service une fois la session SSH établie (le proxy local du service écoute sur le port 2222) :

![Sortie de terminal SSH](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Sortie de console du programme client SSH (le client SSH communique avec le démon SSH en se connectant au port 22 où le proxy local du service est à l’écoute) :

![Sortie de client SSH](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>Établir une liaison RDP à votre appareil par le biais de flux d’appareil

Utilisez à présent votre programme client RDP pour vous connecter au proxy de service sur le port 2222 (il s’agissait d’un port arbitraire disponible que vous avez choisi précédemment).

> [!NOTE]
> Vérifiez que votre proxy d’appareil est correctement configuré pour RDP avec le port RDP 3389.

![Le client RDP se connecte au proxy local de service](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez configuré un hub IoT, inscrit un appareil et déployé un programme de proxy de service pour autoriser le trafic RDP et SSH vers un appareil IoT. Le trafic RDP et SSH est traité par tunnel par le biais d’un flux d’appareil via IoT Hub. Ainsi, la nécessité d’une connectivité directe à l’appareil est supprimée.

Utilisez les liens ci-dessous pour en savoir plus sur les flux d’appareil :

> [!div class="nextstepaction"]
> [Vue d’ensemble des flux d’appareil](./iot-hub-device-streams-overview.md)
