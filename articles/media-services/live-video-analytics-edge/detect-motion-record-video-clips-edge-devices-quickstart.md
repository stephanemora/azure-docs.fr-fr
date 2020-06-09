---
title: Détecter les mouvements et enregistrer des vidéos sur des périphériques - Azure
description: Ce guide de démarrage rapide vous montre comment utiliser Live Video Analytics sur IoT Edge pour analyser le flux vidéo en direct à partir d’une caméra IP (simulée), détecter la présence d’un mouvement et, le cas échéant, enregistrer un clip vidéo MP4 sur le système de fichiers local du périphérique.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: d824870ea95922bbbdbf01cf2c95692522936f85
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261685"
---
# <a name="quickstart-detect-motion-record-video-on-edge-devices"></a>Démarrage rapide : Détecter les mouvements et enregistrer des vidéos sur des appareils périphériques
 
Ce guide de démarrage rapide vous montre comment utiliser Live Video Analytics sur IoT Edge pour analyser le flux vidéo en direct à partir d’une caméra IP (simulée), détecter la présence d’un mouvement et, le cas échéant, enregistrer un clip vidéo MP4 sur le système de fichiers local du périphérique. Il utilise une machine virtuelle Azure comme appareil IoT Edge et un flux vidéo en direct simulé. Cet article se base sur un exemple de code écrit en C#.

Cet article s’appuie sur [ce](detect-motion-emit-events-quickstart.md) guide de démarrage rapide. 

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) sur votre ordinateur avec les extensions suivantes :
    * [Outils IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Kit de développement logiciel (SDK) .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) installé sur votre système
