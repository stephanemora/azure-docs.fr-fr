---
title: Analyser des vidéos en direct avec Live Video Analytics sur IoT Edge et Azure Custom Vision
description: Apprenez à utiliser Custom Vision pour créer un modèle de conteneurs capable de détecter un camion jouet et d’utiliser la fonctionnalité d’extensibilité de l’IA de Live Video Analytics sur IoT Edge (LVA) pour déployer le modèle en périphérie et détecter des jouets à partir d’un flux vidéo en direct.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 0e980ac73d77b6fbbfdb8178f285904d3bf29920
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929309"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>Tutoriel : Analyser des vidéos en direct avec Live Video Analytics sur IoT Edge et Azure Custom Vision

Dans ce tutoriel, vous allez apprendre à utiliser [Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) pour créer un modèle de conteneurs capable de détecter un camion jouet et d’utiliser la [fonctionnalité d’extensibilité de l’IA](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model) de Live Video Analytics sur IoT Edge (LVA) pour déployer le modèle en périphérie et détecter des jouets à partir d’un flux vidéo en direct.

Nous vous montrerons comment réunir la puissance de Custom Vision, qui permet à chacun de créer et d’entraîner un modèle de vision par ordinateur en chargeant et en étiquetant simplement quelques images, sans posséder la moindre connaissance en science des données, ML ou IA, ainsi que des fonctionnalités de Live Video Analytics permettant de déployer facilement un modèle personnalisé en tant que conteneur en périphérie et d’analyser un flux vidéo en direct. Ce tutoriel utilise une machine virtuelle Azure en tant qu’appareil IoT Edge, est basé sur l’exemple de code écrit en C# et s’appuie sur le guide de démarrage rapide [Détecter les événements de mouvement et d’émission](detect-motion-emit-events-quickstart.md).

Ce didacticiel explique les procédures suivantes :

> [!div class="checklist"]
> * Configurer les ressources appropriées.
> * Créer un modèle Custom Vision dans le cloud détectant les camions jouets dans le cloud et le déployer en périphérie
> * Créer et déployer un graphique multimédia avec l’extension http sur un modèle Custom Vision
> * Exécuter l’exemple de code.
> * Examinez et interprétez les résultats.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée  

Nous vous recommandons de lire les articles suivants avant de commencer : 

