---
title: Terminologie et concepts d’Azure Media Services - Azure | Microsoft Docs
description: Cet article présente brièvement les concepts et la terminologie d’Azure Media Services. Il contient également des liens qui renvoient à des informations supplémentaires.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 39bdcc94b785371044b5d49fd844a06a176a8fba
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970034"
---
# <a name="media-services-concepts"></a>Concepts d’Azure Media Services

Cet article présente brièvement les concepts et la terminologie d’Azure Media Services. Il contient également des liens qui renvoient à des articles présentant une explication détaillée des concepts et des fonctionnalités de Media Services v3. 

Il est recommandé d’examiner les concepts fondamentaux décrits dans ces rubriques avant de commencer le développement.

> [!NOTE]
> Actuellement, vous ne pouvez pas utiliser le portail Azure pour gérer des ressources v3. Utilisez l’[API REST](https://aka.ms/ams-v3-rest-ref), l’interface [CLI](https://aka.ms/ams-v3-cli-ref) ou l’un des kits [SDK](media-services-apis-overview.md#sdks) pris en charge.

## <a name="terminology"></a>Terminologie

Cette section montre comment certains termes courants du secteur mappent à l’API Media Services v3.

### <a name="live-event"></a>Événement en direct

Un **événement en direct** représente un pipeline d’ingestion, de transcodage (éventuellement) et d’empaquetage des streams en direct des fichiers vidéo et audio et des métadonnées en temps réel.

Pour les clients migrant depuis les API Media Services v2, **l’événement en direct** remplace l’entité **Channel** dans la version 2. Pour plus d’informations, consultez l’article [Conseils de migration pour le passage de Media Services v2 à Media Services v3](migrate-from-v2-to-v3.md).

### <a name="streaming-endpoint-packaging-and-origin"></a>Point de terminaison de streaming (empaquetage et origine)

Un **point de terminaison de streaming** représente un empaquetage dynamique (juste-à-temps) et un service d’origine qui permet de transmettre votre contenu en direct et à la demande directement à une application de lecteur cliente, à l’aide de l’un des protocoles de diffusion multimédia en continu courants (HLS ou DASH). En outre, le **point de terminaison de streaming** offre un chiffrement dynamique (juste-à-temps) aux systèmes DRM de pointe.

Dans le secteur de la diffusion multimédia en continu, ce service est communément appelé **packager** ou **origine**.  Autres termes courants de ce secteur pour cette fonctionnalité : JITP (Just-in-time-packager) ou JITE (Just-in-time-encryption). 
 
## <a name="cloud-upload-and-storage"></a>Chargement et stockage sur le cloud

Pour commencer à gérer, chiffrer, coder, analyser et diffuser en continu du contenu multimédia dans Azure, vous devez créer un compte Media Services, puis charger vos fichiers numériques dans **Éléments multimédias**.

- [Chargement et stockage cloud](storage-account-concept.md)
- [Concept des éléments multimédias](assets-concept.md)

## <a name="encoding"></a>Encodage

Une fois que vous avez chargé vos fichiers multimédias numériques haute définition dans Éléments multimédias, vous pouvez les encoder dans des formats pouvant être lus sur un large choix de navigateurs et d’appareils. 

Pour encoder du contenu avec Media Services v3, vous devez créer des **transformations** et des **travaux**.

![Transformations](./media/encoding/transforms-jobs.png)

- [Transformations et travaux](transforms-jobs-concept.md)
- [Encodage avec Media Services](encoding-concept.md)

## <a name="media-analytics"></a>Analytique multimédia

Pour analyser vos fichiers vidéo et audio, vous devez également créer des **transformations** et des **travaux**.

- [Analyse des fichiers vidéo et audio](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Empaquetage, remise, protection

Une fois que votre contenu est encodé, vous pouvez tirer parti de **l’empaquetage dynamique**. Dans Media Services, un **point de terminaison de streaming**/origine est le service d’empaquetage dynamique utilisé pour distribuer du contenu multimédia aux lecteurs clients. Pour rendre les vidéos dans la ressource de sortie disponibles en lecture pour les clients, vous devez créer un **localisateur de streaming**, puis générer des URL de diffusion en continu. 

Lors de la création du **localisateur de streaming**, outre le nom de l’élément multimédia, vous devez spécifier une **stratégie de streaming**. Les **stratégies de streaming** vous permettent de définir des protocoles de streaming et des options de chiffrement (le cas échéant) pour vos **localisateurs de streaming**.

L’empaquetage dynamique est utilisé que le streaming de contenu soit effectué en direct ou à la demande. Le diagramme suivant illustre le flux de travail du streaming à la demande avec l’empaquetage dynamique.

![Empaquetage dynamique](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Media Services vous permet de transmettre votre contenu en direct ou à la demande chiffré dynamiquement avec la norme Advanced Encryption Standard (AES-128) ou l’un des trois principaux systèmes de gestion des droits numériques (DRM) : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et licences (PlayReady, Widevine et FairPlay) DRM aux clients autorisés.

Si vous spécifiez des options de chiffrement sur votre flux, créez la **stratégie de clé de contenu**, puis associez-la à votre **localisateur de streaming**. La **stratégie de clé de contenu** vous permet de configurer le mode de remise de la clé de contenu aux clients finals.

L’image suivante illustre le flux de travail de protection du contenu Media Services : 

![Protéger du contenu](./media/content-protection/content-protection.svg)

\* Le chiffrement dynamique prend en charge les standards AES-128 « clé en clair », CBCS et CENC. 

Vous pouvez utiliser les **manifestes dynamiques** de Media Services pour diffuser en continu uniquement un rendu spécifique ou des sous-clips de votre vidéo. Dans l’exemple suivant, un encodeur a été utilisé pour encoder un élément multimédia mezzanine en sept rendus vidéo ISO MP4s (de 180 p à 1 080 p). L’élément multimédia encodé peut être empaqueté de manière dynamique dans l’un des protocoles de diffusion en continu suivants : HLS, MPEG DASH et Smooth.  En haut du diagramme, le manifeste HLS de l'élément multimédia sans aucun filtre apparaît (il contient les sept rendus).  Dans la partie inférieure gauche apparaît le manifeste HLS auquel un filtre nommé « ott » a été appliqué. Le filtre « ott » indique de supprimer toutes les vitesses de transmission inférieures à 1 Mbit/s, ce qui entraîne la suppression des deux niveaux de qualité les plus bas dans la réponse. Dans la partie inférieure droite apparaît le manifeste HLS auquel un filtre nommé « mobile » a été appliqué. Le filtre « mobile » indique de supprimer les rendus pour lesquels la résolution est supérieure à 720 p, ce qui entraîne la suppression des deux rendus à 1 080 p.

![Filtrage de rendu](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [mise en package dynamique](dynamic-packaging-overview.md)
- [Points de terminaison de streaming](streaming-endpoint-concept.md)
- [Localisateurs de diffusion en continu](streaming-locators-concept.md)
- [Stratégies de diffusion en continu](streaming-policy-concept.md)
- [Stratégies de clé de contenu](content-key-policy-concept.md)
- [Protection du contenu](content-protection-overview.md)
- [Manifestes dynamiques](filters-dynamic-manifest-overview.md)
- [Filtres](filters-concept.md)

> [!NOTE]
> Widevine est un service fourni par Google Inc. soumis aux conditions de service et à la politique de confidentialité de Google, Inc.

## <a name="live-streaming"></a>Vidéo en flux continu

Azure Media Services vous permet de transmettre des événements en direct auprès de vos clients dans le cloud Azure. Les **événements en direct** sont chargés de la réception et du traitement des flux vidéo en direct. Quand vous créez un **événement en direct**, un point de terminaison d’entrée est également créé. Vous pouvez utiliser ce point de terminaison pour envoyer un signal en direct à partir d’un encodeur à distance. Une fois que le flux transite dans **l’événement en direct**, vous pouvez commencer l’événement de streaming en créant un **élément multimédia**, une **sortie en direct** et un **localisateur de streaming**. La **sortie en direct** archive le flux dans **l’élément multimédia** et le met à la disposition des observateurs via le **point de terminaison de streaming**. Un **événement en direct** peut être de deux types : en **transmission directe** et en **encodage en direct**.

L’image suivante illustre le flux de travail de type transmission directe :

![transmission directe](./media/live-streaming/pass-through.svg)

- [Vue d’ensemble du streaming en direct](live-streaming-overview.md)
- [Événements en direct et sorties en direct](live-events-outputs-concept.md)

## <a name="monitoring"></a>Surveillance

### <a name="event-grid"></a>Event Grid

Pour afficher la progression du travail, vous devez utiliser **Event Grid**. Media Services émet aussi les types d’événements en direct. Avec Event Grid, vos applications peuvent écouter les événements de presque tous les services Azure ou de toute source personnalisée, et y réagir. 

- [Traitement des événements Event Grid](reacting-to-media-services-events.md)
- [Schémas](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

Surveillez les métriques et les journaux de diagnostic qui vous aident à comprendre le fonctionnement de vos applications avec Azure Monitor.

- [Métriques et journaux de diagnostic](media-services-metrics-diagnostic-logs.md)
- [Schémas des journaux de diagnostic](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Clients Player

Vous pouvez utiliser Lecteur multimédia Azure pour lire des contenus multimédias diffusés en continu par Media Services sur une grande variété de navigateurs et d’appareils. Azure Media Player exploite des normes du secteur, telles que HTML5, Media Source Extensions (MSE) et Encrypted Media Extensions (EME), pour offrir une expérience enrichie de diffusion en continu adaptative. 

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
