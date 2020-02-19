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
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188805"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Nouveautés de l’API Analyse de texte

L’API Analyse de texte est mise à jour de manière continue. Pour vous informer des développements récents, cet article vous fournit des informations sur les nouvelles versions et fonctionnalités.

## <a name="february-2020"></a>Février 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Prise en charge du Kit de développement logiciel (SDK) pour l’API Analyse de texte v3 (Préversion publique)

Dans le cadre de la [version unifiée du Kit de développement logiciel (SDK) Azure](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290), le Kit de développement logiciel (SDK) de l’API Analyse de texte v3 est désormais disponible en préversion publique pour les langages de programmation suivants :
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [En savoir plus sur le Kit de développement logiciel (SDK) de l’API Analyse de texte v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Préversion publique de la reconnaissance d’entité nommée v3

D’autres types d’entités sont désormais disponibles dans le service Reconnaissance d’entité nommée (NER) v3 en préversion publique, car nous développons la détection des entités d’informations générales et personnelles trouvées dans le texte. Cette mise à jour introduit le [modèle version](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`, qui inclut :

* Reconnaissance des types suivants d’entités générales (en anglais uniquement) :
    * PersonType
    * Produit
    * Événement
    * Geopolitical Entity (GPE) comme sous-type de Location
    * Compétence

* Reconnaissance des types suivants d’entités d’informations personnelles (en anglais uniquement) :
    * Person
    * Organization
    * Age comme sous-type de Quantity
    * Date comme sous-type de DateTime
    * Email 
    * Phone Number (États-Unis uniquement)
    * URL
    * Adresse IP

> [!div class="nextstepaction"]
> [En savoir plus sur la reconnaissance d’entité nommée v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>2 octobre 2019

#### <a name="named-entity-recognition-ner"></a>Reconnaissance d’entité nommée (NER)

* Un [nouveau point de terminaison](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) pour reconnaître les types d’entités d’informations personnelles (en anglais uniquement)

* Points de terminaison distincts pour la [reconnaissance d’entité](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) et la [liaison d’entité](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Modèle version](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`, qui inclut :
    * Détection et catégorisation élargies des entités trouvées dans le texte. 
    * Reconnaissance des nouveaux types d’entités suivants :
        * Numéro de téléphone
        * Adresse IP

La liaison d’entités prend en charge l’anglais et l’espagnol. Le support du langage NER varie selon le type d’entité.

#### <a name="sentiment-analysis-v3-public-preview"></a>Analyse des sentiments v3 - Préversion publique

* Un [nouveau point de terminaison](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) pour l’analyse des sentiments.
* [Modèle version](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`, qui inclut :

    * Améliorations significatives en matière d’exactitude et de détail du score et de la catégorisation du texte de l’API.
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
