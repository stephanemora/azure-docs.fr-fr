---
title: FAQ sur Azure Video Analyzer – Azure
description: Cette article répond aux questions fréquemment posées sur Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 94f85cecdb8ee3d18ad7521d8157c01f1410c23c
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385808"
---
# <a name="faq-about-azure-video-analyzer"></a>FAQ sur Azure Video Analyzer

Cette article répond aux questions couramment posées sur Azure Video Analyzer.

## <a name="general"></a>Général

**Quelles variables système puis-je utiliser dans la définition de topologie de pipeline ?**

| Variable   |  Description  | 
| --- | --- | 
| System.Runtime.DateTime | Représente un instant en heure UTC, généralement exprimé sous la forme d’une date et d’une heure dans la journée selon le format suivant :<br>*yyyyMMddTHHmmssZ* | 
| System.Runtime.PreciseDateTime | Représente une instance de date-heure en temps universel coordonné (UTC, Universal Time Coordinated) dans un format compatible avec les fichiers ISO8601 avec des millisecondes, au format suivant :<br>*yyyyMMddTHHmmss.fffZ* | 
| System.TopologyName    | Représente le nom de la topologie de pipeline. | 
| System.PipelineName | Représente le nom du pipeline en direct. | 

> [!Note] 
> System.Runtime.DateTime and System.Runtime.PreciseDateTime ne peut pas être utilisé dans le nom d’une ressource vidéo Azure Video Analyzer, dans un nœud récepteur vidéo. Ces variables peuvent être utilisées dans un nœud FileSink pour nommer le fichier.

**Quelle est la politique de confidentialité pour Video Indexer ?**

