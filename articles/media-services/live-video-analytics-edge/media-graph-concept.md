---
title: Concept de graphe multimédia – Azure
description: Un graphe multimédia vous permet de définir l’emplacement à partir duquel les médias doivent être capturés, la manière dont ils doivent être traités et où les résultats doivent être remis. Cet article fournit une description détaillée du concept de graphe multimédia.
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 8c6775da6804b5079c89cae73d4621dd8067e90a
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798837"
---
# <a name="media-graph"></a>Graphe multimédia

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée

* [Présentation de Live Video Analytics sur IoT Edge](overview.md)
* [Terminologie de Live Video Analytics sur IoT Edge](terminology.md)

## <a name="overview"></a>Vue d’ensemble

Un graphe multimédia vous permet de définir l’emplacement à partir duquel les médias doivent être capturés, la manière dont ils doivent être traités et où les résultats doivent être remis. Pour ce faire, vous devez connecter des composants ou des nœuds de la manière souhaitée. Le diagramme ci-dessous fournit une représentation graphique d’un graphe multimédia.  

![Représentation graphique d’un graphe multimédia](./media/media-graph/overview.png)

Live Video Analytics sur IoT Edge prend en charge différents types de sources, de processeurs et de récepteurs.

* Les **nœuds source** permettent de capturer des médias dans le graphe multimédia. Dans ce contexte, un média peut être un flux audio, un flux vidéo, un flux de données ou un flux combinant l’audio, la vidéo ou les données en un seul flux.
* Les **nœuds processeur** permettent de traiter les médias dans le graphe multimédia.
* Les **nœuds récepteur** permettent de transmettre les résultats du traitement aux services et aux applications en dehors du graphe multimédia.

## <a name="media-graph-topologies-and-instances"></a>Topologies et instances de graphe multimédia 

Live Video Analytics sur IoT Edge vous permet de gérer les graphes multimédias à l’aide de deux concepts : « topologie de graphe » et « instance de graphe ». Une topologie de graphe vous permet de définir le blueprint d’un graphe, avec des paramètres comme espaces réservés pour les valeurs. La topologie définit quels nœuds sont utilisés dans le graphe multimédia et comment ils sont connectés au sein de ce graphe. Par exemple, si vous souhaitez enregistrer le flux d’une caméra, vous avez besoin d’un graphe doté d’un nœud source qui reçoit des vidéos et d’un nœud récepteur qui écrit la vidéo.

Les valeurs des paramètres de la topologie sont spécifiées lorsque vous créez des instances de graphe qui font référence à la topologie. Cela vous permet de créer plusieurs instances qui font référence à la même topologie, mais avec des valeurs différentes pour les paramètres spécifiés dans la topologie. Dans l’exemple ci-dessus, vous pourriez avoir utilisé des paramètres pour représenter l’adresse IP de la caméra et le nom de la vidéo enregistrée. Vous pouvez créer de nombreuses instances de graphe avec cette topologie, à raison d’une instance pour chaque caméra dans un bâtiment, par exemple, chacune avec une adresse IP et un nom spécifique.

## <a name="media-graph-states"></a>États du graphe multimédia  

Un graphe multimédia peut avoir l’un des états suivants :

* Inactif : état lorsqu’un graphe multimédia est configuré, mais non actif.
* Activation : état d’instanciation d’un graphe multimédia (autrement dit, état de transition entre inactif et actif).
* Actif : état lorsqu’un graphe multimédia est actif. 

    > [!NOTE]
    >  Le graphe multimédia peut être actif sans transmettre de données (par exemple, la source vidéo d’entrée devient hors connexion).
* Désactivation : état lorsqu’un graphe multimédia passe d’actif à inactif.

Le diagramme ci-dessous illustre la machine d’état du graphe multimédia.

![Machine d’état du graphe multimédia](./media/media-graph/media-graph-state-machine.png)

## <a name="sources-processors-and-sinks"></a>Sources, processeurs et récepteurs  

Live Video Analytics sur IoT Edge prend en charge les types de nœuds suivants dans un graphe multimédia :

### <a name="sources"></a>Sources 

#### <a name="rtsp-source"></a>Source RTSP 