* [Présentation de Live Video Analytics sur IoT Edge](overview.md)
* [Présentation d’Azure Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)
* [Terminologie de Live Video Analytics sur IoT Edge](terminology.md)
* [Concepts relatifs aux graphes multimédia](media-graph-concept.md)
* [Live Video Analytics sans enregistrement de vidéo](analyze-live-video-concept.md)
* [Exécuter Live Video Analytics avec votre propre modèle](use-your-model-quickstart.md)
* [Tutoriel : Développement d’un module IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Modification de deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Prérequis

Les prérequis pour ce tutoriel sont les suivants :

* [Visual Studio Code](https://code.visualstudio.com/) sur votre machine de développement avec les extensions [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) et [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > Vous serez peut-être invité à installer Docker. Ignorez cette invite.
* Le [SDK .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) sur votre machine de développement.
* Vérifiez que vous avez :
    
    * [Configuré des ressources Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [Configurer votre environnement de développement](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)

## <a name="review-the-sample-video"></a>Réviser l’exemple de vidéo


Ce tutoriel utilise un fichier de [vidéo d’inférence du jouet](https://lvamedia.blob.core.windows.net/public/t2.mkv/) pour simuler un stream en direct. Vous pouvez regarder la vidéo grâce à une application telle que [VLC Media Player](https://www.videolan.org/vlc/). Sélectionnez Ctrl+N, puis collez un lien vers [la vidéo d’inférence du jouet](https://lvamedia.blob.core.windows.net/public/t2.mkv) pour démarrer la lecture. Pendant que vous regardez la vidéo, notez qu’au bout de 36 secondes un camion jouet apparaît dans la vidéo. Le modèle personnalisé a été entraîné à détecter ce jouet spécifique. Dans ce tutoriel, vous allez utiliser Live Video Analytics sur IoT Edge afin de détecter ce type de jouet et de publier des événements d’inférence associés sur IoT Edge Hub.

## <a name="overview"></a>Vue d’ensemble

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Présentation de Custom Vision":::

Ce diagramme montre comment les signaux circulent dans ce tutoriel. Un [module de périphérie](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simule une caméra IP hébergeant un serveur RTSP (Real-Time Streaming Protocol). Un nœud de [source RTSP](media-graph-concept.md#rtsp-source) récupère le flux vidéo à partir de ce serveur et envoie des images vidéo au nœud [processeur de filtre de fréquence d’images](media-graph-concept.md#frame-rate-filter-processor). Ce processeur limite la fréquence d’images du flux vidéo qui atteint le nœud [processeur d’extension HTTP](media-graph-concept.md#http-extension-processor).
Le nœud d’extension HTTP joue le rôle d’un proxy. Il convertit les images vidéo dans le type d’image spécifié. Ensuite, il relaie l’image sur REST vers un autre module de périphérie qui exécute un modèle IA derrière un point de terminaison HTTP. Dans cet exemple, ce module Edge est le modèle de détecteur de camion jouet créé à l’aide de Custom Vision. Le nœud processeur d’extension HTTP collecte les résultats de la détection et publie les événements sur le nœud [récepteur IoT Hub](media-graph-concept.md#iot-hub-message-sink). Le nœud envoie ensuite ces événements à [IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub).

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Créer et déployer un modèle de détection de jouet avec Custom Vision 

Comme le nom Custom Vision le suggère, vous pouvez utiliser ce module pour créer votre propre détecteur ou classifieur personnalisé d’objets dans le cloud. Il fournit une interface simple, facile à utiliser et intuitive pour créer des modèles Custom Vision qui peuvent être déployés dans le cloud ou en périphérie grâce à des conteneurs. 

Afin de créer un détecteur de camion jouet, nous vous recommandons de suivre les étapes de la section Créer un détecteur d’objet du [guide de démarrage rapide](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector) de Custom Vision disponible sur le portail web.

Informations complémentaires :
 
* Pour ce tutoriel, n’utilisez pas les images fournies dans la [section Prérequis](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#prerequisites) du guide de démarrage rapide. Nous avons préféré utiliser un jeu d’images afin de créer un modèle Custom Vision détectant les jouets. Nous vous suggérons d’utiliser [ces images](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip) lorsque vous êtes invité à [choisir vos images d’entraînement ](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#choose-training-images) dans le guide de démarrage rapide.
* Dans la section Balisage d’images du guide démarrage rapide, veillez à baliser le camion jouet vu dans l’image avec la balise « camion de livraison ».

Une fois que vous avez terminé, si le modèle vous convient, vous pouvez l’exporter vers un conteneur Docker à l’aide du bouton Exporter de l’onglet Performances. Vérifiez que vous avez choisi Linux comme type de plateforme de conteneur. Il s’agit de la plateforme sur laquelle le conteneur s’exécutera. L’ordinateur sur lequel vous téléchargez le conteneur peut fonctionner sous Windows ou sous Linux. Les instructions qui suivent se basent sur le fichier conteneur téléchargé sur un ordinateur Windows.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Dockerfile":::
 
1. Vous devez trouver un fichier zip nommé `<projectname>.DockerFile.Linux.zip` sur votre ordinateur local. 
1. Vérifiez que Docker est bien installé. Si ce n’est pas le cas, installez [Docker](https://docs.docker.com/get-docker/) sur votre ordinateur Windows.
1. Décompressez le fichier téléchargé à l’emplacement de votre choix. Utilisez la ligne de commande pour accéder au répertoire du dossier décompressé.
    
    Exécutez les commandes suivantes 
    
    1. `docker build -t cvtruck` 
    
        Cette commande télécharge un ensemble de packages et génère l’image Docker et lui ajoute la balise `cvtruck:latest`. 
    
        > [!NOTE]
        > Si la génération réussit, la sortie suivante doit s’afficher `- Successfully built <docker image id> and Successfully tagged cvtruck:latest.`. Si la commande de génération a échoué, réessayez, car les packages de dépendances ne se téléchargent pas la première fois.
    1. `docker  image ls`

        Cette commande vérifie si la nouvelle image se trouve dans votre registre local.
    1. `docker run -p 127.0.0.1:80:80 -d cvtruck`
    
        Cette commande doit publier le port exposé de Docker (80) sur le port de votre ordinateur local (80).
    1. `docker container ls`
    
        Cette commande vérifie les mappages de port et si le conteneur Docker s’exécute correctement sur votre ordinateur. La sortie suivante doit s’afficher :

        ```
        CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
        8b7505398367        cvtruck             "/bin/sh -c 'python …"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            Cette commande teste le conteneur sur l’ordinateur local et si un camion identique à celui de notre modèle se trouve dans l’image, la sortie suivante doit s’afficher, ce qui suggère que le camion de livraison a été détecté avec une probabilité de 90,12 %.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>Examiner les exemples de fichiers

1. Dans VSCode, accédez à « src/edge ». Vous verrez le fichier. env que vous avez créé, ainsi que quelques fichiers de modèle de déploiement.

    Le modèle de déploiement fait référence au manifeste de déploiement du périphérique avec des valeurs d’espace réservé. Le fichier .env contient les valeurs de ces variables.
1. Ensuite, accédez au dossier « src/cloud-to-device-console-app ». Vous y voyez le fichier appsettings.json que vous avez créé, ainsi que quelques autres fichiers :

    * c2d-console-app.csproj : fichier projet pour VSCode.
    * operations.json : répertorie les différentes opérations que vous souhaitez que le programme exécute.
    * Program.cs : exemple de code de programme, qui effectue les opérations suivantes :

        * Il charge les paramètres de l’application.
        * Utilisez les méthodes directes du module Live Video Analytics sur IoT Edge pour créer une topologie, instancier le graphique et l’activer.
        * Il s’interrompt pour vous permettre d’examiner la sortie du graphique dans la fenêtre TERMINAL et les événements générés par le module IoT dans la fenêtre SORTIE.
        * Désactivez l’instance de graphique, supprimez-la, puis supprimez la topologie de graphique.
        
## <a name="generate-and-deploy-the-deployment-manifest"></a>Générer et déployer le manifeste de déploiement

1. Dans VSCode, accédez à « src/cloud-to-device-console-app/operations.json ».

1. Sous GraphTopologySet, vérifiez les points suivants :<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
1. Sous GraphInstanceSet, vérifiez les points suivants : 
    1. « topologyName » : « InferencingWithHttpExtension »
    1. Ajoutez ce qui suit au groupe de paramètres : `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. Remplacez la valeur du paramètre rtspUrl par « rtsp://rtspsim:554/media/t2.mkv ».    
1. Sous GraphTopologyDelete, vérifiez le nom est : « InferencingWithHttpExtension »
1. Cliquez ensuite avec le bouton droit sur le fichier « src/edge/deployment.customvision.json », puis cliquez sur **Générer le manifeste de déploiement IoT Edge**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="Générer un manifeste de déploiement IoT Edge":::
  
    Un fichier manifeste nommé « deployment.amd64.json » devrait être créé dans le dossier src/edge/config.
1. Ouvrez le fichier « src/Edge/Deployment.customvision.template.json » et recherchez le bloc JSON registryCredentials. Dans ce bloc, vous trouverez l’adresse de votre registre de conteneurs Azure, ainsi que son nom d’utilisateur et son mot de passe.
1. Transférez le conteneur Custom Vision local dans votre registre de conteneurs Azure grâce à la ligne de commande.

    1. Connectez-vous au registre en exécutant la commande suivante :
    
        `docker login <address>`
    
        Saisissez le nom d’utilisateur et le mot de passe lorsque ces derniers vous sont demandés. 
        
        > [!NOTE]
        > Le mot de passe ne s’affiche pas sur la ligne de commande.
    1. Ajoutez une balise à votre image à l’aide de la commande :<br/>`docker tag cvtruck   <address>/cvtruck`
    1. Transférez votre image à l’aide de la commande :<br/>`docker push <address>/cvtruck`

        Si le transfert réussit, la remarque « Pushed » doit s’afficher sur la ligne de commande avec l’algorithme SHA pour l’image. 
    1. Vous pouvez également vérifier que le transfert a réussi en accédant à votre registre de conteneurs Azure sur le portail Azure. À cet endroit, le nom du référentiel et la balise s’afficheront. 
1. Définissez la chaîne de connexion IoT Hub en cliquant sur l’icône « Autres actions » en regard du volet AZURE IOT HUB dans l’angle inférieur gauche. Vous pouvez copier la chaîne à partir du fichier appsettings.json. (Voici une autre approche recommandée pour vous assurer que l’IoT Hub approprié est configuré dans VSCode via la [commande Sélectionner IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Chaîne de connexion":::
1. Ensuite, cliquez avec le bouton droit sur « src/edge/config/ deployment.customvision.amd64.json », puis cliquez sur **Créer un déploiement pour un seul appareil**. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Créer un déploiement pour un seul appareil":::
1. Vous êtes ensuite invité à sélectionner un appareil IoT Hub. Sélectionnez lva-sample-device dans la liste déroulante.
1. Environ 30 secondes plus tard, actualisez Azure IoT Hub dans la section inférieure gauche et vous devriez voir le périphérique avec les modules suivants déployés :

    * Le module Live Video Analytics sur IoT Edge nommé « lvaEdge ».
    * Un module nommé `rtspsim`, qui simule un serveur RTSP, agissant comme source d’un flux vidéo en direct.
    * Un module nommé `cv` qui, comme son nom l’indique, est le modèle de détection de camion jouet Custom Vision qui applique une vision personnalisée aux images et retourne plusieurs types de balises. (Notre modèle a été entraîné à une seule balise, « camion de livraison »).

## <a name="prepare-for-monitoring-events"></a>Préparez-vous à des événements de supervision

Cliquez avec le bouton droit sur l’appareil Live Video Analytics, puis sélectionnez sur **Démarrer la supervision du point de terminaison d’événement intégré**. Vous devez effectuer cette étape pour superviser les événements IoT Hub dans la fenêtre SORTIE de Visual Studio Code.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="Démarrer la supervision du point de terminaison d'événement intégré":::

## <a name="run-the-sample-program"></a>Exécuter l'exemple de programme

Si vous ouvrez la topologie de graphique de ce tutoriel dans un navigateur, vous constaterez que la valeur de la commande inferencingUrl a été définie sur http://cv:80/image, ce qui signifie que le serveur d’inférence retournera les résultats après la détection des camions jouets, le cas échéant, dans la vidéo en direct.

1. Pour démarrer une session de débogage, appuyez sur la touche F5. Des messages s’affichent dans la fenêtre TERMINAL.
1. Le code operations.json commence par appeler les méthodes directes GraphTopologyList et GraphInstanceList. Si vous avez nettoyé les ressources après avoir suivi les guides de démarrage rapide précédents, ce processus retourne des listes vides, puis s’interrompt. Pour continuer, sélectionnez la touche Entrée.
    
   La fenêtre TERMINAL affiche le jeu d’appels de méthode directe suivant :
    
   * Un appel à GraphTopologySet qui utilise l’élément topologyUrl précédent.
   * Un appel à GraphInstanceSet qui utilise le corps suivant :
        
   ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "CustomVisionWithHttpExtension",
            "description": "Sample graph description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv:80/image"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
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
    
   * Un appel à GraphInstanceActivate qui démarre l’instance de graphique et le flux vidéo.
   * Un deuxième appel à GraphInstanceList qui indique que l’instance de graphique est en cours d’exécution.
    
1. La sortie de la fenêtre TERMINAL s’interrompt avec l’invite Appuyez sur Entrée pour continuer. Ne sélectionnez pas encore Entrée. Faites défiler vers le haut pour voir les charges utiles de réponse JSON pour les méthodes directes que vous avez invoquées.
1. Basculez vers la fenêtre SORTIE de Visual Studio Code. Les messages indiquant que le module Live Video Analytics sur IoT Edge effectue des envois à IoT Hub s’affichent. La section suivante de ce tutoriel décrit ces messages.
1. Le graphe multimédia continue à s’exécuter et à afficher les résultats. Le simulateur RTSP continue de boucler la vidéo source. Pour arrêter le graphe multimédia, retournez dans la fenêtre TERMINAL, puis sélectionnez Entrée.
La série d’appels suivante nettoie les ressources :
    
   * Un appel à GraphInstanceDeactivate désactive l’instance de graphique.
   * Un appel à GraphInstanceDelete supprime l’instance.
   * Un appel à GraphTopologyDelete supprime la topologie.
   * Un dernier appel à GraphTopologyList indique que la liste est maintenant vide.
    
## <a name="interpret-the-results"></a>Interpréter les résultats

Quand vous exécutez le graphe multimédia, les résultats du nœud processeur d’extension HTTP passe par le nœud récepteur IoT Hub au hub IoT. Les messages que vous voyez dans la fenêtre SORTIE contiennent une section « body » et une section « applicationProperties ». Pour plus d’informations, consultez [Créer et lire des messages IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

Dans les messages suivants, le module Live Video Analytics définit les propriétés de l’application et le contenu du corps.

### <a name="mediasessionestablished-event"></a>Événement MediaSessionEstablished

Quand un graphe multimédia est instancié, le nœud source RTSP tente de se connecter au serveur RTSP qui s’exécute sur le conteneur rtspsim-live555. Si la connexion réussit, l’événement suivant est affiché. Le type d’événement est Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

Dans ce message, notez les informations suivantes :

* Le message est un événement de diagnostic. L’événement MediaSessionEstablished indique que le nœud source RTSP (l’objet) s’est connecté au simulateur RTSP et a commencé à recevoir un flux en direct (simulé).
* Dans applicationProperties, « subject » indique que le message a été généré à partir du nœud source RTSP dans le graphique multimédia.
* Dans applicationProperties, « eventType » indique qu’il s’agit d’un événement de diagnostic.
* « eventTime » indique l’heure à laquelle l’événement s’est produit.
* Le corps contient des données relatives à l’événement de diagnostic. Dans ce cas, les données comprennent les détails du [protocole SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Événement d'inférence

Le nœud de processeur d’extension HTTP reçoit les résultats d’inférence du conteneur Custom Vision et les émet via le nœud du récepteur IoT Hub en tant qu’événements d’inférence.

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

Notez les points suivants dans les messages ci-dessus :

* Dans applicationProperties, « subject » fait référence au nœud dans le graphique multimédia à partir duquel le message a été généré. Dans ce cas, le message provient du processeur d’extension HTTP.
* Dans applicationProperties, « eventType » indique qu’il s’agit d’un événement d’inférence Analytics.
* « eventTime » indique l’heure à laquelle l’événement s’est produit.
* « body » contient des données relatives à l’événement Analytics. Dans ce cas, l’événement est un événement d’inférence et, par conséquent, le corps contient un groupe d’inférences appelées « prédictions ».
* La section « predictions » contient la liste des prédictions où le camion de livraison (balise = camion de livraison) se trouve dans le cadre. Comme vous vous en souvenez, camion de livraison est la balise personnalisée que vous avez fournie à votre modèle personnalisé entraîné à reconnaître un camion jouet et le modèle détecte et identifie le camion jouet dans la vidéo d’entrée avec différents scores de confiance de probabilité.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez d’essayer les autres tutoriels ou guides de démarrage rapide, vous devez vous en tenir aux ressources créées. Dans le cas contraire, accédez au portail Azure et à vos groupes de ressources, sélectionnez le groupe de ressources sous lequel vous avez exécuté ce tutoriel, puis supprimez le groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue les défis supplémentaires pour les utilisateurs expérimentés :

* Utilisez une [caméra IP](https://en.wikipedia.org/wiki/IP_camera) qui prend en charge RTSP au lieu d’utiliser le simulateur RTSP. Vous pouvez rechercher les caméras IP qui prennent RTSP en charge dans la page des produits [conformes ONVIF](https://www.onvif.org/conformant-products/). Recherchez les appareils conformes aux profils G, S ou T.
* Utilisez un appareil Linux AMD64 ou x64 plutôt qu’une machine virtuelle Linux Azure. Cet appareil doit se trouver dans le même réseau que la caméra IP. Vous pouvez suivre les instructions mentionnées dans [Installer le runtime Azure IoT Edge sur Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux). 

Inscrivez ensuite l’appareil auprès d’Azure IoT Hub en suivant les instructions mentionnées dans [Déployer votre premier module IoT Edge sur un appareil virtuel Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).

