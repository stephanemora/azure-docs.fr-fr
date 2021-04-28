---
title: Démarrage rapide pour envoyer la télémétrie des appareils à Azure IoT Hub (Python)
description: Dans ce démarrage rapide, vous utilisez le Kit de développement logiciel (SDK) Azure IoT Hub Device pour Python afin d’envoyer les données de télémétrie d’un appareil à un hub IoT.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 03/24/2021
ms.openlocfilehash: be44ecf6dd154a21fd024ee37d92f61504350664
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876424"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-azure-iot-hub-python"></a>Démarrage rapide : Envoyer des données de télémétrie d’un appareil à un hub IoT (Python)

**S’applique à** : [Développement d’applications pour appareils](about-iot-develop.md#device-application-development)

Dans ce démarrage rapide, vous allez apprendre un workflow de base relatif au développement d’applications pour appareils IoT. Vous utilisez l’interface de ligne de commande Azure pour créer un hub Azure IoT et un appareil simulé, puis vous utilisez le Kit de développement logiciel (SDK) Azure IoT Python pour créer un appareil client simulé et envoyer la télémétrie au hub. 

## <a name="prerequisites"></a>Prérequis
- Si vous n’avez pas d’abonnement Azure, [créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Azure CLI. Vous pouvez exécuter toutes les commandes dans ce démarrage rapide à l’aide de Azure Cloud Shell, un interpréteur de commandes CLI interactif qui s’exécute dans votre navigateur. Si vous utilisez Cloud Shell, vous n’avez rien à installer. Si vous préférez installer et utiliser l’interface de ligne de commande en local, ce démarrage rapide nécessite au minimum Azure CLI version 2.0.76. Pour déterminer la version, exécutez la commande az--version. Pour installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).
- [Python 3.7+](https://www.python.org/downloads/). Pour les autres versions de Python prises en charge, consultez les [fonctionnalités des appareils Azure IoT](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    Pour vous assurer que votre version de Python est à jour, exécutez `python --version`. Si vous avez installé Python 2 et Python 3 et que vous utilisez un environnement Python 3, installez toutes les bibliothèques en utilisant `pip3`. Cette commande permet de s’assurer que les bibliothèques sont installées sur votre runtime Python 3.
    > [!IMPORTANT]
    > Dans le programme d’installation de Python, sélectionnez l’option permettant d’**ajouter Python à PATH**. Si vous avez déjà installé Python 3.7 ou une version ultérieure, vérifiez que vous avez ajouté le dossier d’installation de Python à la variable d’environnement `PATH`.

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-python-sdk-to-send-messages"></a>Utiliser le Kit de développement logiciel (SDK) Python pour envoyer des messages
Dans cette section, vous allez utiliser le Kit de développement logiciel (SDK) Python pour envoyer des messages de votre appareil simulé à votre hub IoT.

1. Ouvrez une nouvelle fenêtre de terminal. Vous allez utiliser ce terminal pour installer le Kit de développement logiciel (SDK) Python et travailler avec des exemples de code Python. Vous devez maintenant avoir deux terminaux ouverts : celui que vous venez d’ouvrir pour travailler avec Python et l’interpréteur de commandes CLI que vous avez utilisé dans les sections précédentes pour entrer des commandes Azure CLI.       

1. Copiez les [exemples d’appareils du Kit de développement logiciel (SDK) Azure IoT Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) sur votre ordinateur local :

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. Accédez au répertoire *azure-iot-sdk-python/azure-iot-device/samples/pnp* :

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. Installez le Kit de développement logiciel (SDK) Azure IoT Python :

    ```console
    pip install azure-iot-device
    ```
1. Définissez les deux variables d’environnement suivantes pour permettre à votre appareil simulé de se connecter à Azure IoT.
    * Définissez une variable d’environnement appelée `IOTHUB_DEVICE_CONNECTION_STRING`. Pour la valeur de la variable, utilisez la chaîne de connexion de l’appareil que vous avez enregistrée dans la section précédente.
    * Définissez une variable d’environnement appelée `IOTHUB_DEVICE_SECURITY_TYPE`. Pour la variable, utilisez la valeur de chaîne littérale `connectionString`.

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > Pour Windows CMD, il n’y a pas de guillemets avant et après les valeurs de chaîne pour chaque variable.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    ```
    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash (Linux ou Windows)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```
    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

1. Dans votre interpréteur de commandes CLI ouvert, exécutez la commande [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) pour commencer à surveiller les événements sur votre appareil IoT simulé.  Les messages d’événement sont imprimés dans le terminal au fur et à mesure de leur arrivée.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Dans votre terminal Python, exécutez le code de l’exemple de fichier *simple_thermostat.py* installé. Ce code accède à l’appareil IoT simulé et envoie un message au hub IoT.

    Pour exécuter l’exemple Python à partir du terminal :
    ```console
    python ./simple_thermostat.py
    ```
    > [!NOTE]
    > Cet exemple de code utilise Azure IoT Plug-and-Play, qui vous permet d’intégrer des appareils intelligents dans vos solutions sans aucune configuration manuelle.  Par défaut, la plupart des exemples de cette documentation utilisent IoT Plug-and-Play. Pour en savoir plus sur les avantages et les cas d’utilisation d’IoT PnP, consultez [Qu’est ce qu’IoT Plug-and-Play ?](../iot-pnp/overview-iot-plug-and-play.md)

 Lorsque le code Python envoie un message de votre appareil au hub IoT, le message s’affiche dans votre interpréteur de commandes CLI qui analyse les événements :

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device name>
  payload:
    temperature: 35
```

Votre appareil est maintenant connecté en toute sécurité et envoie des données de télémétrie à Azure IoT Hub.

## <a name="clean-up-resources"></a>Nettoyer les ressources
Si vous n’avez plus besoin des ressources Azure créées dans ce démarrage rapide, vous pouvez utiliser Azure CLI pour les supprimer.

> [!IMPORTANT]
> La suppression d’un groupe de ressources est irréversible. Le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects.

Pour supprimer un groupe de ressources par nom :
1. Exécutez la commande [az group delete](/cli/azure/group#az_group_delete). Cette commande supprime le groupe de ressources, le hub IoT et l’inscription de l’appareil que vous avez créés.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Exécutez la commande [az group list](/cli/azure/group#az_group_list) pour confirmer la suppression du groupe de ressources.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Étapes suivantes
Dans ce démarrage rapide, vous avez appris un workflow de base d’application Azure IoT pour connecter de façon sécurisée un appareil au cloud et envoyer des données de télémétrie appareil-à-cloud. Vous avez utilisé l’interface de ligne de commande Azure pour créer un hub IoT et un appareil, puis avez utilisé le Kit de développement logiciel (SDK) Azure IoT Python pour créer un appareil simulé et envoyer la télémétrie au hub. 

L’étape suivante consiste à explorer le Kit de développement logiciel (SDK) Azure IoT Python à l’aide d’exemples d’applications.

- [Exemples asynchrones](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios) : Ce répertoire contient des exemples Python asynchrones pour d’autres scénarios IoT Hub.
- [Exemples synchrones](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples) : Ce répertoire contient des exemples Python à utiliser avec Python 2.7 ou des scénarios de compatibilité synchrone pour Python 3.6 et ultérieur
- [Exemples IoT Edge](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios) : Ce répertoire contient des exemples Python pour l’utilisation des modules Edge et des appareils en aval.
