---
title: Analyse de vidéo en direct à l’aide de votre propre modèle - Azure
description: Dans ce guide de démarrage rapide, vous allez appliquer la vision par ordinateur pour analyser le flux vidéo en direct à partir d’une caméra IP (simulée).
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 0b502fb4bcfa3a11890167c5ef297463a3c51cdc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261487"
---
# <a name="quickstart-analyze-live-video-with-your-own-model"></a>Démarrage rapide : Analyse de vidéo en direct à l’aide de votre propre modèle

Ce guide de démarrage illustre l’utilisation de Live Video Analytics sur IoT Edge pour analyser le flux vidéo en direct à partir d’une caméra IP (simulée) en appliquant un modèle de vision par ordinateur pour détecter des objets. Un sous-ensemble d’images du flux vidéo en direct est envoyé à un service d’inférence et ses résultats sont envoyés au hub IoT Edge. Il utilise une machine virtuelle Azure comme appareil IoT Edge et un flux vidéo en direct simulé. Cet article se base sur un exemple de code écrit en C#.

Cet article s’appuie sur [ce](detect-motion-emit-events-quickstart.md) guide de démarrage rapide. 

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) sur votre ordinateur avec les extensions suivantes :
    * [Outils IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Kit de développement logiciel (SDK) .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) installé sur votre système
* Si vous n’avez pas encore effectué [ce](detect-motion-emit-events-quickstart.md) guide de démarrage rapide, procédez comme suit :
     * [Configurer des ressources Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)

> [!TIP]
> Lors de l’installation d’Azure IoT Tools, vous pouvez être invité à installer Docker. N’hésitez pas à l’ignorer.

## <a name="review-the-sample-video"></a>Réviser l’exemple de vidéo
Dans le cadre de la procédure ci-dessus permettant de configurer les ressources Azure, une (courte) vidéo du trafic d’une autoroute va être copiée dans Azure sur la machine virtuelle Linux qui est utilisée comme appareil IoT Edge. Ce fichier vidéo permet de simuler un flux en direct pour ce didacticiel.

