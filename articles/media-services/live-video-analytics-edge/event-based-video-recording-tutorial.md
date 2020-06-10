---
title: 'Tutoriel : Enregistrement vidéo basé sur les événements et lecture vidéo dans le cloud - Azure'
description: Dans ce tutoriel, vous allez apprendre à utiliser Live Video Analytics sur IoT Edge pour effectuer un enregistrement vidéo basé sur les événements et lire cette vidéo dans le cloud.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 92367634a2f5785ecbb102db1e03f3d5f12d744e
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300822"
---
# <a name="tutorial-event-based-video-recording-to-cloud-and-playback-from-cloud"></a>Tutoriel : Enregistrement vidéo basé sur les événements et lecture vidéo dans le cloud

Dans ce tutoriel, vous allez apprendre à utiliser Live Video Analytics sur IoT Edge pour enregistrer certaines parties d’une source vidéo live dans Media Services, dans le cloud. Ici, nous appellerons ce cas d’usage un [enregistrement vidéo basé sur les événements](event-based-video-recording-concept.md). Pour procéder à l’enregistrement, vous allez utiliser un modèle d’intelligence artificielle de détection d’objets afin de rechercher des objets dans la vidéo et d’enregistrer des clips vidéo uniquement lorsqu’un certain type d’objet est détecté. Vous allez également découvrir comment lire les clips vidéo enregistrés à l’aide de Media Services. Ceci est utile pour un grand nombre de scénarios dans lesquels il est nécessaire de conserver une archive des clips vidéo d’intérêt.

> [!div class="checklist"]
> * Configurer les ressources appropriées
> * Examiner le code qui effectue l’enregistrement EVR
> * Exécuter l’exemple de code
> * Examiner les résultats et regarder la vidéo

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée  

Nous vous recommandons de lire les pages de documentation suivantes

