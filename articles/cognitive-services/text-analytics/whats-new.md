---
title: Nouveautés de l’API Analyse de texte
titleSuffix: Text Analytics - Azure Cognitive Services
description: Cet article fournit des informations sur les nouvelles versions et fonctionnalités de l’API Analyse de texte Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 6fa7d6a93a56cc531df238a8580207ef7a89d5d0
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732618"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Nouveautés de l’API Analyse de texte

L’API Analyse de texte est mise à jour de manière continue. Pour vous informer des développements récents, cet article vous fournit des informations sur les nouvelles versions et fonctionnalités.

## <a name="named-entity-recognition-v3-public-preview---october-2019"></a>Préversion publique de la reconnaissance d’entité nommée v3 - octobre 2019

La version suivante de la reconnaissance d’entités nommées (NER) est désormais disponible pour la préversion publique et fournit une détection et une catégorisation étendues des entités trouvées dans le texte. Elle fournit :

* Reconnaissance des nouveaux types d’entités suivants :
    * Numéro de téléphone
    * Adresse IP

* Un [nouveau point de terminaison](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) pour reconnaître les types d’entités d’informations personnelles (en anglais uniquement)
* Points de terminaison distincts pour la [reconnaissance d’entité](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) et la [liaison d’entité](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

La liaison d’entités prend en charge l’anglais et l’espagnol. Le support du langage NER varie selon le type d’entité. 

> [!div class="nextstepaction"]
> [En savoir plus sur la reconnaissance d’entité nommée v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

## <a name="sentiment-analysis-v3-public-preview---october-2019"></a>Préversion publique de l’analyse des sentiments v3 - octobre 2019

La [version suivante d’Analyse des sentiments](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) est maintenant disponible en préversion publique et fournit des améliorations significatives dans la précision et les détails de catégorisation et de scoring de texte de l’API. Elle fournit également les éléments suivants :

* Étiquetage automatique pour différents sentiments dans le texte.
* Analyse des sentiments et sortie au niveau du document et de la phrase. 

Elle prend en charge l’anglais (`en`), le japonnais (`ja`), le chinois simplifié (`zh-Hans`),  le chinois traditionnel (`zh-Hant`), le français (`fr`), l’italien (`it`), l’espagnol (`es`), le néerlandais (`nl`), la portugais (`pt`) et l’allemand (`de`) et est disponible dans les régions suivantes : `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe` et `West US 2`. 

> [!div class="nextstepaction"]
> [En savoir plus sur Analyse des sentiments v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Étapes suivantes

* [Qu’est-ce que l’API Analyse de texte ?](overview.md)  
* [Exemples de scénarios utilisateur](text-analytics-user-scenarios.md)
* [Analyse des sentiments](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Détection de la langue](how-tos/text-analytics-how-to-language-detection.md)
* [Reconnaissance d’entités](how-tos/text-analytics-how-to-entity-linking.md)
* [Extraction de phrases clés](how-tos/text-analytics-how-to-keyword-extraction.md)