Video Indexer est couvert par la [Déclaration de confidentialité Microsoft](https://privacy.microsoft.com/privacystatement). La Déclaration de confidentialité décrit les données personnelles que Microsoft traite, comment et dans quel but Microsoft les traite. Pour en savoir plus sur la confidentialité, visitez le site [Microsoft Trust Center](https://www.microsoft.com/trustcenter).

## <a name="configuration-and-deployment"></a>Configuration et déploiement

**Puis-je déployer le module Edge sur un appareil Windows 10 ?**

Oui. Pour plus d’informations, consultez [Configurer des conteneurs Linux sur Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Capture des paramètres de caméra IP et RTSP

**Dois-je utiliser un Kit de développement logiciel (SDK) spécial sur mon appareil pour envoyer un flux vidéo ?**

Non, Video Analyzer prend en charge la capture de média à l’aide du protocole RTSP (Real-Time Streaming Protocol) de diffusion vidéo en continu que prennent en charge la plupart des caméras IP.

**Puis-je envoyer un média à Video Analyzer en utilisant des protocoles autres que RTSP ?**

Non, Video Analyzer ne prend en charge que le protocole RTSP pour la capture de vidéo à partir de caméras IP. Toute caméra qui prend en charge le streaming RTSP sur TCP/HTTP doit être compatible. 

**Puis-je réinitialiser ou mettre à jour l’URL source RTSP dans un pipeline en direct ?**

Oui, quand le pipeline en direct est à l’état *inactif*.  

**Existe-t-il un simulateur RTSP pouvant être utilisé pendant les tests et le développement ?**

Oui, un module Edge [simulateur RTSP]()<!--add-valid-link.md)--><!-- https://github.com/Azure/video-analyzer/tree/main/utilities/rtspsim-live555 --> est disponible les démarrages rapides et tutoriels destinés à soutenir le processus d’apprentissage. Ce module est fourni en guise de service Meilleur effort et peut ne pas toujours être disponible. Nous vous recommandons vivement de ne *pas* utiliser le simulateur pendant plus de quelques heures. Nous vous recommandons d’effectuer des tests avec votre source RTSP actuelle avant de planifier un déploiement de production.

## <a name="design-your-ai-model"></a>Conception d’un modèle IA 

**J’ai plusieurs modèles IA enveloppés dans un conteneur Docker. Comment les utiliser avec Azure Video Analyzer ?** 

Les solutions varient selon le protocole de communication utilisé par le serveur d’inférence pour communiquer avec Azure Video Analyzer. Les sections suivantes décrivent le fonctionnement de chaque protocole.

*Utiliser le protocole HTTP* :

* Conteneur unique (module nommé *avaextension*) :  

   Dans votre serveur d’inférence, vous pouvez utiliser un seul port, mais des points de terminaison différents selon les modèles IA. Par exemple, pour un échantillon Python, vous pouvez utiliser différentes `routes` par modèle, comme illustré ici : 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   Ainsi, dans votre déploiement Video Analyzer, quand vous activez des pipelines en direct, définissez l’URL du serveur d’inférence pour chacun d’eux comme suit : 

   Premier pipeline en direct : URL du serveur d’inférence =`http://avaextension:44000/score/face_detection`<br/>
   Deuxième pipeline en direct : URL du serveur d’inférence =`http://avaextension:44000/score/vehicle_detection`  
   
    > [!NOTE]
    > Vous pouvez également exposer vos modèles IA sur des ports différents et les appeler quand vous activez les pipelines en direct.  

* Plusieurs conteneurs : 

   Chaque conteneur est déployé avec un nom différent. Dans les démarrages rapides et les tutoriels, nous vous avons montré comment déployer une extension nommée *avaextension*. Vous pouvez maintenant développer deux conteneurs différents, chacun avec la même interface HTTP, ce qui signifie qu’ils ont le même point de terminaison `/score`. Déployez ces deux conteneurs avec des noms différents et veillez à ce que les deux écoutent des *ports distincts*. 

   Par exemple, un conteneur nommé `avaextension1` écoute le port `44000`, l’autre conteneur nommé `avaextension2` écoute le port `44001`. 

   Dans votre topologie Video Analyzer, vous instanciez deux pipelines en direct avec différentes URL d’inférence, comme illustré ici : 

   Premier pipeline en direct : URL du serveur d’inférence =`http://avaextension1:44001/score`    
   Deuxième pipeline en direct : URL du serveur d’inférence =`http://avaextension2:44001/score`
   
*Utiliser le protocole gRPC* : 

* Le nœud d’extension gRPC a une propriété `extensionConfiguration`, une chaîne facultative utilisable dans la cadre du contrat gRPC. Si vous avez plusieurs modèles d’IA empaquetés dans un seul serveur d’inférence, vous n’aurez pas besoin d’exposer un nœud par modèle d’IA. Au lieu de cela, pour un pipeline en direct, en tant que fournisseur d’extension, vous pouvez définir comment sélectionner les différents modèles IA à l’aide de la propriété `extensionConfiguration`. Pendant l’exécution, Video Analyzer transmet cette chaîne à votre serveur d’inférence qui peut l’utiliser pour appeler le modèle IA souhaité. 

**Je crée un serveur gRPC autour d’un modèle IA et je souhaite pouvoir prendre en charge l’utilisation de plusieurs caméras ou pipelines en direct. Comment faire pour créer mon serveur ?** 

 Tout d’abord, assurez-vous que votre serveur peut traiter plusieurs demandes à la fois ou travailler dans des threads parallèles. 

Par exemple, dans l’[exemple gRPC Azure Video Analyzer](), un nombre par défaut de canaux parallèles est défini<!--add-valid-link.md)--><!-- https://github.com/Azure/video-analyzer/tree/main/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/avaextension/server/server.py -->: 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

Dans l’instanciation de serveur gRPC ci-dessus, le serveur ne peut ouvrir simultanément que trois canaux par caméra ou cinq par pipeline en direct. N’essayez pas de connecter plus de trois instances au serveur. Si vous ouvrez plus de trois canaux, les demandes resteront en attente jusqu’à ce qu’un canal existant abandonne.  

L’implémentation de serveur gRPC précédente est utilisée dans nos exemples Python. En tant que développeur, vous pouvez implémenter votre propre serveur ou utiliser l’implémentation par défaut précédente pour augmenter le numéro de travail, que vous définissez sur le nombre de caméras à utiliser pour les flux vidéo. 

Pour configurer et utiliser plusieurs caméras, vous pouvez instancier plusieurs pipelines en direct pointant ou non vers le même serveur d’inférence (par exemple le serveur mentionné dans le paragraphe précédent). 

