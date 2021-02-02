---
title: Recommandations en matière de migration basées sur un scénario de protection de contenu | Microsoft Docs
description: Cet article fournit des recommandations basées sur un scénario de protection de contenu, qui vous aideront à opérer une migration de la version v2 à la version v3 d’Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: a546120a93f311be29083d5f23d4716316bf64f4
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689431"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Recommandations en matière de migration basées sur un scénario de protection de contenu

![logo du guide de migration](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![étapes de migration 2](./media/migration-guide/steps-4.svg)

Cet article fournit des recommandations basées sur un scénario de protection de contenu, qui vous aideront à opérer une migration de la version v2 à la version v3 d’Azure Media Services.

## <a name="protect-content-in-v3-api"></a>Protection de contenu dans l’API v3

Utilisez la prise en charge des fonctionnalités [à plusieurs clés](design-multi-drm-system-with-access-control.md) dans la nouvelle API V3.

Pour connaître les étapes spécifiques, consultez les concepts, tutoriels et guides pratiques relatifs à la protection de contenu.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Concepts, tutoriels et guides pratiques relatifs à la protection de contenu

### <a name="concepts"></a>Concepts

- [Protéger votre contenu à l’aide du chiffrement dynamique de Media Services](content-protection-overview.md)
- [Concevoir un système de protection de contenu multi-DRM avec contrôle d’accès](design-multi-drm-system-with-access-control.md)
- [Azure Media Services V3 avec le modèle de licence PlayReady](playready-license-template-overview.md)
- [Vue d’ensemble du modèle de licence Widevine avec Media Services v3](widevine-license-template-overview.md)
- [Configuration et conditions de licence Apple FairPlay](fairplay-license-overview.md)
- [Stratégies de diffusion en continu](streaming-policy-concept.md)
- [Stratégies de clé de contenu](content-key-policy-concept.md)

### <a name="tutorials"></a>Tutoriels

[Démarrage rapide : Utiliser le portail pour chiffrer du contenu](encrypt-content-quickstart.md)

### <a name="how-to-guides"></a>Guides pratiques

- [Obtenir une clé de signature à partir de la stratégie existante](get-content-key-policy-dotnet-howto.md)
- [FairPlay Streaming hors connexion pour iOS avec Media Services v3](offline-fairplay-for-ios.md)
- [Diffusion en continu Widevine hors connexion pour Android avec Media Services v3](offline-widevine-for-android.md)
- [Diffusion en continu PlayReady hors connexion pour Windows 10 avec Media Services v3](offline-plaready-streaming-for-windows-10.md)

## <a name="samples"></a>Exemples

Vous pouvez aussi [comparer les codes V2 et V3 dans les exemples de code](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]