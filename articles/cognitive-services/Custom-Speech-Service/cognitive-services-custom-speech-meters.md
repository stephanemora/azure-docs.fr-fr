---
title: Compteurs et quotas de service - Custom Speech
titlesuffix: Azure Cognitive Services
description: Informations à propos des compteurs et des quotas de Custom Speech Service sur Azure.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 07/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 68fba1d6038a044336ba6aecd3ff163d29131b1d
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224696"
---
# <a name="custom-speech-service-meters-and-quotas"></a>Compteurs et quotas Custom Speech Service

Avec le service cloud Custom Speech Service, vous pouvez personnaliser des modèles vocaux pour la transcription de la reconnaissance vocale en texte.

Pour faire vos premiers pas avec Custom Speech Service, accédez au [portail de Custom Speech Service](https://cris.ai).

Pour connaître la tarification en vigueur, accédez à la page [Tarification de Cognitive Services - Service vocal personnalisé](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/).

## <a name="tiers-explained"></a>Niveaux expliqués
Pour des tests et des prototypes uniquement, nous vous proposons d’utiliser le niveau gratuit (F0). Pour les systèmes de production, nous vous proposons d’utiliser le niveau S2. Grâce au niveau S2, vous pouvez faire évoluer votre déploiement afin d’obtenir le nombre d’unités d’échelle dont vous avez besoin.

> [!NOTE]
> La migration entre les niveaux F0 et S2 *est impossible*.
>

## <a name="meters-explained"></a>Compteurs expliqués

### <a name="scale-out"></a>Scale Out
Scale Out est une nouvelle fonctionnalité publiée en même temps que le nouveau modèle de tarification. À l’aide de Scale Out, vous pouvez contrôler le nombre de requêtes simultanées traitées par votre modèle.

Vous pouvez définir des requêtes simultanées à l’aide de la mesure SU dans la vue **Créer un modèle de déploiement**. Pour plus d’informations, consultez la page [Créer un point de terminaison de reconnaissance vocale personnalisé](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md). Selon votre estimation de la quantité de trafic consommé par le modèle, vous pouvez choisir le nombre approprié de SU. 

> [!NOTE]
> Chaque unité d’échelle garantit 5 requêtes simultanées. Vous pouvez acheter 1 SU ou plus, selon vos besoins. Comme le nombre de SU augmente par incréments de 1, le nombre de requêtes simultanées garanties augmente par incréments de 5.
>

### <a name="log-management"></a>Gestion du journal
Moyennant surcoût, vous pouvez choisir de désactiver les traces audio pour un modèle qui vient d’être déployé. Custom Speech Service n’enregistre pas les requêtes audio ou les transcriptions depuis ce modèle.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’utilisation de Custom Speech Service, accédez au [portail de Custom Speech Service](https://cris.ai).

* [Démarrer](cognitive-services-custom-speech-get-started.md)
* [FORUM AUX QUESTIONS](cognitive-services-custom-speech-faq.md)
* [Glossaire](cognitive-services-custom-speech-glossary.md)
 