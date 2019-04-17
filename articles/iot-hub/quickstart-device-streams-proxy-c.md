---
title: Démarrage rapide C sur les flux d’appareil Azure IoT Hub pour SSH/RDP (préversion) | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous allez exécuter un exemple d’application C qui joue le rôle d’un proxy pour activer des scénarios SSH/RDP sur des flux d’appareil IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 1468268e407eeac6196c8e8e4db0fc5a52ca09c7
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501567"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-c-proxy-application-preview"></a>Démarrage rapide : SSH/RDP sur des flux d’appareil IoT Hub à l’aide d’une application proxy C (préversion)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub prend actuellement en charge les flux d’appareil en tant que [fonctionnalité d’évaluation](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les [flux d’appareil IoT Hub](./iot-hub-device-streams-overview.md) permettent aux applications de service et d’appareil de communiquer de manière sécurisée à travers des pare-feux. Consultez [cette page](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) pour obtenir une vue d’ensemble de la configuration.

Le présent document décrit la configuration du tunneling du trafic SSH (qui utilise le port 22) par le biais de flux d’appareil. La configuration pour le trafic RDP est similaire et nécessite une simple modification. Étant donné que les flux d’appareil ne dépendent pas des applications et des protocoles, vous pouvez modifier le présent guide de démarrage rapide (en modifiant les ports de communication) pour prendre en charge d’autres types de trafic d’application.

## <a name="how-it-works"></a>Fonctionnement

La figure ci-dessous illustre la manière dont la configuration des programmes de proxy locaux d’appareil (et de service) active une connectivité de bout en bout entre le client SSH et les processus de démon SSH. Dans la préversion publique, le SDK C prend uniquement en charge les flux d’appareil côté appareil. Ainsi, le présent guide de démarrage rapide aborde uniquement les instructions permettant d’exécuter l’application proxy locale de l’appareil. Vous devez exécuter une application proxy locale de service complémentaire, disponible dans les guides de [démarrage rapide C#](./quickstart-device-streams-proxy-csharp.md) ou de [démarrage rapide Node.js](./quickstart-device-streams-proxy-nodejs.md).

![Texte de remplacement](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg "Configuration de proxy local")

1. Le proxy local de service se connecte au hub IoT et lance un flux d’appareil sur l’appareil cible.

2. Le proxy local d’appareil termine la négociation du lancement du flux et établit un tunnel de streaming de bout en bout par le biais du point de terminaison de streaming IoT Hub côté service.

3. Le proxy local d’appareil se connecte au démon SSH (SSHD) qui écoute le port 22 de l’appareil (ce dernier est configurable, comme décrit [ci-dessous](#run-the device-local-proxy-application)).

4. Le proxy local de service attend de nouvelles connexions SSH de l’utilisateur en écoutant un port désigné, dans cet exemple, le port 2222 (celui-ci est également configurable, comme décrit [ci-dessous](#run-the-device-local-proxy-application)). Quand l’utilisateur se connecte par le biais d’un client SSH, le tunnel permet au trafic d’application SSH d’être transféré entre les programmes client et serveur SSH.

> [!NOTE]
> Le trafic SSH envoyé sur un flux d’appareil est traité par tunnel par le biais d’un point de terminaison de streaming IoT Hub, plutôt que directement entre le service et l’appareil. Ce processus offre [ces avantages](./iot-hub-device-streams-overview.md#benefits). De plus, la figure illustre le démon SSH en cours d’exécution sur le même appareil (ou ordinateur) en tant que proxy local de l’appareil. Dans ce guide de démarrage rapide, le fait de fournir l’adresse IP du démon SSH permet au proxy local de l’appareil et au démon de s’exécuter également sur des ordinateurs différents.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Pour le moment, la préversion des flux d’appareil est uniquement prise en charge pour les hubs IoT créés dans les régions suivantes :

  * **USA Centre**
  * **EUAP USA Centre**

* Installez [Visual Studio 2017](https://www.visualstudio.com/vs/) avec la charge de travail [« Développement Desktop en C++ »](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) activée.
* Installez la dernière version de [Git](https://git-scm.com/download/).
* Exécutez la commande suivante afin d’ajouter l’extension Microsoft Azure IoT pour Azure CLI à votre instance Cloud Shell. L’extension IoT ajoute des commandes IoT Hub, IoT Edge et IoT Device Provisioning Service (DPS) à Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

Pour ce guide de démarrage rapide, vous devez utiliser le [kit SDK Azure IoT device SDK for C](iot-hub-device-sdk-c-intro.md). Vous allez préparer un environnement de développement pour cloner et générer le [SDK C Azure IoT](https://github.com/Azure/azure-iot-sdk-c) à partir de GitHub. Le kit SDK sur GitHub comprend l’exemple de code utilisé dans ce guide de démarrage rapide. 

1. Téléchargez le [système de génération CMake](https://cmake.org/download/).

    Il est important que les composants requis Visual Studio (Visual Studio et la charge de travail « Développement Desktop en C++ ») soient installés sur votre machine, **avant** de commencer l’installation de l’élément `CMake`. Une fois les composants requis en place et le téléchargement effectué, installez le système de génération de CMake.

2. Ouvrez une invite de commandes ou l’interpréteur de commandes Git Bash. Exécutez la commande suivante pour cloner le référentiel GitHub du [Kit de développement logiciel (SDK) Azure IoT pour C](https://github.com/Azure/azure-iot-sdk-c) :
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```
    Attendez-vous à ce que cette opération prenne plusieurs minutes.

3. Créez un sous-répertoire `cmake` dans le répertoire racine du référentiel Git et accédez à ce dossier. 

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Exécutez les commandes suivantes à partir du répertoire `cmake` pour générer une version du kit SDK propre à votre plateforme cliente de développement.

   * Dans Linux :

      ```bash
      cmake ..
      make -j
      ```

   * Dans Windows, exécutez les commandes suivantes à l’invite de commandes développeur pour Visual Studio 2015 ou 2017. Une solution Visual Studio pour l’appareil simulé est générée dans le répertoire `cmake`.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Inscrire un appareil

Un appareil doit être inscrit dans votre hub IoT pour pouvoir se connecter. Dans cette section, vous allez utiliser Azure Cloud Shell avec l’[extension IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) pour inscrire un appareil simulé.

1. Exécutez les commandes suivantes dans Azure Cloud Shell pour créer l’identité d’appareil.

   **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

   **MyDevice** : il s’agit du nom donné à l’appareil inscrit. Utilisez MyDevice comme indiqué. Si vous choisissez un autre nom pour votre appareil, vous devez également utiliser ce nom pour l’ensemble de cet article et mettre à jour le nom de l’appareil dans les exemples d’application avant de les exécuter.

    ```azurecli-interactive
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

## <a name="ssh-to-a-device-via-device-streams"></a>Liaison SSH à un appareil par le biais de flux d’appareil

### <a name="run-the-device-local-proxy-application"></a>Exécuter l’application proxy locale de l’appareil

1. Modifiez le fichier source `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample/iothub_client_c2d_streaming_proxy_sample.c` et indiquez la chaîne de connexion de votre appareil, le nom d’hôte/adresse IP cible ainsi que le port SSH 22 :

   ```C
   /* Paste in the your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

2. Compilez l’exemple :

   ```bash
    # In Linux
    # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
    make -j
   ```

   ```cmd
    rem In Windows
    rem Go to cmake at root of repository
    cmake --build . -- /m /p:Configuration=Release
   ```

3. Exécutez le programme compilé sur l’appareil :

   ```bash
    # In Linux
    # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
    ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
    rem In Windows
    rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
    iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>Exécuter l’application proxy locale du service

Comme indiqué [précédemment](#how-it-works), l’établissement d’un flux de bout en bout pour tunneliser le trafic SSH nécessite un proxy local à chaque extrémité (au niveau du service et de l’appareil). Dans la préversion publique, le SDK C IoT Hub prend uniquement en charge les flux d’appareil côté appareil. Pour générer et exécuter le proxy local de service, suivez les étapes disponibles dans le [démarrage rapide C#](./quickstart-device-streams-proxy-csharp.md) ou le [démarrage rapide Node.js](./quickstart-device-streams-proxy-nodejs.md).

### <a name="establish-an-ssh-session"></a>Établir une session SSH

Dès lors que les proxys locaux d’appareil et de service sont tous deux en cours d’exécution, utilisez votre programme client SSH et connectez-vous au proxy local de service sur le port 2222 (au lieu du démon SSH directement).

```cmd/sh
ssh <username>@localhost -p 2222
```

À ce stade, l’invite de connexion SSH s’affiche pour vous permettre d’entrer vos informations d’identification.

Sortie de console sur le proxy local de l’appareil qui se connecte au démon SSH à l’adresse `IP_address:22` : ![Texte de remplacement](./media/quickstart-device-streams-proxy-c/device-console-output.PNG "Sortie du proxy local d’appareil")

Sortie de console du programme client SSH (le client SSH communique avec le démon SSH en se connectant au port 22 qu’écoute le proxy local du service) : ![Texte de remplacement](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png "Sortie du client SSH")

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez configuré un hub IoT, inscrit un appareil, déployé un programme proxy local d’appareil (et de service) pour établir un flux d’appareil par le biais d’IoT Hub, puis vous avez utilisé les proxys pour traiter par tunnel le trafic SSH.

Utilisez les liens ci-dessous pour en savoir plus sur les flux d’appareils :

> [!div class="nextstepaction"]
> [Vue d’ensemble des flux d’appareil](./iot-hub-device-streams-overview.md)
