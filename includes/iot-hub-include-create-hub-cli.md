---
title: Fichier include
description: Fichier include
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 01/14/2021
ms.openlocfilehash: 45a9a0821dec98637ade4940bee915483d5e9aa1
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112041494"
---
## <a name="create-an-iot-hub"></a>Créer un hub IoT
Dans cette section, vous utilisez Azure CLI pour créer un hub IoT et un groupe de ressources.  Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Un hub IoT agit en tant que hub de messages central pour la communication bidirectionnelle entre votre application IoT et les appareils.

Pour créer un hub IoT et un groupe de ressources :

1. Lancez votre application CLI.  Pour exécuter les commandes CLI dans le reste de ce guide de démarrage rapide, copiez la syntaxe de la commande, collez-la dans votre application CLI, modifiez les valeurs des variables et appuyez sur Entrée.
    - Si vous utilisez Cloud Shell, sélectionnez le bouton **Essayer** dans les commandes CLI pour lancer Cloud Shell dans une fenêtre de navigateur partagée. Ou vous pouvez ouvrir [Cloud Shell](https://shell.azure.com/bash) dans un onglet de navigateur distinct.
    - Si vous utilisez Azure CLI localement, démarrez votre application console CLI et connectez-vous à Azure CLI.

1. Exécutez [az extension add](/cli/azure/extension?view=azure-cli-latest#az_extension_add) pour installer ou mettre à niveau l’extension *azure-iot* vers la version actuelle.

    ```azurecli-interactive
    az extension add --upgrade --name azure-iot
    ```

1. Dans votre application CLI, exécutez la commande [az group create](/cli/azure/group#az_group_create) pour créer un groupe de ressources. La commande suivante crée un groupe de ressources nommé *MyResourceGroup* à l’emplacement *eastus* : 
    >[!NOTE]
    > Vous pouvez éventuellement définir une autre région. Pour voir les régions disponibles, exécutez `az account list-locations`. Ce tutoriel utilise *eastus* comme indiqué dans l’exemple de commande. 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```

1. Exécutez la commande [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) pour créer un hub IoT. La création de votre hub IoT peut prendre plusieurs minutes. 

    *YourIotHubName*. Remplacez cet espace réservé et les accolades qui l’entourent dans la commande suivante, en utilisant le nom que vous avez choisi pour votre hub IoT. Le nom du hub IoT doit être globalement unique dans Azure. Utilisez le nom de votre hub IoT dans le reste de ce guide de démarrage rapide là où vous voyez l’espace réservé.

    ```azurecli-interactive
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```
    > [!TIP]
    > Après avoir créé un hub IoT, dans le reste de ce guide de démarrage rapide, vous pouvez continuer à utiliser les commandes CLI pour interagir avec le hub. Si vous le souhaitez, vous pouvez utiliser Azure IoT Explorer à la place des commandes CLI. IoT Explorer est une application GUI qui vous permet de vous connecter à un hub IoT existant et d’ajouter, de gérer et de surveiller des appareils. Pour plus d’informations, consultez [Installer et utiliser l’explorateur Azure IoT](../articles/iot-pnp/howto-use-iot-explorer.md).

## <a name="create-a-simulated-device"></a>Créez un appareil simulé.
Dans cette section, vous créez un appareil IoT simulé connecté à votre hub IoT. 

Pour créer un appareil simulé :
1. Exécutez la commande [az iot hub device-identity create](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) dans votre shell CLI. Cela crée l’identité de l’appareil simulé. 

    *YourIotHubName*. Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT. 

    *myDevice*. Vous pouvez utiliser ce nom directement pour l’ID de l’appareil simulé dans le reste de cet article. Vous pouvez aussi utiliser un autre nom. 

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDevice --hub-name {YourIoTHubName} 
    ```

1.  Exécutez la commande [az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string#az_iot_hub_device_identity_connection_string_show). 

    ```azurecli-interactive
    az iot hub device-identity connection-string show --device-id myDevice --hub-name {YourIoTHubName}
    ```

    La chaîne de connexion en sortie est au format suivant :

    ```Output
    HostName=<your IoT Hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<some value>
    ```

1. Enregistrez la chaîne de connexion en lieu sûr. 

> [!NOTE]
> Maintenez votre application CLI ouverte. Vous l’utiliserez plus tard.