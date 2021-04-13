---
title: Guide de migration relatif à la protection du contenu
description: Cet article fournit des conseils basés sur un scénario de protection de votre contenu qui vous aideront à opérer votre migration de l’API v2 vers l’API v3 d’Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 04/05/2021
ms.author: inhenkel
ms.openlocfilehash: a481759da3f1e7d67accdca7b4322db53abbcb0c
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490945"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Recommandations en matière de migration basées sur un scénario de protection de contenu

![logo du guide de migration](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![étapes de migration 2](./media/migration-guide/steps-4.svg)

Cet article fournit des informations et des conseils sur la migration de cas d’utilisation de protection de contenu de l’API v2 vers la nouvelle API v3 d’Azure Media Services.

## <a name="protect-content-in-v3-api"></a>Protection de contenu dans l’API v3

Utilisez la prise en charge des fonctionnalités [à plusieurs clés](architecture-design-multi-drm-system.md) dans la nouvelle API V3.

Pour connaître les étapes spécifiques, consultez Concepts, tutoriels et guides pratiques relatifs à la protection de contenu à la fin de cet article.

> [!NOTE]
> Le reste de cet article explique comment vous pouvez migrer votre protection du contenu v2 vers v3 avec .NET.  Si vous avez besoin d’instructions ou d’un exemple de code pour un autre langage ou une autre méthode, créez un problème GitHub pour cette page.

## <a name="v3-visibility-of-v2-assets-streaminglocators-and-properties"></a>Visibilité v3 des ressources v2, StreamingLocators et propriétés

Dans l’API v2, `Assets`, `StreamingLocators` et `ContentKeys` servaient à protéger votre contenu diffusé. Lors de la migration vers l’API V3, vos `Assets`, `StreamingLocators` et `ContentKeys` de l’API v2 sont toutes exposées automatiquement dans l’API V3, et toutes leurs données y sont accessibles.

Cependant, vous ne pouvez pas *mettre à jour* les propriétés des entités v2 via l’API v3 si elles ont été créées dans la v2.

Si vous devez mettre à jour, modifier ou transformer du contenu stocké sur des entités v2, mettez-les à jour via l’API v2 ou créer des entités d’API v3 pour les migrer.

## <a name="asset-identifier-differences"></a>Différences entre les identificateurs de ressource

Pour migrer, vous devez accéder aux propriétés ou aux clés de contenu à partir de vos ressources v2.  Il est important de comprendre que l’API v2 utilise l’`AssetId` comme clé d’identification principale, et que la nouvelle API v3 utilise le *nom de Gestion des ressources Azure* de l’entité comme identificateur principal.  (La propriété `Asset.Name` v2 n’est pas utilisée comme identificateur unique.) Avec l’API v3, votre nom de ressource v2 s’affiche désormais en tant que `Asset.Description`.

Par exemple, si vous aviez précédemment une ressource v2 avec l’ID `nb:cid:UUID:8cb39104-122c-496e-9ac5-7f9e2c2547b8`, l’identificateur se trouve maintenant à la fin du GUID `8cb39104-122c-496e-9ac5-7f9e2c2547b8`. Vous le verrez lorsque vous répertorierez vos ressources v2 par le biais de l’API v3.

Toutes les ressources créées et publiées à l’aide de l’API v2 auront une `ContentKeyPolicy` et une `ContentKey` dans l’API v3, au lieu d’utiliser une stratégie de clé de contenu par défaut sur la `StreamingPolicy`.

Pour plus d’informations, consultez la documentation sur la [stratégie de clé de contenu](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept) et la documentation sur la [stratégie de diffusion en continu](https://docs.microsoft.com/azure/media-services/latest/stream-streaming-policy-concept).

## <a name="use-azure-media-services-explorer-amse-v2-and-amse-v3-tools-side-by-side"></a>Utiliser les outils Azure Media Services Explorer (AMSE) v2 et AMSE v3 côte à côte

Utilisez l’[outil Azure Media Services Explorer v2](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) avec l’[outil Azure Media Services Explorer v3](https://github.com/Azure/Azure-Media-Services-Explorer) pour comparer des données côte à côte pour un élément créé et publié via les API v2. Les propriétés doivent toutes être visibles, mais à des emplacements différents.

## <a name="use-the-net-content-protection-migration-sample"></a>Utiliser l’exemple de migration de la protection du contenu .NET

Vous trouverez un exemple de code pour comparer les différences des identificateurs de ressource à l’aide de [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration) sous ContentProtection dans les exemples de code de Media Services.

## <a name="list-the-streaming-locators"></a>Répertorier les localisateurs de streaming

Vous pouvez interroger les `StreamingLocators` associés aux ressources créées dans l’API v2 à l’aide de la nouvelle méthode v3 [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) sur l’entité Ressource.  Référencez également la version de Kit de développement logiciel (SDK) de client .NET [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true)

Les résultats de la méthode `ListStreamingLocators` vous fourniront les valeurs `Name` et `StreamingLocatorId` du localisateur, ainsi que la valeur `StreamingPolicyName`.

## <a name="find-the-content-keys"></a>Rechercher les clés de contenu

Pour trouver les `ContentKeys` utilisées dans vos `StreamingLocators`, vous pouvez appeler la méthode [StreamingLocator.ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true).  

Pour plus d’informations sur la protection du contenu dans l’API v3, consultez l’article [Protéger votre contenu à l’aide du chiffrement dynamique de Media Services](https://docs.microsoft.com/azure/media-services/latest/drm-content-protection-concept).

## <a name="change-the-v2-contentkeypolicy-keeping-the-same-contentkey"></a>Modifier la ContentKeyPolicy v2 en conservant la même ContentKey

Vous devez d’abord annuler la publication (supprimer tous les localisateurs de streaming) sur la ressource via le kit de développement logiciel (SDK) v2. Voici comment faire :

1. Supprimez le localisateur.
1. Dissociez la `ContentKeyAuthorizationPolicy`.
1. Dissociez la `AssetDeliveryPolicy`.
1. Dissociez la `ContentKey`.
1. Supprimez la `ContentKey`.
1. Créez un nouveau `StreamingLocator` dans v3 à l’aide de `StreamingPolicy` et `ContentKeyPolicy` v3 en spécifiant l’identificateur de clé de contenu et la valeur de clé spécifiques nécessaires.

> [!NOTE]
> Il est possible de supprimer le localisateur v2 à l’aide de l’API v3, mais que cela n’a pas pour effet de supprimer la clé de contenu ou la stratégie de clé de contenu si elles ont été créées dans l’API v2.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Concepts, tutoriels et guides pratiques relatifs à la protection de contenu

### <a name="concepts"></a>Concepts

- [Protéger votre contenu à l’aide du chiffrement dynamique de Media Services](drm-content-protection-concept.md)
- [Concevoir un système de protection de contenu multi-DRM avec contrôle d’accès](architecture-design-multi-drm-system.md)
- [Azure Media Services V3 avec le modèle de licence PlayReady](drm-playready-license-template-concept.md)
- [Vue d’ensemble du modèle de licence Widevine avec Media Services v3](drm-widevine-license-template-concept.md)
- [Configuration et conditions de licence Apple FairPlay](drm-fairplay-license-overview.md)
- [Stratégies de diffusion en continu](stream-streaming-policy-concept.md)
- [Stratégies de clé de contenu](drm-content-key-policy-concept.md)

### <a name="tutorials"></a>Tutoriels

[Démarrage rapide : Utiliser le portail pour chiffrer du contenu](drm-encrypt-content-how-to.md)

### <a name="how-to-guides"></a>Guides pratiques

- [Obtenir une clé de signature à partir de la stratégie existante](drm-get-content-key-policy-dotnet-how-to.md)
- [FairPlay Streaming hors connexion pour iOS avec Media Services v3](drm-offline-fairplay-for-ios-concept.md)
- [Diffusion en continu Widevine hors connexion pour Android avec Media Services v3](drm-offline-widevine-for-android.md)
- [Diffusion en continu PlayReady hors connexion pour Windows 10 avec Media Services v3](drm-offline-playready-streaming-for-windows-10.md)

## <a name="samples"></a>Exemples

- [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration)
- Vous pouvez aussi [comparer les codes V2 et V3 dans les exemples de code](migrate-v-2-v-3-migration-samples.md).

## <a name="tools"></a>Outils

- [Outil Azure Media Services Explorer v3](https://github.com/Azure/Azure-Media-Services-Explorer)
- [Outil Azure Media Services Explorer v2](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
