---
ms.openlocfilehash: 94044e95e83742487a0d4d650814a5324f07011a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750401"
---
Le manifeste de déploiement définit les modules qui sont déployés sur un appareil de périphérie. Il définit également les paramètres de configuration de ces modules. 

Effectuez les étapes suivantes pour générer le manifeste à partir du fichier de modèle, puis déployez-le sur l’appareil de périphérie.

1. Ouvrez Visual Studio Code.
1. En regard du volet **AZURE IOT HUB**, sélectionnez l’icône **Autres actions** pour définir la chaîne de connexion IoT Hub. Vous pouvez copier la chaîne à partir du fichier *src/cloud-to-device-console-app/appsettings.json*. 

    ![Définir la chaîne de connexion Azure IoT](../../../media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Vous pouvez être invité à fournir des informations sur le point de terminaison intégré pour le hub IoT. Pour obtenir ces informations, dans le portail Azure, accédez à votre hub IoT et recherchez l’option **Points de terminaison intégrés** dans le volet de navigation gauche. Cliquez et recherchez l’option **Point de terminaison compatible Event Hub** sous la section **Point de terminaison compatible Event Hub**. Copiez et utilisez le texte dans la zone. Le point de terminaison doit ressembler à ceci :  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Cliquez avec le bouton droit sur **src/edge/deployment.template.json**, puis sélectionnez **Générez un manifeste de déploiement IoT Edge**.

    ![Générer le manifeste de déploiement IoT Edge](../../../media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Cette action doit créer un fichier manifeste nommé *deployment.amd64.json* dans le dossier *src/edge/config*.
1. Cliquez avec le bouton droit sur **src/edge/config/deployment.amd64.json**, sélectionnez **Créer un déploiement pour un seul appareil**, puis sélectionnez le nom de votre appareil de périphérie.

    ![Créer un déploiement pour un seul appareil](../../../media/quickstarts/create-deployment-single-device.png)

1. Quand vous êtes invité à sélectionner un appareil IoT Hub, choisissez **lva-sample-device** dans le menu déroulant.
1. Après environ 30 secondes, dans l’angle en bas à gauche de la fenêtre, actualisez Azure IoT Hub. L’appareil de périphérie affiche maintenant les modules déployés suivants :

    * Live Video Analytics sur IoT Edge (nom de module`lvaEdge`)
    * Simulateur RTSP (Real-Time Streaming Protocol) (nom de module `rtspsim`)

Le module de simulateur RTSP simule un flux vidéo en direct à l’aide d’un fichier vidéo qui a été copié sur votre appareil de périphérie quand vous avez exécuté le [script d’installation des ressources Live Video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

> [!NOTE]
> Si vous utilisez votre propre périphérique au lieu de celui approvisionné par notre script de configuration, accédez à votre périphérique et exécutez les commandes suivantes avec des **droits d’administrateur** pour extraire et stocker l’exemple de fichier vidéo utilisé pour ce démarrage rapide :  

```
mkdir /home/lvaedgeuser/samples      
mkdir /home/lvaedgeuser/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
```
À ce stade, les modules sont déployés, mais aucun graphe multimédia n’est actif.
