---
title: Démarrage rapide (Python) pour envoyer des données de télémétrie à Azure IoT Hub | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous exécutez un exemple d’application Python pour envoyer des données de télémétrie simulée à un IoT Hub et utilisez un utilitaire pour lire les données de télémétrie provenant du IoT Hub.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/28/2019
ms.openlocfilehash: 8dab132cd03b24f4f9e55cb777cc0f984526ba12
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243361"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Démarrage rapide : Envoyer des données de télémétrie d’un appareil à un hub IoT et les lire avec une application back-end (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub est un service Azure qui vous permet de traiter de gros volumes de données de télémétrie envoyées par vos appareils IoT dans le cloud à des fins de stockage ou de traitement. Dans ce guide de démarrage rapide, vous envoyez des données de télémétrie à partir d’une application d’appareil simulé, via IoT Hub, à une application back-end pour traitement.

Ce démarrage rapide utilise une application Python prédéfinie pour envoyer des données de télémétrie, et un utilitaire CLI pour lire ces données de télémétrie provenant du concentrateur. Avant d’exécuter ces deux applications, vous créez un IoT Hub et inscrivez un appareil auprès du concentrateur.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Les deux exemples d’applications que vous exécutez dans ce guide de démarrage rapide sont écrits à l’aide de Python. Actuellement, les SDK Microsoft Azure IoT pour Python prennent en charge uniquement les versions spécifiques de Python pour chaque plateforme. Pour plus d’informations, consultez le [fichier Lisez-moi du kit de développement logiciel Python](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues).

Ce démarrage rapide repose sur l’hypothèse que vous utilisez un ordinateur de développement Windows. Pour les systèmes Windows, seul [Python 3.6.x](https://www.python.org/downloads/release/python-368/) est pris en charge. Le programme d’installation de Python que vous choisissez doit être basé sur l’architecture du système que vous utilisez. Si votre architecture de processeur est 32 bits, téléchargez le programme d’installation x86. Pour l’architecture 64 bits, téléchargez le programme d’installation x86-64. En outre, assurez-vous que le [redistribuable Microsoft Visual C++ pour Visual Studio 2017](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) est installé pour votre architecture (x64 ou x86).

Vous pouvez télécharger Python pour plusieurs plateformes sur [Python.org](https://www.python.org/downloads/).

Vous pouvez vérifier la version actuelle de Python sur votre machine de développement à l’aide d’une des commandes suivantes :

```python
python --version
```

```python
python3 --version
```

Téléchargez l’exemple de projet Python à partir de https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip et extrayez l’archive ZIP.

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Inscrire un appareil

Un appareil doit être inscrit dans votre hub IoT pour pouvoir se connecter. Dans ce démarrage rapide, vous utilisez Azure Cloud Shell pour inscrire un appareil simulé.

1. Exécutez les commandes suivantes dans Azure Cloud Shell pour ajouter l’extension CLI IoT Hub et créer l’identité d’appareil. 

    **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

    **MyPythonDevice** : il s’agit du nom donné à l’appareil inscrit. Utilisez MyPythonDevice comme indiqué. Si vous choisissez un autre nom pour votre appareil, vous devez également utiliser ce nom pour l’ensemble de cet article et mettre à jour le nom de l’appareil dans les exemples d’application avant de les exécuter.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyPythonDevice
    ```

1. Exécutez les commandes suivantes dans Azure Cloud Shell pour obtenir la _chaîne de connexion_ à l’appareil que vous venez d’inscrire :

    **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyPythonDevice --output table
    ```

    Notez la chaîne de connexion à l’appareil, qui ressemble à ce qui suit :

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Vous utiliserez cette valeur plus loin dans ce démarrage rapide.

## <a name="send-simulated-telemetry"></a>Envoyer des données de télémétrie simulée

L’application d’appareil simulé se connecte à un point de terminaison spécifique de l’appareil sur votre IoT Hub et envoie les données de télémétrie simulée (température et humidité).

1. Dans une fenêtre de terminal local, accédez au dossier racine de l’exemple de projet Python. Ensuite, accédez au dossier **iot-hub\Quickstarts\simulated-device**.

1. Utilisez un éditeur de texte pour ouvrir le fichier **SimulatedDevice.py**.

    Remplacez la valeur de la variable `CONNECTION_STRING` par la chaîne de connexion d’appareil que vous avez notée précédemment. Puis, enregistrez les modifications apportées au fichier **SimulatedDevice.py**.

1. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour installer les bibliothèques requises pour l’application d’appareil simulé :

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour démarrer l’application d’appareil simulé :

    ```cmd/sh
    python SimulatedDevice.py
    ```

    La capture d’écran suivante présente la sortie lorsque l’application d’appareil simulé envoie des données de télémétrie à votre IoT Hub :

    ![Exécuter l’appareil simulé](media/quickstart-send-telemetry-python/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Lire les données de télémétrie envoyées par votre hub

L’extension CLI IoT Hub peut se connecter au point de terminaison **Événements** côté service sur votre IoT Hub. L’extension reçoit les messages appareil-à-cloud envoyés à partir de votre appareil simulé. Une application back-end IoT Hub s’exécute généralement dans le cloud pour recevoir et traiter les messages appareil-à-cloud.

Exécutez les commandes suivantes dans Azure Cloud Shell, en remplaçant `YourIoTHubName` par le nom de votre hub IoT :

```azurecli-interactive
az iot hub monitor-events --hub-name YourIoTHubName --device-id MyPythonDevice 
```

La capture d’écran suivante présente la sortie lorsque l’extension reçoit les données de télémétrie envoyées par l’appareil simulé au hub :

![Exécuter l’application back-end](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez configuré un IoT Hub, inscrit un appareil, envoyé des données de télémétrie simulée au concentrateur à l’aide d’une application Python et lu les données de télémétrie à partir du concentrateur à l’aide d’une application back-end simple.

Pour savoir comment contrôler votre appareil simulé à partir d’une application back-end, passez au démarrage rapide suivant.

> [!div class="nextstepaction"]
> [Démarrage rapide : Contrôler un appareil connecté à un hub IoT](quickstart-control-device-python.md)