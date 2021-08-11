---
title: Fichier Include
description: inclure fichier
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/05/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 1922efb97feda19154bd965f6d383daba4830338
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114712835"
---
[![Parcourir le code](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample)

Dans ce guide de démarrage rapide, vous allez découvrir un workflow simple de développement d’application Azure IoT. Vous utilisez Azure CLI pour créer un hub Azure IoT et un appareil. Ensuite, vous utilisez un exemple Azure IoT device SDK pour exécuter un contrôleur de température simulé, le connecter en toute sécurité au hub et envoyer des données de télémétrie.

## <a name="prerequisites"></a>Prérequis
- Si vous n’avez pas d’abonnement Azure, [créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- [Git](https://git-scm.com/downloads).
- Une machine de développement avec Java SE Development Kit 8 ou version ultérieure. Vous pouvez télécharger le Kit JDK Java 8 (LTS) pour plusieurs plateformes à partir du site [Download Zulu Builds of OpenJDK](https://www.azul.com/downloads/zulu-community/). Dans le programme d’installation, sélectionnez l’option **Ajouter au chemin**.
- [Apache Maven 3](https://maven.apache.org/download.cgi). Après avoir extrait le téléchargement dans un dossier local, ajoutez le chemin complet du dossier */bin* de Maven à la variable Windows PATH.
- Azure CLI. Vous avez le choix entre deux options pour exécuter les commandes Azure CLI dans ce guide de démarrage rapide :
    - Utilisez Azure Cloud Shell, un interpréteur de commandes interactif qui exécute des commandes CLI dans votre navigateur. Cette option est recommandée, car vous n’avez pas besoin d’installer quoi que ce soit. Si vous utilisez Cloud Shell pour la première fois, connectez-vous au [portail Azure](https://portal.azure.com). Suivez les étapes décrites dans [Démarrage rapide de Cloud Shell](../articles/cloud-shell/quickstart.md) pour **démarrer Cloud Shell** et **sélectionner l’environnement Bash**.
    - Si vous le souhaitez, exécutez Azure CLI sur votre ordinateur local. Si l’interface de ligne de commande Azure est déjà installée, exécutez `az upgrade` pour la mettre à niveau, ainsi que les extensions, vers la version actuelle. Pour installer l’interface de ligne de commande Azure, consultez [Installer l’interface de ligne de commande Azure]( /cli/azure/install-azure-cli).

[!INCLUDE [iot-hub-include-create-hub-cli](iot-hub-include-create-hub-cli.md)]

## <a name="run-a-simulated-device"></a>Exécuter un appareil simulé
Dans cette section, vous allez utiliser le kit SDK Java pour envoyer des messages de votre appareil simulé à votre hub IoT.

### <a name="configure-your-environment"></a>Configurer votre environnement
1. Ouvrez une console pour installer le kit Azure IoT Java device SDK, créer l’exemple de code et l’exécuter.
    > [!NOTE]
    > Vous devez maintenant avoir deux fenêtres de console ouvertes : celle que vous venez d’ouvrir et la console Cloud Shell ou CLI que vous avez utilisée précédemment pour entrer les commandes CLI.
1. Définissez les variables d’environnement suivantes pour permettre à votre appareil simulé de se connecter à Azure IoT.
    * Définissez une variable d’environnement appelée `IOTHUB_DEVICE_CONNECTION_STRING`. Pour la valeur de la variable, utilisez la chaîne de connexion de l’appareil que vous avez enregistrée dans la section précédente.
    * Définissez une variable d’environnement appelée `IOTHUB_DEVICE_SECURITY_TYPE`. Pour la variable, utilisez la valeur de chaîne littérale `connectionString`.

    **CMD**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```
    > [!NOTE]
    > Pour Windows CMD, il n’y a pas de guillemets avant et après les valeurs de chaîne pour chaque variable.

    **Bash**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

### <a name="build-the-sample"></a>Générer l’exemple
1. Dans votre console, clonez le kit Azure IoT Java device SDK sur votre ordinateur local :
    ```console
    git clone https://github.com/Azure/azure-iot-sdk-java.git
    ```
1. Accédez au dossier racine du kit SDK et exécutez la commande suivante pour créer le SDK et mettre à jour les exemples.
    ```console
    cd azure-iot-sdk-java
    mvn install -T 2C -DskipTests
    ```
    Cette opération prend plusieurs minutes.

### <a name="run-the-code"></a>Exécuter le code
1. Dans votre application CLI, exécutez la commande [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) pour commencer à surveiller les événements sur votre appareil IoT simulé.  Les messages d’événement sont imprimés dans le terminal au fur et à mesure de leur arrivée.

    ```azurecli-interactive
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```
1. Dans votre console Java, accédez au répertoire d’exemples.
    ```console
    cd device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample
    ```
1. Dans votre console Java, exécutez l’exemple de code suivant. L’exemple crée un contrôleur de température simulé avec des capteurs de thermostat.
    ```console
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
    ```
    > [!NOTE]
    > Cet exemple de code utilise Azure IoT Plug-and-Play, qui vous permet d’intégrer des appareils intelligents dans vos solutions sans aucune configuration manuelle.  Par défaut, la plupart des exemples de cette documentation utilisent IoT Plug-and-Play. Pour en savoir plus sur les avantages et les cas d’utilisation d’IoT PnP, consultez [Qu’est ce qu’IoT Plug-and-Play ?](../articles/iot-develop/overview-iot-plug-and-play.md)

    Une fois que votre appareil simulé est connecté à votre hub IoT, il se connecte à l’instance d’appareil que vous avez créée dans l’application et commence à envoyer la télémétrie. Après certains détails de provisionnement initiaux, la console commence à générer la télémétrie pour le contrôleur de température.
    
    ```output
    2021-05-13 22:11:05.639 DEBUG TemperatureController:518 - Telemetry - Response from IoT Hub: message Id=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx, status=OK_EMPTY
    2021-05-13 22:11:10.229 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 22:11:10.229 INFO  IotHubTransport:1473 - Sending message ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 22:11:10.231 DEBUG TemperatureController:465 - Telemetry: Sent - {"temperature": 22.8░C} with message Id xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
    2021-05-13 22:11:10.234 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 22:11:10.236 DEBUG TemperatureController:465 - Telemetry: Sent - {"temperature": 35.3░C} with message Id xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
    ```