Vous pouvez utiliser une application, telle que [VLC Player](https://www.videolan.org/vlc/), la lancer, appuyer sur Ctrl+N et coller [ce](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) lien dans la vidéo pour commencer la lecture. Vous verrez que la distance concerne le trafic sur une autoroute, parcourue par de nombreux véhicules.

Lorsque vous exécutez les étapes ci-dessous, vous utilisez Live Video Analytics sur IoT Edge pour détecter des objets, tels que des véhicules, des personnes, etc. et publier des événements d’inférence associés sur le hub IoT Edge.

## <a name="overview"></a>Vue d’ensemble

![Vue d’ensemble](./media/quickstarts/overview-qs5.png)

Le diagramme ci-dessus montre comment les signaux circulent dans ce guide de démarrage rapide. Un module de périphérie (détaillé [ici](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) simule une caméra IP qui héberge un serveur RTSP. Un nœud de [source RTSP](media-graph-concept.md#rtsp-source) récupère le flux vidéo à partir de ce serveur et envoie des images vidéo au nœud de [processeur de filtre de fréquence d’images](media-graph-concept.md#frame-rate-filter-processor). Ce processeur limite la fréquence d’images du flux vidéo qui atteint le nœud du [processeur d’extension HTTP](media-graph-concept.md#http-extension-processor). 

Le nœud d’extension HTTP joue le rôle d’un proxy en convertissant les images vidéo en type d’image spécifié et en relayant l’image sur REST à un autre module Edge exécutant un modèle d’IA après un point de terminaison HTTP. Dans cet exemple, le module Edge est généré à l’aide du modèle [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), qui est capable de détecter de nombreux types d’objets. Le nœud de processeur d’extension HTTP collecte les résultats de la détection et publie les événements sur le nœud [récepteur du hub IoT](media-graph-concept.md#iot-hub-message-sink ), qui envoie ensuite ces événements au [hub IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

Dans ce guide de démarrage rapide, vous allez :

1. créer et déployer le graphe multimédia ;
1. interpréter les résultats ;
1. Supprimer des ressources.



## <a name="create-and-deploy-the-media-graph"></a>Créer et déployer le graphe multimédia
    
### <a name="examine-and-edit-the-sample-files"></a>Examiner et modifier les exemples de fichiers

Dans le cadre des conditions préalables, vous auriez téléchargé l’exemple de code dans un dossier. Lancez Visual Studio Code et ouvrez le dossier.

1. Dans Visual Studio Code, accédez à « src/edge ». Vous verrez le fichier. env que vous avez créé, ainsi que quelques fichiers de modèle de déploiement.

    * Le modèle de déploiement fait référence au manifeste de déploiement du périphérique avec des valeurs d’espace réservé. Le fichier .env contient les valeurs de ces variables.
1. Ensuite, accédez au dossier « src/cloud-to-device-console-app ». Vous y voyez le fichier appsettings.json que vous avez créé, ainsi que quelques autres fichiers :

    * c2d-console-app.csproj : fichier projet pour Visual Studio Code.
    * operations.json : répertorie les différentes opérations que vous souhaitez que le programme exécute.
    * Program.cs : exemple de code de programme, qui effectue les opérations suivantes :

        * Il charge les paramètres de l’application.
        *  Il invoque les méthodes directes exposées par le module Live Video Analytics sur IoT Edge. Vous pouvez utiliser le module pour analyser des flux vidéo en direct en invoquant ses [méthodes directes](direct-methods.md) 
        * Il s’interrompt pour vous permettre d’examiner la sortie du programme dans la fenêtre TERMINAL et les événements générés par le module dans la fenêtre SORTIE
        * Il invoque des méthodes directes pour nettoyer des ressources   


1. Apportez les modifications suivantes au fichier operations.json
    * Modifiez le lien vers la topologie de graphe : `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
    * Sous GraphInstanceSet, modifiez le nom de la topologie de graphe de manière à ce qu’il corresponde à la valeur du lien au-dessus de `"topologyName" : "InferencingWithHttpExtension"`
    * Sous GraphTopologyDelete, modifiez le nom `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Générez et déployez le manifeste de déploiement IoT Edge

1. Cliquez avec le bouton droit sur le fichier « src/edge/ deployment.yolov3.template.json », puis cliquez sur Générer le manifeste de déploiement IoT Edge.

    ![Générez un manifeste de déploiement IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  
1. Cela doit créer un fichier manifeste dans le dossier src/edge/config « deployment.yolov3.amd64.json ».
1. Si vous avez déjà effectué le [démarrage rapide](detect-motion-emit-events-quickstart.md), ignorez cette étape. Sinon, définissez la chaîne de connexion IoTHub en cliquant sur l’icône « Autres actions » en regard du volet AZURE IOT HUB dans l’angle inférieur gauche. Vous pouvez copier la chaîne à partir du fichier appsettings.json. (Voici une autre approche recommandée pour vous assurer que l’IoT Hub approprié est configuré dans Visual Studio Code via la [commande Sélectionner IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).
    
    ![Chaîne de connexion IoTHub](./media/quickstarts/set-iotconnection-string.png)
1. Ensuite, cliquez avec le bouton droit sur « src/edge/config/ deployment.yolov3.amd64.json », puis cliquez sur « Créer un déploiement pour un seul appareil ». 

    ![Créer un déploiement pour un seul appareil](./media/quickstarts/create-deployment-single-device.png)
1. Vous êtes ensuite invité à sélectionner un appareil IoT Hub. Sélectionnez lva-sample-device dans la liste déroulante.
1. Environ 30 secondes plus tard, actualisez Azure IoT Hub dans la section inférieure gauche et vous devriez voir le périphérique avec les modules suivants déployés :

    1. Le module Live Video Analytics, nommé « lvaEdge ».
    1. Un module nommé « rtspsim », qui simule un serveur RTSP, agissant comme source d’un flux vidéo en direct.
    1. Un module nommé « yolov3 », qui, comme le nom le suggère, est le modèle de détection de l’objet YOLOv3 qui applique la vision par ordinateur aux images et retourne plusieurs classes de types d’objets.
 
        ![Modèle de détection d’objets YOLOv3](./media/quickstarts/yolov3.png)

### <a name="prepare-for-monitoring-events"></a>Préparez-vous à des événements de surveillance

Cliquez avec le bouton droit sur l’appareil Live Video Analytics et cliquez sur « Démarrer la supervision du point de terminaison d'événement intégré ». Cette étape est nécessaire pour surveiller les événements IoT Hub et les afficher dans la fenêtre Sortie de Visual Studio Code. 

![Démarrer la surveillance](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Exécutez l'exemple de programme

1. Démarrez une session de débogage (appuyez sur F5). Vous allez commencer à voir certains messages imprimés dans la fenêtre TERMINAL.
1. Operations.json commence par appeler les méthodes directes GraphTopologyList et GraphInstanceList. Si vous avez nettoyé des ressources après les démarrages rapides précédents, cette opération renvoie des listes vides, puis s’interrompt pour que vous puissiez appuyer sur Entrée
   ```
   --------------------------------------------------------------------------
   Executing operation GraphTopologyList
   -----------------------  Request: GraphTopologyList  --------------------------------------------------
   {
   "@apiVersion": "1.0"
   }
   ---------------  Response: GraphTopologyList - Status: 200  ---------------
   {
   "value": []
   }
   --------------------------------------------------------------------------
   Executing operation WaitForInput
   Press Enter to continue
   ```

1. Lorsque vous appuyez sur la touche « Entrée » dans la fenêtre TERMINAL, l’ensemble d’appels de méthode directe suivant est créé
     * Un appel à GraphTopologySet à l’aide de topologyUrl ci-dessus
     * Un appel à GraphInstanceSet à l’aide du corps suivant
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "InferencingWithHttpExtension",
         "description": "Sample graph description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
           },
           {
             "name": "rtspUserName",
             "value": "testuser"
           },
           {
             "name": "rtspPassword",
             "value": "testpassword"
           }
         ]
       }
     }
     ```
     * Un appel à GraphInstanceActivate pour démarrer l’instance de graphe et démarrer le flux vidéo
     * Un deuxième appel à GraphInstanceList pour indiquer que l’instance de graphe est effectivement en cours d’exécution
1. La sortie de la fenêtre TERMINAL s’interrompt maintenant à l’invite « Appuyez sur Entrée pour continuer ». N’appuyez pas sur « Entrée » pour l’instant. Vous pouvez faire défiler vers le haut pour voir les charges utiles de réponse JSON pour les méthodes directes que vous avez invoquées
1. Si vous basculez maintenant vers la fenêtre SORTIE de Visual Studio Code, vous verrez les messages envoyés à IoT Hub par le module Live Video Analytics sur IoT Edge.
     * Ces messages sont traités dans les sections ci-dessous
1. Le graphe multimédia continue à s’exécuter et à imprimer les résultats : le simulateur RTSP continue de boucler la vidéo source. Pour arrêter le graphe multimédia, revenez à la fenêtre TERMINAL et appuyer sur « Entrée ». La série d’appels suivante permet de nettoyer les ressources :
     * Un appel à GraphInstanceDeactivate désactive l’instance de graphe
     * Un appel à GraphInstanceDelete supprime l’instance de graphe
     * Un appel à GraphTopologyDelete supprime la topologie
     * Un dernier appel à GraphTopologyList indique que la liste est maintenant vide

## <a name="interpret-results"></a>Interpréter les résultats

Lorsque vous exécutez le graphe multimédia, les résultats du nœud de processeur d’extension HTTP sont envoyés via le nœud du récepteur IoT Hub à IoT Hub. Les messages qui s’affichent dans la fenêtre SORTIE de Visual Studio Code contiennent une section « body » et une section « applicationProperties ». Pour comprendre ce que ces sections représentent, lisez [cet](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) article.

Dans les messages ci-dessous, les propriétés de l’application et le contenu du corps sont définis par le module Live Video Analytics. 



### <a name="mediasession-established-event"></a>Événement MediaSession établi

Lorsqu’un graphe multimédia est instancié, le nœud source RTSP tente de se connecter au serveur RTSP exécuté sur le conteneur rtspsim-live55. En cas de réussite, il affiche cet événement. Le type d’événement est Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

Notez les points suivants dans le message ci-dessus :
* Le message est un événement de diagnostic, MediaSessionEstablished, il indique que le nœud source RTSP (l’objet) a pu établir une connexion avec le simulateur RTSP et commence à recevoir un flux en direct (simulé).
* « Subject » dans applicationProperties indique que le message a été généré à partir du nœud source RTSP dans le graphe multimédia.
* « EventType » dans applicationProperties indique qu’il s’agit d’un événement de diagnostic.
* « EventTime » indique l’heure à laquelle l’événement s’est produit.
* « Body » contient des données relatives à l’événement de diagnostic, qui, dans ce cas, sont les détails [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Événement d'inférence

Le nœud de processeur d’extension HTTP reçoit les résultats d’inférence du module yolov3 et les émet via le nœud du récepteur IoT Hub en tant qu’événements d’inférence. Dans ces événements, le type est défini sur « entity » pour indiquer qu’il s’agit d’une entité, comme une voiture ou un camion, et eventTime vous indique à quelle heure (UTC) l’objet a été détecté. Vous trouverez ci-dessous un exemple dans lequel deux voitures ont été détectées avec des niveaux de confiance différents dans la même image vidéo.

```
[IoTHubMonitor] [11:37:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "entity": {
          "box": {
            "h": 0.0344108157687717,
            "l": 0.5756940841674805,
            "t": 0.5929375966389974,
            "w": 0.04484643936157227
          },
          "tag": {
            "confidence": 0.8714089393615723,
            "value": "car"
          }
        },
        "type": "entity"
      },
      {
        "entity": {
          "box": {
            "h": 0.03960910373263889,
            "l": 0.2750667095184326,
            "t": 0.6102327558729383,
            "w": 0.031027007102966308
          },
          "tag": {
            "confidence": 0.7042660713195801,
            "value": "car"
          }
        },
        "type": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-04-23T06:37:16.097Z"
  }
}
```

Notez les points suivants dans les messages ci-dessus :

* « Subject » dans applicationProperties fait référence au nœud dans la topologie de graphe à partir duquel le message a été généré. 
* « EventType » dans applicationProperties indique qu’il s’agit d’un événement Analytics.
* « EventTime » indique l’heure à laquelle l’événement s’est produit.
* « Body » contient des données relatives à l’événement Analytics. Dans ce cas, l’événement est un événement d’inférence et, par conséquent, le corps contient des données « inferences ».
* La section « inferences » indique que le « type » est « entity » et contient des données supplémentaires sur l’« entity ».

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez d’essayer les autres guides de démarrage rapide, vous devez vous en tenir aux ressources créées. Dans le cas contraire, accédez au Portail Azure et à vos groupes de ressources, sélectionnez le groupe de ressources sous lequel vous avez exécuté ce guide de démarrage rapide, puis supprimez toutes les ressources.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue les défis supplémentaires pour les utilisateurs expérimentés :

* Utilisez une [caméra IP](https://en.wikipedia.org/wiki/IP_camera) avec prise en charge de RTSP au lieu d’utiliser le simulateur RTSP. Vous pouvez consulter les caméras IP prenant RTSP en charge sur la page de produits [conformes ONVIF](https://www.onvif.org/conformant-products/) en recherchant les appareils conformes aux profils G, S ou T.
* Utilisez un périphérique Linux AMD64 ou x64 (au lieu d’utiliser une machine virtuelle Linux Azure). Cet appareil doit se trouver dans le même réseau que la caméra IP. Vous pouvez suivre les instructions figurant dans [Installer le runtime Azure IoT Edge sur Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux), puis suivre les instructions du guide de démarrage rapide [Déployer votre premier module IoT Edge sur un appareil Linux virtuel](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) pour inscrire l’appareil auprès d’Azure IoT Hub.

