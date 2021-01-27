---
title: FAQ Live Video Analytics sur IoT Edge – Azure
description: Cet article répond aux questions fréquentes à propos de Live Video Analytics sur IoT Edge.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 0cb378bf614582070dd1bdd0a11706b26437af53
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880048"
---
# <a name="live-video-analytics-on-iot-edge-faq"></a>FAQ Live Video Analytics sur IoT Edge

Cet article répond aux questions fréquentes à propos de Live Video Analytics sur Azure IoT Edge.

## <a name="general"></a>Général

**Quelles variables système peuvent être utilisées dans une définition de topologie de graphique ?**

| Variable   |  Description  | 
| --- | --- | 
| [System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods) | Représente un instant en heure UTC, généralement exprimé sous la forme d’une date et d’une heure dans la journée selon le format suivant :<br>*yyyyMMddTHHmmssZ* | 
| System.PreciseDateTime | Représente une instance de date-heure en temps universel coordonné (UTC, Universal Time Coordinated) dans un format compatible avec les fichiers ISO8601 avec des millisecondes, au format suivant :<br>*yyyyMMddTHHmmss.fffZ* | 
| System.GraphTopologyName   | Représente une topologie de graphique média, et contient le blueprint d’un graphique. | 
| System.GraphInstanceName |    Représente une instance de graphique média ; contient des valeurs de paramètre et référence la topologie. | 

## <a name="configuration-and-deployment"></a>Configuration et déploiement

**Puis-je déployer le module multimédia Edge sur un appareil Windows 10 ?**

