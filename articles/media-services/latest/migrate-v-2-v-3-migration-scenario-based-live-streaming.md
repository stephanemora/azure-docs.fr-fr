---
title: Aide sur la migration avec streaming en direct de Media Services
description: Cet article vous donne des conseils en fonction de votre scénario de streaming en direct, pour vous aider à opérer une migration de la version v2 à la version v3 d’Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: d021267c38f0043d2361b1a6392fbacd0634a164
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279695"
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

- Créez un [événement en direct](live-event-outputs-concept.md#live-events) v3 à encoder. Vous pouvez activer les [présélections d’encodage 1080P et 720P](live-event-types-comparison-reference.md#system-presets).
- Utilisez l’entité [Sortie en direct](live-event-outputs-concept.md#live-outputs) plutôt que Programmes.
- Créez des [localisateurs de streaming](stream-streaming-locators-concept.md).
- Tenez compte de vos besoins en streaming en direct [HLS et DASH](encode-dynamic-packaging-concept.md).
- Si vous avez besoin d’un démarrage rapide des événements en direct, explorez les nouvelles fonctionnalités du [mode veille](live-event-outputs-concept.md#standby-mode).
- Si vous voulez transcrire votre événement en direct pendant qu’il se produit, explorez la nouvelle fonctionnalité de [transcription en direct](live-event-live-transcription-how-to.md).
- Créez des événements en direct qui ont lieu 24 h/24, 7 j/7 et 365 jours par an dans v3 si vous avez besoin d’une durée de streaming plus longue.
- Utilisez [Event Grid](monitoring/monitor-events-portal-how-to.md) pour superviser vos événements en direct.

Pour connaître les étapes spécifiques, consultez les concepts, les tutoriels et les guides pratiques relatifs aux événements en direct ci-dessous.

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>Concepts, tutoriels et guides pratiques des événements en direct

### <a name="concepts"></a>Concepts

- [Streaming en direct avec Azure Media Services v3](stream-live-streaming-concept.md)
- [Événements en direct et sorties en direct dans Media Services](live-event-outputs-concept.md)
- [Encodeurs de streaming en direct locaux vérifiés](encode-recommended-on-premises-live-encoders.md)
- [Utiliser le décalage temporel et des sorties en direct pour créer une lecture vidéo à la demande](live-event-cloud-dvr-time-how-to.md)
- [live-event-live-transcription-how-to (préversion)](live-event-live-transcription-how-to.md)
- [Comparaison des types d’événements en direct](live-event-types-comparison-reference.md)
- [États et facturation des événements en direct](live-event-states-billing-concept.md)
- [Paramètres de latence faible d’événement en direct](live-event-latency-reference.md)
- [Codes d'erreur des événements en direct Media Services](live-event-error-codes-reference.md)

### <a name="tutorials-and-quickstarts"></a>Tutoriels et guides de démarrage rapide

- [Tutoriel : Diffuser en direct avec Media Services](stream-live-tutorial-with-api.md)
- [Créer un stream en direct Azure Media Services avec OBS](live-event-obs-quickstart.md)
- [Démarrage rapide : Charger, encoder et diffuser en streaming du contenu via le portail](asset-create-asset-upload-portal-quickstart.md)
- [Démarrage rapide : Créer un stream en direct Azure Media Services avec Wirecast](live-event-wirecast-quickstart.md)

## <a name="samples"></a>Exemples

Vous pouvez aussi [comparer les codes V2 et V3 dans les exemples de code](migrate-v-2-v-3-migration-samples.md).
