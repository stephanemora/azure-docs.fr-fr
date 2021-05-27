---
author: russell-cooks
ms.topic: include
ms.service: azure-video-analyzer
ms.date: 05/03/2021
ms.author: juliako
ms.openlocfilehash: 8e9992c6005c0bd3ba86bae005a1252a753e0c99
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371836"
---
### <a name="get-the-sample-code"></a>Obtenir l’exemple de code

1. Clonez le [dépôt d’exemples Python AVA](https://github.com/Azure-Samples/video-analyzer-iot-edge-python).
1. Démarrez Visual Studio Code et ouvrez le dossier où le dépôt a été téléchargé.
1. Dans Visual Studio Code, accédez au dossier src/cloud-to-device-console-app et créez un fichier nommé **appsettings.json**. Ce fichier contient les paramètres nécessaires à l’exécution du programme.
1. Accédez au partage de fichiers dans le compte de stockage créé à l’étape de configuration ci-dessus, puis recherchez le fichier **appsettings.json** sous le partage de fichiers « deployment-output ». Cliquez sur le fichier, puis appuyez sur le bouton « Télécharger ». Le contenu doit s’ouvrir dans un nouvel onglet de navigateur, qui doit se présenter comme ceci :
   ```json
   {
     "IoThubConnectionString": "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",
     "deviceId": "avasample-iot-edge-device",
     "moduleId": "avaedge"
   }
   ```
   La chaîne de connexion IoT Hub vous permet d’utiliser Visual Studio Code pour envoyer des commandes aux modules de périphérie par le biais d’Azure IoT Hub. Copiez le code JSON ci-dessus dans le fichier **src/cloud-to-device-console-app/appsettings.json**.
1. Ensuite, accédez au dossier src/edge et créez un fichier nommé **.env**. Ce fichier contient les propriétés que Visual Studio Code utilise pour déployer des modules sur un appareil périphérique.
1. Accédez au partage de fichiers dans le compte de stockage créé à l’étape de configuration ci-dessus et recherchez le fichier **env.txt** sous le partage de fichiers « deployment-output ». Cliquez sur le fichier, puis appuyez sur le bouton « Télécharger ». Le contenu doit s’ouvrir dans un nouvel onglet de navigateur, qui doit se présenter comme ceci :
   ```
        SUBSCRIPTION_ID="<Subscription ID>"
        RESOURCE_GROUP="<Resource Group>"
        AVA_PROVISIONING_TOKEN="<Provisioning token>"
        VIDEO_INPUT_FOLDER_ON_DEVICE="/home/localedgeuser/samples/input"
        VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
        APPDATA_FOLDER_ON_DEVICE="/var/lib/videoAnalyzer"
        CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"
        CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"
   ```
   Copiez le code JSON à partir de votre fichier **env.txt** dans le fichier **src/edge/.env**.

### <a name="connect-to-the-iot-hub"></a>Se connecter au hub IoT

1.  Dans Visual Studio Code, définissez la chaîne de connexion IoT Hub en sélectionnant l’icône **Plus d’actions** à côté du volet **AZURE IOT HUB** en bas à gauche. Copiez la chaîne à partir du fichier src/cloud-to-device-console-app/appsettings.json.

    <!-- commenting out the image for now ![Set IoT Hub connection string]()./media/quickstarts/set-iotconnection-string.png-->

    > [!NOTE]
    > Vous pouvez être invité à fournir des informations sur le point de terminaison intégré pour le hub IoT. Pour obtenir ces informations, dans le portail Azure, accédez à votre hub IoT et recherchez l’option **Points de terminaison intégrés** dans le volet de navigation gauche. Cliquez et recherchez l’option **Point de terminaison compatible Event Hub** sous la section **Point de terminaison compatible Event Hub**. Copiez et utilisez le texte dans la zone. Le point de terminaison doit ressembler à ceci : <br/>

    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1.  Après environ 30 secondes, actualisez Azure IoT Hub dans la section inférieure gauche. Vous devez voir l’appareil de périphérie `avasample-iot-edge-device`, dont les modules suivants sont normalement déployés :
    - Edge Hub (nom de module **edgeHub**)
    - Edge Agent (nom de module **edgeAgent**)
    - Video Analyzer (nom de module **avaedge**)
    - Simulateur RTSP (nom du module **rtspsim**)

### <a name="prepare-to-monitor-the-modules"></a>Préparer la surveillance des modules

Si vous exécutez ce guide de démarrage rapide ou ce tutoriel, des événements sont envoyés au hub IoT. Pour voir ces événements, effectuez les étapes suivantes :

1.  Ouvrez le volet Explorateur dans Visual Studio Code, puis recherchez **Azure IoT Hub** dans l’angle inférieur gauche.
1.  Développez le nœud **Appareils**.
1.  Cliquez avec le bouton droit sur `avasample-iot-edge-device`, puis sélectionnez **Démarrer la supervision du point de terminaison d’événement intégré**.

    > [!NOTE]
    > Vous pouvez être invité à fournir des informations sur le point de terminaison intégré pour le hub IoT. Pour obtenir ces informations, dans le portail Azure, accédez à votre hub IoT et recherchez l’option **Points de terminaison intégrés** dans le volet de navigation gauche. Cliquez et recherchez l’option **Point de terminaison compatible Event Hub** sous la section **Point de terminaison compatible Event Hub**. Copiez et utilisez le texte dans la zone. Le point de terminaison doit ressembler à ceci :

    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
