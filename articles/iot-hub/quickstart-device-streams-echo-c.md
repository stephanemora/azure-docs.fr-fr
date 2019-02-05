---
title: Démarrage rapide C sur les flux d’appareil Azure IoT Hub (préversion) | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous allez exécuter une application côté service C qui communique avec un appareil IoT par le biais d’un flux d’appareil.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 24b00a589454bfa8413cd98407c2022671cb92ce
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54887952"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Démarrage rapide : Communiquer avec une application d’appareil dans C par le biais de flux d’appareil IoT Hub (préversion)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Les [flux d’appareil IoT Hub](./iot-hub-device-streams-overview.md) permettent aux applications de service et d’appareil de communiquer de manière sécurisée à travers des pare-feux. Dans la préversion publique, le SDK C prend uniquement en charge les flux d’appareil côté appareil. Par conséquent, le présent guide de démarrage rapide couvre uniquement les instructions permettant d’exécuter l’application côté appareil. Vous devez exécuter une application côté service complémentaire, disponible dans les guides de [démarrage rapide C#](./quickstart-device-streams-echo-csharp.md) ou [démarrage rapide Node.js](./quickstart-device-streams-echo-nodejs.md).

L’application C côté appareil utilisée dans ce guide de démarrage rapide présente les fonctionnalités suivantes :

* Établissez un flux d’appareil vers un appareil IoT.

* Recevez des données envoyées à partir du côté service et renvoyez-les.

Le code illustre le processus de lancement d’un flux d’appareil, ainsi que la manière d’envoyer et de recevoir des données.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Installez [Visual Studio 2017](https://www.visualstudio.com/vs/) avec la charge de travail [« Développement Desktop en C++ »](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) activée.
* Installez la dernière version de [Git](https://git-scm.com/download/).

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

Pour ce guide de démarrage rapide, vous devez utiliser le [kit SDK Azure IoT device SDK for C](iot-hub-device-sdk-c-intro.md). Vous allez préparer un environnement de développement pour cloner et générer le [SDK C Azure IoT](https://github.com/Azure/azure-iot-sdk-c) à partir de GitHub. Le kit SDK sur GitHub comprend l’exemple de code utilisé dans ce guide de démarrage rapide. 


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

2. Ouvrez une invite de commandes ou l’interpréteur de commandes Git Bash. Exécutez la commande suivante pour cloner le référentiel GitHub du [Kit de développement logiciel (SDK) Azure IoT pour C](https://github.com/Azure/azure-iot-sdk-c) :
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Pour le moment, ce référentiel a une taille d’environ 220 Mo.

3. Créez un sous-répertoire `cmake` dans le répertoire racine du référentiel Git et accédez à ce dossier. 

    ```
    cd azure-iot-sdk-c
    git checkout public-preview
    mkdir cmake
    cd cmake
    ```

4. Exécutez la commande suivante qui génère une version du kit SDK propre à votre plateforme cliente de développement. Sur Windows, une solution Visual Studio pour l’appareil simulé est générée dans le répertoire `cmake`. 

```
    # In Linux
    cmake ..
    make -j
```

Sur Windows, exécutez les commandes suivantes à l’invite de commandes développeur Visual Studio 2015 ou 2017 :

```
    # In Windows
    # For VS2015
    cmake .. -G "Visual Studio 15 2015"
    
    # Or for VS2017
    cmake .. -G "Visual Studio 15 2017

    # Then build the project
    cmake --build . -- /m /p:Configuration=Release
```

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Inscrire un appareil

Un appareil doit être inscrit dans votre hub IoT pour pouvoir se connecter. Dans cette section, vous allez utiliser Azure Cloud Shell avec l’[extension IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) pour inscrire un appareil simulé.

1. Exécutez les commandes suivantes dans Azure Cloud Shell pour ajouter l’extension CLI IoT Hub et créer l’identité d’appareil. 

   **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

   **MyDevice** : il s’agit du nom donné à l’appareil inscrit. Utilisez MyDevice comme indiqué. Si vous choisissez un autre nom pour votre appareil, vous devez également utiliser ce nom pour l’ensemble de cet article et mettre à jour le nom de l’appareil dans les exemples d’application avant de les exécuter.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Exécutez les commandes suivantes dans Azure Cloud Shell pour obtenir la _chaîne de connexion_ à l’appareil que vous venez d’inscrire :

   **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Notez la chaîne de connexion de l’appareil, qui ressemble à l’exemple suivant :

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Vous utiliserez cette valeur plus loin dans ce démarrage rapide.


## <a name="communicate-between-device-and-service-via-device-streams"></a>Communiquer entre appareil et service par le biais de flux d’appareil

### <a name="run-the-device-side-application"></a>Exécuter l’application côté appareil

Pour exécuter l’application côté appareil, vous avez besoin d’effectuer les étapes suivantes :
- Configurez votre environnement de développement en suivant les instructions données dans cet [article sur les flux d’appareil](https://github.com/Azure/azure-iot-sdk-c-tcpstreaming/blob/master/iothub_client/readme.md#compiling-the-device-sdk-for-c).

- Indiquez les informations d’identification de votre appareil en modifiant le fichier source `iothub_client/samples/iothub_client_c2d_streaming_sample/iothub_client_c2d_streaming_sample.c` et indiquez la chaîne de connexion de votre appareil.
```C
  /* Paste in the your iothub connection string  */
  static const char* connectionString = "[device connection string]";
```

- Compilez le code comme suit :

```
  # In Linux
  # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  make -j


  # In Windows
  # Go to the cmake folder at the root of repo
  cmake --build . -- /m /p:Configuration=Release
```

- Exécutez le programme compilé :

```
  # In Linux
  # Go to sample's folder
  cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  ./iothub_client_c2d_streaming_sample


  # In Windows
  # Go to sample's release folder
  cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
  iothub_client_c2d_streaming_sample.exe
```

### <a name="run-the-service-side-application"></a>Exécuter l’application côté service

Comme mentionné précédemment, le SDK C IoT Hub prend uniquement en charge des flux d’appareil côté appareil. Pour l’application côté service, utilisez les programmes de service complémentaires disponibles dans les guides de démarrage rapide pour [C#](./quickstart-device-streams-echo-csharp.md) ou [Node.js](./quickstart-device-streams-echo-nodejs.md).


## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez configuré un hub IoT, inscrit un appareil, envoyé des données de télémétrie simulées au hub avec une application C, puis lu les données de télémétrie à partir du hub grâce à Azure Cloud Shell.

Utilisez les liens ci-dessous pour en savoir plus sur les flux d’appareil :

> [!div class="nextstepaction"]
> [Vue d’ensemble des flux d’appareil](./iot-hub-device-streams-overview.md)