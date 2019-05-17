---
title: Limites de requête - API de traduction de texte Translator Text
titleSuffix: Azure Cognitive Services
description: Cet article liste les limites de requête pour l’API de traduction de texte Translator Text. Des frais sont facturés sur la base du nombre de caractères, et non pas de la fréquence des requêtes, avec une limite de 5 000 caractères par requêtes. Les limites de caractères sont fonction de l’abonnement, les abonnements F0 étant limités à 2 millions de caractères par heure.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 91cc002f373318e5124fc21f76edbfd000d17238
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796905"
---
# <a name="request-limits-for-translator-text"></a>Limites de requête pour la traduction de texte Translator Text

Cet article fournit des seuils de limitation de requêtes pour l’API de traduction de texte Translator Text. Les services incluent la traduction, la translittération, la détection de longueur de phrase, la détection de la langue et les traductions alternatives.

## <a name="character-and-array-limits-per-request"></a>Limites de caractère et de tableau par demande

Chaque requête Translate est limitée à 5 000 caractères. Vous êtes facturé au caractère, et non pas au nombre de requêtes. Il est recommandé d’envoyer des requêtes plus courts.

Les table listes tableau élément et caractère limites suivantes pour chaque opération de l’API Translator Text.

| Opération | Taille maximale de l’élément de tableau |   Nombre maximal d’éléments de tableau |  Taille de demande maximale (caractères) |
|:----|:----|:----|:----|
| Déplacer | 5 000 | 100   | 5 000 |
| Transliterate | 5 000 | 10    | 5 000 |
| Détecter | 10 000 | 100 |   50 000 |
| BreakSentence | 10 000    | 100 | 5,0000 |
| Recherche dans le dictionnaire| 100 |  10  | 1 000 |
| Exemples de dictionnaire | 100 pour le texte et 100 pour la traduction (total de 200)| 10|   2 000 |

## <a name="character-limits-per-hour"></a>Limites de caractères par heure

Votre limite de caractère par heure est basée sur votre niveau d’abonnement Translator Text. Le quota de toutes les heures doit être utilisé uniformément tout au long de l’heure. Si vous atteindrez ou dépasser ces limites ou envoyez trop volumineux d’une partie du quota sur une courte période de temps, vous recevrez probablement une réponse de quota à l’emploi. 

| Niveau | Limite de caractères |
|------|-----------------|
| F0 | 2 millions de caractères par heure |
| S1 | 40 millions de caractères par heure |
| S2 / C2 | 40 millions de caractères par heure |
| S3 / C3 | 120 millions de caractères par heure |
| S4 / C4 | 200 millions de caractères par heure |

Limite pour [abonnements multiservice](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) sont les mêmes que le niveau S1.

Ces limites sont limités aux modèles de traduction standard de Microsoft. Les modèles de traduction personnalisée qui utilisent personnalisé Translator sont limités à 1 800 caractères par seconde.

## <a name="latency"></a>Latence

L’API Translator Text a une latence maximale de 15 secondes à l’aide de modèles standards. Traduction à l’aide de modèles personnalisés a une latence maximale de 25 secondes. Dans ce délai, vous recevez un résultat ou une réponse de délai dépassé. En général, les réponses sont retournées dans un délai de 150 à 300 millisecondes. Temps de réponse peut varier en fonction de la taille de la paire de demande et la langue. Si vous ne recevez pas une traduction ou une [réponse d’erreur](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) pendant ce laps de temps, vous devez vérifier votre connexion réseau et réessayez.

## <a name="sentence-length-limits"></a>Limites de longueur de phrase

Quand vous utilisez la fonction [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence), la longueur des phrases est limitée à 275 caractères. Il existe des exceptions pour les langues suivantes :

| Langue | Code | Limite de caractères |
|----------|------|-----------------|
| Chinois | zh | 132 |
| Allemand | de | 290 |
| Italien | it | 280 |
| Japonais | ja | 150 |
| Portugais | pt | 290 |
| Espagnol | es | 280 |
| Italien | it | 280 |
| Thaï | e | 258 |

> [!NOTE]
> Cette limite ne s’applique pas aux traductions.

## <a name="next-steps"></a>Étapes suivantes

* [Tarification](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Disponibilité régionale](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Référence de l’API de traduction de texte Translator Text v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
