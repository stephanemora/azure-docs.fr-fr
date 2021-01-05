---
title: FAQ Live Video Analytics sur IoT Edge – Azure
description: Cette rubrique fournit des réponses pour les FAQ concernant Live Video Analytics sur IoT Edge.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 521cd0e4f5fc8232a000e10520298a979ba1c14c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401574"
---
# <a name="frequently-asked-questions-faqs"></a>Forum Aux Questions (FAQ)

Cette rubrique fournit des réponses pour les FAQ concernant Live Video Analytics sur IoT Edge.

## <a name="general"></a>Général

### <a name="what-are-the-system-variables-that-can-be-used-in-graph-topology-definition"></a>Quelles variables système peuvent être utilisées dans une définition de topologie de graphique ?

|Variable   |Description|
|---|---|
|[System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|Représente un instant en heure UTC, généralement exprimé sous la forme d’une date et d’une heure de la journée (représentation de base : aaaaMMjjTHHmmssZ).|
|System.PreciseDateTime|Représente une instance de date/heure UTC au format conforme de fichier ISO8601 avec les millisecondes (représentation de base : aaaaMMjjTHHmmss.fffZ).|
|System.GraphTopologyName   |Représente une topologie de graphique multimédia, et contient le blueprint d’un graphique.|
|System.GraphInstanceName|  Représente une instance de graphique multimédia ; contient des valeurs de paramètre et référence la topologie.|

## <a name="configuration-and-deployment"></a>Configuration et déploiement

### <a name="can-i-deploy-the-media-edge-module-to-a-windows-10-device"></a>Puis-je déployer le module multimédia Edge sur un appareil Windows 10 ?

Oui. Consultez l’article [Conteneurs Linux sur Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Capture des paramètres de caméra IP et RTSP

### <a name="do-i-need-to-use-a-special-sdk-on-my-device-to-send-in-a-video-stream"></a>Dois-je utiliser un Kit de développement logiciel (SDK) spécial sur mon appareil pour envoyer un flux vidéo ?

Non. Live Video Analytics sur IoT Edge prend en charge la capture multimédia à l’aide du protocole RTSP de diffusion vidéo en continu (qui est géré par la plupart des caméras IP).

### <a name="can-i-push-media-to-live-video-analytics-on-iot-edge-using-rtmp-or-smooth-like-a-media-services-live-event"></a>Puis-je envoyer des données multimédias à Live Video Analytics sur IoT Edge à l’aide de RTMP ou Smooth (comme un événement en direct Media Services) ?

* Non. Live Video Analytics ne prend en charge que le protocole RTSP pour la capture de vidéos à partir de caméras IP.
* Toute caméra qui prend en charge le streaming RTSP sur TCP/HTTP doit être compatible. 

### <a name="can-i-reset-or-update-the-rtsp-source-url-on-a-graph-instance"></a>Puis-je réinitialiser ou mettre à jour l’URL source RTSP sur une instance de graphique ?

Oui, quand l’instance de graphique est à l’état inactif.  

### <a name="is-there-a-rtsp-simulator-available-to-use-during-testing-and-development"></a>Existe-t-il un simulateur RTSP pouvant être utilisé pendant les tests et le développement ?

Oui. Il existe un module de périphérie de [simulateur RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555), qui peut être utilisé dans les didacticiels et les guides de démarrage rapide afin de prendre en charge le processus d’apprentissage. Ce module est fourni en guise de service Meilleur effort et peut ne pas toujours être disponible. Il est vivement recommandé de ne pas l’utiliser pendant plus de quelques heures. Nous vous recommandons d’effectuer des tests avec votre source RTSP réelle avant de planifier un déploiement de production.

### <a name="do-you-support-onvif-discovery-of-ip-cameras-at-the-edge"></a>Prenez-vous en charge la découverte ONVIF des caméras IP en périphérie ?

Non, la découverte ONVIF des appareils en périphérie n’est pas gérée.

## <a name="streaming-and-playback"></a>Diffusion en continu et lecture

### <a name="can-assets-recorded-to-ams-from-the-edge-be-played-back-using-media-services-streaming-technologies-like-hls-or-dash"></a>Les ressources enregistrées dans AMS à partir de la périphérie sont-elles lues à l’aide de technologies de diffusion en continu de Media Services, comme HLS ou DASH ?

Oui. Les ressources enregistrées peuvent être diffusées en continu comme n’importe quelle autre ressource dans Azure Media Services. Pour pouvoir diffuser le contenu en continu, vous devez créer un point de terminaison de streaming, qui doit être en cours d’exécution. Le processus de création du localisateur de streaming standard vous donne accès à un manifeste HLS ou DASH pour la diffusion en continu vers n’importe quelle infrastructure de lecteur la prenant en charge. Pour plus d’informations sur la création de manifestes de publication HLS ou DASH, voir [L’empaquetage dynamique](../latest/dynamic-packaging-overview.md).

### <a name="can-i-use-the-standard-content-protection-and-drm-features-of-media-services-on-an-archived-asset"></a>Puis-je utiliser la protection de contenu standard et les fonctionnalités DRM de Media Services sur une ressource archivée ?

Oui. Toutes les fonctionnalités de protection de contenu, de chiffrement dynamique et DRM standard peuvent être utilisées sur les ressources enregistrées à partir d’un graphique multimédia.

### <a name="what-players-can-i-use-to-view-content-from-the-recorded-assets"></a>Quels lecteurs puis-je utiliser pour afficher le contenu des ressources enregistrées ?

Tous les lecteurs standard qui gèrent les instances Apple HTTP Live Streaming (HLS) conformes, version 3 ou 4, sont pris en charge. En outre, tout lecteur respectant la norme de lecture MPEG-DASH est également pris en charge.

Les lecteurs recommandés pour les tests sont les suivants :

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Shaka-Player](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple HTTP Live Streaming natif](https://developer.apple.com/streaming/)
* Lecteur vidéo HTML5 intégré dans Edge, Chrome ou Safari
* Lecteurs commerciaux qui prennent en charge la lecture HLS ou DASH

### <a name="what-are-the-limits-on-streaming-a-media-graph-asset"></a>Quelles sont les limites de diffusion en continu d’une ressource de graphique multimédia ?

La diffusion en continu d’une ressource en direct ou enregistrée à partir d’un graphique multimédia utilise l’infrastructure à grande échelle et le point de terminaison de streaming que Media Services prend en charge dans le cadre de la diffusion en continu à la demande et en direct pour les clients OTT, de l’audiovisuel, des médias et du divertissement. Cela signifie que vous pouvez rapidement et facilement activer Azure CDN, Verizon ou Akamai pour distribuer votre contenu à un public de quelques spectateurs ou de plusieurs millions de personnes, selon votre scénario.

Le contenu peut être proposé via Apple HTTP Live Streaming (HLS) ou MPEG-DASH.

## <a name="design-your-ai-model"></a>Conception d’un modèle IA 

### <a name="i-have-multiple-ai-models-wrapped-in-a-docker-container-how-should-i-use-them-with-live-video-analytics"></a>J’ai plusieurs modèles IA inclus dans un conteneur Docker. Comment les utiliser avec Live Video Analytics ? 

Les solutions sont différentes selon le protocole de communication utilisé par le serveur d’inférence pour communiquer avec Live Video Analytics. Voici quelques méthodes possibles.

#### <a name="http-protocol"></a>Protocole HTTP :

* Conteneur unique (lvaExtension unique) :  

   Dans votre serveur d’inférence, vous pouvez utiliser un seul port, mais des points de terminaison différents selon les modèles IA. Prenons un exemple Python. Vous pouvez utiliser une `route` par modèle : 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   Dans votre déploiement Live Video Analytics, quand vous instanciez des graphes, définissez ainsi l’URL du serveur d’inférence pour chaque instance : 

   Première instance : URL du serveur inférence = `http://lvaExtension:44000/score/face_detection`<br/>
   Deuxième instance : URL du serveur inférence = `http://lvaExtension:44000/score/vehicle_detection`  
    > [!NOTE]
    > Vous pouvez également exposer vos modèles IA sur des ports différents et les appeler quand vous instanciez des graphes.  

* Plusieurs conteneurs : 

   Chaque conteneur est déployé avec un nom différent. Dans la documentation Live Video Analytics, nous vous avons pour le moment montré comment déployer une extension sous le nom **lvaExtension**. Vous pouvez maintenant développer deux conteneurs distincts. Ils ont la même interface HTTP (c’est-à-dire le même point de terminaison `/score`). Déployez-les avec des noms différents et veillez à ce que les deux écoutent des **ports distincts**. 

   Par exemple, un conteneur nommé `lvaExtension1` écoute le port `44000`, l’autre conteneur nommé `lvaExtension2` écoute le port `44001`. 

   Dans votre topologie Live Video Analytics, vous instanciez deux graphes avec différentes URL d’inférence : 

   Première instance : URL du serveur inférence = `http://lvaExtension1:44001/score`    
   Deuxième instance : URL du serveur inférence = `http://lvaExtension2:44001/score`
   
#### <a name="grpc-protocol"></a>Protocole gRPC : 

Avec la version 1.0 du module Live Video Analytics, le seul moyen d’utiliser un protocole gRPC serait de faire en sorte que le serveur gRPC expose des modèles IA différents sur des ports distincts. Dans [cet exemple](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json), un seul port (44000) expose tous les modèles Yolo. En théorie, le serveur gRPC Yolo pourrait être réécrit pour exposer des modèles sur 44000, d’autres sur 45000, etc. 

Avec la version 2.0 du module Live Video Analytics, une nouvelle propriété est ajoutée au nœud de l’extension gRPC. Cette propriété s’appelle **extensionConfiguration**, une chaîne facultative utilisable dans le contrat gRPC. Si vous avez plusieurs modèles IA empaquetés dans un seul serveur d’inférence, vous n’aurez pas besoin d’exposer un nœud par modèle IA. Pour une instance de graphe, vous pourrez plutôt, en tant que fournisseur d’extensions, définir la manière de sélectionner les différents modèles IA à l’aide de la propriété **extensionConfiguration**. Pendant l’exécution, Live Video Analytics transmettra cette chaîne au serveur d’inférence, qui pourra l’utiliser pour appeler le modèle IA souhaité. 

### <a name="i-am-building-a-grpc-server-around-an-ai-model-and-want-to-be-able-to-support-being-used-by-multiple-camerasgraph-instances-how-should-i-build-my-server"></a>Je crée un serveur gRPC autour d’un modèle IA et je souhaite pouvoir prendre en charge l’utilisation de plusieurs caméras/instances de graphes. Comment créer mon serveur ? 

 Tout d’abord, vérifiez que votre serveur peut traiter plusieurs demandes à la fois, ou bien fonctionner dans des threads parallèles. 

Dans l’un des [exemples gRPC Live Video Analytics](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py), un nombre par défaut de canaux parallèles est défini. Consultez l'article : 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

Dans l’instanciation de serveur gRPC ci-dessus, le serveur ne peut ouvrir que trois canaux par caméra (et donc par instance de topologie de graphe) à la fois. N’essayez pas de connecter plus de trois instances au serveur. Les demandes seraient alors en attente jusqu’à ce qu’une demande existante soit abandonnée.  

L’implémentation de serveur gRPC ci-dessus est utilisée dans nos exemples Python. Les développeurs peuvent implémenter leurs propres serveurs ; il est également possible, dans l’implémentation par défaut ci-dessus, d’augmenter le nombre de threads de travail définis jusqu’au nombre de caméras utilisées pour obtenir le flux vidéo. 

Pour configurer et utiliser plusieurs caméras, les développeurs peuvent instancier plusieurs instances de topologie de graphe, qui pointent vers le même serveur d’inférence ou non (par exemple un serveur mentionné dans le paragraphe ci-dessus). 

### <a name="i-want-to-be-able-to-receive-multiple-frames-from-upstream-before-i-make-an-inferencing-decision-how-can-i-enable-that"></a>Je souhaite pouvoir recevoir plusieurs trames d’en amont avant de prendre une décision d’inférence. Comment faire ? 

Les [exemples actuels par défaut](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) fonctionnent en mode « sans état ». Ils ne conservent pas l’état des appels précédents, ni même les appelants (ce qui signifie que plusieurs instances de topologie peuvent appeler le même serveur d’inférence sans que celui-ci puisse distinguer qui appelle et l’état par appelant). 

#### <a name="http-protocol"></a>Protocole HTTP

Avec le protocole HTTP : 

Pour conserver l’état, chaque appelant (instance de topologie de graphe) appellera le serveur d’inférence avec un paramètre de requête HTTP qui lui est propre. Par exemple, voici les adresses URL du serveur d’inférence :  

Première instance de topologie = `http://lvaExtension:44000/score?id=1`<br/>
Deuxième instance de topologie = `http://lvaExtension:44000/score?id=2`

… 

Côté serveur, l’itinéraire du score indique qui appelle. Si ID = 1, il peut conserver l’état séparément pour cet appelant (instance de topologie de graphe). Vous pouvez alors conserver les trames vidéo reçues dans une mémoire tampon (par exemple un tableau ou un dictionnaire avec une clé DateTime et la trame pour valeur), puis définir le serveur à traiter (inférer) après réception de x trames. 

#### <a name="grpc-protocol"></a>Protocole gRPC 

Avec le protocole gRPC : 

Avec une extension gRPC, chaque session est destinée à un seul flux de caméra. Il n’est donc pas nécessaire de fournir un ID. Vous pouvez alors, avec la propriété extensionConfiguration, stocker les trames vidéo dans une mémoire tampon et définir le serveur à traiter (inférer) après réception de x trames. 

### <a name="do-all-processmediastreams-on-a-particular-container-run-the-same-ai-model"></a>Tous les ProcessMediaStreams d’un conteneur particulier exécutent-ils le même modèle IA ? 

Non.  

Les appels Start/Stop de l’utilisateur final sur une instance de graphe constituent une session. Il y a peut-être sinon déconnexion/reconnexion de la caméra. L’objectif est de conserver une unique session si la caméra diffuse de la vidéo. 

* Deux caméras qui envoient des vidéos pour traitement créent deux sessions. 
* Une caméra reliée à un graphe comportant deux nœuds gRPCExtension crée deux sessions. 

Chaque session constitue une connexion duplex intégral entre Live Video Analytics et le serveur gRPC, qui peut avoir son propre modèle/pipeline. 

> [!NOTE]
> En cas de déconnexion/reconnexion d’une caméra (passée hors ligne pendant une période qui dépasse les limites de tolérance), Live Video Analytics ouvre une nouvelle session avec le serveur gRPC. Le serveur n’a pas besoin d’effectuer le suivi de l’état d’une session à l’autre. 

Live Video Analytics a également ajouté la prise en charge de plusieurs extensions gRPC pour une seule caméra dans une instance de graphe. Vous pourrez utiliser ces extensions gRPC pour effectuer le traitement IA de manière séquentielle ou en parallèle, voire les deux. 

> [!NOTE]
> L’exécution en parallèle de plusieurs extensions aura un impact sur vos ressources matérielles. Vous devrez garder ce point à l’esprit dans le choix du matériel adapté à vos besoins de calcul. 

### <a name="what-is-the-max--of-simultaneous-processmediastreams"></a>Quel est le nombre maximal de ProcessMediaStreams simultanés ? 

Live Video Analytics n’applique aucune limite.  

### <a name="how-should-i-decide-if-my-inferencing-server-should-use-cpu-or-gpu-or-any-other-hardware-accelerator"></a>Comment déterminer si mon serveur d’inférence doit utiliser l’accélérateur de processeur, de GPU ou tout autre accélérateur matériel ? 

Cela dépend entièrement de la complexité du modèle IA développé et de la façon dont le développeur souhaite utiliser les accélérateurs de processeur et de matériel. Lors du développement du modèle IA, les développeurs peuvent spécifier les ressources utilisées par le modèle pour effectuer les différentes actions. 

### <a name="how-do-i-store-images-with-bounding-boxes-post-processing"></a>Comment stocker des images avec post-traitement des cadres englobants ? 

À l’heure actuelle, nous ne fournissons les coordonnées des cadres englobants que sous forme de messages d’inférence. Les développeurs peuvent créer un wrapper de diffusion MJPEG personnalisé qui utilise ces messages pour superposer les cadres englobants sur les trames vidéo.  

## <a name="grpc-compatibility"></a>Compatibilité gRPC 

### <a name="how-will-i-know-what-the-mandatory-fields-for-the-media-stream-descriptor-are"></a>Comment savoir quels sont les champs obligatoires du descripteur de flux multimédia ? 

Toute valeur de champ non fournie reçoit une valeur par défaut [spécifiée par gRPC](https://developers.google.com/protocol-buffers/docs/proto3#default).  

Live Video Analytics utilise la version **proto3** du langage de la mémoire tampon du protocole. Toutes les données de la mémoire tampon du protocole utilisées par les contrats Live Video Analytics sont disponibles dans les fichiers de mémoire tampon du protocole [définis ici](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). 

### <a name="how-should-i-ensure-that-i-am-using-the-latest-protocol-buffer-files"></a>Comment être sûr d’utiliser les derniers fichiers de mémoire tampon du protocole ? 

Les derniers fichiers de mémoire tampon du protocole peuvent être [téléchargés ici](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). Chaque fois que nous les mettons à jour, les fichiers de contrat s’affichent à cet emplacement. Bien qu’il n’existe aucun projet immédiat de mettre à jour les fichiers du protocole, recherchez le nom du package en haut des fichiers pour connaître la version. Elle devrait indiquer : 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

Toutes les mises à jour de ces fichiers incrémentent la valeur « v-value » à la fin du nom. 

> [!NOTE]
> Étant donné que Live Video Analytics utilise la version proto3 du langage, les champs sont facultatifs, ce qui offre une compatibilité ascendante et descendante. 

### <a name="what-grpc-features-are-available-for-me-to-use-with-live-video-analytics-which-features-are-mandatory-and-which-ones-are-optional"></a>Quelles sont les fonctionnalités gRPC utilisables avec Live Video Analytics ? Lesquelles sont obligatoires et lesquelles sont facultatives ? 

Toutes les fonctionnalités gRPC côté serveur peuvent être utilisées, à condition que le contrat protobuf soit respecté. 

## <a name="monitoring-and-metrics"></a>Analyse et métriques

### <a name="can-i-monitor-the-media-graph-on-the-edge-using-event-grid"></a>Puis-je analyser les graphiques multimédias sur la périphérie à l’aide de Microsoft Azure Event Grid ?

Oui. Vous pouvez consommer les métriques Prometheus et les publier dans Event Grid. 

### <a name="can-i-use-azure-monitor-to-view-the-health-metrics-and-performance-of-my-media-graphs-in-the-cloud-or-on-the-edge"></a>Puis-je utiliser Azure Monitor pour afficher l’intégrité, les métriques et les performances de mes graphiques multimédias dans le Cloud ou à la périphérie ?

Oui. Cette topologie est prise en charge. Pour plus d’informations, consultez [Guide pratique pour utiliser les métriques Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

### <a name="are-there-any-tools-to-make-it-easier-to-monitor-the-media-services-iot-edge-module"></a>Existe-t-il des outils qui facilitent l’analyse du module IoT Edge de Media Services ?

Visual Studio Code prend en charge l’extension Azure IoT Tools, qui vous permet d’analyser facilement les points de terminaison du module LVAEdge. Vous pouvez utiliser cet outil pour commencer rapidement à analyser les « événements » du point de terminaison intégré IoT Hub et pour voir les messages d’inférence qui sont acheminés du périphérique vers le cloud. 

En outre, vous pouvez utiliser cette extension pour modifier le jumeau du module LVAEdge, afin de redéfinir les paramètres du graphique multimédia.

Pour plus d’informations, consultez l’article [Surveillance et enregistrement](monitoring-logging.md).

## <a name="billing-and-availability"></a>Facturation et disponibilité

### <a name="how-is-live-video-analytics-on-iot-edge-billed"></a>Comment Live Video Analytics sur IoT Edge est-il facturé ?

Pour plus d’informations, consultez la [page des tarifs](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="next-steps"></a>Étapes suivantes

[Démarrage rapide : Bien démarrer – Live Video Analytics sur IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
