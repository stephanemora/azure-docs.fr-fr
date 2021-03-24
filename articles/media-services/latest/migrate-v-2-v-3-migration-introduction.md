---
title: Présentation de la migration de Media Services v2 vers v3
description: Cet article est une introduction à la migration de Media Services v2 vers v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 3514a7c809e939ea2c45afa5ab60539232b8781f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98953289"
---
# <a name="migrate-from-media-services-v2-to-v3-introduction"></a>Présentation de la migration de Media Services v2 vers v3

![logo du guide de migration](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

Le guide de migration de Media Services vous aide à passer des API V2 de Media Services aux API V3 en effectuant une migration qui tire parti des nouvelles fonctionnalités et fonctions désormais disponibles. Nous vous conseillons de bien réfléchir avant de déterminer ce qui convient le mieux à votre scénario.

## <a name="how-to-use-this-guide"></a>Comment utiliser ce guide

### <a name="navigating"></a>Navigation

Tout au long du guide, vous verrez le visuel ci-dessous.

![étapes de migration](./media/migration-guide/steps.svg)<br/>

Votre étape en cours est indiquée par un changement de couleur de son numéro, comme ceci :

![étapes de migration 2](./media/migration-guide/steps-2.svg)<br/>

À la fin de chaque page, vous trouverez des liens vers les autres documents de migration que vous pouvez lire sous le titre **Étapes suivantes**.

### <a name="guidance"></a>Assistance

L’aide fournie ici est *générale*. Elle inclut du contenu visant à améliorer votre connaissance de ce qui est maintenant disponible dans V3, ainsi que de ce qui a changé dans les workflows Media Services.

Pour obtenir de l’aide moins générale, notamment des captures d’écran et des graphiques conceptuels, des liens vers des concepts, des tutoriels, des guides de  démarrage rapide, des exemples et des informations de référence sur les API sont donnés dans chaque rubrique basée sur un scénario. Nous avons également listé des exemples pour vous aider à comparer l’API V2 à l’API V3.

## <a name="migration-guidance-overview"></a>Vue d’ensemble de l’aide à la migration

Vous avez quatre étapes générales à suivre pendant votre migration :

## <a name="step-1-benefits"></a>Étape 1 : Avantages

<hr color="#5ea0ef" size="10">

[Comprendre les avantages](migrate-v-2-v-3-migration-benefits.md) de la migration vers l’API V3 de Media Services.

## <a name="step-2-differences"></a>Étape 2 : Différences

<hr color="#5ea0ef" size="10">

Comprendre les différences entre l’API V2 de Media Services et l’API V3.

- [Accès d’API](migrate-v-2-v-3-differences-api-access.md)
- [Absences de fonctionnalités](migrate-v-2-v-3-differences-feature-gaps.md)
- [Changements terminologiques et d’entités](migrate-v-2-v-3-differences-terminology.md)

## <a name="step-3-sdk-setup"></a>Étape 3 : Configuration du SDK

<hr color="#5ea0ef" size="10">

Comprendre les différences du SDK et [préparer la migration vers l’API REST V3 ou le SDK client](migrate-v-2-v-3-migration-setup.md).

## <a name="step-4-scenario-based-guidance"></a>Étape 4 : Aide selon le scénario

<hr color="#5ea0ef" size="10">

Votre application de Media Services V2 est peut-être unique. Par conséquent, nous proposons une aide selon le scénario, laquelle s’appuie sur la manière dont vous *avez éventuellement* utilisé Media Services par le passé et sur les étapes de chaque fonctionnalité du service, comme :

- [Encodage](migrate-v-2-v-3-migration-scenario-based-encoding.md)
- [Vidéo en flux continu](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)
- [Empaquetage et distribution](migrate-v-2-v-3-migration-scenario-based-publishing.md)
- [Protection du contenu](migrate-v-2-v-3-migration-scenario-based-content-protection.md)
- [Unités réservées multimédias (MRU)](migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md)

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
