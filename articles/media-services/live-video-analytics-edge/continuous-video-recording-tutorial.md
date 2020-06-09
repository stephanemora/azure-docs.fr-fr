---
title: Tutoriel sur l’enregistrement vidéo en continu dans le cloud et la lecture à partir du cloud - Azure
description: Ce tutoriel va vous apprendre à utiliser Live Video Analytics sur IoT Edge pour enregistrer des vidéos en continu dans le cloud et diffuser toute partie de cette vidéo avec Azure Media Services.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: a69d3f5db9dd8cbe25bbf79f44921f26258005cc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84259986"
---
# <a name="tutorial-continuous-video-recording-to-cloud-and-playback-from-cloud"></a>Tutoriel : Enregistrement vidéo en continu dans le cloud et lecture à partir du cloud  

Ce tutoriel va vous apprendre à utiliser Live Video Analytics sur IoT Edge pour effectuer des [enregistrements vidéo en continu](continuous-video-recording-concept.md) (CVR) dans le cloud et diffuser toute partie de cette vidéo avec Azure Media Services. Ces opérations peuvent s’avérer utiles dans des scénarios tels que la sécurité, la conformité, etc., où il est nécessaire de conserver une archive des images d’une caméra pendant plusieurs jours (ou semaines).

> [!div class="checklist"]
> * Configurer les ressources appropriées
> * Examiner le code qui effectue l’enregistrement CVR
> * Exécuter l’exemple de code
> * Examiner les résultats et regarder la vidéo

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée  

Nous vous recommandons de lire les pages de documentation suivantes