**Je souhaite pouvoir recevoir plusieurs trames avant de prendre une décision d’inférence. Comment puis procéder ?** 

Nos [exemples par défaut]() actuels<!--add-valid-link.md)--><!--https://github.com/Azure/video-analyzer/tree/main/utilities/video-analysis--> opèrent en mode *sans état*. Ils ne conservent pas l’état des appels précédents ou l’ID de l’appelant. Cela signifie que plusieurs pipelines en direct peuvent appeler le même serveur d’inférence, mais que le serveur ne peut pas distinguer qui appelle ou l’état de l’appelant. 

*Utiliser le protocole HTTP* :

Pour conserver l’état, chaque appelant, ou pipeline en direct, appelle le serveur d’inférence avec le paramètre de requête HTTP qui lui est propre. Par exemple, les adresses URL de serveur d’inférence pour chaque pipeline en direct sont présentées ici :  

Premier pipeline en direct : `http://avaextension:44000/score?id=1`<br/>
Deuxième pipeline en direct : `http://avaextension:44000/score?id=2`

… 

Côté serveur, l’`id` permet d’identifier l’appelant. Si `id`=1, le serveur peut conserver l’état séparément pour ce pipeline en direct. Il peut ensuite conserver les trames vidéo reçues dans un tampon. Par exemple, utilisez un tableau ou un dictionnaire avec une clé DateTime, et la valeur est le frame. Vous pouvez ensuite définir le serveur à traiter (inférer) après la réception de *x* nombre de trames. 

*Utiliser le protocole gRPC* : 

Avec une extension gRPC, chaque session est destinée à un seul flux de caméra. Il n’est donc pas nécessaire de fournir un identificateur. Avec la propriété extensionConfiguration, vous pouvez stocker les trames vidéo dans un tampon et définir le serveur à traiter (inférer) après réception de *x* trames. 

**Tous les ProcessMediaStreams d’un conteneur particulier exécutent-ils le même modèle d’IA ?** 

Non. Les appels de démarrage ou d’arrêt de l’utilisateur final dans un pipeline en direct constituent une session. Il peut aussi y avoir une déconnexion ou reconnexion de caméra. L’objectif est de conserver une unique session si la caméra diffuse de la vidéo. 

* Deux caméras envoyant de la vidéo pour traitement (à deux pipelines en direct distincts) créent deux sessions. 
* Une caméra reliée à un pipeline en direct comportant deux nœuds d’extension gRPC crée deux sessions. 

Chaque session constitue une connexion bidirectionnelle entre Video Analyzer et le serveur gRPC, qui peut avoir son propre modèle. 

> [!NOTE]
> En cas de déconnexion ou reconnexion d’une caméra qui passe hors ligne pendant une période dépassant les limites de tolérance, Video Analyzer ouvre une session avec le serveur gRPC. Le serveur n’a pas besoin d’effectuer le suivi de l’état d’une session à l’autre. 

Video Analyzer ajoute également la prise en charge de plusieurs extensions gRPC pour une seule caméra dans un pipeline en direct. Vous pouvez utiliser ces extensions gRPC pour effectuer le traitement IA de manière séquentielle, en parallèle, voire les deux. 

> [!NOTE]
> Le fait que plusieurs extensions s’exécutent en parallèle affecte vos ressources matérielles. Gardez cela à l’esprit lorsque vous choisissez le matériel adapté à vos besoins de calcul. 

**Quel est le nombre maximal de ProcessMediaStreams simultanés ?** 

Video Analyzer n’applique aucune limite.  

**Comment déterminer si mon serveur d’inférence doit utiliser l’accélérateur de processeur, de GPU ou tout autre accélérateur matériel ?** 

Votre décision dépend de la complexité du modèle d’IA développé et de la façon dont vous souhaitez utiliser les accélérateurs de processeur et de matériel. Lorsque vous développez le modèle d’IA, vous pouvez préciser les ressources que le modèle doit utiliser et les actions qu’il doit effectuer. 

**Comment faire pour afficher les cadres englobants générés par mon serveur d’inférence ?** 

