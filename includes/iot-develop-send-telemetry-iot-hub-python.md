---
title: Fichier include
description: Fichier include
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 08/03/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: ef3c7af3150cd492be4d56d51bf31ecb989f45a2
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122636419"
---
[![Parcourir le code](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp)

Dans ce guide de démarrage rapide, vous allez découvrir un workflow simple de développement d’application Azure IoT. Vous utilisez Azure CLI pour créer un hub Azure IoT et un appareil. Ensuite, vous utilisez un exemple Azure IoT device SDK pour exécuter un contrôleur de température simulé, le connecter en toute sécurité au hub et envoyer des données de télémétrie.

## <a name="prerequisites"></a>Prérequis
- Si vous n’avez pas d’abonnement Azure, [créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- [Git](https://git-scm.com/downloads).
- [Python](https://www.python.org/downloads/) version 3.7 ou ultérieure. Pour vérifier votre version de Python, exécutez `python --version`.
- [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) : utilitaire multiplateforme pour surveiller et gérer Azure IoT 
- Azure CLI. Vous avez le choix entre deux options pour exécuter les commandes Azure CLI dans ce guide de démarrage rapide :
    - Utilisez Azure Cloud Shell, un interpréteur de commandes interactif qui exécute des commandes CLI dans votre navigateur. Cette option est recommandée, car vous n’avez pas besoin d’installer quoi que ce soit. Si vous utilisez Cloud Shell pour la première fois, connectez-vous au [portail Azure](https://portal.azure.com). Suivez les étapes décrites dans [Démarrage rapide de Cloud Shell](../articles/cloud-shell/quickstart.md) pour **démarrer Cloud Shell** et **sélectionner l’environnement Bash**.
    - Si vous le souhaitez, exécutez Azure CLI sur votre ordinateur local. Si l’interface de ligne de commande Azure est déjà installée, exécutez `az upgrade` pour la mettre à niveau, ainsi que les extensions, vers la version actuelle. Pour installer l’interface de ligne de commande Azure, consultez [Installer l’interface de ligne de commande Azure]( /cli/azure/install-azure-cli).

[!INCLUDE [iot-hub-include-create-hub-iot-explorer](iot-hub-include-create-hub-iot-explorer.md)]

## <a name="run-a-simulated-device"></a>Exécuter un appareil simulé
Dans cette section, vous utilisez le Kit de développement logiciel (SDK) Python pour envoyer des messages de votre appareil simulé à votre hub IoT. Vous allez exécuter un exemple qui implémente un contrôleur de température avec deux capteurs de thermostat.

1. Ouvrez une nouvelle fenêtre de console. Vous allez utiliser cette console pour installer le Kit de développement logiciel (SDK) Python et travailler avec des exemples de code Python.

    > [!NOTE]
    > Si vous utilisez une installation locale d’Azure CLI, vous pourriez maintenant avoir deux fenêtres de console ouvertes. Veillez à entrer les commandes de cette section dans la console que vous venez d’ouvrir et non dans celle que vous avez utilisée pour l’interface CLI.

1. Dans votre console Python, clonez les [exemples d’appareil du Kit de développement logiciel (SDK) Azure IoT Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) sur votre ordinateur local :

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. Accédez au répertoire des exemples :

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. Installez le Kit de développement logiciel (SDK) Azure IoT Python :

    ```console
    pip3 install azure-iot-device
    ```
1. Définissez les variables d’environnement suivantes pour permettre à votre appareil simulé de se connecter à Azure IoT.
    * Définissez une variable d’environnement appelée `IOTHUB_DEVICE_CONNECTION_STRING`. Pour la valeur de la variable, utilisez la chaîne de connexion de l’appareil que vous avez enregistrée dans la section précédente.
    * Définissez une variable d’environnement appelée `IOTHUB_DEVICE_SECURITY_TYPE`. Pour la variable, utilisez la valeur de chaîne littérale `connectionString`.

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```
    > [!NOTE]
    > Pour Windows CMD, il n’y a pas de guillemets avant et après les valeurs de chaîne pour chaque variable.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash (Linux ou Windows)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

1. Dans votre console Python, exécutez le code de l’exemple de fichier suivant.

    ```console
    python temp_controller_with_thermostats.py
    ```
    > [!NOTE]
    > Cet exemple de code utilise Azure IoT Plug-and-Play, qui vous permet d’intégrer des appareils intelligents dans vos solutions sans aucune configuration manuelle.  Par défaut, la plupart des exemples de cette documentation utilisent IoT Plug-and-Play. Pour en savoir plus sur les avantages et les cas d’utilisation ou non d’IoT Plug-and-Play, consultez [Qu’est ce qu’IoT Plug-and-Play ?](../articles/iot-develop/overview-iot-plug-and-play.md).

L’échantillon se connecte en toute sécurité à votre hub IoT en tant qu’appareil que vous avez inscrit et commence à envoyer des messages de télémétrie. L’échantillon de sortie s’affiche dans votre console.

## <a name="view-telemetry"></a>Afficher les données de télémétrie

Vous pouvez afficher la télémétrie des appareils avec IoT Explorer. Si vous le souhaitez, vous pouvez afficher les données de télémétrie à l’aide d’Azure CLI.

Pour afficher la télémétrie dans Azure IoT Explorer :

1. À partir de votre hub IoT dans IoT Explorer, sélectionnez **Afficher les appareils dans ce hub**, puis sélectionnez votre appareil dans la liste. 
1. Dans le menu de gauche pour votre appareil, sélectionnez **Télémétrie**.
1. Confirmez que **Utiliser un Event Hub intégré** est défini sur *Oui*, puis sélectionnez **Démarrer**.
1. Affichez la télémétrie à mesure que l’appareil envoie des messages au cloud.

    :::image type="content" source="media/iot-develop-send-telemetry-iot-hub-python/iot-explorer-device-telemetry.png" alt-text="Capture d’écran de télémétrie d’appareil dans IoT Explorer":::

1. Sélectionnez **Arrêter** pour cesser de recevoir des événements.

Pour lire les données de télémétrie envoyées par les composants d’appareils individuels, vous pouvez utiliser les fonctionnalités Plug-and-Play dans IoT Explorer. Par exemple, le contrôleur de température dans ce démarrage rapide a deux thermostats : thermostat1 et thermostat2. Pour afficher la température signalée par thermostat1 : 

1. Sur votre appareil dans IoT Explorer, sélectionnez **Composants IoT Plug-and-Play** dans le menu de gauche. Ensuite, sélectionnez **thermostat1** dans la liste des composants.

1. Sur le volet du composant **thermostat1**, sélectionnez **Télémétrie** dans le menu supérieur.

1. Sur le volet **Télémétrie**, suivez les mêmes étapes que précédemment. Vérifiez que **Utiliser un Event Hub intégré** est défini sur *Oui*, puis sélectionnez **Démarrer**.

Pour afficher la télémétrie de l’appareil avec Azure CLI :

1. Dans votre application CLI, exécutez la commande [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) pour analyser les événements envoyés par l’appareil simulé à votre hub IoT. Utilisez les noms que vous avez créés précédemment dans Azure IoT pour votre appareil et le hub IoT.

    ```azurecli
    az iot hub monitor-events --output table --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. Affichez les détails de connexion et la sortie de la télémétrie dans la console.

    ```output
    Starting event monitor, filtering on device: mydevice, use ctrl-c to stop...
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 29
    
    event:
      component: thermostat2
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 48
    ```
    