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
ms.date: 01/22/2019
ms.author: juliako
ms.openlocfilehash: 3be7ad84cf0d45276c136465d7247ec43621aceb
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810956"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Streaming en direct avec Azure Media Services v3

Azure Media Services vous permet de transmettre des événements en direct auprès de vos clients dans le cloud Azure. Pour cela, vous avez besoin des éléments suivants :  

- Une caméra, pour capturer l’événement en direct.
- Un encodeur vidéo live, pour convertir les signaux de la caméra (ou d’un autre appareil, comme un ordinateur portable) en flux de contribution qui sera ensuite envoyé à Media Services. Le flux de contribution peut inclure des signaux de publicité, tels que les marqueurs SCTE-35.
- Des composants dans Media Services, pour ingérer, prévisualiser, empaqueter, enregistrer, chiffrer et diffuser l’événement en direct auprès de vos clients, ou dans un CDN en vue d’une diffusion ultérieure.

Cet article présente les principaux composants utilisés pour le streaming en direct avec Media Services. Il fournit également des conseils d’utilisation et des diagrammes de ces composants.

## <a name="live-streaming-workflow"></a>Workflow de streaming en direct

Voici les étapes d’un workflow de streaming en direct :

1. Vérifiez que le **Point de terminaison de streaming** est en cours d’exécution. 
2. Créez un **Événement en direct**. 
  
    Lors de la création de l’événement, vous pouvez spécifier qu’il démarre automatiquement. Sinon, lancez-le dès que vous souhaitez commencer le streaming.<br/> Lorsque le démarrage automatique est défini sur true, l’événement en direct démarre juste après sa création. La facturation commence donc dès qu’il est en cours d’exécution. Vous devez appeler explicitement la commande Stop sur la ressource de l’événement en remps réel pour arrêter toute facturation supplémentaire. Pour plus d’informations, voir [États et facturation des événements en direct](live-event-states-billing.md).
3. Récupérez la ou les URL de réception et configurez votre encodeur sur site de façon à ce qu’il utilise cette URL pour envoyer le flux de contribution.<br/>Voir [Encodeurs live recommandés](recommended-on-premises-live-encoders.md).
4. Récupérez l’URL d’aperçu et utilisez-la pour vérifier que l’entrée de l’encodeur est bien reçue.
5. Créez un objet **Asset**.
6. Créez une **Sortie en direct** en utilisant le nom de la ressource que vous venez de créer.

     La **Sortie en direct** archive le flux dans la **Ressource**.
7. Créez un **Localisateur de streaming** avec les types de **Stratégies de streaming** prédéfinis.

    Pour chiffrer le contenu, voir [Vue d’ensemble de la protection du contenu](content-protection-overview.md).
8. Listez les chemins d’accès dans le **Localisateur de streaming** pour récupérer les URL à utiliser (elles sont déterministes).
9. Récupérez le nom d’hôte du **Point de terminaison de streaming** à partir duquel vous souhaitez effectuer le streaming.
10. Combinez l’URL de l’étape 8 avec le nom d’hôte de l’étape 9 pour obtenir l’URL complète.
11. Si vous ne souhaitez plus afficher votre **Événement en direct**, arrêtez le streaming de l'événement et supprimez le **Localisateur de streaming**.

Pour plus d’informations, voir le [tutoriel Streaming en direct](stream-live-tutorial-with-api.md).

## <a name="overview-of-main-components"></a>Présentation des principaux composants

Pour transmettre des flux en direct ou à la demande avec Media Services, vous devez avoir au moins un point de terminaison de streaming ([StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints)). Quand votre compte Media Services est créé, un StreamingEndpoint **par défaut**, avec l’état **Arrêté**, est ajouté à ce compte. Vous devez démarrer le StreamingEndpoint à partir duquel vous souhaitez transmettre votre contenu à vos clients. Vous pouvez utiliser le **StreamingEndpoint** par défaut, ou créer un autre **StreamingEndpoint** personnalisé avec les paramètres de CDN et de configuration souhaités. Vous pouvez choisir d’activer plusieurs StreamingEndpoint, chacun d’eux ciblant un CDN différent et spécifiant un nom d’hôte unique pour la distribution du contenu. 