* Si vous n’avez pas encore effectué [ce](detect-motion-emit-events-quickstart.md) guide de démarrage rapide, terminez les étapes suivantes :
     * [Configurer des ressources Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     * [Configurer votre environnement de développement](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     * [Générer et déployer le manifeste de déploiement IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest)
     * [Prépare des événements de surveillance](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events)

> [!TIP]
> Lors de l’installation d’Azure IoT Tools, vous pouvez être invité à installer Docker. N’hésitez pas à l’ignorer.

## <a name="review-the-sample-video"></a>Réviser l’exemple de vidéo
Dans le cadre de la procédure ci-dessus permettant de configurer les ressources Azure, une (courte) vidéo d’une aire de stationnement va être copiée dans Azure sur la machine virtuelle Linux qui est utilisée comme périphérique IoT Edge. Ce fichier vidéo permet de simuler un flux en direct pour ce tutoriel.

Vous pouvez utiliser une application, telle que [VLC Player](https://www.videolan.org/vlc/), la lancer, appuyer sur Ctrl+N et coller [ce](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) lien dans la vidéo de l’aire de stationnement pour commencer la lecture. Au repère d’approximativement 5 secondes, une voiture blanche traverse l’aire de stationnement.

Lorsque vous terminez la procédure ci-dessous, vous avez utilisé Live Video Analytics sur IoT Edge pour détecter ce mouvement de la voiture et enregistrer un clip vidéo commençant au repère d’approximativement 5 secondes.

## <a name="overview"></a>Vue d’ensemble

![vue d'ensemble](./media/quickstarts/overview-qs4.png)

Le diagramme ci-dessus montre la circulation des signaux dans ce guide de démarrage rapide. Un module de périphérie (détaillé [ici](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) simule une caméra IP qui héberge un serveur RTSP. Un nœud [source RTSP](media-graph-concept.md#rtsp-source) extrait le flux vidéo de ce serveur et envoie des images vidéo au nœud [processeur de détection de mouvement](media-graph-concept.md#motion-detection-processor). La source RTSP envoie les mêmes images vidéo à un nœud [processeur de porte de signal](media-graph-concept.md#signal-gate-processor), qui reste fermé jusqu’à ce qu’il soit déclenché par un événement.

Lorsque le processeur de détection de mouvement détermine la présence d’un mouvement dans la vidéo, il envoie un événement au nœud processeur de porte de signal, et le déclenche. La porte s’ouvre pendant la durée configurée, et les images vidéo sont envoyées au nœud [récepteur de fichiers](media-graph-concept.md#file-sink). Ce nœud récepteur enregistre la vidéo au format de fichier MP4 sur le système de fichiers local de votre périphérique, à l’emplacement configuré.

Dans ce guide de démarrage rapide, vous allez :

1. Créer et déployer le graphe multimédia
1. Interpréter les résultats
1. Nettoyer les ressources

## <a name="examine-and-edit-the-sample-files"></a>Examiner et modifier les exemples de fichiers
Dans le cadre des conditions préalables, vous avez dû télécharger l’exemple de code dans un dossier. Lancez Visual Studio Code et ouvrez le dossier.

1. Dans Visual Studio Code, accédez à « src/edge ». Vous verrez le fichier. env que vous avez créé, ainsi que quelques fichiers de modèle de déploiement
    * Le modèle de déploiement fait référence au manifeste de déploiement du périphérique avec des valeurs d’espace réservé. Le fichier .env contient les valeurs de ces variables.
1. Ensuite, accédez au dossier « src/cloud-to-device-console-app ». Vous y voyez le fichier appsettings.json que vous avez créé, ainsi que quelques autres fichiers :
    * c2d-console-app.csproj : fichier projet pour Visual Studio Code
    * operations.json : répertorie les différentes opérations que vous souhaitez que le programme exécute
    * Program.cs : exemple de code de programme, qui effectue les opérations suivantes :

        * Il charge les paramètres de l’application.
        * Il invoque les méthodes directes exposées par le module Live Video Analytics sur IoT Edge. Vous pouvez utiliser le module pour analyser des flux vidéo en direct en invoquant ses [méthodes directes](direct-methods.md) 
        * Il s’interrompt pour vous permettre d’examiner la sortie du programme dans la fenêtre TERMINAL et les événements générés par le module dans la fenêtre SORTIE
        * Il invoque des méthodes directes pour nettoyer des ressources   

1. Apportez les modifications suivantes au fichier operations.json
    * Modifiez le lien vers la topologie de graphe : `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * Sous GraphInstanceSet, modifiez le nom de la topologie de graphe de manière à ce qu’il corresponde à la valeur du lien ci-dessus `"topologyName" : "EVRToFilesOnMotionDetection"`
    * Modifiez également l’URL RTSP pour qu’elle pointe vers le fichier vidéo souhaité `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * Sous GraphTopologyDelete, modifiez le nom `"name": "EVRToFilesOnMotionDetection"`

## <a name="review---check-status-of-the-modules"></a>Vérification - vérifier l’état des modules
À l'étape [Générer et déployer le manifeste de déploiement IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest), dans Visual Studio Code, si vous développez le nœud « Llva-sample-device » sous AZURE IOT HUB (dans la section en bas à gauche), vous devriez voir les modules suivants déployés

    1. Le module Live Video Analytics, nommé « lvaEdge »
    1. Un module nommé « rtspsim », qui simule un serveur RTSP, agissant comme source d’un flux vidéo en direct

        ![Modules](./media/quickstarts/lva-sample-device-node.png)


## <a name="review---prepare-for-monitoring-events"></a>Vérification - préparez des événements de surveillance
Vérifiez que vous avez terminé les étapes pour [préparer des événements de surveillance](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events)

![Démarrer la supervision du point de terminaison d'événement intégré](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Exécuter l'exemple de programme

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
       "name": "Sample-Graph",
       "properties": {
         "topologyName": "EVRToFilesOnMotionDetection",
         "description": "Sample graph description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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
     * Un appel à GraphInstanceDelete supprime l’instance
     * Un appel à GraphTopologyDelete supprime la topologie
     * Un dernier appel à GraphTopologyList indique que la liste est maintenant vide

## <a name="interpret-results"></a>Interpréter les résultats 
Lorsque vous exécutez le graphe multimédia, les résultats du nœud processeur de détection de mouvement sont envoyés via le nœud récepteur IoT Hub à IoT Hub. Les messages qui s’affichent dans la fenêtre SORTIE de Visual Studio Code contiennent une section « body » et une section « applicationProperties ». Pour comprendre ce que ces sections représentent, lisez [cet](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) article.

Dans les messages ci-dessous, les propriétés de l’application et le contenu du corps sont définis par le module Live Video Analytics.

## <a name="mediasession-established-event"></a>Événement MediaSession Established

Lorsqu’un graphe multimédia est instancié, le nœud source RTSP tente de se connecter au serveur RTSP exécuté sur le conteneur rtspsim-live555. En cas de réussite, il affiche cet événement :

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

* Le message est un événement de diagnostic, MediaSessionEstablished, il indique que le nœud source RTSP (l’objet) a pu établir une connexion avec le simulateur RTSP et commence à recevoir un flux en direct (simulé).
* « Subject » dans applicationProperties fait référence au nœud dans la topologie de graphe à partir duquel le message a été généré. Dans ce cas, le message provient du nœud source RTSP.
* « eventType » dans applicationProperties indique qu’il s’agit d’un événement de diagnostic.
* « eventTime » indique l’heure à laquelle l’événement s’est produit.
* « Body » contient des données relatives à l’événement de diagnostic, qui, dans ce cas, sont les détails [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).


## <a name="recording-started-event"></a>Événement Enregistrement démarré

Comme expliqué [ici](#overview), en cas de détection d’un mouvement, le nœud processeur de porte de signal est activé et le nœud récepteur de fichiers dans le graphe multimédia commence à écrire un fichier MP4. Le nœud récepteur de fichiers envoie un événement opérationnel. Le type est défini sur « mouvement » pour indiquer qu’il s’agit d’un résultat provenant du processeur de détection de mouvement, et eventTime vous indique à quelle heure (UTC) le mouvement s’est produit. Voici un exemple :

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

* « subject » dans applicationProperties fait référence au nœud dans le graphe multimédia à partir duquel le message a été généré. Dans ce cas, le message provient du nœud récepteur de fichiers.
* « eventType » dans applicationProperties indique qu’il s’agit d’un événement opérationnel.
* « eventTime » indique l’heure à laquelle l’événement s’est produit. Notez que cela se produit 5-6 secondes après l’événement MediaSessionEstablished et le début du flux vidéo. Cela correspond au repère de 5-6 secondes lorsque la [voiture a commencé à se déplacer](#review-the-sample-video) dans l’aire de stationnement
* « Body » contient des données relatives à l’événement opérationnel, dans ce cas les données « outputType » et « outputLocation ».
* « outputType » indique que ces informations concernent le chemin d’accès au fichier
* « outputLocation » fournit l’emplacement du fichier MP4, à partir du module de périphérie

## <a name="recording-stopped-and-available-events"></a>Enregistrement des événements arrêtés et disponibles

Si vous examinez les propriétés du nœud processeur de porte de signal dans la [topologie de graphe](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json), vous remarquerez que les durées d’activation ont été définies sur 5 secondes. Ainsi, 5 secondes environ après la réception de l’événement RecordingStarted, vous obtiendrez un
* événement RecordingStopped, indiquant que l’enregistrement a été arrêté
* événement RecordingAvailable, indiquant que le fichier MP4 peut maintenant être visualisé

Les deux événements sont généralement émis à quelques secondes d’intervalle.

### <a name="playing-back-the-mp4-clip"></a>Lecture du clip MP4

1. Les fichiers MP4 sont écrits dans un répertoire sur le périphérique que vous avez configuré dans le fichier. env via cette clé : OUTPUT_VIDEO_FOLDER_ON_DEVICE. Si vous n’avez pas modifié la valeur par défaut, les résultats doivent se trouver dans/home/lvaadmin/samples/output/
1. Accédez à votre groupe de ressources, recherchez la machine virtuelle et connectez-vous avec Bastion

    ![Resource group](./media/quickstarts/resource-group.png)
 
    ![Machine virtuelle](./media/quickstarts/virtual-machine.png)
1. Une fois connecté (à l’aide des informations d’identification générées lors de [cette](detect-motion-emit-events-quickstart.md#set-up-azure-resources) étape), dans l’invite de commandes, accédez au répertoire approprié (par défaut: /home/lvaadmin/samples/output) et vous devriez y trouver les fichiers MP4. Vous pouvez [déplacer les fichiers avec SCP](https://docs.microsoft.com/azure/virtual-machines/linux/copy-files-to-linux-vm-using-scp) sur votre ordinateur local et les lire via [VLC Player](https://www.videolan.org/vlc/) ou tout autre lecteur MP4.

    ![Output](./media/quickstarts/samples-output.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez d’essayer les autres guides de démarrage rapide, vous devez vous en tenir aux ressources créées. Dans le cas contraire, accédez au Portail Azure et à vos groupes de ressources, sélectionnez le groupe de ressources sous lequel vous avez exécuté ce guide de démarrage rapide, puis supprimez toutes les ressources.

## <a name="next-steps"></a>Étapes suivantes

* Exécutez le guide de démarrage rapide [Exécuter Live Video Analytics avec votre propre modèle](use-your-model-quickstart.md), qui montre comment appliquer l’intelligence artificielle aux flux vidéo en direct.
* Passez en revue les défis supplémentaires pour les utilisateurs expérimentés :

    * Utilisez une [caméra IP](https://en.wikipedia.org/wiki/IP_camera) avec prise en charge de RTSP au lieu d’utiliser le simulateur RTSP. Vous pouvez consulter les caméras IP prenant RTSP en charge sur la page de [produits conformes ONVIF](https://en.wikipedia.org/wiki/IP_camera) en recherchant les appareils conformes aux profils G, S ou T.
    * Utilisez un périphérique Linux AMD64 ou x64 (au lieu d’utiliser une machine virtuelle Linux Azure). Cet appareil doit se trouver dans le même réseau que la caméra IP. Vous pouvez suivre les instructions figurant dans [Installer le runtime Azure IoT Edge sur Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux), puis suivre les instructions du guide de démarrage rapide [Déployer votre premier module IoT Edge sur un appareil Linux virtuel](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) pour inscrire l’appareil auprès d’Azure IoT Hub.
