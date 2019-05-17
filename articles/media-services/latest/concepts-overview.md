---
title: Azure la terminologie de Media Services et concepts - Azure | Microsoft Docs
description: Cette rubrique fournit une vue d’ensemble des concepts et terminologie d’Azure Media Services et fournit des liens pour plus d’informations.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/13/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 1e76569c7f5157dce681d15ec8d499b90e080102
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65762306"
---
# <a name="media-services-concepts"></a>Concepts Media Services

Cette rubrique donne une vue d’ensemble des concepts et terminologie d’Azure Media Services. L’article fournit également des liens vers des articles avec une explication détaillée des concepts de Media Services v3. 

Il est recommandé d’examiner les concepts fondamentaux décrits dans ces rubriques avant de commencer le développement.

> [!NOTE]
> Actuellement, vous ne pouvez pas utiliser le portail Azure pour gérer des ressources v3. Utilisez l’[API REST](https://aka.ms/ams-v3-rest-ref), l’interface [CLI](https://aka.ms/ams-v3-cli-ref) ou l’un des kits [SDK](media-services-apis-overview.md#sdks) pris en charge.

## <a name="terminology"></a>Terminologie

Cette section montre comment certains termes courants mappent à l’API v3 de Media Services.

### <a name="live-event"></a>Événement en temps réel

Un **événement réel** représente un pipeline de réception, le transcodage (éventuellement) et l’empaquetage des flux live de métadonnées de vidéo et audio en temps réel.

Pour les clients de la migration à partir de Media Services v2 API, le **événement réel** remplace le **canal** entity dans v2. Pour plus d’informations, consultez [migration de v2 vers v3](migrate-from-v2-to-v3.md).

### <a name="streaming-endpoint-packaging-and-origin"></a>Point de terminaison (empaquetage et l’origine)

Un **le point de terminaison de diffusion en continu** représente un dynamique (juste-à-temps) empaquetage et l’origine service qui peut fournir votre contenu en direct et à la demande directement à une application de lecteur client, à l’aide de la diffusion en continu media des protocoles courants (TLS ou DASH). En outre, le **le point de terminaison de diffusion en continu** fournit le chiffrement dynamique (juste-à-temps) pour ses systèmes de DRM de pointe.

Dans la diffusion multimédia en continu du secteur, ce service est communément appelé un **Packager** ou **origine**.  Parmi les autres conditions dans le secteur pour cette fonctionnalité incluent JITP (juste en temps packager) ou JITE (juste en temps chiffrement). 
 
## <a name="cloud-upload-and-storage"></a>Chargement et stockage sur le cloud

Pour démarrer la gestion, le chiffrement, codage, l’analyse et diffusion en continu de contenu multimédia dans Azure, vous devez créer un compte Media Services et charger vos fichiers numériques dans **ressources**.

- [Chargement et stockage cloud](storage-account-concept.md)
- [Concept de ressources](assets-concept.md)

## <a name="encoding"></a>Encodage

Une fois que vous téléchargez vos fichiers multimédias numériques de haute qualité en ressources, vous pouvez les encoder dans des formats qui peuvent être lus sur un large éventail de navigateurs et appareils. 

Pour encoder avec Media Services v3, vous devez créer **transforme** et **travaux**.

![Transformations](./media/encoding/transforms-jobs.png)

- [Transformations et travaux](transforms-jobs-concept.md)
- [Encodage avec Media Services](encoding-concept.md)

## <a name="media-analytics"></a>Media Analytics

Pour analyser vos fichiers vidéos et audio, vous devez également créer **transforme** et **travaux**.

- [Analyse des fichiers vidéos et audio](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Empaquetage, remise, protection

Une fois que votre contenu est encodé, vous pouvez tirer parti de **empaquetage dynamique**. Dans Media Services, un **le point de terminaison de diffusion en continu**  /origine est le service de mise en package dynamique utilisé pour distribuer du contenu multimédia aux joueurs de client. Pour rendre vidéos dans la ressource en sortie aux clients pour la lecture, vous devez créer un **localisateur de diffusion en continu** , puis générer l’URL de diffusion. 

Lorsque vous créez le **localisateur de diffusion en continu**, en plus du nom de l’élément multimédia, vous devez spécifier **stratégie de diffusion en continu**. **Stratégies de diffusion en continu** vous permettent de définir les protocoles de diffusion en continu et options de chiffrement (le cas échéant) pour votre **localisateurs de diffusion en continu**.

Empaquetage dynamique est utilisé si vous diffusez votre contenu en direct ou à la demande. Le diagramme suivant illustre la diffusion en continu à la demande avec les flux d’empaquetage dynamique.

![Empaquetage dynamique](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

Avec Media Services, vous pouvez distribuer votre contenu en direct et à la demande chiffré dynamiquement avec Advanced Encryption Standard (AES-128) ou / et tous les trois principales droits numériques (DRM) de systèmes de gestion : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et licences (PlayReady, Widevine et FairPlay) DRM aux clients autorisés.

Si vous spécifiez les options de chiffrement sur votre flux, créer le **stratégie clé de contenu** et associez-la à votre **localisateur de diffusion en continu**. Le **stratégie de la clé de contenu** vous permet de configurer le mode de remise de la clé de contenu pour les clients.

L’image suivante illustre le flux de travail de protection du contenu Media Services : 

![Protéger du contenu](./media/content-protection/content-protection.svg)

&#42;chiffrement dynamique prend en charge la clé AES-128 « clair », CBCS et CENC. 

Vous pouvez utiliser Media Services **manifestes dynamiques** pour diffuser un rendu spécifique ou sous-éléments de votre vidéo. Dans l’exemple suivant, un encodeur a été utilisé pour encoder un élément multimédia mezzanine en sept rendus vidéo ISO MP4s (de 180 p à 1 080 p). L’élément multimédia encodé peut être empaqueté de manière dynamique dans l’un des protocoles de diffusion en continu suivants : HLS, MPEG DASH et Smooth.  En haut du diagramme, le manifeste HLS de l'élément multimédia sans aucun filtre apparaît (il contient les sept rendus).  Dans la partie inférieure gauche apparaît le manifeste HLS auquel un filtre nommé « ott » a été appliqué. Le filtre « ott » indique de supprimer toutes les vitesses de transmission inférieures à 1 Mbit/s, ce qui entraîne la suppression des deux niveaux de qualité les plus bas dans la réponse. Dans la partie inférieure droite apparaît le manifeste HLS auquel un filtre nommé « mobile » a été appliqué. Le filtre « mobile » indique de supprimer les rendus pour lesquels la résolution est supérieure à 720 p, ce qui entraîne la suppression des deux rendus à 1 080 p.

![Filtrage de rendu](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [mise en package dynamique](dynamic-packaging-overview.md)
- [Points de terminaison de streaming](streaming-endpoint-concept.md)
- [Localisateurs de diffusion en continu](streaming-locators-concept.md)
- [Stratégies de diffusion en continu](streaming-policy-concept.md)
- [Stratégies de clé de contenu](content-key-policy-concept.md)
- [Protection du contenu](content-protection-overview.md)
- [Manifestes dynamiques](filters-dynamic-manifest-overview.md)
- [Les filtres](filters-concept.md)

## <a name="live-streaming"></a>Streaming en direct

Azure Media Services vous permet de transmettre des événements en direct auprès de vos clients dans le cloud Azure. Les **événements en direct** sont chargés de la réception et du traitement des flux vidéo en direct. Lorsque vous créez un **événement réel**, un point de terminaison d’entrée est créé que vous pouvez utiliser pour envoyer un signal en direct à partir d’un codeur. Une fois que vous avez le flux transite dans le **événement réel**, vous pouvez commencer l’événement de diffusion en continu en créant un **Asset**, **sortie Live**, et **localisateur de diffusion en continu** . **Live sortie** archive le flux dans le **Asset** et le rendre disponible aux observateurs via le **le point de terminaison de diffusion en continu**. Un **événement réel** peut prendre l’une des deux types : **directe** et **encodage live**.

L’image suivante illustre le flux de travail de type directe :

![transmission directe](./media/live-streaming/pass-through.svg)

- [Vue d’ensemble du streaming en direct](live-streaming-overview.md)
- [Événements en direct et sorties en direct](live-events-outputs-concept.md)

## <a name="monitoring"></a>Surveillance

### <a name="event-grid"></a>Event Grid

Pour afficher la progression du travail, vous devez utiliser **Event Grid**. Media Services émet également les types d’événements en direct. Avec Event Grid, vos applications peuvent écouter les événements de presque tous les services Azure ou de toute source personnalisée, et y réagir. 

- [Gestion des événements Event Grid](reacting-to-media-services-events.md)
- [Schémas](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

Mesures de surveillance et les journaux de diagnostic qui vous aident à comprennent le fonctionnement de vos applications avec Azure Monitor.

- [Métriques et journaux de diagnostic](media-services-metrics-diagnostic-logs.md)
- [Schémas de journaux de diagnostic](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Clients Player

Vous pouvez utiliser Azure Media Player pour lire du contenu multimédia diffusé en continu par Media Services sur un large éventail de navigateurs et appareils. Azure Media Player exploite des normes du secteur, telles que HTML5, Media Source Extensions (MSE) et Encrypted Media Extensions (EME), pour offrir une expérience enrichie de diffusion en continu adaptative. 

- [Présentation du Lecteur multimédia Azure](use-azure-media-player.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="next-steps"></a>Étapes suivantes

* [Encoder le fichier distant et diffuser la vidéo – REST](stream-files-tutorial-with-rest.md)
* [Encoder le fichier chargé et diffuser la vidéo – REST](stream-files-tutorial-with-api.md)
* [Diffuser en direct - .NET](stream-live-tutorial-with-api.md)
* [Analyser votre vidéo - .NET](analyze-videos-tutorial-with-api.md)
* [Chiffrement dynamique AES-128 - .NET](protect-with-aes128.md)
* [Chiffrer dynamiquement avec multi-DRM - .NET](protect-with-drm.md) 
