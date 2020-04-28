---
title: 'Démarrage rapide : Envoyer des données de télémétrie à Azure IoT Hub avec Java'
description: Dans ce guide de démarrage rapide, vous exécutez deux exemples d’applications Java pour envoyer des données de télémétrie simulées à un IoT Hub et lire les données de télémétrie provenant de l’IoT Hub à traiter dans le cloud.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- seo-java-august2019
- seo-java-september2019
- mqtt
ms.date: 06/21/2019
ms.openlocfilehash: 954fe6f92b68d011aaff0fff7925b3409ee628cb
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769102"
---
# <a name="quickstart-send-telemetry-to-an-azure-iot-hub-and-read-it-with-a-java-application"></a>Démarrage rapide : Envoyer de la télémétrie à un hub IoT Azure et la lire avec une application Java

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Dans ce guide de démarrage rapide, vous envoyez des données de télémétrie à un hub Azure IoT et vous les lisez avec une application Java. IoT Hub est un service Azure qui vous permet de traiter de gros volumes de données de télémétrie envoyées par vos appareils IoT dans le cloud à des fins de stockage ou de traitement. Ce guide de démarrage rapide utilise deux applications Java prédéfinies : une pour envoyer la télémétrie et l’autre pour lire cette télémétrie à partir du hub. Avant d’exécuter ces deux applications, vous créez un IoT Hub et inscrivez un appareil auprès du concentrateur.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Java SE Development Kit 8. Dans [Prise en charge à long terme de Java pour Azure et Azure Stack](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable), sous **Prise en charge à long terme**, sélectionnez **Java 8**.

* [Apache Maven 3](https://maven.apache.org/download.cgi).

* [Un exemple de projet Java](https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip).

* Le port 8883 ouvert dans votre pare-feu. L’exemple d’appareil de ce guide de démarrage rapide utilise le protocole MQTT, lequel communique sur le port 8883. Ce port peut être bloqué dans certains environnements réseau professionnels et scolaires. Pour plus d'informations sur les différentes façons de contourner ce problème, consultez [Se connecter à IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

Vous pouvez vérifier la version actuelle de Java sur votre machine de développement à l’aide de la commande suivante :

```cmd/sh
java -version
```

Vous pouvez vérifier la version actuelle de Maven sur votre machine de développement à l’aide de la commande suivante :

```cmd/sh
mvn --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Ajouter une extension Azure IoT

Exécutez la commande suivante afin d’ajouter l’extension Microsoft Azure IoT pour Azure CLI à votre instance Cloud Shell. L’extension IoT ajoute des commandes IoT Hub, IoT Edge et du service IoT Hub Device Provisioning (DPS) à Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Inscrire un appareil

Un appareil doit être inscrit dans votre hub IoT pour pouvoir se connecter. Dans ce démarrage rapide, vous utilisez Azure Cloud Shell pour inscrire un appareil simulé.

1. Exécutez les commandes suivantes dans Azure Cloud Shell pour créer l’identité d’appareil.

   **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

   **MyJavaDevice** : il s’agit du nom de l’appareil que vous inscrivez. Il est recommandé d’utiliser **MyJavaDevice** comme indiqué. Si vous choisissez un autre nom pour votre appareil, vous devrez utiliser ce nom tout au long de cet article et mettre à jour le nom de l'appareil dans les exemples d'application avant de les exécuter.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Exécutez la commande suivante dans Azure Cloud Shell pour obtenir la _chaîne de connexion d’appareil_ pour celui que vous venez d’inscrire :

    **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Notez la chaîne de connexion à l’appareil, qui ressemble à ce qui suit :

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}`

    Vous utiliserez cette valeur plus loin dans ce guide de démarrage rapide.

3. Vous avez aussi besoin du _point de terminaison compatible Event Hubs_, du _chemin d’accès compatible Event Hubs_ et de la _clé principale du service_ à partir de votre IoT Hub pour permettre à l’application back-end de se connecter à votre IoT Hub et de récupérer les messages. Les commandes suivantes extraient ces valeurs pour votre IoT Hub :

     **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Prenez note de ces trois valeurs, que vous utiliserez plus loin dans ce guide de démarrage rapide.

## <a name="send-simulated-telemetry"></a>Envoyer des données de télémétrie simulée

L’application d’appareil simulé se connecte à un point de terminaison spécifique de l’appareil sur votre IoT Hub et envoie les données de télémétrie simulée (température et humidité).

1. Dans une fenêtre de terminal local, accédez au dossier racine de l’exemple de projet Java. Ensuite, accédez au dossier **iot-hub\Quickstarts\simulated-device**.

2. Ouvrez le fichier **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** dans l’éditeur de texte de votre choix.

    Remplacez la valeur de la variable `connString` par la chaîne de connexion d’appareil que vous avez notée précédemment. Enregistrez ensuite les changements apportés à **SimulatedDevice.java**.

3. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour installer les bibliothèques requises et générer l’application d’appareil simulé :

    ```cmd/sh
    mvn clean package
    ```

4. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour démarrer l’application d’appareil simulé :

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    La capture d’écran suivante présente la sortie lorsque l’application d’appareil simulé envoie des données de télémétrie à votre IoT Hub :

    ![Sortie des données de télémétrie envoyées par l’appareil à votre hub IoT](media/quickstart-send-telemetry-java/iot-hub-simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Lire les données de télémétrie envoyées par votre hub

L’application back-end se connecte au point de terminaison **Événements** du service sur votre IoT Hub. L’application reçoit les messages appareil-à-cloud envoyés à partir de votre appareil simulé. Une application back-end IoT Hub s’exécute généralement dans le cloud pour recevoir et traiter les messages appareil-à-cloud.

1. Dans une autre fenêtre de terminal local, accédez au dossier racine de l’exemple de projet Java. Puis, accédez au dossier **iot-hub\Quickstarts\read-d2c-messages**.

2. Ouvrez le fichier **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** dans l’éditeur de texte de votre choix. Mettez à jour les variables suivantes et enregistrez vos modifications dans le fichier.

    | Variable | Valeur |
    | -------- | ----------- |
    | `eventHubsCompatibleEndpoint` | Remplacez la valeur de la variable par le point de terminaison compatible Event Hubs que vous avez noté précédemment. |
    | `eventHubsCompatiblePath`     | Remplacez la valeur de la variable par le chemin compatible Event Hubs que vous avez noté précédemment. |
    | `iotHubSasKey`                | Remplacez la valeur de la variable par la clé principale de service que vous avez notée précédemment. |

3. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour installer les bibliothèques requises et générer l’application back-end :

    ```cmd/sh
    mvn clean package
    ```

4. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour exécuter l’application back-end :

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    La capture d’écran suivante présente la sortie lorsque l’application back-end reçoit les données de télémétrie envoyées par l’appareil simulé au concentrateur :

    ![La sortie en tant qu’application back-end reçoit les données de télémétrie envoyées à votre hub IoT](media/quickstart-send-telemetry-java/iot-hub-read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez configuré un hub IoT, inscrit un appareil, envoyé des données de télémétrie simulée au hub à l’aide d’une application Java et lu les données de télémétrie à partir du hub à l’aide d’une application back-end simple.

Pour savoir comment contrôler votre appareil simulé à partir d’une application back-end, passez au démarrage rapide suivant.

> [!div class="nextstepaction"]
> [Démarrage rapide : Contrôler un appareil connecté à un hub IoT](quickstart-control-device-java.md)
