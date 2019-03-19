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
ms.date: 02/01/2019
ms.author: juliako
ms.openlocfilehash: 67876532496aa0a295bf32692534b16d38599492
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839506"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Streaming en direct avec Azure Media Services v3

Azure Media Services vous permet de transmettre des événements en direct auprès de vos clients dans le cloud Azure. Pour cela, vous avez besoin des éléments suivants :  

- Une caméra, pour capturer l’événement en direct.<br/>Pour obtenir des idées de configuration, consultez [Simple and portable event video gear setup]( https://link.medium.com/KNTtiN6IeT).
- Un encodeur vidéo live, pour convertir les signaux de la caméra (ou d’un autre appareil, comme un ordinateur portable) en flux de contribution qui sera ensuite envoyé à Media Services. Le flux de contribution peut inclure des signaux de publicité, tels que les marqueurs SCTE-35.<br/>Pour obtenir une liste d’encodeurs de streaming live recommandés, consultez [Encodeurs de streaming live](recommended-on-premises-live-encoders.md). Consultez également ce billet de blog : [Live streaming production with OBS](https://link.medium.com/ttuwHpaJeT).
- Des composants dans Media Services, pour ingérer, prévisualiser, empaqueter, enregistrer, chiffrer et diffuser l’événement en direct auprès de vos clients, ou dans un CDN en vue d’une diffusion ultérieure.

Avec Media Services, vous pouvez utiliser l’**empaquetage dynamique**, qui vous permet de prévisualiser et diffuser vos flux temps réel dans divers formats ([MPEG DASH, HLS et Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) à partir du flux de contribution envoyé au service. Vos clients peuvent alors lire le flux en direct au moyen de n’importe quel lecteur compatible avec HLS, DASH ou Smooth Streaming. Vous pouvez utiliser le lecteur multimédia [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) dans vos applications web ou mobiles afin de transmettre votre flux dans un de ces protocoles.

Media Services vous permet de transmettre votre contenu chiffré dynamiquement (**Chiffrement dynamique**) avec la norme Advanced Encryption Standard (AES-128) ou un des trois principaux systèmes de gestion des droits numériques (DRM) : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et de licences DRM aux clients autorisés. Pour plus d’informations sur le chiffrement de votre contenu avec Media Services, consultez [Présentation de la protection du contenu](content-protection-overview.md).

Vous pouvez également appliquer un filtrage dynamique pour contrôler le nombre de pistes, les formats, les vitesses de transmission et les fenêtres de temps de présentation qui sont envoyés aux lecteurs. Pour plus d’informations, consultez [Filtres et manifestes dynamiques](filters-dynamic-manifest-overview.md).

Cet article offre une vue d’ensemble et des conseils relatifs au streaming en direct avec Media Services.

## <a name="prerequisites"></a>Conditions préalables

Pour comprendre le workflow de streaming en direct dans Media Services v3, vous devez passer en revue et comprendre les concepts suivants : 

- [Points de terminaison de streaming](streaming-endpoint-concept.md)
- [Événements en direct et sorties en direct](live-events-outputs-concept.md)
- [Localisateurs de diffusion en continu](streaming-locators-concept.md)

## <a name="live-streaming-workflow"></a>Workflow de streaming en direct

Voici les étapes d’un workflow de streaming en direct :

1. Accédez à votre compte Media Services et vérifiez que le **point de terminaison de streaming** est en cours d'exécution. 
2. Créez un [événement en temps réel](live-events-outputs-concept.md). <br/>Lors de la création de l’événement, vous pouvez spécifier qu’il démarre automatiquement. Sinon, lancez-le dès que vous souhaitez commencer le streaming.<br/> Lorsque le démarrage automatique est défini sur true, l’événement en direct démarre juste après sa création. La facturation commence donc dès que son exécution démarre. Vous devez appeler explicitement la commande Stop sur la ressource de l’événement en direct pour arrêter toute facturation supplémentaire. Pour plus d’informations, consultez [États et facturation des événements en direct](live-event-states-billing.md).
3. Récupérez la ou les URL de réception et configurez votre encodeur sur site de façon à ce qu’il utilise cette URL pour envoyer le flux de contribution.<br/>Voir [Encodeurs live recommandés](recommended-on-premises-live-encoders.md).
4. Récupérez l’URL d’aperçu et utilisez-la pour vérifier que l’entrée de l’encodeur est bien reçue.
5. Créez un objet **Asset**.
6. Créez un objet **LiveOutput** et utilisez le nom de l’objet Asset que vous venez de créer.<br/>La **sortie en direct** archive le flux dans l’**actif multimédia**.
7. Créez un **localisateur de streaming**  avec les types intégrés de la **stratégie de streaming**.<br/>Pour chiffrer le contenu, voir [Vue d’ensemble de la protection du contenu](content-protection-overview.md).
8. Listez les chemins d’accès dans le **Localisateur de streaming** pour récupérer les URL à utiliser (elles sont déterministes).
9. Récupérez le nom d’hôte du **Point de terminaison de streaming** à partir duquel vous souhaitez effectuer le streaming.
10. Combinez l’URL de l’étape 8 avec le nom d’hôte de l’étape 9 pour obtenir l’URL complète.
11. Si vous ne souhaitez plus afficher votre **événement en direct**, arrêtez le streaming de l’événement et supprimez le **localisateur de streaming**.

## <a name="other-important-articles"></a>Autres articles importants

- [Encodeurs live recommandés](recommended-on-premises-live-encoders.md)
- [Utiliser un magnétoscope numérique cloud](live-event-cloud-dvr.md)
- [Comparaison des fonctionnalités des types d'événements en direct](live-event-types-comparison.md)
- [États et facturation](live-event-states-billing.md)
- [Latence](live-event-latency.md)

## <a name="next-steps"></a>Étapes suivantes

* [Didacticiel sur le streaming en direct](stream-live-tutorial-with-api.md)
* [Conseils de migration pour le passage de Media Services v2 à Media Services v3](migrate-from-v2-to-v3.md)
