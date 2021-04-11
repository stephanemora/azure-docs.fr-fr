---
title: Guide de migration relatif à la protection du contenu
description: Cet article fournit des conseils basés sur un scénario de protection de votre contenu qui vous aideront à opérer votre migration de l’API v2 vers l’API v3 d’Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 74f15fc302a8499e41a1413dd8915e6442d4bbe7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064492"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Recommandations en matière de migration basées sur un scénario de protection de contenu

![logo du guide de migration](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![étapes de migration 2](./media/migration-guide/steps-4.svg)

Cet article fournit des informations et des conseils sur la migration de cas d’utilisation de protection de contenu de l’API v2 vers la nouvelle API v3 d’Azure Media Services.

## <a name="protect-content-in-v3-api"></a>Protection de contenu dans l’API v3

Utilisez la prise en charge des fonctionnalités [à plusieurs clés](architecture-design-multi-drm-system.md) dans la nouvelle API V3.

Pour connaître les étapes spécifiques, consultez les concepts, tutoriels et guides pratiques relatifs à la protection de contenu.

## <a name="visibility-of-v2-assets-streaminglocators-and-properties-in-the-v3-api-for-content-protection-scenarios"></a>Visibilité des ressources, des StreamingLocators et des propriétés v2 dans l’API v3 pour les scénarios de protection de contenu

Pendant la migration vers l’API v3, vous constaterez que vous devrez accéder à certaines propriétés ou clés de contenu de vos ressources v2. L’une des principales différences réside dans le fait que l’API v2 utilise l’**AssetID** comme clé d’identification principale, et que la nouvelle API v3 utilise le nom de Gestion des ressources Azure de l’entité comme identificateur principal.  La propriété **Asset.Name** de l’API v2 n’étant généralement pas utilisée en tant qu’identificateur unique. lors de la migration vers l’API v3, vous constaterez que vos noms de ressources v2 s’affichent désormais dans le champ **Asset.Description**.

Par exemple, si vous aviez précédemment une ressource v2 avec l’ID **« nb:cid:UUID:8cb39104-122c-496e-9ac5-7f9e2c2547b8 »** , vous constaterez, lors de l’affichage de la liste des anciennes ressources v2 via l’API V3, que le nom sera désormais la partie GUID à la fin (en l’occurrence, **« 8cb39104-122c-496e-9ac5-7f9e2c2547b8 »** ).

Vous pouvez interroger les **StreamingLocators** associés aux ressources créées dans l’API v2 à l’aide de la nouvelle méthode v3 [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) sur l’entité Ressource.  Référencez également la version de Kit de développement logiciel (SDK) de client .NET [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true)

Les résultats de la méthode **ListStreamingLocators** vous fourniront les valeurs **Name** et **StreamingLocatorId** du localisateur, ainsi que la valeur **StreamingPolicyName**.

Pour trouver les **ContentKeys** utilisées dans vos **StreamingLocators** pour la protection du contenu, vous pouvez appeler la méthode [StreamingLocator.ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true).  

Toutes les **Ressources** créées et publiées à l’aide de l’API v2 auront une [Stratégie de clé de contenu](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept) et une Clé de contenu définies dans l’API v3, au lieu d’utiliser une stratégie de clé de contenu par défaut sur la [Stratégie de diffusion en continu](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept).

Pour plus d’informations sur la protection du contenu dans l’API v3, consultez l’article [Protéger votre contenu à l’aide du chiffrement dynamique de Media Services](https://docs.microsoft.com/azure/media-services/latest/drm-content-protection-concept).

## <a name="how-to-list-your-v2-assets-and-content-protection-settings-using-the-v3-api"></a>Comment répertorier vos ressources et paramètres de protection de contenu de l’API v2 à l’aide de l’API v3

Dans l’API v2, vous utilisez communément **Assets**, **StreamingLocators** et **ContentKeys** pour protéger votre contenu de diffusion en continu.
Lors de la migration vers l’API V3, vos Assets, StreamingLocators et ContentKeys de l’API v2 sont toutes exposées automatiquement dans l’API V3, et toutes leurs données y sont accessibles.

## <a name="can-i-update-v2-properties-using-the-v3-api"></a>Puis-je mettre à jour des propriétés de l’API v2 à l’aide de l’API v3 ?

Non, vous ne pouvez pas mettre à jour via l’API v3 des propriétés d’entités créées à l’aide de StreamingLocators, de StreamingPolicies, de Stratégies de clé de contenu et de Clés de contenu dans l’API v2.
Si vous devez mettre à jour, modifier ou transformer du contenu stocké sur des entités v2, vous devez le faire via l’API v2 ou créer des entités d’API v3 pour les migrer.

## <a name="how-do-i-change-the-contentkeypolicy-used-for-a-v2-asset-that-is-published-and-keep-the-same-content-key"></a>Comment faire pour modifier la ContentKeyPolicy utilisée pour une ressource v2 publiée tout en conservant la même clé de contenu ?

Dans ce cas, vous devez d’abord annuler la publication (supprimer tous les localisateurs de diffusion en continu) sur la ressource à l’aide du Kit de développement logiciel (SDK) v2 (supprimer le localisateur, dissocier la stratégie d’autorisation de clé de contenu, dissocier la stratégie de remise de ressources, dissocier la clé de contenu, supprimer la clé de contenu), puis créer un nouveau **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** dans l’API v3 à l’aide d’une [StreamingPolicy](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept) et d’une [ContentKeyPolicy](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept) v3.

Vous devez spécifier l’identificateur de clé de contenu et la valeur de clé spécifiques nécessaires lors de la création du **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** .

Notez qu’il est possible de supprimer le localisateur v2 à l’aide de l’API v3, mais que cela n’a pas pour effet de supprimer la clé de contenu ou la stratégie de clé de contenu utilisées si elles ont été créées dans l’API v2.  

## <a name="using-amse-v2-and-amse-v3-side-by-side"></a>Utilisation d’AMSE v2 et d’AMSE v3 côte à côte

Lors de la migration de votre contenu de l’API v2 vers l’API v3, il est recommandé d’installer l’[outil Azure Media Services Explorer v2](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0), ainsi que l’[outil Azure Media Services Explorer v3](https://github.com/Azure/Azure-Media-Services-Explorer), pour faciliter la comparaison des données qu’ils affichent côte à côte pour une ressource créée et publiée via des API v2. Les propriétés doivent toutes être visibles, mais à des emplacements légèrement différents.  


## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Concepts, tutoriels et guides pratiques relatifs à la protection de contenu

### <a name="concepts"></a>Concepts

- [Protéger votre contenu à l’aide du chiffrement dynamique de Media Services](drm-content-protection-concept.md)
- [Concevoir un système de protection de contenu multi-DRM avec contrôle d’accès](architecture-design-multi-drm-system.md)
- [Azure Media Services V3 avec le modèle de licence PlayReady](drm-playready-license-template-concept.md)
- [Vue d’ensemble du modèle de licence Widevine avec Media Services v3](drm-widevine-license-template-concept.md)
- [Configuration et conditions de licence Apple FairPlay](drm-fairplay-license-overview.md)
- [Stratégies de diffusion en continu](streaming-policy-concept.md)
- [Stratégies de clé de contenu](drm-content-key-policy-concept.md)

### <a name="tutorials"></a>Tutoriels

[Démarrage rapide : Utiliser le portail pour chiffrer du contenu](drm-encrypt-content-how-to.md)

### <a name="how-to-guides"></a>Guides pratiques

- [Obtenir une clé de signature à partir de la stratégie existante](drm-get-content-key-policy-dotnet-how-to.md)
- [FairPlay Streaming hors connexion pour iOS avec Media Services v3](drm-offline-fairplay-for-ios-concept.md)
- [Diffusion en continu Widevine hors connexion pour Android avec Media Services v3](drm-offline-widevine-for-android.md)
- [Diffusion en continu PlayReady hors connexion pour Windows 10 avec Media Services v3](drm-offline-playready-streaming-for-windows-10.md)

## <a name="samples"></a>Exemples

Vous pouvez aussi [comparer les codes V2 et V3 dans les exemples de code](migrate-v-2-v-3-migration-samples.md).

## <a name="tools"></a>Outils

- [Outil Azure Media Services Explorer v3](https://github.com/Azure/Azure-Media-Services-Explorer)
- [Outil Azure Media Services Explorer v2](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