* [Présentation de Live Video Analytics sur IoT Edge](overview.md)
* [Terminologie de Live Video Analytics sur IoT Edge](terminology.md)
* [Concepts de graphique multimédia](media-graph-concept.md) 
* [Scénarios d’enregistrement vidéo en continu](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Prérequis

Les prérequis pour ce tutoriel sont les suivants :

* [Visual Studio Code](https://code.visualstudio.com/) sur votre machine de développement avec l’extension [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools), ainsi que l’extension [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > Vous serez peut-être invité à installer Docker. Vous pouvez ignorer cette invitation.
* [Le kit de développement logiciel .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) sur votre machine de développement.
* Exécuter [le script d’installation des ressources Video Analytics en direct](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)

À la fin de la procédure ci-dessus, certaines ressources Azure seront déployées dans l’abonnement Azure, notamment :

* IoT Hub
* Compte de stockage
* Compte Azure Media Services
* Machine virtuelle Linux dans Azure, avec le [runtime IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) installé

## <a name="concepts"></a>Concepts

Comme expliqué [ici](media-graph-concept.md), un graphe multimédia vous permet de définir l’emplacement à partir duquel les ressources multimédias doivent être capturées, la manière dont elles doivent être traitées et où les résultats doivent être remis. Pour effectuer un enregistrement CVR, vous devez capturer la vidéo à partir d’une caméra compatible RTSP et l’enregistrer en continu sur une [ressource Azure Media Services](terminology.md#asset). Le diagramme ci-dessous montre une représentation graphique de ce graphe multimédia.

![Graphe multimédia](./media/continuous-video-recording-tutorial/continuous-video-recording-overview.png)

Dans ce tutoriel, vous allez utiliser un module Edge créé avec le [serveur multimédia Live555](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) pour simuler une caméra RTSP. Dans le graphe multimédia, vous allez utiliser un nœud [source RTSP](media-graph-concept.md#rtsp-source) pour obtenir le flux en direct, puis envoyer cette vidéo au [nœud récepteur de ressources](media-graph-concept.md#asset-sink) qui enregistrera la vidéo sur une ressource.

## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement

Avant de commencer, vérifiez que vous avez bien exécuté le troisième point de la section [Prérequis](#prerequisites). Une fois le script d’installation des ressources terminé, cliquez sur les accolades pour exposer la structure du dossier. Vous verrez quelques fichiers créés sous le répertoire ~/clouddrive/lva-sample.

![Paramètres de l’application](./media/quickstarts/clouddrive.png)

Les fichiers qui nous intéressent dans le cadre de ce tutoriel sont les suivants :

     * ~/clouddrive/lva-sample/edge-deployment/.env  - contains properties that Visual Studio Code uses to deploy modules to an edge device
     * ~/clouddrive/lva-sample/appsettings.json - used by Visual Studio Code for running the sample code

Vous aurez besoin de ces fichiers pour la procédure ci-dessous.

1. Clonez le référentiel à partir d’ici https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Lancez Visual Studio Code et ouvrez le dossier dans lequel vous avez téléchargé le référentiel.
1. Dans Visual Studio Code, accédez au dossier « src/cloud-to-device-console-app » et créez un fichier nommé « appsettings.json ». Ce fichier contiendra les paramètres nécessaires à l’exécution du programme.
1. Copiez le contenu du fichier ~/clouddrive/lva-sample/appsettings.json. Le texte doit ressembler à :
    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
    La chaîne de connexion IoT Hub vous permet d’utiliser Visual Studio Code pour envoyer des commandes aux modules Edge via Azure IoT Hub.
    
1. Ensuite, accédez au dossier « src/edge » et créez un fichier nommé « .env ».
1. Copiez le contenu à partir du fichier ~/clouddrive/lva-sample/.env. Le texte devrait ressembler à ceci :

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

## <a name="examine-the-sample-files"></a>Examiner les exemples de fichiers

Dans Visual Studio Code, ouvrez le fichier « src/edge/deployment.template.json ». Ce modèle définit les modules de périphérie que vous allez déployer sur le périphérique (la machine virtuelle Azure Linux). Notez qu’il existe deux entrées sous la section « modules », portant les noms suivants :

* lvaEdge : module Live Video Analytics sur IoT Edge
* rtspsim : simulateur RTSP

Accédez ensuite au dossier « src/cloud-to-device-console-app ». Vous y voyez le fichier appsettings.json que vous avez créé, ainsi que quelques autres fichiers :

* c2d-console-app.csproj : fichier projet pour Visual Studio Code.
* operations.json : répertorie les différentes opérations que vous allez exécuter
* Program.cs : exemple de code de programme, qui effectue les opérations suivantes :
    * Il charge les paramètres de l’application.
    * Il invoque les méthodes directes exposées par le module Live Video Analytics sur IoT Edge. Vous pouvez utiliser le module pour analyser des flux vidéo en direct en invoquant ses [méthodes directes](direct-methods.md)
    * Il s’interrompt pour vous permettre d’examiner la sortie du programme dans la fenêtre TERMINAL et les événements générés par le module dans la fenêtre SORTIE
    * Il invoque des méthodes directes pour nettoyer des ressources

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Générer et déployer le manifeste de déploiement IoT Edge 

Le manifeste de déploiement définit les modules qui sont déployés sur un périphérique et les paramètres de configuration de ces modules. Procédez comme suit pour générer ce manifeste à partir du fichier de modèle, puis déployez-le sur le périphérique.

1. Lancer Visual Studio Code
1. Définissez la chaîne de connexion IoT Hub en cliquant sur l’icône « Autres actions » en regard du volet AZURE IOT HUB dans l’angle inférieur gauche. Vous pouvez copier la chaîne à partir du fichier src/cloud-to-device-console-app/appsettings.json. 

    ![Définir la chaîne de connexion Azure IoT](./media/quickstarts/set-iotconnection-string.png)
1. Cliquez ensuite avec le bouton droit sur le fichier « src/edge/deployment.template.json », puis cliquez sur « Générer le manifeste de déploiement IoT Edge ». Visual Studio Code utilise les valeurs du fichier .env pour remplacer les variables trouvées dans le fichier du modèle de déploiement. Un fichier manifeste nommé « deployment.amd64.json » devrait être créé dans le dossier src/edge/config.

   ![Générer un manifeste de déploiement IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Cliquez avec le bouton droit sur « src/edge/config/deployment.amd64.json », puis cliquez sur « Créer un déploiement pour un seul appareil ».

   ![Créer un déploiement pour un seul appareil](./media/quickstarts/create-deployment-single-device.png)
1. Vous êtes ensuite invité à sélectionner un appareil IoT Hub. Sélectionnez lva-sample-device dans la liste déroulante.
1. Environ 30 secondes plus tard, actualisez Azure IoT Hub dans la section inférieure gauche et vous devriez voir le périphérique avec les modules suivants déployés :
    * Live Video Analytics sur IoT Edge (nom du module « lvaEdge »)
    * Simulateur RTSP (nom du module « rtspsim »)
 
    ![hub IOT](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Préparer la surveillance des modules 

Lorsque vous utilisez le module Live Video Analytics sur IoT Edge pour enregistrer le flux vidéo en direct, il envoie des événements à IoT Hub. Pour afficher ces événements, procédez comme suit :

1. Ouvrez le volet Explorateur dans Visual Studio Code et recherchez Azure IoT Hub dans l’angle inférieur gauche.
1. Développez le nœud Appareils.
1. Cliquez avec le bouton droit sur lva-sample-device et choisissez l’option « Démarrer la supervision de l’événement intégré ».

    ![Démarrer la supervision du point de terminaison d’événement intégré](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Exécuter le programme 

1. Dans Visual Studio Code, accédez à « src/cloud-to-device-console-app/operations.json ».
1. Sous le nœud GraphTopologySet, modifiez les points suivants :

    `"topologyUrl" : "https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json" `
1. Ensuite, sous les nœuds GraphInstanceSet et GraphTopologyDelete, assurez-vous que la valeur de topologyName correspond à celle de la propriété « Name » dans la topologie de graphe ci-dessus :

    `"topologyName" : "CVRToAMSAsset"`  
1. Ouvrez la [topologie](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/topology.json) dans un navigateur et observez assetNamePattern. Pour vous assurer que votre ressource porte un nom unique, vous pouvez modifier le nom de l’instance de graphe dans le fichier operations.json (la valeur par défaut est « Sample-Graph-1 »).

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
1. Démarrez une session de débogage (appuyez sur F5). Vous allez commencer à voir certains messages imprimés dans la fenêtre TERMINAL.
1. Operations.json commence par appeler GraphTopologyList et GraphInstanceList. Si vous avez nettoyé des ressources après des tutoriels ou des guides de démarrage rapide précédents, cette opération renvoie des listes vides, puis s’interrompt pour que vous puissiez appuyer sur Entrée, comme ci-dessous :

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
     * Un appel à GraphTopologySet à l’aide de topologyUrl ci-dessus.
     * Un appel à GraphInstanceSet à l’aide du corps suivant.
     
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "CVRToAMSAsset",
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
     * Un appel à GraphInstanceActivate pour démarrer l’instance de graphe et lancer le flux vidéo
     * Un deuxième appel à GraphInstanceList pour indiquer que l’instance de graphe est effectivement en cours d’exécution  
1. La sortie de la fenêtre TERMINAL s’interrompt maintenant à l’invite « Appuyez sur Entrée pour continuer ». N’appuyez pas sur « Entrée » pour l’instant. Vous pouvez faire défiler vers le haut pour voir les charges utiles de réponse JSON pour les méthodes directes que vous avez invoquées
1. Si vous basculez maintenant vers la fenêtre SORTIE de Visual Studio Code, vous verrez les messages envoyés à IoT Hub par le module Live Video Analytics sur IoT Edge.

     * Ces messages sont traités dans la section ci-dessous
1. L’instance de graphe continue à s’exécuter et à enregistrer la vidéo : le simulateur RTSP continue de boucler la vidéo source. Pour arrêter l’enregistrement, revenez à la fenêtre TERMINAL et appuyer sur « Entrée ». La série d’appels suivante permet de nettoyer les ressources :

     * Un appel à GraphInstanceDeactivate désactive l’instance de graphe
     * Un appel à GraphInstanceDelete supprime l’instance
     * Un appel à GraphTopologyDelete supprime la topologie
     * Un dernier appel à GraphTopologyList indique que la liste est maintenant vide

## <a name="interpret-the-results"></a>Interpréter les résultats 

Lorsque vous exécutez le graphe multimédia, le module Live Video Analytics sur IoT Edge envoie certains événements opérationnels et de diagnostic au hub IoT Edge. Ces événements sont les messages qui s’affichent dans la fenêtre SORTIE de Visual Studio Code et qui contiennent une section « body » et une section « applicationProperties ». Pour comprendre ce que ces sections représentent, lisez [cet](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) article.

Dans les messages ci-dessous, les propriétés de l’application et le contenu du corps sont définis par le module Live Video Analytics.

## <a name="diagnostic-events"></a>Événements de diagnostic 

### <a name="mediasession-established-event"></a>Événement MediaSession Established

Lorsque l’instance de graphe est activée, le nœud source RTSP tente de se connecter au serveur RTSP exécuté dans le module rtspsim. En cas de réussite, l’événement suivant est imprimé :

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* Le message est un événement de diagnostic, MediaSessionEstablished, il indique que le nœud source RTSP (l’objet) a pu établir une connexion avec le simulateur RTSP et commence à recevoir un flux en direct (simulé).
* « Subject » dans applicationProperties fait référence au nœud dans la topologie de graphe à partir duquel le message a été généré. Dans ce cas, le message provient du nœud source RTSP.
* « eventType » dans applicationProperties indique qu’il s’agit d’un événement de diagnostic.
* « eventTime » indique l’heure à laquelle l’événement s’est produit.
* « Body » contient des données relatives à l’événement de diagnostic, qui, dans ce cas, sont les détails [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

## <a name="operational-events"></a>Événements opérationnels 

### <a name="recordingstarted-event"></a>Événement RecordingStarted

Lorsque le nœud récepteur de ressource commence à enregistrer une vidéo, il émet cet événement de type Microsoft.Media.Graph.Operational.RecordingStarted

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

« Subject » dans applicationProperties fait référence au nœud récepteur de ressource du graphe qui a généré ce message.

Le corps contient des informations sur l’emplacement de sortie, qui, dans ce cas, est le nom de la ressource Azure Media Services dans laquelle la vidéo est enregistrée. Notez cette valeur.

### <a name="recordingavailable-event"></a>Événement RecordingAvailable

Comme son nom l’indique, l’événement RecordingStarted est envoyé lorsque l’enregistrement a démarré. Il est cependant possible que les données vidéo ne soient pas encore chargées sur la ressource. Lorsque le nœud récepteur de ressource a chargé des données vidéo sur la ressource, il émet cet événement de type Microsoft.Media.Graph.Operational.RecordingAvailable

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

Cet événement indique que des données suffisantes ont été écrites sur la ressource pour permettre aux lecteurs/clients de lancer la lecture vidéo.

« Subject » dans applicationProperties fait référence au nœud récepteur de ressource du graphe qui a généré ce message.

Le corps contient des informations sur l’emplacement de sortie, qui, dans ce cas, est le nom de la ressource Azure Media Services dans laquelle la vidéo est enregistrée.

### <a name="recordingstopped-event"></a>Événement RecordingStopped

Quand vous désactivez l’instance de graphe, le nœud récepteur de ressource arrête d’enregistrer la vidéo sur la ressource et émet cet événement de type Microsoft.Media.Graph.Operational.RecordingStopped.

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

Cet événement indique que l’enregistrement s’est arrêté.

« Subject » dans applicationProperties fait référence au nœud récepteur de ressource du graphe qui a généré ce message.

Le corps contient des informations sur l’emplacement de sortie, qui, dans ce cas, est le nom de la ressource Azure Media Services dans laquelle la vidéo est enregistrée.

## <a name="media-services-asset"></a>Ressource Media Services  

Vous pouvez examiner la ressource Media Services qui a été créée par le graphe multimédia en vous connectant au portail Azure et en visionnant la vidéo.

1. Ouvrez votre navigateur web et accédez au [portail Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.
1. Localisez votre compte Media Services parmi les ressources que vous possédez dans votre abonnement, puis ouvrez le panneau du compte.
1. Cliquez sur Ressources dans la liste Media Services

    ![Éléments multimédias](./media/continuous-video-recording-tutorial/assets.png)
1. Dans la liste, vous trouverez une ressource nommée sampleAsset-CVRToAMSAsset-Sample-Graph-1 : il s’agit du modèle d’attribution de nom choisi dans votre fichier de topologie de graphe.
1. Cliquez sur la ressource.
1. Sur la page des détails de la ressource, cliquez sur **Créer** en dessous de la zone de texte URL de diffusion en continu.

    ![Nouvelle ressource](./media/continuous-video-recording-tutorial/new-asset.png)

1. Dans l’Assistant qui s’ouvre, acceptez les options par défaut et appuyez sur « Ajouter ». Pour plus d’informations, consultez [Lecture vidéo](video-playback-concept.md).

    > [!TIP]
    > Assurez-vous que votre [point de terminaison de streaming est en cours d’exécution](../latest/streaming-endpoint-concept.md).
1. Le lecteur doit charger la vidéo et vous devez pouvoir toucher **Lire**>** pour l’afficher.

> [!NOTE]
> Étant donné que la source de la vidéo était un conteneur simulant le flux d’une caméra, les horodatages de la vidéo sont liés au moment où vous avez activé l’instance de graphe et où vous l’avez désactivée. Vous pouvez consulter [ce](playback-multi-day-recordings-tutorial.md) tutoriel pour voir comment parcourir un enregistrement de plusieurs jours et afficher des parties de cette archive. Dans ce tutoriel, vous pourrez également voir les horodatages dans la vidéo affichée à l’écran.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez d’essayer les autres tutoriels, vous devez vous en tenir aux ressources créées. Dans le cas contraire, accédez au portail Azure et à vos groupes de ressources, sélectionnez le groupe de ressources sous lequel vous avez exécuté ce tutoriel, puis supprimez le groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes

* Utilisez une [caméra IP](https://en.wikipedia.org/wiki/IP_camera) avec prise en charge de RTSP au lieu d’utiliser le simulateur RTSP. Vous pouvez consulter les caméras IP prenant RTSP en charge sur la [page de produits conformes ONVIF](https://www.onvif.org/conformant-products/) en recherchant les appareils conformes aux profils G, S ou T.
* Utilisez un périphérique Linux AMD64 ou x64 (au lieu d’utiliser une machine virtuelle Linux Azure). Cet appareil doit se trouver dans le même réseau que la caméra IP. Vous pouvez suivre les instructions figurant dans [Installer le runtime Azure IoT Edge sur Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux), puis suivre les instructions du guide de démarrage rapide [Déployer votre premier module IoT Edge sur un appareil Linux virtuel](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) pour inscrire l’appareil auprès d’Azure IoT Hub.
