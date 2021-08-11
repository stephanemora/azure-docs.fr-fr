---
title: Analyser la vidéo en direct à l’aide de Live Video Analytics avec Intel OpenVINO™ DL Streamer - Edge IA Extension via gRPC
description: Ce tutoriel montre comment utiliser Live Video Analytics avec Intel OpenVINO™ DL Streamer – Edge AI Extension pour analyser un flux vidéo en direct provenant d’une caméra IP (simulée).
ms.topic: tutorial
ms.date: 02/04/2021
ms.service: media-services
ms.author: faneerde
author: fvneerden
ms.openlocfilehash: c2a704d5e87e5b5813a59f9c5951688f5c3b893a
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2021
ms.locfileid: "113300913"
---
# <a name="tutorial-analyze-live-video-by-using-live-video-analytics-with-intel-openvino-dl-streamer--edge-ai-extension"></a>Tutoriel : Analyser la vidéo en direct à l’aide de Live Video Analytics avec Intel OpenVINO™ DL Streamer - Edge IA Extension 

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

Ce tutoriel montre comment utiliser Intel OpenVINO™ DL Streamer – Edge AI Extension pour analyser un flux vidéo en direct provenant d’une caméra IP (simulée). Vous allez voir comment ce serveur d’inférence vous donne accès à différents modèles pour détecter des objets (une personne, un véhicule ou un vélo), classifier des objets (attributions de véhicule) et un modèle pour suivre des objets (personne, véhicule et vélo). L’intégration au module gRPC vous permet d’envoyer des images vidéo au serveur d’inférence IA. Les résultats sont alors envoyés au hub IoT Edge. Quand vous exécutez ce service d’inférence sur le même nœud de calcul que Live Video Analytics, vous pouvez profiter de l’envoi des données vidéo par le biais de la mémoire partagée. Ainsi, vous pouvez exécuter une inférence à la fréquence d’images du flux vidéo en direct (par exemple, 30 images/s). 

Ce tutoriel utilise une machine virtuelle Azure comme appareil IoT Edge, ainsi qu’un flux vidéo en direct simulé. Il est basé sur l’exemple de code écrit en C# et s’appuie sur le guide de démarrage rapide [Détecter les événements de mouvement et d’émission](detect-motion-emit-events-quickstart.md).

> [!NOTE]
> Ce didacticiel nécessite l’utilisation d’un ordinateur x86-64 comme appareil de périphérie.

## <a name="prerequisites"></a>Prérequis

