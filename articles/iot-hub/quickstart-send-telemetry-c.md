---
title: Démarrage rapide (C) pour envoyer des données de télémétrie à Azure IoT Hub | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous exécutez deux exemples d’application C pour envoyer des données de télémétrie simulée à un hub IoT, et pour lire les données de télémétrie provenant du hub IoT à traiter dans le cloud.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/27/2018
ms.author: wesmc
ms.openlocfilehash: 77b76ac5b30c4f5f647c532dbc5db68b396b3d20
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2018
ms.locfileid: "45636139"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-c"></a>Démarrage rapide : Envoyer des données de télémétrie d’un appareil à un hub IoT et les lire depuis ce hub avec une application back-end (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub est un service Azure qui vous permet de traiter de gros volumes de données de télémétrie envoyées par vos appareils IoT dans le cloud à des fins de stockage ou de traitement. Dans ce guide de démarrage rapide, vous envoyez des données de télémétrie à partir d’une application d’appareil simulé, via IoT Hub, à une application back-end pour traitement.

Le guide de démarrage rapide utilise un exemple d’application C à partir du [kit SDK Azure IoT device SDK for C](iot-hub-device-sdk-c-intro.md) pour envoyer des données de télémétrie à un hub IoT. Les kits SDK Azure IoT device sont écrits en [ANSI C (C99)](https://wikipedia.org/wiki/C99) pour la portabilité et la compatibilité avec de nombreuses plateformes. Avant d’exécuter l’exemple de code, vous devez créer un hub IoT et inscrire l’appareil simulé avec ce hub.

Cet article est écrit pour Windows, mais vous pouvez également utiliser ce guide de démarrage rapide sur Linux.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Installez [Visual Studio 2017](https://www.visualstudio.com/vs/) avec la charge de travail [« Développement Desktop en C++ »](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) activée.
* Installez la dernière version de [Git](https://git-scm.com/download/).

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

Pour ce guide de démarrage rapide, vous devez utiliser le [kit SDK Azure IoT device SDK for C](iot-hub-device-sdk-c-intro.md). 

Vous pouvez utiliser le kit SDK en installant les packages et les bibliothèques pour les environnements suivants :

* **Linux** : les packages apt-get sont disponibles pour Ubuntu 16.04 et 18.04, avec les architectures d’UC suivantes : amd64, arm64, armhf et i386. Pour plus d’informations, consultez [Utilisation de apt-get pour créer un projet client d’appareil C sur Ubuntu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed** : pour les développeurs créant des applications d’appareil sur la plateforme mbed, nous avons publié une bibliothèque et des exemples qui les aident à démarrer en quelques minutes avec Azure IoT Hub. Pour plus d’informations, consultez [Utiliser la bibliothèque mbed](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino** : si vous développez sur Arduino, vous pouvez exploiter la bibliothèque Azure IoT disponible dans le Gestionnaire de bibliothèque de l’IDE Arduino. Pour plus d’informations, consultez [La bibliothèque Azure IoT Hub pour Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS** : le kit SDK IoT Hub Device SDK est disponible en tant que CocoaPods pour le développement d’appareils iOS et Mac. Pour plus d’informations, consultez [Exemples iOS pour Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient).

Dans cette section, en revanche, vous allez préparer un environnement de développement pour cloner et générer le [kit SDK Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) à partir de GitHub. Le kit SDK sur GitHub comprend l’exemple de code utilisé dans ce guide de démarrage rapide. 


1. Téléchargez la version 3.11.4 du [système de génération de CMake](https://cmake.org/download/). Vérifiez le binaire téléchargé à l’aide de la valeur de hachage de chiffrement correspondante. L’exemple suivant utilise Windows PowerShell pour vérifier le hachage de chiffrement pour la version 3.11.4 de la distribution MSI x64 :

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    Les valeurs de hachage suivantes pour la version 3.11.4 étaient celles indiquées sur le site de CMake au moment de la rédaction de cet article :

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    Il est important que les composants requis Visual Studio (Visual Studio et la charge de travail « Développement Desktop en C++ ») soient installés sur votre machine, **avant** de commencer l’installation de l’élément `CMake`. Une fois les composants requis en place et le téléchargement effectué, installez le système de génération de CMake.

2. Ouvrez une invite de commandes ou l’interpréteur de commandes Git Bash. Exécutez la commande suivante pour cloner le dépôt GitHub du kit [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) :
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Pour le moment, ce dépôt a une taille d’environ 220 Mo. Attendez-vous à ce que cette opération prenne plusieurs minutes.


3. Créez un sous-répertoire `cmake` dans le répertoire racine du dépôt Git et accédez à ce dossier. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Exécutez la commande suivante qui génère une version du kit SDK propre à votre plateforme cliente de développement. Une solution Visual Studio pour l’appareil simulé est générée dans le répertoire `cmake`. 

    ```cmd
    cmake ..
    ```
    
    Si `cmake` ne trouve pas votre compilateur C++, vous obtiendrez peut-être des erreurs de build lors de l’exécution de la commande ci-dessus. Si cela se produit, essayez d’exécuter cette commande dans [l’invite de commandes de Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Une fois la génération terminée, les dernières lignes de sortie doivent ressembler à la sortie suivante :

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Inscrire un appareil

Un appareil doit être inscrit dans votre hub IoT pour pouvoir se connecter. Dans cette section, vous allez utiliser l’interface Azure CLI avec l’[extension IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) pour inscrire un appareil simulé.

1. Ajoutez l’extension CLI d’IoT Hub et créez l’identité de l’appareil. Remplacez `{YourIoTHubName}` par le nom que vous avez choisi pour votre IoT Hub :

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

    Si vous choisissez un autre nom pour votre appareil, mettez à jour le nom de l’appareil dans les exemples d’applications avant de les exécuter.

2. Exécutez la commande suivante pour obtenir la _chaîne de connexion_ à l’appareil que vous venez d’inscrire :

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    Notez la chaîne de connexion à l’appareil, du type `Hostname=...=`. Vous utiliserez cette valeur plus loin dans ce démarrage rapide.


## <a name="send-simulated-telemetry"></a>Envoyer des données de télémétrie simulée

L’application d’appareil simulé se connecte à un point de terminaison spécifique à l’appareil sur votre hub IoT et envoie une chaîne sous la forme de données de télémétrie simulée.

1. À l’aide d’un éditeur de texte, ouvrez le fichier source iothub_convenience_sample.c et examinez l’exemple de code pour l’envoi des données de télémétrie. Le fichier se trouve à l’emplacement suivant :

    ```
    \azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Recherchez la déclaration de la constante `connectionString` :

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```
    Remplacez la valeur de la constante `connectionString` par la chaîne de connexion de l’appareil que vous avez notée précédemment. Enregistrez ensuite vos modifications dans **iothub_convenience_sample.c**.

3. Dans une fenêtre de terminal, accédez au répertoire du projet *iothub_convenience_sample*, dans le répertoire CMake que vous avez créé dans le kit Azure IoT C SDK.

    ```
    cd /azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Exécutez CMake au moyen de la ligne de commande suivante pour générer l’exemple avec votre valeur `connectionString` mise à jour :

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. Dans une invite de commandes, exécutez la commande suivante pour exécuter l’application d’appareil simulé :

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    La capture d’écran suivante montre la sortie lorsque l’application d’appareil simulé envoie des données de télémétrie au hub IoT :

    ![Exécuter l’appareil simulé](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Lire les données de télémétrie envoyées par votre hub


Dans cette section, vous allez utiliser l’interface Azure CLI avec l’[extension IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) pour superviser les messages d’appareil qui ne sont pas envoyés par l’appareil simulé.

1. À l’aide de l’interface Azure CLI, exécutez la commande suivante pour vous connecter et lire les messages à partir de votre hub IoT :

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![Lire les messages d’appareil à l’aide de l’interface Azure CLI](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

    

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez configuré un hub IoT, inscrit un appareil, envoyé des données de télémétrie simulée au hub avec une application C, puis lu les données de télémétrie à partir du hub par le biais de l’interface Azure CLI.

Pour en apprendre davantage sur le développement avec le kit Azure IoT Hub C SDK, continuez avec le guide pratique suivant :

> [!div class="nextstepaction"]
> [Développer avec le kit Azure IoT Hub C SDK](iot-hub-devguide-develop-for-constrained-devices.md)