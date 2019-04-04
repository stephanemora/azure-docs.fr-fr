---
title: Vue d’ensemble du streaming en direct à l’aide d’Azure Media Services | Microsoft Docs
description: Cet article offre une vue d’ensemble du streaming en direct à l’aide d’Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/03/2019
ms.author: juliako
ms.openlocfilehash: ad8e84d84665b20bfff53cf09473bc8bce9760d8
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916023"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Streaming en direct avec Azure Media Services v3

Azure Media Services vous permet de transmettre des événements en direct auprès de vos clients dans le cloud Azure. Pour cela, vous avez besoin des éléments suivants :  

- Une caméra, pour capturer l’événement en direct.<br/>Pour obtenir des idées de configuration, consultez [Simple and portable event video gear setup]( https://link.medium.com/KNTtiN6IeT).

    Si vous n’avez pas accès à un appareil photo, des outils tels que [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) peut être utilisé générer un flux en direct à partir d’un fichier vidéo.
- Un encodeur vidéo live, pour convertir les signaux de la caméra (ou d’un autre appareil, comme un ordinateur portable) en flux de contribution qui sera ensuite envoyé à Media Services. Le flux de contribution peut inclure des signaux de publicité, tels que les marqueurs SCTE-35.<br/>Pour obtenir une liste d’encodeurs de streaming live recommandés, consultez [Encodeurs de streaming live](recommended-on-premises-live-encoders.md). Consultez également ce billet de blog : [Live streaming production with OBS](https://link.medium.com/ttuwHpaJeT).
- Des composants dans Media Services, pour ingérer, prévisualiser, empaqueter, enregistrer, chiffrer et diffuser l’événement en direct auprès de vos clients, ou dans un CDN en vue d’une diffusion ultérieure.

Cet article donne une vue d’ensemble et les conseils de diffusion en continu avec Media Services et des liens vers d’autres articles pertinents.

> [!NOTE]
> Actuellement, vous ne pouvez pas utiliser le portail Azure pour gérer des ressources v3. Utilisez l’[API REST](https://aka.ms/ams-v3-rest-ref), l’interface [CLI](https://aka.ms/ams-v3-cli-ref) ou l’un des kits [SDK](developers-guide.md) pris en charge.

## <a name="dynamic-packaging"></a>Empaquetage dynamique

Avec Media Services, vous pouvez tirer parti de Packaging](dynamic-packaging-overview.md) dynamique, ce qui vous permet d’afficher un aperçu et de diffuser votre flux live dans [formats MPEG DASH, HLS et Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) à partir de la contribution flux que vous envoyez au service. Vos clients peuvent alors lire le flux en direct au moyen de n’importe quel lecteur compatible avec HLS, DASH ou Smooth Streaming. Vous pouvez utiliser le lecteur multimédia [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) dans vos applications web ou mobiles afin de transmettre votre flux dans un de ces protocoles.

## <a name="dynamic-encryption"></a>Chiffrement dynamique

Chiffrement dynamique permet de chiffrer dynamiquement votre contenu en direct ou à la demande avec AES-128 ou un des trois systèmes principaux droits numériques management (DRM) : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et licences (PlayReady, Widevine et FairPlay) DRM aux clients autorisés. Pour plus d’informations, consultez [chiffrement dynamique](content-protection-overview.md).

## <a name="dynamic-manifest"></a>Manifeste dynamique

Le filtrage dynamique est utilisé pour contrôler le nombre de pistes, des formats, des débits binaires et des fenêtres de temps de présentation qui sont envoyés aux joueurs. Pour plus d’informations, consultez [filtres et manifestes dynamiques](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Types d’événements en direct

Un événement en direct peut prendre l’une des deux types : encodage direct et en direct. Pour plus d’informations sur la diffusion en continu dans Media Services v3, consultez [événements Live et les sorties de Live](live-events-outputs-concept.md).

### <a name="pass-through"></a>Requête directe

![transmission directe](./media/live-streaming/pass-through.svg)

Quand vous utilisez l’**événement en direct** de type pass-through, vous chargez l’encodeur live local de générer un flux vidéo à vitesse de transmission multiple et d’envoyer ce flux comme flux de contribution à l’événement en direct (à l’aide du protocole RTMP ou MP4 fragmenté). L’événement en direct est ensuite transmis dans les flux vidéo entrants sans traitement supplémentaire. Tel un pass-through Live événement est optimisé pour les événements en direct de longs ou la diffusion en continu 24 et 365 linéaire. 

### <a name="live-encoding"></a>Encodage en direct  

![encodage en temps réel](./media/live-streaming/live-encoding.svg)

Quand vous utilisez Live Encoding avec Media Services, vous configurez votre encodeur live local pour qu’il envoie un flux vidéo à une seule vitesse de transmission comme flux de contribution à l’événement en direct (à l’aide du protocole RTMP ou MP4 fragmenté). L’événement en direct encode ce flux vidéo à une seule vitesse de transmission entrant en [flux vidéo à vitesse de transmission multiple](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), pour rendre sa transmission et sa lecture possibles sur les appareils via des protocoles comme MPEG-DASH, HLS et Smooth Streaming. 

## <a name="live-streaming-workflow"></a>Workflow de streaming en direct

Pour comprendre le flux de travail de diffusion en continu en direct dans Media Services v3, vous avez au premier examen et comprenez les concepts suivants : 

- [Points de terminaison de diffusion en continu](streaming-endpoint-concept.md)
- [Événements en direct et Sorties en direct](live-events-outputs-concept.md)
- [Localisateurs de diffusion en continu](streaming-locators-concept.md)

### <a name="general-steps"></a>Étapes générales

1. Accédez à votre compte Media Services et vérifiez que le **point de terminaison de streaming** est en cours d'exécution. 
2. Créez un [événement en temps réel](live-events-outputs-concept.md). <br/>Lors de la création de l’événement, vous pouvez spécifier qu’il démarre automatiquement. Sinon, lancez-le dès que vous souhaitez commencer le streaming.<br/> Lorsque le démarrage automatique est défini sur true, l’événement en direct démarre juste après sa création. La facturation commence donc dès que son exécution démarre. Vous devez appeler explicitement la commande Stop sur la ressource de l’événement en direct pour arrêter toute facturation supplémentaire. Pour plus d’informations, consultez [États et facturation des événements en direct](live-event-states-billing.md).
3. Obtenir les URL de réception et configurez votre encodeur local pour utiliser l’URL pour envoyer la flux de contribution.<br/>Voir [Encodeurs live recommandés](recommended-on-premises-live-encoders.md).
4. Récupérez l’URL d’aperçu et utilisez-la pour vérifier que l’entrée de l’encodeur est bien reçue.
5. Créez un objet **Asset**.
6. Créez un objet **LiveOutput** et utilisez le nom de l’objet Asset que vous venez de créer.<br/>La **sortie en direct** archive le flux dans l’**actif multimédia**.
7. Créez un **localisateur de streaming**  avec les types intégrés de la **stratégie de streaming**.<br/>Pour chiffrer le contenu, voir [Vue d’ensemble de la protection du contenu](content-protection-overview.md).
8. Listez les chemins dans le **Localisateur de streaming** pour récupérer les URL à utiliser (elles sont déterministes).
9. Récupérez le nom d’hôte du **Point de terminaison de streaming** à partir duquel vous souhaitez effectuer le streaming.
10. Combinez l’URL de l’étape 8 avec le nom d’hôte de l’étape 9 pour obtenir l’URL complète.
11. Si vous ne souhaitez plus afficher votre **événement en direct**, arrêtez le streaming de l’événement et supprimez le **localisateur de streaming**.

## <a name="other-important-articles"></a>Autres articles importants

- [Encodeurs live recommandés](recommended-on-premises-live-encoders.md)
- [Utiliser un magnétoscope numérique cloud](live-event-cloud-dvr.md)
- [Comparaison des fonctionnalités des types d’événements en direct](live-event-types-comparison.md)
- [États et facturation](live-event-states-billing.md)
- [Latence](live-event-latency.md)

## <a name="next-steps"></a>Étapes suivantes

* [Didacticiel de diffusion en continu en direct](stream-live-tutorial-with-api.md)
* [Conseils de migration pour le passage de Media Services v2 à Media Services v3](migrate-from-v2-to-v3.md)