* Un compte Azure incluant un abonnement actif. Si vous n’en avez pas déjà un, [créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
  > [!NOTE]
  > Vous aurez besoin d’un abonnement Azure avec des autorisations pour créer des principaux de service (le **rôle propriétaire** permet d’effectuer cette opération). Si vous ne disposez pas des autorisations appropriées, contactez l’administrateur de votre compte pour qu’il vous les accorde. 
* [Visual Studio Code](https://code.visualstudio.com/), avec les extensions suivantes :
    * [Outils IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Kit SDK .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Si vous n’avez pas suivi le guide de démarrage rapide [Détecter les événements de mouvement et d’émission](detect-motion-emit-events-quickstart.md), veillez à effectuez les étapes de [configuration des ressources Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources).

> [!TIP]
> Lors de l’installation d’Azure IoT Tools, vous pouvez être invité à installer Docker. Vous pouvez ignorer cette invite.

## <a name="review-the-sample-video"></a>Réviser l’exemple de vidéo

Quand vous configurez les ressources Azure, une courte vidéo d’un parking est copiée dans Azure sur la machine virtuelle Linux que vous utilisez comme appareil IoT Edge. Ce guide de démarrage rapide utilise le fichier vidéo pour simuler un flux en direct.

Ouvrez une application comme le [lecteur multimédia VLC](https://www.videolan.org/vlc/). Sélectionnez Ctrl+N, puis collez un lien vers [la vidéo](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) pour démarrer la lecture. Vous voyez la séquence vidéo des véhicules sur le parking, la plupart d’entre eux garés et l’un d’eux en déplacement.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

Dans ce guide de démarrage rapide, vous allez utilisez Live Video Analytics sur IoT Edge de concert avec Intel OpenVINO™ DL Streamer – Edge AI Extension pour détecter des objets comme des véhicules, des personnes ou des vélos, afin de les classifier ou d’en effectuer un suivi. Vous publierez les événements d’inférence associés sur un hub IoT Edge.

## <a name="overview"></a>Vue d’ensemble

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/grpc-vas-extension-with-vino.svg" alt-text="Vue d’ensemble de LVA MediaGraph":::

Ce diagramme montre comment les signaux circulent dans ce guide de démarrage rapide. Un [module Edge](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simule une caméra IP hébergeant un serveur RTSP (Real-Time Streaming Protocol). Un nœud [source RTSP](media-graph-concept.md#rtsp-source) extrait le flux vidéo provenant de ce serveur et envoie des images vidéo au nœud [processeur d’extension gRPC](media-graph-concept.md#grpc-extension-processor). 

Le nœud processeur d’extension gRPC prend les images vidéo décodées comme entrée et les relaie à un point de terminaison [gRPC](terminology.md#grpc) exposé par un serveur gRPC. Le nœud prend en charge le transfert de données à l’aide de la [mémoire partagée](https://en.wikipedia.org/wiki/Shared_memory) ou l’incorporation directe du contenu dans le corps des messages gRPC. De plus, le nœud dispose d’un formateur d’image intégré pour la mise à l’échelle et l’encodage des images vidéo avant qu’elles soient relayées au point de terminaison gRPC. Le processus de mise à l’échelle propose des options permettant de préserver les proportions de l’image, de la remplir ou de l’étirer. L’encodeur d’image prend en charge les formats JPEG, PNG ou BMP. Découvrez-en plus sur le processeur [ici](media-graph-extension-concept.md#grpc-extension-processor).

Dans ce tutoriel, vous allez :

1. Déployer le graphe multimédia.
1. interpréter les résultats ;
1. Supprimer des ressources.

## <a name="about-intel-openvino-dl-streamer--edge-ai-extension-module"></a>À propos du module Intel OpenVINO™ DL Streamer – Edge AI Extension


Le module OpenVINO™ DL Streamer - Edge AI Extension est un microservice basé sur Video Analytics Serving (VA Serving) d’Intel qui sert des pipelines d’analytique vidéo créés avec OpenVINO™ DL Streamer. Les développeurs peuvent envoyer des images vidéo décodées au module d’extension IA qui effectue la détection, la classification ou le suivi avant de retourner les résultats. Le module d’extension IA expose des API gRPC compatibles avec les plateformes d’analytique vidéo comme Live Video Analytics sur IoT Edge de Microsoft. 

Pour créer des solutions d’analytique vidéo en direct complexes et à hautes performances, le module Live Video Analytics sur IoT Edge doit être associé à un moteur d’inférence puissant capable de tirer parti de l’échelle à la périphérie. Dans ce tutoriel, les requêtes d’inférence sont envoyées à [Intel OpenVINO™ DL Streamer – Edge AI Extension](), un module Edge conçu pour fonctionner avec Live Video Analytics sur IoT Edge.

Dans la version initiale de ce serveur d’inférence, vous avez accès aux [modèles](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options) suivants :

- object_detection for person_vehicle_bike_detection ![détection d’objet pour véhicule](./media/use-intel-openvino-tutorial/object-detection.png)

- object_classification for vehicle_attributes_recognition ![classification d’objet pour véhicule](./media/use-intel-openvino-tutorial/object-classification.png)

- object_tracking for person_vehicle_bike_tracking ![suivi d’objet pour personne ou véhicule](./media/use-intel-openvino-tutorial/object-tracking.png)

Ce modèle utilise les pipelines de détection d’objet préchargé, de classification d’objet et de suivi d’objet pour démarrer rapidement. De plus, il est fourni avec les modèles préchargés [person-vehicle-bike-detection-crossroad-0078](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/person-vehicle-bike-detection-crossroad-0078/README.md) et [vehicle-attributes-recognition-barrier-0039](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/vehicle-attributes-recognition-barrier-0039/README.md).

> [!NOTE]
> En téléchargeant et en utilisant le module Edge OpenVINO™ DL Streamer – Edge AI Extension d’Intel et le logiciel inclus, vous acceptez les termes et conditions du [contrat de licence](https://www.intel.com/content/www/us/en/legal/terms-of-use.html).
> Intel s’engage à respecter les droits de l’homme et à éviter toute complicité en matière d’abus des droits de l’homme. Consultez les [principes généraux d’Intel en matière de droits de l’homme](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Les produits et logiciels Intel sont destinés uniquement à être utilisés dans des applications qui n’entraînent pas ou ne contribuent pas à une violation d’un droit humain reconnu au niveau international.

Vous pouvez utiliser la flexibilité des différents pipelines pour votre cas d’usage spécifique en modifiant simplement les variables d’environnement de pipeline dans votre modèle de déploiement. Ainsi, vous pouvez modifier rapidement le modèle de pipeline et, en utilisant Live Video Analytics conjointement, changer de pipeline de média et de modèle d’inférence n’est qu’une question de secondes.  

## <a name="create-and-deploy-the-media-graph"></a>Créer et déployer le graphe multimédia

### <a name="examine-and-edit-the-sample-files"></a>Examiner et modifier les exemples de fichiers

Dans le cadre des prérequis, vous avez téléchargé l’exemple de code dans un dossier. Effectuez les étapes suivantes pour examiner et modifier les exemples de fichiers.

1. Dans Visual Studio Code, accédez à *src/edge*. Votre fichier  *.env* et quelques fichiers de modèle de déploiement s’affichent.

    Le modèle de déploiement fait référence au manifeste de déploiement de l’appareil de périphérie. Il inclut des valeurs d’espace réservé. Le fichier  *.env* inclut les valeurs de ces variables.

1. Accédez au dossier *src/cloud-to-device-console-app*. Ce dernier contient votre fichier *appsettings.json* et quelques autres fichiers :

    * ***c2d-console-app.csproj*** : fichier projet pour Visual Studio Code.
    * ***operations.json*** : liste des opérations que vous voulez que le programme exécute.
    * ***Program.cs*** : exemple de code de programme. Ce code :

        * Il charge les paramètres de l’application.
        * Invoque les méthodes directes exposées par le module Live Video Analytics sur IoT Edge. Vous pouvez utiliser le module pour analyser des flux vidéo en direct en invoquant ses [méthodes directes](direct-methods.md).
        * S’interrompt pour vous permettre d’examiner la sortie du programme dans la fenêtre **TERMINAL** et d’examiner les événements qui ont été générés par le module dans la fenêtre **SORTIE**.
        * Invoque des méthodes directes pour nettoyer des ressources.


1. Modifiez le fichier *operations.json* :
    * Changez le lien vers la topologie de graphe :

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json"`

    * Sous `GraphInstanceSet`, modifiez le nom de la topologie de graphe de manière à ce qu’il corresponde à la valeur figurant dans le lien ci-dessus précédent :

      `"topologyName" : "InferencingWithOpenVINOgRPC"`

    * Sous `GraphTopologyDelete`, modifiez le nom :

      `"name": "InferencingWithOpenVINOgRPC"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Générez et déployez le manifeste de déploiement IoT Edge

1. Cliquez avec le bouton droit sur le fichier *src/edge/deployment.openvino.grpc.cpu.template.json*, puis sélectionnez **Générer un manifeste de déploiement IoT Edge**.

    ![Générez un manifeste de déploiement IoT Edge](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    Le fichier manifeste *deployment.openvino.grpc.cpu.amd64.json* est créé dans le dossier *src/edge/config*.

> [!NOTE]
> Nous avons également inclus un modèle *deployment.openvino.grpc.gpu.template.json* qui permet de prendre en charge du GPU pour le module Intel OpenVINO DL Streamer - Edge AI Extension. Ces modèles pointent vers l’image du hub Docker d’Intel.

Les modèles mentionnés ci-dessus pointent vers l’image du hub Docker d’Intel. Si vous voulez plutôt héberger une copie sur Azure Container Registry, vous pouvez effectuer les étapes 1 et 2 ci-dessous :
1. Connectez-vous avec SSH à un appareil sur lequel les outils CLI Docker sont installés (par exemple, votre périphérique) et tirez (pull)/étiquetez/envoyez (push) le conteneur en suivant ces étapes :
    * Tirez (pull) l’image d’Intel à partir du hub Docker :

        `sudo docker pull intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`
    
    * Étiquetez l’image d’Intel avec votre propre nom Azure Container Registry. Remplacez {YOUR ACR NAME} par votre nom ACR que vous pouvez trouver dans le fichier .env :

        `sudo docker image tag intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
    * Envoyez (push) votre image étiquetée à votre Azure Container Registry :

        `sudo docker push {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
2. À présent, vous avez besoin de modifier les modèles pour faire référence à votre nouvelle image hébergée sur Azure Container Registry.
    * Cliquez avec le bouton droit sur *deployment.openvino.grpc.cpu.template.json* et accédez à la partie *lavExtension* du module pour remplacer :

        `intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`

        par :

        `{YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    * Répétez l’étape 2 pour *deployment.openvino.grpc.gpu.template.json*.


3. Si vous avez suivi le guide de démarrage rapide [Détecter les événements de mouvement et d’émission](detect-motion-emit-events-quickstart.md), ignorez cette étape. 

    Dans le cas contraire, en regard du volet **AZURE IOT HUB** dans l’angle en bas à gauche, sélectionnez l’icône **Autres actions**, puis sélectionnez **Définir la chaîne de connexion IoT Hub**. Vous pouvez copier la chaîne à partir du fichier *appsettings.json*. Ou, pour garantir que vous avez configuré le hub IoT approprié dans Visual Studio Code, utilisez la [commande Sélectionner IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Définir la chaîne de connexion IoT Hub](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Vous pouvez être invité à fournir des informations sur le point de terminaison intégré pour le hub IoT. Pour obtenir ces informations, dans le portail Azure, accédez à votre hub IoT et recherchez l’option **Points de terminaison intégrés** dans le volet de navigation gauche. Cliquez et recherchez l’option **Point de terminaison compatible Event Hub** sous la section **Point de terminaison compatible Event Hub**. Copiez et utilisez le texte dans la zone. Le point de terminaison doit ressembler à ceci :  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Cliquez avec le bouton droit sur *src/edge/config/deployment.openvino.grpc.cpu.template.json*, puis sélectionnez **Créer un déploiement pour un seul appareil**. 

    ![Créer un déploiement pour un seul appareil](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. Quand vous êtes invité à sélectionner un appareil IoT Hub, sélectionnez **lva-sample-device**.
1. Après environ 30 secondes, dans l’angle en bas à gauche de la fenêtre, actualisez Azure IoT Hub. L’appareil de périphérie affiche maintenant les modules déployés suivants :

    * Le module Live Video Analytics, nommé **lvaEdge**
    * Le module **rtspsim**, qui simule un serveur RTSP et fait office de source d’un flux vidéo en direct
    * Le module **lvaExtension**, à savoir Intel OpenVINO™ DL Streamer – Edge AI Extension 

### <a name="prepare-to-monitor-events"></a>Préparer la supervision d’événements

Cliquez avec le bouton droit sur l’appareil Live Video Analytics, puis sélectionnez sur **Démarrer la supervision du point de terminaison d’événement intégré**. Vous devez effectuer cette étape pour superviser les événements IoT Hub dans la fenêtre **SORTIE** de Visual Studio Code. 

![Démarrer la surveillance](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles-persons-or-bike"></a>Exécuter l’exemple de programme pour détecter des véhicules, des personnes ou un vélo
Si vous ouvrez la [topologie du graphe](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) pour ce tutoriel dans un navigateur, vous pouvez voir que la valeur de `grpcExtensionAddress` a été définie sur `tcp://lvaExtension:5001`. Comparativement à l’exemple *httpExtensionOpenVINO*, vous n’avez pas besoin de remplacer l’URL par celle du serveur gRPC. En revanche, vous indiquez au module d’exécuter un pipeline spécifique par les variables d’environnement comme mentionné précédemment. Dans le modèle par défaut, nous avons défini la valeur « object_detection » pour « person_vehicle_bike_detection ». Vous pouvez faire des essais avec d’autres pipelines pris en charge. 

1. Dans Visual Studio Code, ouvrez l’onglet **Extensions** (ou appuyez sur Ctrl+Maj+X) et recherchez Azure IoT Hub.
1. Cliquez avec le bouton droit et sélectionnez **Paramètres d’extension**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Paramètres d’extension":::
1. Recherchez et activez « Afficher le message détaillé ».

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Afficher le message détaillé":::
1. Pour démarrer une session de débogage, appuyez sur la touche F5. Des messages s’affichent dans la fenêtre **TERMINAL**.
1. Le code *operations.json* commence par appeler les méthodes directes `GraphTopologyList` et `GraphInstanceList`. Si vous avez nettoyé les ressources après avoir suivi les guides de démarrage rapide précédents, ce processus retourne des listes vides, puis s’interrompt. Pour continuer, sélectionnez la touche Entrée.

    La fenêtre **TERMINAL** affiche le jeu d’appels de méthode directe suivant :

     * Un appel à `GraphTopologySet` qui utilise le `topologyUrl` précédent
     * Un appel à `GraphInstanceSet` qui utilise le corps suivant :

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithOpenVINOgRPC",
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

     * Un appel à `GraphInstanceActivate` qui démarre l’instance de graphe et le flux vidéo
     * Un deuxième appel à `GraphInstanceList` qui indique que l’instance de graphe est dans l’état En cours d’exécution
1. La sortie affichée dans la fenêtre **TERMINAL** fait une pause au niveau d’une invite `Press Enter to continue`. Ne sélectionnez pas encore Entrée. Faites défiler vers le haut pour voir les charges utiles de réponse JSON pour les méthodes directes que vous avez invoquées.
1. Basculez vers la fenêtre **SORTIE** de Visual Studio Code. Les messages indiquant que le module Live Video Analytics sur IoT Edge effectue des envois à IoT Hub s’affichent. La section suivante de ce guide de démarrage rapide décrit ces messages.
1. Le graphe multimédia continue à s’exécuter et à afficher les résultats. Le simulateur RTSP continue de boucler la vidéo source. Pour arrêter le graphe multimédia, retournez dans la fenêtre **TERMINAL**, puis sélectionnez Entrée. 

    La série d’appels suivante nettoie les ressources :
      * Un appel à `GraphInstanceDeactivate` désactive l’instance de graphe.
      * Un appel à `GraphInstanceDelete` supprime l’instance.
      * Un appel à `GraphTopologyDelete` supprime la topologie.
      * Un dernier appel à `GraphTopologyList` indique que la liste est vide.

## <a name="interpret-results"></a>Interpréter les résultats

Quand vous exécutez le graphe multimédia, les résultats du nœud processeur d’extension HTTP passe par le nœud récepteur IoT Hub au hub IoT. Les messages qui s’affichent dans la fenêtre **SORTIE** contiennent une section `body` et une section `applicationProperties`. Pour plus d’informations, consultez [Créer et lire des messages IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

Dans les messages suivants, le module Live Video Analytics définit les propriétés de l’application et le contenu du corps. 

### <a name="mediasessionestablished-event"></a>Événement MediaSessionEstablished

Quand un graphe multimédia est instancié, le nœud source RTSP tente de se connecter au serveur RTSP qui s’exécute sur le conteneur rtspsim-live555. Si la connexion réussit, l’événement suivant est affiché. Le type d’événement est **Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished**.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1612432131600584 1 IN IP4 172.18.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/homes_00425.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-214.166\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/homes_00425.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=64001F;sprop-parameter-sets=Z2QAH6zZQFAFuwFsgAAAAwCAAAAeB4wYyw==,aOvhEsiw\r\na=control:track1\r\n"
}
```

Dans ce message, notez les informations suivantes :

* Le message est un événement de diagnostic. `MediaSessionEstablished` indique que le nœud source RTSP (l’objet) s’est connecté au simulateur RTSP et a commencé à recevoir un flux en direct (simulé).
* Dans `applicationProperties`, `subject` indique que le message a été généré à partir du nœud source RTSP dans le graphe multimédia.
* Dans `applicationProperties`, `eventType` indique que cet événement est un événement de diagnostic.
* `eventTime` indique l’heure à laquelle l’événement s’est produit.
* `body` contient des données relatives à l’événement de diagnostic. Dans ce cas, les données comprennent les détails du [protocole SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Événement d'inférence

Le nœud processeur d’extension gRPC reçoit les résultats d’inférence en provenance d’Intel OpenVINO™ DL Streamer – Edge AI Extension. Il émet ensuite les résultats par le biais du nœud récepteur IoT Hub sous la forme d’événements d’inférence. 

Dans ces événements, le type est défini sur `entity` pour indiquer qu’il s’agit d’une entité, comme une voiture ou un camion. La valeur `eventTime` est l’heure UTC à laquelle l’objet a été détecté. 

Dans l’exemple suivant, vous voyez qu’il a identifié un véhicule, le type du véhicule (camionnette) et sa couleur (blanc), le tout avec un niveau de confiance au-dessus de 0,9. Un ID a également été affecté à l’entité du fait de l’utilisation du modèle de suivi d’objet.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "timestamp": 145118912223221,
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9605301
        },
        "attributes": [
          {
            "name": "color",
            "value": "white",
            "confidence": 0.9605301
          },
          {
            "name": "type",
            "value": "car",
            "confidence": 0.9605301
          }
        ],
        "box": {
          "l": 0.3958135,
          "t": 0.078730375,
          "w": 0.48403296,
          "h": 0.94352424
        },
        "id&quot;: &quot;1"
      }
    }
}
```

Dans le message, notez les informations suivantes :

* Dans `applicationProperties`, `subject` fait référence au nœud présent dans la topologie de graphe à partir duquel le message a été généré. 
* Dans `applicationProperties`, `eventType` indique que cet événement est un événement d’analytique.
* La valeur `eventTime` correspond à l’heure à laquelle l’événement s’est produit.
* La section `body` contient des données relatives à l’événement d’analytique. Dans ce cas, l’événement est un événement d’inférence. Par conséquent, le corps contient des données `inferences`.
* La section `inferences` indique que le `type` est `entity`. Cette section inclut des données supplémentaires sur l’entité.

## <a name="run-the-sample-program-to-detect-persons-or-vehicles-or-bikes"></a>Exécuter l’exemple de programme pour détecter les personnes, les véhicules ou les vélos
Pour utiliser un modèle différent, vous devez modifier le modèle de déploiement. Pour basculer d’un modèle pris en charge à un autre, vous pouvez modifier les variables d’environnement situées dans le module lvaExtension. Consultez ce [document sur GitHub](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options) pour connaître les valeurs et combinaisons prises en charge pour les modèles.

```
"Env":[
"PIPELINE_NAME=object_classification",
"PIPELINE_VERSION=vehicle_attributes_recognition"
],
```
> [!TIP]
> Copiez le modèle et stockez-le sous un nouveau nom pour chaque pipeline possible. De cette façon, vous pouvez basculer entre les modèles en créant un déploiement basé sur l’un d’eux.

Une fois que vous avez modifié les variables, vous pouvez redéployer le modèle sur l’appareil. Vous pouvez maintenant répéter les étapes ci-dessus pour réexécuter l’exemple de programme avec le nouveau pipeline. Les résultats d’inférence sont similaires (dans le schéma), mais ils présentent plus ou moins d’informations selon le modèle de pipeline que vous avez choisi.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez d’essayer d’autres guides de démarrage rapide ou tutoriels, conservez les ressources que vous avez créées. Dans le cas contraire, accédez au portail Azure, puis à vos groupes de ressources. Sélectionnez le groupe de ressources où vous avez exécuté ce tutoriel, puis supprimez toutes les ressources.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue les défis supplémentaires pour les utilisateurs expérimentés :

* Utilisez une [caméra IP](https://en.wikipedia.org/wiki/IP_camera) qui prend en charge RTSP au lieu d’utiliser le simulateur RTSP. Vous pouvez rechercher les caméras IP qui prennent RTSP en charge dans la page des produits [conformes ONVIF](https://www.onvif.org/conformant-products/). Recherchez les appareils conformes aux profils G, S ou T.
* Utilisez un appareil Linux x64 Intel plutôt qu’une machine virtuelle Linux Azure. Cet appareil doit se trouver dans le même réseau que la caméra IP. Vous pouvez suivre les instructions mentionnées dans [Installer le runtime Azure IoT Edge sur Linux](../../iot-edge/how-to-install-iot-edge.md). Inscrivez ensuite l’appareil auprès d’Azure IoT Hub en suivant les instructions mentionnées dans [Déployer votre premier module IoT Edge sur un appareil virtuel Linux](../../iot-edge/quickstart-linux.md).
