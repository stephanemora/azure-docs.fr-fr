---
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: include
ms.custom:
- mvc
- mqtt
- devx-track-python
- devx-track-azurecli
ms.date: 09/14/2020
ms.openlocfilehash: 8e9388f8b929743dddde0db21270e92eddf08a40
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114732099"
---
 Ce guide de démarrage rapide utilise deux applications Python : une application d’appareil simulé qui répond aux méthodes directes appelées à partir d’une application back-end et une application back-end qui appelle les méthodes directes sur l’appareil simulé.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.7+](https://www.python.org/downloads/). Pour les autres versions de Python prises en charge, consultez les [fonctionnalités des appareils Azure IoT](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).

* [Exemple de projet Python](https://github.com/Azure-Samples/azure-iot-samples-python/) tiré de GitHub. Téléchargez ou clonez les exemples à l’aide du bouton **Code** dans le dépôt GitHub.

* Le port 8883 ouvert dans votre pare-feu. L’exemple d’appareil de ce guide de démarrage rapide utilise le protocole MQTT, lequel communique sur le port 8883. Ce port peut être bloqué dans certains environnements réseau professionnels et scolaires. Pour plus d’informations sur les différentes façons de contourner ce problème, consultez [Connexion à IoT Hub (MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Si vous avez terminé le [Démarrage rapide : envoyer des données de télémétrie à partir d’un appareil à un IoT Hub](../articles/iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) précédent, vous pouvez ignorer cette étape.

[!INCLUDE [iot-hub-include-create-hub](iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Inscrire un appareil

Si vous avez terminé le [Démarrage rapide : envoyer des données de télémétrie à partir d’un appareil à un IoT Hub](../articles/iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) précédent, vous pouvez ignorer cette étape.

Un appareil doit être inscrit dans votre hub IoT pour pouvoir se connecter. Dans ce démarrage rapide, vous utilisez Azure Cloud Shell pour inscrire un appareil simulé.

1. Exécutez les commandes suivantes dans Azure Cloud Shell pour créer l’identité d’appareil.

    **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

    **MyPythonDevice** : il s’agit du nom de l’appareil que vous inscrivez. Il est recommandé d’utiliser **MyPythonDevice** comme indiqué. Si vous choisissez un autre nom pour votre appareil, vous devez utiliser ce nom tout au long de cet article et mettre à jour le nom de l’appareil dans les exemples d’application avant de les exécuter.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. Exécutez les commandes suivantes dans Azure Cloud Shell pour obtenir la _chaîne de connexion_ à l’appareil que vous venez d’inscrire :

    **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Notez la chaîne de connexion à l’appareil, qui ressemble à ce qui suit :

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Vous utiliserez cette valeur plus loin dans ce démarrage rapide.

3. Vous avez également besoin d’une _chaîne de connexion de service_ pour activer l’application back-end afin de vous connecter à votre IoT Hub et récupérer les messages. La commande suivante récupère la chaîne de connexion de service correspondant à votre hub IoT :

    **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

    ```azurecli-interactive
    az iot hub connection-string show \
      --policy-name service \
      --hub-name {YourIoTHubName} \
      --output table
    ```

    Notez la chaîne de connexion de service, qui ressemble à ce qui suit :

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Vous utiliserez cette valeur plus loin dans ce démarrage rapide. La chaîne de connexion de service est différente de la chaîne de connexion d’appareil que vous avez notée à l’étape précédente.

## <a name="listen-for-direct-method-calls"></a>Écouter les appels de méthode directe

L’application d’appareil simulé se connecte à un point de terminaison spécifique de l’appareil sur votre IoT Hub, envoie les données de télémétrie simulée et écoute les appels de méthode directe provenant de votre concentrateur. Dans ce démarrage rapide, l’appel de méthode directe à partir du concentrateur indique à l’appareil de modifier la fréquence à laquelle il envoie des données de télémétrie. L’appareil simulé renvoie un accusé de réception à votre hub après l’exécution de la méthode directe.

1. Dans une fenêtre de terminal local, accédez au dossier racine de l’exemple de projet Python. Ensuite, accédez au dossier **iot-hub\Quickstarts\simulated-device-2**.

1. Utilisez un éditeur de texte pour ouvrir le fichier **SimulatedDevice.py**.

    Remplacez la valeur de la variable `CONNECTION_STRING` par la chaîne de connexion d’appareil que vous avez notée précédemment. Enregistrez ensuite les changements apportés à **SimulatedDevice.py**.

1. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour installer les bibliothèques requises pour l’application d’appareil simulé :

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour démarrer l’application d’appareil simulé :

    ```cmd/sh
    python SimulatedDevice.py
    ```

    La capture d’écran suivante présente la sortie lorsque l’application d’appareil simulé envoie des données de télémétrie à votre IoT Hub :

    ![Exécuter l’appareil simulé](./media/quickstart-control-device-python/simulated-device-1.png)

## <a name="call-the-direct-method"></a>Appeler la méthode directe

L’application back-end se connecte au point de terminaison côté service sur votre IoT Hub. L’application effectue des appels de méthode directe à un appareil via votre IoT Hub et écoute les accusés de réception. Une application back-end de l’IoT Hub s’exécute généralement dans le cloud.

1. Dans une autre fenêtre de terminal local, accédez au dossier racine de l’exemple de projet Python. Ensuite, accédez au dossier **iot-hub\Quickstarts\back-end-application**.

1. Utilisez un éditeur de texte pour ouvrir le fichier **BackEndApplication.py**.

    Remplacez la valeur de la variable `CONNECTION_STRING` par la chaîne de connexion de service que vous avez notée précédemment. Ensuite, enregistrez vos modifications dans **BackEndApplication.py**.

1. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour installer les bibliothèques requises pour l’application d’appareil simulé :

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour exécuter l’application back-end :

    ```cmd/sh
    python BackEndApplication.py
    ```

    La capture d’écran suivante montre la sortie lorsque l’application effectue un appel de méthode directe sur l’appareil et reçoit un accusé de réception :

    ![Exécuter l’application back-end](./media/quickstart-control-device-python/backend-application.png)

    Après avoir exécuté l’application back-end, un message s’affiche dans la fenêtre de la console exécutant l’appareil simulé, et la fréquence à laquelle il envoie les messages change :

    ![Changement dans le client simulé](./media/quickstart-control-device-python/simulated-device-2.png)
