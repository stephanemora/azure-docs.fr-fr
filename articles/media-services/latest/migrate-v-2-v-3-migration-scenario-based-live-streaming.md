---
title: Aide selon le scénario de migration de Media Services v2 vers v3 pour le streaming en direct | Microsoft Docs
description: Cet article vous donne des conseils en fonction de votre scénario de streaming en direct, pour vous aider à opérer une migration de la version v2 à la version v3 d’Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 89fcf85b20d11664d5d1caa3fbe142fa5bbdbebc
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689429"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>Aide à la migration selon le scénario de streaming en direct

![logo du guide de migration](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![étapes de migration 2](./media/migration-guide/steps-4.svg)

Le portail Azure prend à présent en charge la configuration et la gestion d’événements en direct.  Nous vous encourageons à en faire l’essai pendant votre migration de v2 vers v3.

## <a name="test-the-v3-live-event-workflow"></a>Tester le workflow des événements en direct v3

> [!NOTE]
> Les canaux et programmes créés avec v2 (mappés à des événements et sorties en direct dans v3) peuvent être gérés avec l’API V3. Il est recommandé de basculer vers des événements et sorties en direct v3 au moment opportun où vous pouvez arrêter votre canal v2 existant. Il n’existe actuellement aucune prise en charge pour une migration sans interruption d’un canal live 24 h/24, 7 j/7 en cours d’exécution continue vers les nouveaux événements en direct v3. Ainsi, le temps d’arrêt pour maintenance doit être coordonné au mieux pour votre public et votre activité.

Testez la nouvelle façon de diffuser des événements en direct avec Media Services avant de déplacer votre contenu de v2 vers v3. Voici les fonctionnalités v3 à utiliser et à prendre en compte pour la migration.

- Créez un [événement en direct](live-events-outputs-concept.md#live-events) v3 à encoder. Vous pouvez activer les [présélections d’encodage 1080P et 720P](live-event-types-comparison.md#system-presets).
- Utilisez l’entité [Sortie en direct](live-events-outputs-concept.md#live-outputs) plutôt que Programmes.
- Créez des [localisateurs de streaming](streaming-locators-concept.md).
- Tenez compte de vos besoins en streaming en direct [HLS et DASH](dynamic-packaging-overview.md).
- Si vous avez besoin d’un démarrage rapide des événements en direct, explorez les nouvelles fonctionnalités du [mode veille](live-events-outputs-concept.md#standby-mode).
- Si vous voulez transcrire votre événement en direct pendant qu’il se produit, explorez la nouvelle fonctionnalité de [transcription en direct](live-transcription.md).
- Créez des événements en direct qui ont lieu 24 h/24, 7 j/7 et 365 jours par an dans v3 si vous avez besoin d’une durée de streaming plus longue.
- Utilisez [Event Grid](monitor-events-portal-how-to.md) pour superviser vos événements en direct.

Pour connaître les étapes spécifiques, consultez les concepts, les tutoriels et les guides pratiques relatifs aux événements en direct ci-dessous.

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>Concepts, tutoriels et guides pratiques des événements en direct

### <a name="concepts"></a>Concepts

- [Streaming en direct avec Azure Media Services v3](live-streaming-overview.md)
- [Événements en direct et sorties en direct dans Media Services](live-events-outputs-concept.md)
- [Encodeurs de streaming en direct locaux vérifiés](recommended-on-premises-live-encoders.md)
- [Utiliser le décalage temporel et des sorties en direct pour créer une lecture vidéo à la demande](live-event-cloud-dvr.md)
- [Transcription en direct (préversion)](live-transcription.md)
- [Comparaison des types d’événements en direct](live-event-types-comparison.md)
- [États et facturation des événements en direct](live-event-states-billing.md)
- [Paramètres de latence faible d’événement en direct](live-event-latency.md)
- [Codes d'erreur des événements en direct Media Services](live-event-error-codes.md)

### <a name="tutorials-and-quickstarts"></a>Tutoriels et guides de démarrage rapide

- [Tutoriel : Diffuser en direct avec Media Services](stream-live-tutorial-with-api.md)
- [Créer un stream en direct Azure Media Services avec OBS](live-events-obs-quickstart.md)
- [Démarrage rapide : Charger, encoder et diffuser en streaming du contenu via le portail](manage-assets-quickstart.md)
- [Démarrage rapide : Créer un stream en direct Azure Media Services avec Wirecast](live-events-wirecast-quickstart.md)

## <a name="samples"></a>Exemples

Vous pouvez aussi [comparer le code v2 et v3 dans les exemples de code](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