Un nœud source RTSP vous permet d’ingérer des médias à partir d’un serveur [RTSP](https://tools.ietf.org/html/rfc2326 ). Les caméras de surveillance et les caméras IP transmettent leurs données dans le cadre d’un protocole dit RTSP (real-time-streaming-protocol ; protocole de diffusion en continu et en temps réel), au contraire d’autres types d’appareils comme les téléphones et les caméras vidéo. Ce protocole est utilisé pour établir et contrôler les sessions multimédias entre un serveur (la caméra) et un client. Le nœud source RTSP dans un graphe multimédia agit comme un client et peut établir une session avec un serveur RTSP. De nombreux appareils, par exemple la plupart des [caméras IP](https://en.wikipedia.org/wiki/IP_camera), disposent d’un serveur RTSP intégré. [ONVIF](https://www.onvif.org/) exige la prise en charge de RTSP dans sa définition des appareils conformes aux [profils G, S et T](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf). Le nœud source RTSP vous oblige à spécifier une URL RTSP, ainsi que les informations d’identification permettant une connexion authentifiée.

#### <a name="iot-hub-message-source"></a>Source de messages IoT Hub 

Comme d’autres [modules IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-module), le module Live Video Analytics sur IoT Edge peut recevoir des messages via le [hub IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). Ces messages peuvent être envoyés à partir d’autres modules, d’applications en cours d’exécution sur le périphérique, ou du cloud. Ces messages sont remis (acheminés) à une [entrée nommée](../../iot-edge/module-composition.md#sink) sur le module. Un nœud source de messages IoT Hub permet à ces messages d’atteindre un graphe multimédia. Ces messages ou signaux peuvent ensuite être utilisés en interne dans le graphe multimédia, en général pour activer les portes de signal (voir [les portes de signal](#signal-gate-processor) ci-dessous). 

Par exemple, vous pouvez avoir un module IoT Edge qui génère un message quand une porte est ouverte. Le message de ce module peut être acheminé vers un hub IoT Edge, d’où il peut ensuite être acheminé vers la source des messages du hub IoT d’un graphe multimédia. Dans le graphe multimédia, la source des messages du hub IoT peut transmettre l’événement à un processeur de porte de signal, qui peut alors activer l’enregistrement de la vidéo d’une source RTSP vers un fichier. 

### <a name="processors"></a>Processeurs  

#### <a name="motion-detection-processor"></a>Processeur de détection de mouvement 

Le nœud processeur de détection de mouvement vous permet de détecter les mouvements dans une vidéo en direct. Il examine les images vidéo entrantes et détermine s’il y a un mouvement dans la vidéo. Si un mouvement est détecté, le nœud transmet les images vidéo au composant en aval et émet un événement. Le nœud processeur de détection de mouvement (conjointement avec d’autres nœuds) peut être utilisé pour déclencher l’enregistrement de la vidéo entrante quand un mouvement est détecté.

#### <a name="frame-rate-filter-processor"></a>Processeur de filtre de fréquence d’images  

Le nœud processeur de filtre de fréquence d’images vous permet d’échantillonner des images du flux vidéo entrant à une vitesse spécifiée. Cela vous permet de réduire le nombre d’images envoyées aux composants en aval (tels qu’un nœud processeur d’extension HTTP) en vue d’un traitement supplémentaire.

#### <a name="http-extension-processor"></a>Processeur d’extension HTTP

Le nœud processeur d’extension HTTP vous permet de connecter votre propre module IoT Edge à un graphe multimédia. Ce nœud prend les images vidéo décodées comme entrée et les transmet à un point de terminaison REST HTTP exposé par votre module. Ce nœud peut s’authentifier auprès du point de terminaison REST, le cas échéant. En outre, le nœud dispose d’un formateur d’image intégré pour la mise à l’échelle et l’encodage des images vidéo avant qu’elles ne soient relayées vers le point de terminaison REST. Le processus de mise à l’échelle propose des options permettant de préserver les proportions de l’image, de la remplir ou de l’étirer. L’encodeur d’image prend en charge les formats JPEG, PNG ou BMP.

#### <a name="grpc-extension-processor"></a>Processeur d’extension gRPC

Le nœud du processeur d’extension gRPC prend les images vidéo décodées comme entrée et les relaie à un point de terminaison [gRPC](terminology.md#grpc) exposé par votre module. De plus, le nœud dispose d’un formateur d’image intégré pour la mise à l’échelle et l’encodage des images vidéo avant qu’elles soient relayées au point de terminaison gRPC. Le processus de mise à l’échelle propose des options permettant de préserver les proportions de l’image, de la remplir ou de l’étirer. L’encodeur d’image prend en charge les formats JPEG, PNG ou BMP.

#### <a name="signal-gate-processor"></a>Processeur de porte de signal  

Le nœud processeur de la porte de signal vous permet de transférer de manière conditionnelle des médias d’un nœud à un autre. Il agit également comme une mémoire tampon, ce qui permet la synchronisation des médias et des événements. Un cas d’usage classique consiste à insérer un nœud processeur de porte de signal entre le nœud source RTSP et le nœud récepteur de ressources, et à utiliser la sortie d’un nœud processeur de détecteur de mouvement pour déclencher la porte. Avec ce type de graphe multimédia, vous enregistrez la vidéo uniquement lorsqu’un mouvement est détecté.

### <a name="sinks"></a>Récepteurs  

#### <a name="asset-sink"></a>Récepteur de ressources  

Un nœud récepteur de ressources vous permet d’écrire des données multimédias (vidéo ou audio) dans une ressource Azure Media Services. Un graphe multimédia ne peut contenir qu’un seul nœud récepteur de ressources. Pour plus d’informations sur les ressources et leur rôle dans l’enregistrement et la lecture des médias, consultez la section [Ressource](terminology.md#asset). Pour plus d’informations sur l’utilisation des propriétés de ce nœud, vous pouvez également consulter l’article relatif à [l’enregistrement vidéo continu](continuous-video-recording-concept.md).

#### <a name="file-sink"></a>Récepteur de fichiers  

Le nœud récepteur de fichiers vous permet d’écrire des données multimédias (vidéo ou audio) à un emplacement sur le système de fichiers local de l’appareil IoT Edge. Il ne peut y avoir qu’un seul nœud récepteur de fichiers dans un graphe multimédia, et il doit être en aval d’un nœud processeur de porte de signal. Cela limite la durée des fichiers de sortie aux valeurs spécifiées dans les propriétés du nœud processeur de la porte de signal.

#### <a name="iot-hub-message-sink"></a>Récepteur de messages IoT Hub  

Un nœud récepteur de messages IoT Hub vous permet de publier des événements sur un hub IoT Edge. Le hub IoT Edge peut ensuite acheminer les données vers d’autres modules ou applications sur le périphérique ou vers IoT Hub dans le cloud (par itinéraires spécifiés dans le manifeste de déploiement). Le nœud récepteur de messages IoT Hub peut accepter des événements provenant de processeurs en amont, tels qu’un nœud processeur de détection de mouvement, ou d’un service d’inférence externe via un nœud processeur d’extension HTTP.

## <a name="rules-on-the-use-of-nodes"></a>Règles d’utilisation des nœuds

Pour plus d’informations sur les autres règles d’utilisation de nœuds différents dans un graphe multimédia, consultez [Limitations relatives aux topologies de graphe](quotas-limitations.md#limitations-on-graph-topologies-at-preview).

## <a name="scenarios"></a>Scénarios

À l’aide d’une combinaison des sources, des processeurs et des récepteurs définis ci-dessus, vous pouvez créer des graphes multimédias pour un large éventail de scénarios impliquant une analyse de vidéos en direct. Par exemple :

* [Enregistrement vidéo en continu](continuous-video-recording-concept.md)
* [Enregistrement de vidéo basé sur les événements](event-based-video-recording-concept.md)
* [Live Video Analytics sans enregistrement de vidéo](analyze-live-video-concept.md)

## <a name="next-steps"></a>Étapes suivantes

Pour voir de quelle façon vous pouvez exécuter la détection de mouvement sur un flux vidéo en direct, consultez [Démarrage rapide : Exécuter Live Video Analytics avec votre propre modèle](use-your-model-quickstart.md).