Vous pouvez enregistrer les résultats de l’inférence avec le média dans votre ressource vidéo. Vous pouvez utiliser un [widget]()<!--add-valid-link.md)--><!-- pointer to widget md --> pour lire la vidéo avec une superposition des données de l’inférence.

## <a name="grpc-compatibility"></a>Compatibilité gRPC 

**Comment savoir quels sont les champs obligatoires du descripteur de flux multimédia ?** 

Tout champ auquel vous ne fournissez pas de valeur reçoit une [valeur par défaut, comme spécifié par gRPC](https://developers.google.com/protocol-buffers/docs/proto3#default).  

Video Analyzer utilise la version *proto3* du langage de la mémoire tampon du protocole. Toutes les données de la mémoire tampon du protocole que les contrats Video Analyzer utilisent sont disponibles dans les [fichiers de mémoire tampon du protocole]().<!--add-valid-link.md)--><!--https://github.com/Azure/azree-video-analyzer/tree/master/contracts/grpc-->. 

**Comment être sûr d’utiliser les derniers fichiers de mémoire tampon du protocole ?** 

Vous pouvez obtenir les derniers fichiers de mémoire tampon du protocole sur le [site de fichiers du contrat]().<!--add-valid-link.md)--><!--https://github.com/Azure/azree-video-analyzer/tree/master/contracts/grpc-->. Chaque fois que nous les mettons à jour, les fichiers de contrat s’affichent à cet emplacement. Il n’existe aucun projet immédiat de mettre à jour les fichiers du protocole, recherchez donc le nom du package en haut des fichiers pour connaître la version. Elle devrait indiquer : 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1
```

Toutes les mises à jour de ces fichiers incrémentent la valeur « v-value » à la fin du nom. 

> [!NOTE]
> Étant donné que Video Analyzer utilise la version proto3 du langage, les champs sont facultatifs, ce qui offre une compatibilité ascendante et descendante. 

**Quelles sont les fonctionnalités gRPC utilisables avec Video Analyzer ? Lesquelles sont obligatoires et lesquelles facultatives ?** 

Vous pouvez utiliser toutes les fonctionnalités gRPC côté serveur, à condition que le contrat Protobuf (Protocol Buffers) soit rempli. 

## <a name="monitoring-and-metrics"></a>Analyse et métriques

**Puis-je surveiller le pipeline à la périphérie à l’aide d’Azure Event Grid ?**

Oui. Vous pouvez consommer des [métriques Prometheus](monitor-log-edge.md#azure-monitor-collection-via-telegraf) et les publier dans votre Event Grid. 

**Puis-je utiliser Azure Monitor pour afficher l’intégrité, les métriques et les performances de mes pipelines dans le cloud ou à la périphérie ?**

Oui, nous prenons en charge cette approche. Pour en savoir plus, consultez [Guide pratique pour utiliser les métriques Azure Monitor.](../../azure-monitor/essentials/data-platform-metrics.md)

**Existe-t-il des outils qui facilitent la surveillance du module IoT Edge Azure Video Analyzer ?**

Visual Studio Code prend en charge l’extension Azure IoT Tools qui vous permet de surveiller facilement les points de terminaison du module Edge Video Analyzer. Vous pouvez utiliser cet outil pour commencer rapidement à analyser les « événements » du point de terminaison intégré IoT Hub et pour voir les messages d’inférence qui sont acheminés du périphérique vers le cloud. 

En outre, vous pouvez utiliser cette extension pour modifier le jumeau du module Edge Video Analyzer afin de redéfinir les paramètres du pipeline.

Pour plus d’informations, consultez l’article [Surveillance et enregistrement](monitor-log-edge.md).

## <a name="billing-and-availability"></a>Facturation et disponibilité

**Comment Azure Video Analyzer est-il facturé ?**

Pour plus d’informations sur la facturation, consultez la page [Tarification de Video Analyzer]()<!--add-valid-link.md)--><!--https://azure.microsoft.com/pricing/details/media-services/-->.

## <a name="next-steps"></a>Étapes suivantes

[Démarrage rapide : Bien démarrer avec Azure Video Analyzer](get-started-detect-motion-emit-events.md)
