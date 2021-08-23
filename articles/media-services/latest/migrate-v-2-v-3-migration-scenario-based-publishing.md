---
title: Recommandations en matière de migration basées sur un empaquetage et une livraison
description: Cet article fournit des recommandations basées sur un scénario pour l’empaquetage et la livraison, qui vous aideront à opérer une migration de la version v2 vers la version v3 d’Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: d8cba5c69802828a0bd486d902d85199f3ae1d00
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122563817"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>Recommandations en matière de migration basées sur un scénario d’empaquetage et de livraison

![logo du guide de migration](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![étapes de migration 2](./media/migration-guide/steps-4.svg)

Cet article fournit des recommandations basées sur un scénario pour l’empaquetage et la livraison, qui vous aideront à opérer une migration de la version v2 vers la version v3 d’Azure Media Services.

Modifications majeures apportées à la façon dont le contenu est publié dans l’API v3. Le nouveau modèle de publication est simplifié et utilise moins d’entités pour créer un localisateur de streaming. L’API a été réduite à deux entités par rapport aux quatre entités précédemment requises. Les stratégies de clé de contenu et les localisateurs de streaming remplacent désormais le besoin de `ContentKeyAuthorizationPolicy`, `AssetDeliveryPolicy`, `ContentKey` et `AccessPolicy`.

## <a name="packaging-and-delivery-in-v3"></a>Empaquetage et livraison dans la version v3

1. Créez des [Stratégie de clé de contenu](drm-content-key-policy-concept.md).
1. Créez des [Localisateurs de streaming](stream-streaming-locators-concept.md).
1. Obtenez les [Chemins de streaming](create-streaming-locator-build-url.md). 
    1. Configurez pour un lecteur [DASH](encode-dynamic-packaging-concept.md#deliver-dash) ou [HLS](encode-dynamic-packaging-concept.md#deliver-hls).

Pour connaître les étapes spécifiques, consultez les concepts, tutoriels et guides pratiques relatifs à la publication.

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>Concepts, tutoriels et guides pratiques relatifs à la publication

### <a name="concepts"></a>Concepts

- [Empaquetage dynamique dans Media Services v3](encode-dynamic-packaging-concept.md)
- [Filtres](filters-concept.md)
- [Filtrer vos manifestes à l’aide de Dynamic Packager](filters-dynamic-manifest-concept.md)
- [Points de terminaison de streaming (origine) dans Azure Media Services](stream-streaming-endpoint-concept.md)
- [Diffuser du contenu avec l’intégration de CDN](stream-scale-streaming-cdn-concept.md)
- [Localisateurs de diffusion en continu](stream-streaming-locators-concept.md)

### <a name="how-to-guides"></a>Guides pratiques

- [Gérer les points de terminaison de streaming avec Media Services v3](stream-manage-streaming-endpoints-how-to.md)
- [Exemple CLI : Publier un élément multimédia](cli-publish-asset.md)
- [Créer un localisateur de streaming et générer des URL](create-streaming-locator-build-url.md)
- [Télécharger les résultats d’un travail](job-download-results-how-to.md)
- [Signaler des pistes audio descriptives](signal-descriptive-audio-howto.md)
- [Installation complète du Lecteur multimédia Azure](../azure-media-player/azure-media-player-full-setup.md)
- [Guide pratique pour utiliser le lecteur Video.js avec Azure Media Services](player-how-to-video-js-player.md)
- [Guide pratique pour utiliser le lecteur Shaka avec Azure Media Services](player-shaka-player-how-to.md)

## <a name="samples"></a>Exemples

Vous pouvez aussi [comparer les codes V2 et V3 dans les exemples de code](migrate-v-2-v-3-migration-samples.md).
