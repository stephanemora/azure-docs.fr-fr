---
title: Détecter les événements de mouvement et d’émission - Azure
description: Ce guide de démarrage rapide vous montre comment utiliser Live Video Analytics sur IoT Edge pour détecter les événements de mouvement et d’émission, en appelant par programmation des méthodes directes.
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: fdc80c4d734902309e8b6dc5a6bfee38514fcdb7
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067798"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Démarrage rapide : Détecter les événements de mouvement et d’émission

Ce guide de démarrage rapide vous guide tout au long des étapes de prise en main de Live Video Analytics sur IoT Edge. Il utilise une machine virtuelle Azure comme appareil IoT Edge et un flux vidéo en direct simulé. Une fois la procédure de configuration terminée, vous pourrez exécuter un flux vidéo en direct simulé par le biais d’un graphe multimédia qui détecte et signale tout mouvement dans ce flux. Le diagramme suivant montre une représentation graphique de ce graphe multimédia.

![Live Video Analytics basé sur la détection de mouvement](./media/analyze-live-video/motion-detection.png) 

Cet article se base sur un [exemple de code](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) écrit en C#.

## <a name="prerequisites"></a>Prérequis

* Un compte Azure disposant d’un abonnement actif. Si vous n’en avez pas déjà un, [créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) avec les extensions suivantes :
    * [Outils IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Kit SDK .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1). 

> [!TIP]
> Vous pouvez être invité à installer Docker pendant l’installation de l’extension Azure IoT Tools. Vous pouvez ignorer cette invite.

## <a name="set-up-azure-resources"></a>Configurer les ressources Azure

Ce tutoriel nécessite les ressources Azure suivantes :

* IoT Hub
* Compte de stockage
* Compte Azure Media Services
* Machine virtuelle Linux dans Azure, avec le [runtime IoT Edge](../../iot-edge/how-to-install-iot-edge-linux.md) installé

Pour ce guide de démarrage rapide, nous vous recommandons d’utiliser le [script d’installation des ressources Live Video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) pour déployer les ressources nécessaires dans votre abonnement Azure. Pour ce faire, procédez comme suit :