Oui. Pour plus d’informations, consultez [Configurer des conteneurs Linux sur Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Capture des paramètres de caméra IP et RTSP

**Dois-je utiliser un Kit de développement logiciel (SDK) spécial sur mon appareil pour envoyer un flux vidéo ?**

Non, Live Video Analytics sur IoT Edge prend en charge la capture média à l’aide du protocole RTSP (Real-Time Streaming Protocol) de diffusion vidéo en continu (qui est géré par la plupart des caméras IP).

**Puis-je envoyer des données multimédias à Live Video Analytics sur IoT Edge à l’aide de RTMP ou Smooth (comme un événement en direct Media Services) ?**

Non, Live Video Analytics ne prend en charge que le protocole RTSP pour la capture de vidéos à partir de caméras IP. Toute caméra qui prend en charge le streaming RTSP sur TCP/HTTP doit être compatible. 

**Puis-je réinitialiser ou mettre à jour l’URL source RTSP sur une instance de graphique ?**

Oui, quand l’instance de graphique est à l’état *inactif*.  

**Existe-t-il un simulateur RTSP pouvant être utilisé pendant les tests et le développement ?**

Oui, il existe un module de périphérie de [simulateur RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555), qui peut être utilisé dans les didacticiels et les guides de démarrage rapide afin de prendre en charge le processus d’apprentissage. Ce module est fourni en guise de service Meilleur effort et peut ne pas toujours être disponible. Nous vous recommandons vivement de ne *pas* utiliser le simulateur pendant plus de quelques heures. Nous vous recommandons d’effectuer des tests avec votre source RTSP actuelle avant de planifier un déploiement de production.

**Prenez-vous en charge la découverte ONVIF des caméras IP en périphérie ?**

Non, la découverte ONVIF des appareils en périphérie n’est pas gérée.

## <a name="streaming-and-playback"></a>Diffusion en continu et lecture

**Puis-je lire les ressources enregistrées sur Azure Media Services à partir de la périphérie à l’aide de technologies de diffusion en continu telles que HLS ou DASH ?**

Oui. Vous pouvez diffuser en continu les ressources enregistrées comme n’importe quelle autre ressource dans Azure Media Services. Pour pouvoir diffuser le contenu en continu, vous devez créer un point de terminaison de streaming, qui doit être en cours d’exécution. Utiliser le processus de création de localisateur de streaming standard vous donnera accès à un HTTP Live Streaming (HLS) Apple ou une diffusion en continu adaptative sur le manifeste HTTP (DASH, aussi connu sous MPEG-DASH) pour la diffusion en continu vers n’importe quelle infrastructure de lecteur la prenant en charge. Pour plus d’informations sur la création de manifestes de publication HLS ou DASH, voir [L’empaquetage dynamique](../latest/dynamic-packaging-overview.md).

**Puis-je utiliser la protection de contenu standard et les fonctionnalités DRM de Media Services sur une ressource archivée ?**

Oui. Toutes les fonctionnalités de protection de contenu, de chiffrement dynamique et de gestion des droits numériques (DRM) standard peuvent être utilisées sur les ressources à partir d’un graphique multimédia.

**Quels lecteurs puis-je utiliser pour afficher le contenu des ressources enregistrées ?**

Tous les lecteurs standard qui sont conformes aux versions 3 ou 4 HLS sont pris en charge. En outre, tout lecteur respectant la norme de lecture MPEG-DASH est également pris en charge.

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

**Quelles sont les limites de diffusion en continu d’une ressource de graphique multimédia ?**

La diffusion en continu d’une ressource en direct ou enregistrée à partir d’un graphique multimédia utilise l’infrastructure à grande échelle et le point de terminaison de streaming que Media Services prend en charge dans le cadre de la diffusion en continu à la demande et en direct pour les clients OTT, de l’audiovisuel, des médias et du divertissement. Cela signifie que vous pouvez rapidement et facilement activer Azure CDN, Verizon ou Akamai pour distribuer votre contenu à un public de quelques spectateurs ou de plusieurs millions de personnes, selon votre scénario.

Vous pouvez distribuer du contenu à l’aide d’Apple HLS ou MPEG-DASH.

## <a name="design-your-ai-model"></a>Conception d’un modèle IA 

**J’ai plusieurs modèles IA inclus dans un conteneur Docker. Comment les utiliser avec Live Video Analytics ?** 

Les solutions varient selon le protocole de communication utilisé par le serveur d’inférence pour communiquer avec Live Video Analytics. Les sections suivantes décrivent le fonctionnement de chaque protocole.

*Utiliser le protocole HTTP* :

* Conteneur unique (lvaExtension unique) :  

   Dans votre serveur d’inférence, vous pouvez utiliser un seul port, mais des points de terminaison différents selon les modèles IA. Prenons un exemple Python. Vous pouvez utiliser une `route` par modèle, comme vous pouvez le voir ici : 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   Ensuite, dans votre déploiement Live Video Analytics, quand vous instanciez des graphiques, définissez ainsi l’URL du serveur d’inférence pour chaque instance : 

   Première instance : URL du serveur inférence = `http://lvaExtension:44000/score/face_detection`<br/>
   Deuxième instance : URL du serveur inférence = `http://lvaExtension:44000/score/vehicle_detection`  
   
    > [!NOTE]
    > Vous pouvez également exposer vos modèles d’IA sur des ports différents et les appeler quand vous instanciez des graphiques.  

* Plusieurs conteneurs : 

   Chaque conteneur est déployé avec un nom différent. Précédemment, dans la documentation Live Video Analytics, nous vous avons montré comment déployer une extension sous le nom *lvaExtension*. Vous pouvez maintenant développer deux conteneurs différents, chacun avec la même interface HTTP, ce qui signifie qu’ils ont le même point de terminaison `/score`. Déployez ces deux conteneurs avec des noms différents et veillez à ce que les deux écoutent des *ports distincts*. 

   Par exemple, un conteneur nommé `lvaExtension1` écoute le port `44000`, l’autre conteneur nommé `lvaExtension2` écoute le port `44001`. 

   Dans votre topologie Live Video Analytics, vous instanciez deux graphes avec différentes URL d’inférence : 

   Première instance : URL du serveur inférence = `http://lvaExtension1:44001/score`    
   Deuxième instance : URL du serveur inférence = `http://lvaExtension2:44001/score`
   
*Utiliser le protocole gRPC* : 

* Avec la version 1.0 du module Live Video Analytics, quand vous utilisez un protocole d’appel de procédure distante (gRPC), le seul moyen d’utiliser un protocole gRPC serait de faire en sorte que le serveur gRPC expose des modèles IA différents sur des ports distincts. Dans [cet exemple de code](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json), un port unique, 44000, expose tous les modèles Yolo. En théorie, le serveur gRPC Yolo pourrait être réécrit pour exposer des modèles sur 44000, d’autres sur 45000, etc. 

* Avec la version 2.0 du module Live Video Analytics, une nouvelle propriété est ajoutée au nœud de l’extension gRPC. Cette propriété s’appelle **extensionConfiguration**, une chaîne facultative utilisable dans le contrat gRPC. Si vous avez plusieurs modèles d’IA empaquetés dans un seul serveur d’inférence, vous n’aurez pas besoin d’exposer un nœud par modèle d’IA. Au lieu de cela, pour une instance de graphique, vous, en tant que fournisseur d’extensions, pouvez définir comment sélectionner les différents modèles d’IA à l’aide de la propriété **extensionConfiguration**. Pendant l’exécution, Live Video Analytics transmet cette chaîne au serveur d’inférence qui peut l’utiliser pour appeler le modèle d’IA souhaité. 

**Je crée un serveur gRPC autour d’un modèle IA et je souhaite pouvoir prendre en charge l’utilisation de plusieurs caméras/instances de graphes. Comment créer mon serveur ?** 

 Tout d’abord, assurez-vous que votre serveur peut traiter plusieurs demandes à la fois ou travailler dans des threads parallèles. 

Par exemple, dans l’un des [exemples gRPC Live Video Analytics](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py), un nombre par défaut de canaux parallèles est défini : 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

Dans l’instanciation de serveur gRPC ci-dessus, le serveur ne peut ouvrir que trois canaux par caméra ou par instance de topologie de graphique, à la fois. N’essayez pas de connecter plus de trois instances au serveur. Les demandes seraient alors en attente jusqu’à ce qu’une demande existante soit abandonnée.  

L’implémentation de serveur gRPC précédente est utilisée dans nos exemples Python. En tant que développeur, vous pouvez implémenter votre propre serveur ou utiliser l’implémentation par défaut précédente pour augmenter le numéro de travail, que vous définissez sur le nombre de caméras à utiliser pour les flux vidéo. 

Pour configurer et utiliser plusieurs caméras, vous pouvez instancier plusieurs instances de topologie de graphe, qui pointent vers le même serveur d’inférence ou non (par exemple un serveur mentionné dans le paragraphe précédent). 

**Je souhaite pouvoir recevoir plusieurs trames d’en amont avant de prendre une décision d’inférence. Comment faire ?** 

Les [exemples actuels par défaut](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) fonctionnent en mode *sans état*. Ils ne conservent pas l’état des appels précédents ou même ceux qui ont appelé. Cela signifie que plusieurs instances de topologie peuvent appeler le même serveur d’inférence, mais que le serveur ne peut pas distinguer qui appelle ou l’état par appelant. 

*Utiliser le protocole HTTP* :

Pour conserver l’état, chaque appelant (instance de topologie de graphe) appelle le serveur d’inférence avec un paramètre de requête HTTP qui lui est propre. Par exemple, les adresses URL du serveur d’inférence pour chaque instance sont indiquées ici :  

Première instance de topologie = `http://lvaExtension:44000/score?id=1`<br/>
Deuxième instance de topologie = `http://lvaExtension:44000/score?id=2`

… 

Côté serveur, l’itinéraire du score indique qui appelle. Si ID = 1, il peut conserver l’état séparément pour cet appelant ou l’instance de topologie de graphe. Vous pouvez ensuite conserver les trames vidéo reçues dans une mémoire tampon. Par exemple, utilisez un tableau ou un dictionnaire avec une clé DateTime, et la valeur est le frame. Vous pouvez ensuite définir le serveur à traiter (inférer) après la réception de *x* nombre de trames. 

*Utiliser le protocole gRPC* : 

Avec une extension gRPC, chaque session est destinée à un seul flux de caméra. Il n’est donc pas nécessaire de fournir un ID. Vous pouvez alors, avec la propriété extensionConfiguration, stocker les trames vidéo dans une mémoire tampon et définir le serveur à traiter (inférer) après réception de *x* trames. 

**Tous les ProcessMediaStreams d’un conteneur particulier exécutent-ils le même modèle d’IA ?** 

Non. Les appels Start ou Stop de l’utilisateur final sur une instance de graphe constituent une session. Il y a peut-être sinon déconnexion/reconnexion de la caméra. L’objectif est de conserver une unique session si la caméra diffuse de la vidéo. 

* Deux caméras qui envoient des vidéos pour traitement créent deux sessions. 
* Une caméra reliée à un graphe comportant deux nœuds d’extension gRPC crée deux sessions. 

Chaque session constitue une connexion duplex intégrale entre Live Video Analytics et le serveur gRPC. Elles peuvent avoir leur propre modèle ou pipeline. 

> [!NOTE]
> En cas de déconnexion/reconnexion d’une caméra (passée hors ligne pendant une période qui dépasse les limites de tolérance), Live Video Analytics ouvre une nouvelle session avec le serveur gRPC. Le serveur n’a pas besoin d’effectuer le suivi de l’état d’une session à l’autre. 

Live Video Analytics ajoute également la prise en charge de plusieurs extensions gRPC pour une seule caméra dans une instance de graphe. Vous pouvez utiliser ces extensions gRPC pour effectuer le traitement IA de manière séquentielle, en parallèle, voire les deux. 

> [!NOTE]
> Le fait que plusieurs extensions s’exécutent en parallèle affecte vos ressources matérielles. Gardez cela à l’esprit lorsque vous choisissez le matériel adapté à vos besoins de calcul. 

**Quel est le nombre maximal de ProcessMediaStreams simultanés ?** 

Live Video Analytics n’applique aucune limite.  

**Comment déterminer si mon serveur d’inférence doit utiliser l’accélérateur de processeur, de GPU ou tout autre accélérateur matériel ?** 

Votre décision dépend de la complexité du modèle d’IA développé et de la façon dont vous souhaitez utiliser les accélérateurs de processeur et de matériel. Lorsque vous développez le modèle d’IA, vous pouvez préciser les ressources que le modèle doit utiliser et les actions qu’il doit effectuer. 

**Comment stocker des images avec post-traitement des cadres englobants ?** 

À l’heure actuelle, nous ne fournissons les coordonnées des cadres englobants que sous forme de messages d’inférence. Vous pouvez créer un wrapper de diffusion MJPEG personnalisé qui utilise ces messages pour superposer les cadres englobants sur les trames vidéo.  

## <a name="grpc-compatibility"></a>Compatibilité gRPC 

**Comment savoir quels sont les champs obligatoires du descripteur de flux multimédia ?** 

Tout champ auquel vous ne fournissez pas de valeur reçoit une [valeur par défaut, comme spécifié par gRPC](https://developers.google.com/protocol-buffers/docs/proto3#default).  

Live Video Analytics utilise la version *proto3* du langage de la mémoire tampon du protocole. Toutes les données de la mémoire tampon du protocole utilisées par les contrats Live Video Analytics sont disponibles dans les [fichiers de mémoire tampon du protocole](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). 

**Comment être sûr d’utiliser les derniers fichiers de mémoire tampon du protocole ?** 

Vous pouvez obtenir les derniers fichiers de mémoire tampon de protocole sur le site [fichiers de contrat](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). Chaque fois que nous les mettons à jour, les fichiers de contrat s’affichent à cet emplacement. Il n’existe aucun projet immédiat de mettre à jour les fichiers du protocole, recherchez donc le nom du package en haut des fichiers pour connaître la version. Elle devrait indiquer : 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

Toutes les mises à jour de ces fichiers incrémentent la valeur « v-value » à la fin du nom. 

> [!NOTE]
> Étant donné que Live Video Analytics utilise la version proto3 du langage, les champs sont facultatifs, ce qui offre une compatibilité ascendante et descendante. 

**Quelles sont les fonctionnalités gRPC utilisables avec Live Video Analytics ? Lesquelles sont obligatoires et lesquelles sont facultatives ?** 

Vous pouvez utiliser toutes les fonctionnalités gRPC côté serveur, à condition que le contrat Protobuf (Protocol Buffers) soit rempli. 

## <a name="monitoring-and-metrics"></a>Analyse et métriques

**Puis-je analyser les graphiques multimédias sur la périphérie à l’aide d’Azure Event Grid ?**

Oui. Vous pouvez consommer les métriques Prometheus et les publier dans Event Grid. 

**Puis-je utiliser Azure Monitor pour afficher l’intégrité, les métriques et les performances de mes graphiques multimédias dans le Cloud ou à la périphérie ?**

Oui, nous prenons en charge cette approche. Pour en savoir plus, consultez [Guide pratique pour utiliser les métriques Azure Monitor.](../../azure-monitor/platform/data-platform-metrics.md)

**Existe-t-il des outils qui facilitent l’analyse du module IoT Edge de Media Services ?**

Visual Studio Code prend en charge l’extension Azure IoT Tools, qui vous permet d’analyser facilement les points de terminaison du module LVAEdge. Vous pouvez utiliser cet outil pour commencer rapidement à analyser les « événements » du point de terminaison intégré IoT Hub et pour voir les messages d’inférence qui sont acheminés du périphérique vers le cloud. 

En outre, vous pouvez utiliser cette extension pour modifier le jumeau du module LVAEdge, afin de redéfinir les paramètres du graphique multimédia.

Pour plus d’informations, consultez l’article [Surveillance et enregistrement](monitoring-logging.md).

## <a name="billing-and-availability"></a>Facturation et disponibilité

**Comment Live Video Analytics sur IoT Edge est-il facturé ?**

Pour plus d’informations sur la facturation, consultez [la tarification Media Services](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="next-steps"></a>Étapes suivantes

[Démarrage rapide : Bien démarrer avec Live Video Analytics sur IoT Edge](get-started-detect-motion-emit-events-quickstart.md)