Dans Media Services, ce sont les objets [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) qui gèrent l’ingestion et le traitement des flux vidéo en direct. Quand vous créez un objet LiveEvent, un point de terminaison d’entrée est également créé. Vous pouvez utiliser ce point de terminaison pour envoyer un signal en direct à partir d’un encodeur à distance. L’encodeur live à distance envoie le flux de contribution à ce point de terminaison d’entrée par le biais du protocole [RTMP](https://www.adobe.com/devnet/rtmp.html) ou [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (MP4 fragmenté). Pour le protocole de réception de diffusion en continu lisse, les schémas d’URL pris en charge sont `http://` ou `https://`. Pour le protocole de réception RTMP, les schémas d’URL pris en charge sont `rtmp://` ou `rtmps://`. Pour plus d’informations, consultez [Encodeurs de vidéos en flux continu recommandés](recommended-on-premises-live-encoders.md).<br/>
Quand vous créez un **Événement en direct**, vous pouvez spécifier des adresses IP autorisées aux formats suivants : adresses IPv4 à quatre chiffres, plage d’adresses CIDR.

Une fois que le **LiveEvent** commence à recevoir le flux de contribution, vous pouvez utiliser son point de terminaison d’aperçu (URL d’aperçu) pour prévisualiser et valider le flux en direct que vous recevez avant de continuer la publication. Après avoir vérifié que le flux d’aperçu est correct, vous pouvez utiliser le LiveEvent pour rendre le flux en direct diffusable via un ou plusieurs **StreamingEndpoints** (créés au préalable). Pour cela, vous créez un objet [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) sur le **LiveEvent**. 

L’objet **LiveOutput** fonctionne comme un magnétoscope qui va capturer et enregistrer le flux en direct dans un actif multimédia dans votre compte Media Services. Le contenu enregistré est conservé dans le compte de stockage Azure associé à votre compte, dans le conteneur défini par l’actif multimédia.  Avec l’objet **LiveOuput**, vous pouvez également contrôler certaines propriétés du flux en direct sortant, telles que la quantité du flux à conserver dans l’enregistrement archive (par exemple, la capacité du DVR cloud). L’archive sur le disque est une archive circulaire de type « fenêtre » qui stocke uniquement la quantité de contenu spécifiée dans la propriété **archiveWindowLength** de l’objet **LiveOutput**. Le contenu qui dépasse cette fenêtre d’archive est automatiquement supprimé du conteneur de stockage et n’est pas récupérable. Vous pouvez créer plusieurs objets LiveOutput (trois maximum) sur un objet LiveEvent et définir pour chacun d’eux des paramètres et des longueurs de fenêtre d’archive différents.  

Avec Media Services, vous pouvez utiliser l’**empaquetage dynamique**. Cette fonctionnalité vous permet de prévisualiser et diffuser vos flux en direct dans divers formats ([MPEG DASH, HLS et Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) à partir du flux de contribution envoyé au service. Vos clients peuvent alors lire le flux en direct au moyen de n’importe quel lecteur compatible avec HLS, DASH ou Smooth Streaming. Vous pouvez utiliser le lecteur multimédia [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) dans vos applications web ou mobiles afin de transmettre votre flux dans un de ces protocoles.

Media Services vous permet de transmettre votre contenu chiffré dynamiquement (**Chiffrement dynamique**) avec la norme Advanced Encryption Standard (AES-128) ou un des trois principaux systèmes de gestion des droits numériques (DRM) : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et de licences DRM aux clients autorisés. Pour plus d’informations sur le chiffrement de votre contenu avec Media Services, consultez [Présentation de la protection du contenu](content-protection-overview.md).

Si vous le souhaitez, vous pouvez également appliquer un filtrage dynamique pour contrôler le nombre de pistes, les formats, les vitesses de transmission et les fenêtres de temps de présentation qui sont envoyés aux lecteurs. Pour plus d’informations, consultez [Filtres et manifestes dynamiques](filters-dynamic-manifest-overview.md).

Pour plus d’informations sur les nouvelles fonctionnalités de streaming en direct dans la v3, voir [Conseils de migration pour passer de la v2 à la v3 de Media Services](migrate-from-v2-to-v3.md).

## <a name="liveevent-types"></a>Types d’événements en temps réel

Un objet [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) peut être de deux types : pass-through et à encodage en direct. 

### <a name="pass-through"></a>Requête directe

![transmission directe](./media/live-streaming/pass-through.png)

Quand vous utilisez l’objet LiveEvent de type pass-through, vous chargez l’encodeur live local de générer un flux vidéo à vitesse de transmission multiple et d’envoyer ce flux comme flux de contribution au LiveEvent (à l’aide du protocole RTMP ou MP4 fragmenté). Le LiveEvent est ensuite transmis dans les flux vidéo entrants sans traitement supplémentaire. Les objets LiveEvent pass-through sont optimisés pour les événements en direct de longue durée ou le streaming en direct linéaire sans interruption (24 h/24, 365 jours/an). Si vous créez ce type d’objet LiveEvent, spécifiez le paramètre None (LiveEventEncodingType.None).

Vous pouvez envoyer le flux de contribution à une résolution jusqu’à 4 K et à une fréquence de 60 images/seconde, avec des codecs vidéo H.264/AVC ou H.265/HEVC et des codecs audio AAC (AAC-LC, HE-AACv1 ou HE-AACv2).  Pour plus d’informations, consultez l’article [Comparaison et limitations des types LiveEvent](live-event-types-comparison.md).

> [!NOTE]
> La méthode pass-through est le moyen le plus économique de diffuser des vidéos en continu si plusieurs événements vous concernent sur une longue période, et si vous avez déjà investi dans des encodeurs locaux. Consultez les détails de la [tarification](https://azure.microsoft.com/pricing/details/media-services/) .
> 

Vous pouvez voir un exemple concret dans [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Encodage en direct  

![encodage en temps réel](./media/live-streaming/live-encoding.png)

Quand vous utilisez l’encodage en direct avec Media Services, vous configurez votre encodeur live local pour qu’il génère un flux vidéo à une seule vitesse de transmission et qu’il l’envoie comme flux de contribution au LiveEvent (à l’aide du protocole RTMP ou MP4 fragmenté). Le LiveEvent encode ce flux vidéo à une seule vitesse de transmission en [flux vidéo à plusieurs vitesses de transmission](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), pour rendre sa transmission et sa lecture possibles sur les appareils compatibles avec des protocoles comme MPEG-DASH, HLS et Smooth Streaming. Si vous créez ce type d’objet LiveEvent, spécifiez le type d’encodage **Standard** (LiveEventEncodingType.Standard).

Vous pouvez envoyer le flux de contribution à une résolution jusqu’à 1080p et à une fréquence de 30 images/seconde, avec un codec vidéo H.264/AVC et un codec audio AAC (AAC-LC, HE-AACv1 ou HE-AACv2). Pour plus d’informations, consultez l’article [Comparaison et limitations des types LiveEvent](live-event-types-comparison.md).

## <a name="liveevent-types-comparison"></a>Comparaison des types d’événements en temps réel

L’article suivant fournit un tableau qui compare les fonctionnalités des deux types LiveEvent : [Comparaison](live-event-types-comparison.md).

## <a name="liveoutput"></a>Sortie en temps réel

Avec une sortie [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs), vous pouvez contrôler les propriétés du flux temps réel sortant, notamment la quantité à enregistrer (par exemple, la capacité du magnétoscope numérique cloud) et le fait que les destinataires sont ou non autorisés à démarrer la lecture du flux. La relation entre un événement **LiveEvent** et sa sortie **LiveOutput** est comparable à la diffusion télévisuelle traditionnelle, où un canal (**LiveEvent**) représente un flux vidéo constant et un enregistrement (**LiveOutput**) est limité à une plage horaire spécifique (par exemple, un journal télévisé de 18 h 30 à 19 h). Vous pouvez enregistrer une émission de télévision à l’aide d’un magnétoscope numérique (DVR). La fonctionnalité LiveEvent équivalente est gérée par la propriété ArchiveWindowLength. Il s’agit d’une durée d’intervalle ISO-8601 (par exemple, PTHH:MM:SS), qui spécifie la capacité du magnétoscope numérique. Sa valeur peut être comprise entre 3 minutes au minimum et 25 heures au maximum.

> [!NOTE]
> Les sorties **LiveOutput** démarrent dès leur création et s’arrêtent à leur suppression. Lorsque vous supprimez une sortie **LiveOutput**, vous ne supprimez pas l’élément **Asset** sous-jacent, ni son contenu. 
>
> Si vous avez publié la ressource **Sortie en direct** à l’aide d’un **Localisateur de streaming**, **l’Événement en direct** (jusqu’à la longueur de la fenêtre DVR) restera visible jusqu’à l’expiration ou la suppression du **Localisateur de streaming**, en fonction de ce qui survient en premier.

Pour plus d’informations, consultez [Utilisation d’un magnétoscope numérique cloud](live-event-cloud-dvr.md).

## <a name="streamingendpoint"></a>StreamingEndpoint

Une fois que le flux transite dans le **LiveEvent**, vous pouvez commencer l’événement de streaming en créant un objet **Asset**, un objet **LiveOutput** et un objet **StreamingLocator**. Le flux est alors archivé et mis à la disposition des utilisateurs via le [point de terminaison de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints).

Une fois votre compte Media Services créé, un point de terminaison de streaming par défaut est ajouté à votre compte à l’état Arrêté. Pour démarrer la diffusion en continu de votre contenu et tirer parti de l’empaquetage et du chiffrement dynamiques, le point de terminaison de streaming à partir duquel vous souhaitez diffuser du contenu doit se trouver à l’état En cours d’exécution.

## <a name="a-idbilling-liveevent-states-and-billing"></a><a id="billing" />États et facturation des événements LiveEvent

La facturation d’un LiveEvent commence dès que son état passe à **En cours d’exécution**. Pour interrompre la facturation du LiveEvent, vous devez arrêter cet événement.

Pour plus d’informations, consultez [États et facturation](live-event-states-billing.md).

## <a name="latency"></a>Latence

Pour plus d’informations sur la latence des événements LiveEvent, consultez [Latence](live-event-latency.md).

## <a name="next-steps"></a>Étapes suivantes

- [Comparaison des types LiveEvent](live-event-types-comparison.md)
- [États et facturation](live-event-states-billing.md)
- [Latence](live-event-latency.md)