1. Ouvrez [Azure Cloud Shell](https://shell.azure.com).
1. Si vous utilisez Cloud Shell pour la première fois, vous serez invité à sélectionner un abonnement pour créer un compte de stockage et un partage Microsoft Azure Files. Sélectionnez **Créer un stockage** pour créer un compte de stockage pour vos informations de session Cloud Shell. Ce compte de stockage est distinct du compte que le script créera pour être utilisé avec votre compte Azure Media Services.
1. Dans le menu déroulant situé sur le côté gauche de la fenêtre Cloud Shell, sélectionnez **Bash** comme environnement.

    ![Sélecteur d’environnement](./media/quickstarts/env-selector.png)

1. Exécutez la commande suivante :

```
bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
```

Si le script se termine correctement, vous devez voir toutes les ressources nécessaires dans votre abonnement.

1. Une fois le script terminé, sélectionnez les accolades pour exposer la structure des dossiers. Quelques fichiers s’affichent dans le répertoire *~/clouddrive/lva-sample*. L’intérêt de ce guide de démarrage rapide est le suivant :

     * ***~/clouddrive/lva-sample/edge-deployment/.env*** : ce fichier contient des propriétés que Visual Studio Code utilise pour déployer des modules sur un appareil de périphérie.
     * ***~/clouddrive/lva-sample/appsetting.json*** : Visual Studio Code utilise ce fichier pour exécuter l’exemple de code.
     
Vous aurez besoin de ces fichiers quand vous configurerez votre environnement de développement dans Visual Studio Code dans la prochaine section. Vous pouvez les copier dans un fichier local pour l’instant.

 ![Paramètres de l’application](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement

1. Clonez le dépôt à partir de cet emplacement : https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Dans Visual Studio Code, ouvrez le dossier dans lequel le dépôt a été téléchargé.
1. Dans Visual Studio Code, accédez au dossier *src/cloud-to-device-console-app*. À partir de là, créez un fichier et nommez-le *appsettings.json*. Ce fichier contiendra les paramètres nécessaires à l’exécution du programme.
1. Copiez le contenu du fichier *~/clouddrive/lva-sample/appsettings.json* que vous avez généré précédemment dans ce guide de démarrage rapide.

    Le texte doit ressembler à la sortie suivante.

```
{  
    "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
    "deviceId" : "lva-sample-device",  
    "moduleId" : "lvaEdge"  
}
```
5. Accédez au dossier *src/edge*, puis créez un fichier nommé  *.env*.
1. Copiez le contenu du fichier */clouddrive/lva-sample/edge-deployment/.env*. Le texte doit ressembler au code suivant.

```
SUBSCRIPTION_ID="<Subscription ID>"  
RESOURCE_GROUP="<Resource Group>"  
AMS_ACCOUNT="<AMS Account ID>"  
IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
AAD_TENANT_ID="<AAD Tenant ID>"  
AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"      
```

## <a name="examine-the-sample-files"></a>Examiner les exemples de fichiers

1. Dans Visual Studio Code, accédez à *src/edge*. Le fichier  *.env* et quelques fichiers de modèle de déploiement s’affichent.

    Le modèle de déploiement fait référence au manifeste de déploiement de l’appareil de périphérie, où des variables sont utilisées pour certaines propriétés. Le fichier  *.env* contient les valeurs de ces variables.
1. Accédez au dossier *src/cloud-to-device-console-app*. Ce dernier contient le fichier *appsettings.json* et quelques autres fichiers :

    * ***c2d-console-app.csproj*** : fichier projet pour Visual Studio Code.
    * ***operations.json*** : liste des opérations que vous voulez que le programme exécute.
    * ***Program.cs*** : exemple de code de programme. Ce code :
    
      * Il charge les paramètres de l’application.
      * Invoque les méthodes directes qui sont exposées par le module Live Video Analytics sur IoT Edge. Vous pouvez utiliser le module pour analyser des flux vidéo en direct en invoquant ses [méthodes directes](direct-methods.md).
      * S’interrompt pour vous permettre d’examiner la sortie du programme dans la fenêtre **TERMINAL** et d’examiner les événements générés par le module dans la fenêtre **SORTIE**.
      * Invoque des méthodes directes pour nettoyer des ressources.   

## <a name="generate-and-deploy-the-deployment-manifest"></a>Générer et déployer le manifeste de déploiement

Le manifeste de déploiement définit les modules qui sont déployés sur un appareil de périphérie. Il définit également les paramètres de configuration de ces modules. 

Effectuez les étapes suivantes pour générer le manifeste à partir du fichier de modèle, puis déployez-le sur l’appareil de périphérie.

1. Ouvrez Visual Studio Code.
1. En regard du volet **AZURE IOT HUB**, sélectionnez l’icône **Autres actions** pour définir la chaîne de connexion IoT Hub. Vous pouvez copier la chaîne à partir du fichier *src/cloud-to-device-console-app/appsettings.json*. 

    ![Définir la chaîne de connexion Azure IoT](./media/quickstarts/set-iotconnection-string.png)

1. Cliquez avec le bouton droit sur **src/edge/deployment.template.json**, puis sélectionnez **Générez un manifeste de déploiement IoT Edge**.

    ![Générer le manifeste de déploiement IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Cette action doit créer un fichier manifeste nommé *deployment.amd64.json* dans le dossier *src/edge/config*.
1. Cliquez avec le bouton droit sur **src/edge/config/deployment.amd64.json**, sélectionnez **Créer un déploiement pour un seul appareil**, puis sélectionnez le nom de votre appareil de périphérie.

    ![Créer un déploiement pour un seul appareil](./media/quickstarts/create-deployment-single-device.png)

1. Quand vous êtes invité à sélectionner un appareil IoT Hub, choisissez **lva-sample-device** dans le menu déroulant.
1. Après environ 30 secondes, dans l’angle en bas à gauche de la fenêtre, actualisez Azure IoT Hub. L’appareil de périphérie affiche maintenant les modules déployés suivants :

    * Live Video Analytics sur IoT Edge (nom de module`lvaEdge`)
    * Simulateur RTSP (Real-Time Streaming Protocol) (nom de module `rtspsim`)

Le module de simulateur RTSP simule un flux vidéo en direct à l’aide d’un fichier vidéo qui a été copié sur votre appareil de périphérie quand vous avez exécuté le [script d’installation des ressources Live Video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

> [!NOTE]
> Si vous utilisez votre propre périphérique au lieu de celui approvisionné par notre script de configuration, accédez à votre périphérique et exécutez les commandes suivantes avec des **droits d’administrateur** pour extraire et stocker l’exemple de fichier vidéo utilisé pour ce démarrage rapide :  

```
mkdir /home/lvaadmin/samples      
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
À ce stade, les modules sont déployés, mais aucun graphe multimédia n’est actif.

## <a name="prepare-to-monitor-events"></a>Préparer la supervision d’événements

Vous allez utiliser le module Live Video Analytics sur IoT Edge pour détecter les mouvements dans le flux vidéo en direct entrant et envoyer des événements à IoT Hub. Pour voir ces événements, effectuez les étapes suivantes :

1. Ouvrez le volet Explorateur dans Visual Studio Code, puis recherchez Azure IoT Hub dans l’angle inférieur gauche.
1. Développez le nœud **Appareils**.
1. Cliquez avec le bouton droit sur **lva-sample-device**, puis sélectionnez **Démarrer la supervision du point de terminaison d’événement intégré**.

    ![Démarrer la supervision d’un point de terminaison d’événement intégré](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Exécuter l'exemple de programme

Pour exécuter l’exemple de code, effectuez les étapes suivantes :

1. Dans Visual Studio Code, accédez à *src/cloud-to-device-console-app/operations.json*.
1. Sur le nœud **GraphTopologySet**, vérifiez que vous voyez la valeur suivante :

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. Sur les nœuds **GraphInstanceSet** et **GraphTopologyDelete**, vérifiez que la valeur de `topologyName` correspond à celle de la propriété `name` dans la topologie de graphe :

    `"topologyName" : "MotionDetection"`
    
1. Démarrez une session de débogage en appuyant sur la touche F5. La fenêtre **TERMINAL** affiche des messages.
1. Le fichier *operations.json* commence par appeler `GraphTopologyList` et `GraphInstanceList`. Si vous avez nettoyé les ressources après avoir suivi les guides de démarrage rapide précédents, ce processus retourne des listes vides, puis s’interrompt. Pour continuer, sélectionnez la touche Entrée.

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

La fenêtre **TERMINAL** affiche le jeu d’appels de méthode directe suivant :
 * Un appel à `GraphTopologySet` qui utilise le `topologyUrl` précédent
 * Un appel à `GraphInstanceSet` qui utilise le corps suivant :
     
```
{
  "@apiVersion": "1.0",
  "name": "Sample-Graph",
  "properties": {
    "topologyName": "MotionDetection",
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
     
 * Un appel à `GraphInstanceActivate` qui démarre l’instance de graphe et le flux vidéo
 * Un deuxième appel à `GraphInstanceList` qui indique que l’instance de graphe est dans l’état En cours d’exécution
6. La sortie affichée dans la fenêtre **TERMINAL** fait une pause au niveau de `Press Enter to continue`. Ne sélectionnez pas encore Entrée. Faites défiler vers le haut pour voir les charges utiles de réponse JSON pour les méthodes directes que vous avez invoquées.
1. Basculez vers la fenêtre **SORTIE** de Visual Studio Code. Les messages indiquant que le module Live Video Analytics sur IoT Edge effectue des envois à IoT Hub s’affichent. La section suivante de ce guide de démarrage rapide décrit ces messages.
1. Le graphe multimédia continue à s’exécuter et à afficher les résultats. Le simulateur RTSP continue de boucler la vidéo source. Pour arrêter le graphe multimédia, retournez dans la fenêtre **TERMINAL**, puis sélectionnez Entrée. 

    La série d’appels suivante nettoie les ressources :
     * Un appel à `GraphInstanceDeactivate` désactive l’instance de graphe.
     * Un appel à `GraphInstanceDelete` supprime l’instance.
     * Un appel à `GraphTopologyDelete` supprime la topologie.
     * Un dernier appel à `GraphTopologyList` indique que la liste est vide.

## <a name="interpret-results"></a>Interpréter les résultats

Quand vous exécutez le graphe multimédia, les résultats du nœud processeur de détection de mouvement passe par le nœud récepteur IoT Hub pour atteindre le hub IoT. Les messages qui s’affichent dans la fenêtre **SORTIE** de Visual Studio Code contiennent une section `body` et une section `applicationProperties`. Pour plus d’informations, consultez [Créer et lire des messages IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

Dans les messages suivants, le module Live Video Analytics définit les propriétés de l’application et le contenu du corps.

### <a name="mediasessionestablished-event"></a>Événement MediaSessionEstablished

Quand un graphe multimédia est instancié, le nœud source RTSP tente de se connecter au serveur RTSP qui s’exécute sur le conteneur rtspsim-live555. Si la connexion réussit, l’événement suivant est affiché.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
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

Dans la sortie précédente : 
* Le message est un événement de diagnostic, `MediaSessionEstablished`. Il indique que le nœud source RTSP (l’objet) s’est connecté au simulateur RTSP et a commencé à recevoir un flux en direct (simulé).
* Dans `applicationProperties`, `subject` fait référence au nœud présent dans la topologie de graphe à partir duquel le message a été généré. Dans ce cas, le message provient du nœud source RTSP.
* Dans `applicationProperties`, `eventType` indique que cet événement est un événement de diagnostic.
* La valeur `eventTime` indique l’heure à laquelle l’événement s’est produit.
* La section `body` contient des données relatives à l’événement de diagnostic. Dans ce cas, les données comprennent les détails du [protocole SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).


### <a name="motiondetection-event"></a>Événement MotionDetection

Quand un mouvement est détecté, le module Live Video Analytics sur IoT Edge envoie un événement d’inférence. Le `type` est défini sur `motion` pour indiquer qu’il s’agit d’un résultat du processeur de détection de mouvement. La valeur `eventTime` vous indique quand (au format UTC) le mouvement s’est produit. 

Voici un exemple de ce message :

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  },  
  "applicationProperties": {  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAME/processors/md",  
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",  
    "eventTime": "2020-04-17T20:26:32.7010000Z",
    "dataVersion": "1.0"  
  }  
}  
```

Dans cet exemple : 

* Dans `applicationProperties`, `subject` fait référence au nœud présent dans le graphe multimédia à partir duquel le message a été généré. Dans ce cas, le message provient du nœud processeur de détection de mouvement.
* Dans `applicationProperties`, `eventType` indique que cet événement est un événement d’analytique.
* La valeur `eventTime` correspond à l’heure à laquelle l’événement s’est produit.
* La valeur `body` correspond aux données relatives à l’événement d’analytique. Dans ce cas, l’événement est un événement d’inférence. Par conséquent, le corps contient des données `timestamp` et `inferences`.
* Les données de `inferences` indiquent que le `type` est `motion`. Elles contiennent des données supplémentaires sur cet événement `motion`.
* La section `box` contient les coordonnées d’un cadre englobant autour de l’objet en déplacement. Les valeurs sont normalisées par la largeur et la hauteur de la vidéo, en pixels. Par exemple, la largeur est de 1920 et la hauteur est de 1080.

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez d’essayer les autres guides de démarrage rapide, vous devez conserver les ressources que vous avez créées. Dans le cas contraire, dans le portail Azure, accédez à vos groupes de ressources, sélectionnez le groupe de ressources où vous avez exécuté ce guide de démarrage rapide, puis supprimez toutes les ressources.

## <a name="next-steps"></a>Étapes suivantes

Exécutez les autres guides de démarrage rapide, comme la détection d’un objet dans un flux vidéo en direct.        
