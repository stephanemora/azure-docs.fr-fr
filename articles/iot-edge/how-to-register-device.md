---
title: Inscrire un nouvel appareil Azure IoT Edge | Microsoft Docs
description: Utiliser l’extension IoT pour Azure CLI afin d’inscrire un nouvel appareil IoT Edge et de récupérer la chaîne de connexion
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2ac6a38d99f588c9dbab7a40bc092aa01b27649a
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726007"
---
# <a name="register-an-azure-iot-edge-device"></a>Inscrire un appareil Azure IoT Edge

Avant d’utiliser vos appareils IoT avec Azure IoT Edge, vous devez les inscrire auprès de votre IoT Hub. Une fois qu’un appareil est inscrit, vous pouvez récupérer une chaîne de connexion afin de le configurer pour des charges de travail IoT Edge.

Vous avez le choix d’inscrire à l’aide de l’un des outils suivants :

* [Inscrivez un appareil dans le Portail Azure](#register-in-the-azure-portal) si vous préférez une interface utilisateur graphique pour créer, afficher et gérer des ressources Azure.
* [Inscrivez un appareil auprès de Visual Studio Code](#register-with-visual-studio-code) si vous préférez gérer les ressources IoT Azure dans le même emplacement que celui dans lequel vous développez vos solutions IoT.
* [Inscrivez un appareil auprès d’Azure CLI](#register-with-the-azure-cli) si vous préférez des outils de ligne de commande pour la gestion des ressources Azure ou si vous souhaitez automatiser des tâches.

## <a name="register-in-the-azure-portal"></a>S’inscrire dans le portail Azure

Vous pouvez effectuer toutes les tâches d’inscription dans le portail Azure.

### <a name="prerequisites-for-the-azure-portal"></a>Conditions préalables pour le portail Azure

Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuit ou standard dans votre abonnement Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Créer un appareil IoT Edge dans le portail Azure

Dans votre IoT Hub dans le portail Azure, les appareils IoT Edge sont créés et gérés séparément des appareils IoT qui ne sont pas compatibles avec la périphérie.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.
2. Dans le volet gauche, dans le menu, sélectionnez **IoT Edge**.
3. Sélectionnez **Ajouter un appareil IoT Edge**.
4. Indiquez un ID d’appareil descriptif. Utilisez les paramètres par défaut pour générer automatiquement les clés d’authentification et connecter le nouvel appareil à votre hub.
5. Sélectionnez **Enregistrer**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Afficher des appareils IoT Edge dans le portail Azure

Tous les appareils compatibles avec Edge qui se connectent à votre hub IoT sont répertoriés dans la page **IoT Edge**.

![Afficher tous les appareils IoT Edge de votre hub IoT](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Récupérer la chaîne de connexion sur le Portail Azure

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT.

1. Dans la page **IoT Edge** du portail, cliquez sur l’ID de l’appareil dans la liste des appareils IoT Edge.
2. Copiez la valeur de **Chaîne de connexion principale** ou **Chaîne de connexion secondaire**.

## <a name="register-with-visual-studio-code"></a>S’inscrire avec Visual Studio Code

Il existe plusieurs façons d’effectuer la plupart des opérations dans VS Code. Cet article utilise l’Explorateur, mais vous pouvez également utiliser la palette de commandes pour exécuter les étapes.

### <a name="prerequisites-for-visual-studio-code"></a>Conditions préalables pour Visual Studio Code

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) dans votre abonnement Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pour Visual Studio Code.

### <a name="sign-in-to-access-your-iot-hub"></a>Se connecter pour accéder à votre hub ioT

Vous pouvez utiliser les extensions Azure IoT pour Visual Studio Code afin d’effectuer des opérations avec votre IoT Hub. Pour que ces opérations fonctionnent, vous devez vous connecter à votre compte Azure, puis sélectionner votre IoT Hub.

1. Dans Visual Studio Code, ouvrez la vue **Explorateur**.
1. Au bas de l’Explorateur, développez la section **Azure IoT Hub**.

   ![Développer la section Appareils Azure IoT Hub](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Cliquez sur **...** dans l’en-tête de section **Azure IoT Hub**. Si vous ne voyez pas les points de suspension, cliquez ou pointez sur l’en-tête.
1. Choisissez **Sélectionner un hub IoT**.
1. Si vous n’êtes pas connecté à votre compte Azure, suivez les invites de connexion.
1. Sélectionnez votre abonnement Azure.
1. Sélectionnez votre hub IoT.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Créer un appareil IoT Edge avec Visual Studio Code

1. Dans l’Explorateur VS Code, développez la section **Appareils Azure IoT Hub**.
1. Cliquez sur **...** dans l’en-tête de section **Appareils Azure IoT Hub**. Si vous ne voyez pas les points de suspension, cliquez ou pointez sur l’en-tête.
1. Sélectionnez **Créer un appareil IoT Edge**.
1. Dans la zone de texte qui s’ouvre, donnez un ID à votre appareil.

Dans l’écran de sortie, vous voyez le résultat de la commande. Les informations de l’appareil apparaissent, notamment l’ID d’appareil (**deviceId**) que vous avez fourni et la chaîne de connexion (**connectionString**) qui vous permet de connecter votre appareil physique à votre hub IoT.

Dans l’écran de sortie, vous voyez le résultat de la commande. Les informations de l’appareil apparaissent, notamment l’ID d’appareil (**deviceId**) que vous avez fourni et la chaîne de connexion (**connectionString**) qui vous permet de connecter votre appareil physique à votre hub IoT.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Afficher des appareils IoT Edge avec Visual Studio Code

Tous les appareils qui se connectent à votre hub IoT sont listés dans la section **Azure IoT Hub** de l’Explorateur Visual Studio Code. Les appareils IoT Edge se distinguent des appareils non Edge par leur icône et par le fait que les modules **$edgeAgent** et **$edgeHub** sont développés sur chacun d’eux.

![Afficher tous les appareils IoT Edge de votre hub IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Récupérer la chaîne de connexion avec Visual Studio Code

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT.

1. Cliquez avec le bouton droit sur l’ID de votre appareil dans la section **Azure IoT Hub**.
1. Sélectionnez **Copier la chaîne de connexion de l’appareil**.

   La chaîne de connexion est copiée dans le Presse-papiers.

Vous pouvez également sélectionner **Obtenir les informations de l’appareil** dans le menu contextuel pour voir les informations de l’appareil, notamment la chaîne de connexion, dans la fenêtre de sortie.

## <a name="register-with-the-azure-cli"></a>S’inscrire avec Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) est un outil en ligne de commande open source et multiplateforme, destiné à la gestion de ressources Azure, telles que IoT Edge. Il vous permet de gérer les ressources Azure IoT Hub, les instances de service Device Provisioning et les hubs liés dès l’installation. L’extension IoT enrichit Azure CLI avec des fonctionnalités telles que la gestion des appareils, et toutes les fonctionnalités de IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Conditions préalables pour Azure CLI

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) dans votre abonnement Azure.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) dans votre environnement. Vous devez utiliser Azure CLI version 2.0.70 (au minimum) ou ultérieure. Utilisez `az --version` pour valider. Cette version prend en charge les commandes d’extension az et introduit l’infrastructure de la commande Knack.
* [Extension IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Créer un appareil IoT Edge avec Azure CLI

Utilisez la commande [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest#ext-azure-iot-az-iot-hub-device-identity-create) pour créer une identité d’appareil dans votre hub IoT. Par exemple :

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Cette commande inclut trois paramètres :

* **device-id** : fournissez un nom descriptif unique à votre hub IoT.
* **hub-name** : indiquez le nom de votre hub IoT.
* **edge-enabled** : ce paramètre déclare que l’appareil est destiné à être utilisé avec IoT Edge.

   ![az iot hub device-identity create output](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Affichez des appareils IoT Edge avec Azure CLI

Utilisez la commande [az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest#ext-azure-iot-az-iot-hub-device-identity-list) pour voir tous les appareils dans votre hub IoT. Par exemple :

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Tout appareil inscrit en tant qu’appareil IoT Edge a la propriété **capabilities.iotEdge** définie sur **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Récupérer la chaîne de connexion avec Azure CLI

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT. Utilisez la commande [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) pour retourner la chaîne de connexion d’un appareil unique :

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

La valeur du paramètre `device-id` respecte la casse. Ne copiez pas les guillemets qui entourent la chaîne de connexion.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une identité d’appareil inscrite dans votre IoT Hub, vous êtes prêt à installer le runtime IoT Edge sur vos appareils. Installez le runtime en fonction du système d’exploitation de l’appareil :

* [Installer Azure IoT Edge sur Windows](how-to-install-iot-edge-windows.md)
* [Installer le runtime Azure IoT Edge sur Linux](how-to-install-iot-edge-linux.md)