* [Présentation de Live Video Analytics sur IoT Edge](overview.md)
* [Terminologie de Live Video Analytics sur IoT Edge](terminology.md)
* [Concepts relatifs aux graphes multimédia](media-graph-concept.md) 
* [Enregistrement de vidéo basé sur les événements](event-based-video-recording-concept.md)
* [Tutoriel : Développement d’un module IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Modification de deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Section expliquant [comment déclarer des routes dans le manifeste de déploiement IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)

## <a name="prerequisites"></a>Prérequis

Les prérequis pour ce tutoriel sont les suivants :

* [Visual Studio Code](https://code.visualstudio.com/) sur votre machine de développement avec l’extension [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools), ainsi que l’extension [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > Vous serez peut-être invité à installer Docker. Vous pouvez ignorer cette invitation.
* Le [SDK .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) sur votre machine de développement.
* Avoir lu [Script d’installation des ressources Live Video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) et [Configurer l’environnement](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

À la fin de la procédure ci-dessus, certaines ressources Azure seront déployées dans l’abonnement Azure, notamment :

* IoT Hub
* Compte de stockage
* Compte Azure Media Services
* Machine virtuelle Linux dans Azure, avec le [runtime IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) installé

## <a name="concepts"></a>Concepts

Un enregistrement vidéo basé sur les événements est un enregistrement vidéo qui a été déclenché par un événement. Cet événement peut être généré par le traitement du signal vidéo (par exemple, lors de la détection d’un objet mobile dans la vidéo) ou par une source indépendante (par exemple, l’ouverture d’une porte). Vous pouvez également déclencher l’enregistrement uniquement lorsqu’un service d’inférence détecte qu’un événement particulier s’est produit.  Dans ce tutoriel, vous allez utiliser une vidéo de véhicules qui roulent sur une autoroute et enregistrer des clips vidéo chaque fois qu’un camion est détecté.

![Graphe multimédia](./media/event-based-video-recording-tutorial/overview.png)

Le diagramme ci-dessus est une représentation graphique d’un [graphe multimédia](media-graph-concept.md) et des modules supplémentaires qui accomplissent le scénario souhaité. Quatre modules IoT Edge sont impliqués :

* Le module Live Video Analytics sur IoT Edge.
* Un module Edge exécutant un modèle d’intelligence artificielle derrière un point de terminaison HTTP. Ce module d’intelligence artificielle utilise le modèle [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), qui est capable de détecter de nombreux types d’objets.
* Un module personnalisé pour compter et filtrer les objets (appelé Object Counter ou « compteur d’objets » dans le diagramme ci-dessus) que vous allez créer et déployer dans ce tutoriel.
* Un [module de simulateur RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) pour simuler une caméra RTSP.
    
    Comme le montre le diagramme, vous allez utiliser un nœud de [source RTSP](media-graph-concept.md#rtsp-source) dans le graphe multimédia afin de capturer la simulation de vidéo en direct (du trafic sur l’autoroute) et envoyer cette vidéo vers deux chemins.

* Le premier chemin correspond à un [processeur de filtre des fréquences d’images](media-graph-concept.md#frame-rate-filter-processor) qui génère des trames vidéo à la fréquence d’images spécifiée (réduite). Ces trames vidéo sont envoyées à un nœud d’extension HTTP, qui relaie ensuite les trames (en tant qu’images) vers le module d’intelligence artificielle (YOLO v3, le détecteur d’objets) et reçoit les résultats, qui seront les objets (véhicules du trafic) détectés par le modèle. Le nœud d’extension HTTP publie ensuite les résultats via le nœud récepteur de messages IoT Hub dans le hub IoT Edge.
* Le module compteur d’objets est configuré pour recevoir des messages du hub IoT Edge, notamment les résultats de la détection des objets (véhicules du trafic). Il recherche dans ces messages les objets d’un certain type (configuré par le biais d’un paramètre). Lorsqu’un tel objet est trouvé, ce module envoie un message au hub IoT Edge. Les messages « object found » (objet trouvé) sont ensuite routés vers le nœud source IoT Hub du graphe multimédia. À réception d’un tel message, le nœud source IoT Hub du graphe multimédia déclenche le nœud [signal gate processor](media-graph-concept.md#signal-gate-processor) (processeur de signal porte), ce qui entraîne l’ouverture de celui-ci pendant la période spécifiée. Pendant cette période, la vidéo passe par la porte pour aller au nœud récepteur d’actifs multimédias. Cette partie du stream en direct est ensuite enregistrée via le nœud [asset sink](media-graph-concept.md#asset-sink) (récepteur d’actifs multimédias) dans un [actif multimédia](terminology.md#asset) de votre compte Azure Media Service.

## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement

Avant de commencer, vérifiez que vous avez bien exécuté le troisième point de la section [Prérequis](#prerequisites). Une fois le script d’installation des ressources terminé, cliquez sur les accolades pour exposer la structure du dossier. Vous verrez quelques fichiers créés sous le répertoire ~/clouddrive/lva-sample.

![Paramètres de l’application](./media/quickstarts/clouddrive.png)

Les fichiers qui nous intéressent dans le cadre de ce tutoriel sont les suivants :

* ~/clouddrive/lva-sample/edge-deployment/.env  - contient des propriétés que Visual Studio Code utilise pour déployer des modules sur un appareil de périphérie.
* ~/clouddrive/lva-sample/appsetting.json - utilisé par Visual Studio Code pour exécuter l’exemple de code.

Vous aurez besoin de ces fichiers pour la procédure ci-dessous.

1. Clonez le référentiel à partir d’ici https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Lancez Visual Studio Code et ouvrez le dossier dans lequel vous avez téléchargé le dépôt.
1. Dans Visual Studio Code, accédez au dossier « src/cloud-to-device-console-app » et créez un fichier nommé « appsettings.json ». Ce fichier contiendra les paramètres nécessaires à l’exécution du programme.
1. Copiez le contenu du fichier ~/clouddrive/lva-sample/appsettings.json. Le texte doit ressembler à l’exemple suivant :

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```

    La chaîne de connexion IoT Hub vous permet d’utiliser Visual Studio Code pour envoyer des commandes aux modules Edge via Azure IoT Hub.
    
1. Ensuite, accédez au dossier « src/edge » et créez un fichier nommé « .env ».
1. Copiez le contenu à partir du fichier ~/clouddrive/lva-sample/.env. Le texte doit ressembler à l’exemple suivant :

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-template-file"></a>Étudier le fichier de modèle 

À l’étape précédente, vous avez lancé Visual Studio Code et ouvert le dossier contenant l’exemple de code.

Dans Visual Studio Code, accédez à « src/edge ». Vous verrez le fichier .env que vous avez créé, ainsi que quelques fichiers de modèle de déploiement. Ce modèle définit les modules de périphérie que vous allez déployer sur l’appareil de périphérie (la machine virtuelle Azure Linux). Le fichier .env contient des valeurs pour les variables utilisées dans ces modèles, telles que les informations d’identification Media Service.

Ouvrez « src/edge/deployment.objectCounter.template.json ». Notez qu’il existe quatre entrées sous la section « modules », qui correspondent aux éléments listés ci-dessus (section Concepts) :

* lvaEdge : module Live Video Analytics sur IoT Edge
* yolov3 : module IA créé à l’aide du modèle YOLO v3
* rtspsim : simulateur RTSP
* objectCounter : il s’agit du module qui recherche des objets dans les résultats de yolov3

Pour le module objectCounter, regardez la chaîne (${MODULES.objectCounter}) utilisée pour la valeur « image » : celle-ci est basée sur le [tutoriel](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux) concernant le développement d’un module IoT Edge. Visual Studio Code reconnaît automatiquement que le code du module Object Counter se trouve sous « src/edge/modules/objectCounter ». 

Lisez [cette section](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) qui explique comment déclarer des routes dans le manifeste de déploiement IoT Edge, puis étudiez les routes du fichier JSON du modèle. Remarque :

* LVAToObjectCounter est utilisé pour envoyer certains événements à un point de terminaison du module objectCounter.
* ObjectCounterToLVA est utilisé pour envoyer un événement déclencheur à un point de terminaison (qui doit être le nœud source IoT Hub) du module lvaEdge.
* objectCounterToIoTHub est utilisé comme outil de débogage permettant de voir la sortie d’objectCounter lorsque vous exécutez ce tutoriel.

> [!NOTE]
> Vérifiez les propriétés souhaitées pour le module objectCounter, qui sont configurées pour rechercher les objets portant l’étiquette « truck » (camion), avec un niveau de confiance d’au moins 50 %.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Générez et déployez le manifeste de déploiement IoT Edge 

Le manifeste de déploiement définit les modules qui sont déployés sur un périphérique et les paramètres de configuration de ces modules. Effectuez les étapes suivantes pour générer un tel manifeste à partir du fichier de modèle, puis déployez-le sur l’appareil de périphérie.

À l’aide de Visual Studio Code, suivez [ces instructions](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux#build-and-push-your-solution) pour vous connecter à Docker, ainsi que les instructions de la section « Créer et envoyer (push) la solution IoT Edge ». Veillez à utiliser src/edge/deployment.objectCounter.template.json pour cette étape.

![Créer et envoyer (push) la solution IoT Edge](./media/event-based-video-recording-tutorial/build-push.png)

Cela va créer le module objectCounter pour le comptage des objets et envoyer (push) l’image à Azure Container Registry (ACR).

* Vérifiez que les variables d’environnement CONTAINER_REGISTRY_USERNAME_myacr et CONTAINER_REGISTRY_PASSWORD_myacr sont définies dans le fichier .env.

L’étape ci-dessus permet de créer le manifeste de déploiement IoT Edge dans src/edge/config/deployment.objectCounter.amd64.json. Cliquez avec le bouton droit sur ce fichier, puis cliquez sur « Créer un déploiement pour un seul appareil ».

![Créer un déploiement pour un seul appareil](./media/quickstarts/create-deployment-single-device.png)

S’il s’agit de votre premier tutoriel avec Live Video Analytics sur IoT Edge, Visual Studio Code vous invite à entrer la chaîne de connexion IoT Hub. Vous pouvez copier la chaîne à partir du fichier appsettings.json.

Visual Studio Code vous demande ensuite de sélectionner un appareil IoT Hub. Sélectionnez votre appareil IoT Edge (« lva-sample-device »).

À ce stade, le déploiement des modules de périphérie sur votre appareil IoT Edge a démarré.
Après environ 30 secondes, actualisez Azure IoT Hub dans la section située en bas à gauche de Visual Studio Code. Vous devriez voir que 4 modules ont été déployés (lvaEdge, rtspsim, yolov3 et objectCounter).

![4 modules déployés](./media/event-based-video-recording-tutorial/iot-hub.png)

## <a name="prepare-for-monitoring-events"></a>Préparez-vous à des événements de supervision

Pour afficher les événements du module compteur d’objets et du module Live Video Analytics sur IoT Edge, effectuez les étapes suivantes :

1. Ouvrez le volet Explorateur dans Visual Studio Code et recherchez Azure IoT Hub dans l’angle inférieur gauche.
1. Développez le nœud Appareils.
1. Cliquez avec le bouton droit sur lva-sample-device et choisissez l’option **Démarrer la supervision de l’événement intégré**.

![Démarrer la supervision du point de terminaison d’événement intégré](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Exécuter le programme

1. Dans Visual Studio Code, accédez à « src/cloud-to-device-console-app/operations.json ».

1. Sous le nœud GraphTopologySet, modifiez les points suivants :

    `"topologyUrl" : "https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json"`
    
1. Ensuite, sous les nœuds GraphInstanceSet et GraphTopologyDelete, modifiez :

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
1. Démarrez une session de débogage (appuyez sur F5). Vous allez commencer à voir certains messages imprimés dans la fenêtre TERMINAL.

1. Operations.json commence par appeler GraphTopologyList et GraphInstanceList. Si vous avez nettoyé des ressources après avoir suivi des tutoriels ou des guides de démarrage rapide, cette opération renvoie des listes vides, puis s’interrompt pour que vous puissiez appuyer sur Entrée, comme ci-dessous :

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
    1. Lorsque vous appuyez sur la touche « Entrée » dans la fenêtre TERMINAL, l’ensemble d’appels de méthode directe suivant est créé.
     * Un appel à GraphTopologySet à l’aide de topologyUrl ci-dessus.
     * Un appel à GraphInstanceSet à l’aide du corps suivant.
     
        ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "EVRtoAssetsOnObjDetect",
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
     
1. La sortie de la fenêtre TERMINAL s’interrompt maintenant à l’invite « Appuyez sur Entrée pour continuer ». N’appuyez pas sur « Entrée » pour l’instant. Vous pouvez faire défiler vers le haut afin de voir les charges utiles de réponse JSON pour les méthodes directes que vous avez invoquées.

1. Si vous basculez maintenant vers la fenêtre SORTIE de Visual Studio Code, vous verrez les messages envoyés à IoT Hub par le module Live Video Analytics sur IoT Edge.

     * Ces messages sont abordés dans la section qui suit.
     
1. L’instance de graphe continue à s’exécuter et à enregistrer la vidéo : le simulateur RTSP continue de boucler la vidéo source. Passez en revue les messages comme indiqué dans la section ci-dessous, puis, pour arrêter l’instance, revenez à la fenêtre TERMINAL et appuyez sur « Entrée ». La série d’appels suivante permet de nettoyer les ressources :

     * Un appel à GraphInstanceDeactivate désactive l’instance de graphe
     * Un appel à GraphInstanceDelete supprime l’instance de graphe
     * Un appel à GraphTopologyDelete supprime la topologie
     * Un dernier appel à GraphTopologyList indique que la liste est maintenant vide

## <a name="interpret-the-results"></a>Interpréter les résultats 

Lorsque vous exécutez le graphe multimédia, le module Live Video Analytics sur IoT Edge envoie certains événements opérationnels et de diagnostic au hub IoT Edge. Ces événements sont les messages qui s’affichent dans la fenêtre SORTIE de Visual Studio Code et qui contiennent une section « body » et une section « applicationProperties ». Pour comprendre ce que ces sections représentent, consultez [Créer et lire des messages IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

Dans les messages ci-dessous, les propriétés de l’application et le contenu du corps sont définis par le module Live Video Analytics.

## <a name="diagnostic-events"></a>Événements de diagnostic

### <a name="mediasessionestablished-event"></a>Événement MediaSessionEstablished 

Lorsqu’un graphe multimédia est instancié, le nœud source RTSP tente de se connecter au serveur RTSP exécuté dans le conteneur du simulateur RTSP. En cas de réussite, il affiche cet événement. Notez que le type d’événement est Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* Le message est un événement de diagnostic, MediaSessionEstablished, il indique que le nœud source RTSP (l’objet) a pu établir une connexion avec le simulateur RTSP et commence à recevoir un flux en direct (simulé).

* « subject » dans applicationProperties référence le nœud dans la topologie de graphe à partir duquel le message a été généré. Dans ce cas, le message provient du nœud source RTSP.

* « eventType » dans applicationProperties indique qu’il s’agit d’un événement de diagnostic.

* « eventTime » indique l’heure à laquelle l’événement s’est produit, c’est-à-dire l’heure à laquelle la vidéo du trafic (fichier MKV) a commencé à arriver dans le module en tant que stream en direct.

* « Body » contient des données relatives à l’événement de diagnostic, qui, dans ce cas, sont les détails [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).


## <a name="operational-events"></a>Événements opérationnels

Une fois que le graphe multimédia se sera exécuté pendant un certain temps, vous obtiendrez un événement à partir du module compteur d’objets. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

applicationProperties contient l’eventTime, c’est-à-dire l’heure à laquelle le module de compteur d’objets a observé que les résultats du module YOLO v3 contenaient des objets d’intérêt (autrement dit, des camions).

Vous pouvez constater que de plus en plus de ces événements s’affichent à mesure que d’autres camions sont détectés dans la vidéo.

### <a name="recordingstarted-event"></a>Événement RecordingStarted

Presque immédiatement après l’envoi de l’événement par le compteur d’objets, vous verrez un événement de type Microsoft.Media.Graph.Operational.RecordingStarted

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

« subject » dans applicationProperties référence le nœud de récepteur des actifs multimédias du graphe qui a généré ce message. Le corps contient des informations sur l’emplacement de sortie, qui est dans ce cas le nom de l’actif multimédia Azure Media Services dans lequel la vidéo est enregistrée. Notez cette valeur.

### <a name="recordingavailable-event"></a>Événement RecordingAvailable

Une fois que le nœud récepteur d’actifs multimédias a chargé la vidéo dans l’actif multimédia, il émet cet événement de type Microsoft.Media.Graph.Operational.RecordingAvailable

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

Cet événement indique que suffisamment de données ont été écrites dans l’actif pour permettre aux joueurs/clients de lancer la lecture de la vidéo. Dans applicationProperties, « subject » référence le nœud AssetSink du graphe qui a généré ce message. Le corps contient des informations sur l’emplacement de sortie, qui est dans ce cas le nom de l’actif multimédia Azure Media Services dans lequel la vidéo est enregistrée.

### <a name="recordingstopped-event"></a>Événement RecordingStopped

Si vous examinez les paramètres d’activation (maximumActivationTime) pour le nœud de processeur de signal porte dans la [topologie](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), vous verrez que la porte est configurée pour se fermer après 30 secondes d’envoi de vidéo. Donc environ 30 secondes après l’événement RecordingStarted, vous devriez voir un événement de type Microsoft.Media.Graph.Operational.RecordingStopped, qui indique que le nœud récepteur d’actifs multimédias a cessé d’enregistrer la vidéo dans l’actif multimédia.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

Cet événement indique que l’enregistrement s’est arrêté. Dans applicationProperties, « subject » référence le nœud AssetSink du graphe qui a généré ce message. Le corps contient des informations sur l’emplacement de sortie, qui est dans ce cas le nom de l’actif multimédia Azure Media Services dans lequel la vidéo est enregistrée.

## <a name="media-services-asset"></a>Actif multimédia Media Services  

Vous pouvez examiner l’actif multimédia Media Services qui a été créé par le graphe en vous connectant au portail Azure et en visionnant la vidéo.

1. Ouvrez votre navigateur web pour accéder au [portail Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.
1. Localisez votre compte Media Services parmi les ressources que vous avez dans votre abonnement, puis ouvrez le panneau du compte.
1. Cliquez sur Actifs multimédias dans la liste Media Services.

    ![Éléments multimédias](./media/continuous-video-recording-tutorial/assets.png)
1. Vous trouverez un actif nommé sampleAssetFromEVR-LVAEdge-{DateHeure}, qui est le nom fourni dans la propriété outputLocation de l’événement RecordingStarted. Dans la topologie, assetNamePattern détermine la façon dont ce nom a été généré.
1. Cliquez sur l’actif multimédia.
1. Dans la page des détails de l’actif, cliquez sur **Créer** en dessous de la zone de texte URL de streaming.

    ![Nouvel actif](./media/continuous-video-recording-tutorial/new-asset.png)

1. Dans l’Assistant qui s’ouvre, acceptez les options par défaut et appuyez sur « Ajouter ». Pour plus d’informations, consultez [Lecture vidéo](video-playback-concept.md).

    > [!TIP]
    > Vérifiez que votre [point de terminaison de streaming est en cours d’exécution](../latest/streaming-endpoint-concept.md).
1. Le joueur doit charger la vidéo et vous devriez pouvoir appuyer sur **Lire** pour l’afficher.

> [!NOTE]
> Étant donné que la source de la vidéo était un conteneur simulant le flux d’une caméra, les horodatages de la vidéo sont liés au moment où vous avez activé l’instance de graphe et où vous l’avez désactivée. Si vous utilisez les commandes de lecture intégrées au tutoriel [Lecture d’enregistrements sur plusieurs jours](playback-multi-day-recordings-tutorial.md), vous pouvez voir l’horodatage de la vidéo affichée à l’écran.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez d’essayer les autres tutoriels, vous devez vous en tenir aux ressources créées. Sinon, accédez au portail Azure et à vos groupes de ressources, sélectionnez le groupe de ressources sous lequel vous avez exécuté ce tutoriel, puis supprimez le groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes

* Utilisez une [caméra IP](https://en.wikipedia.org/wiki/IP_camera) avec prise en charge de RTSP au lieu d’utiliser le simulateur RTSP. Vous pouvez consulter les caméras IP prenant RTSP en charge dans la [page de produits conformes ONVIF](https://www.onvif.org/conformant-products/) en recherchant les appareils conformes aux profils G, S ou T.
* Utilisez un périphérique Linux AMD64 ou x64 (au lieu d’utiliser une machine virtuelle Linux Azure). Cet appareil doit se trouver dans le même réseau que la caméra IP. Vous pouvez suivre les instructions figurant dans [Installer le runtime Azure IoT Edge sur Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux), puis suivre les instructions du guide de démarrage rapide [Déployer votre premier module IoT Edge sur un appareil Linux virtuel](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) pour inscrire l’appareil auprès d’Azure IoT Hub.
