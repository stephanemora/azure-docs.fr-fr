---
title: Nouveautés de l’API Analyse de texte
titlesuffix: Text Analytics - Azure Cognitive Services
description: En savoir plus sur les nouveaux développements avec le service Analyse de texte
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 44ef6fb118be4d1110a693faded6c57bc8b4e2fd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499956"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Nouveautés de l’API Analyse de texte

L’API Analyse de texte est mise à jour de manière continue. Pour vous informer des développements récents, cet article vous fournit des informations sur les nouvelles versions et fonctionnalités.

## <a name="named-entity-recognition-v3-public-preview---october-2019"></a>Préversion publique de la reconnaissance d’entité nommée v3 - octobre 2019

La version suivante de la reconnaissance d’entités nommées (NER) est désormais disponible pour la préversion publique et fournit une détection et une catégorisation étendues des entités trouvées dans le texte. Elle fournit :

* Reconnaissance des nouveaux types d’entités suivants :
    * Numéro de téléphone
    * Adresse IP

* Un nouveau point de terminaison pour reconnaître les types d’entités d’informations personnelles (en anglais uniquement)
* Points de terminaison distincts pour la reconnaissance d’entité et la liaison d’entité.

La liaison d’entités prend en charge l’anglais et l’espagnol. Le support du langage NER varie selon le type d’entité. Pour plus d’informations, cliquez sur le lien ci-dessous. 

> [!div class="nextstepaction"]
> [En savoir plus sur la reconnaissance d’entité nommée v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-v3-public-preview)

## <a name="sentiment-analysis-v3-public-preview---october-2019"></a>Préversion publique de l’analyse des sentiments v3 - octobre 2019

La version suivante d’Analyse des sentiments est maintenant disponible en préversion publique, et fournit des améliorations significatives dans la précision et les détails de catégorisation et de scoring de texte de l’API. Elle fournit également les éléments suivants :

* Étiquetage automatique pour différents sentiments dans le texte.
* Analyse des sentiments et sortie au niveau du document et de la phrase. 

Elle prend en charge l’anglais (`en`), le japonnais (`ja`), le chinois simplifié (`zh-Hans`),  le chinois traditionnel (`zh-Hant`), le français (`fr`), l’italien (`it`), l’espagnol (`es`), le néerlandais (`nl`), la portugais (`pt`) et l’allemand (`de`) et est disponible dans les régions suivantes : `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe` et `West US 2`.

> [!div class="nextstepaction"]
> [En savoir plus sur Analyse des sentiments v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-v3-public-preview)

## <a name="next-steps"></a>Étapes suivantes

* [Qu’est-ce que l’API Analyse de texte ?](overview.md)  
* [Exemples de scénarios utilisateur](text-analytics-user-scenarios.md)
* [Analyse des sentiments](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Détection de la langue](how-tos/text-analytics-how-to-language-detection.md)
* [Reconnaissance d’entités](how-tos/text-analytics-how-to-entity-linking.md)
* [Extraction de phrases clés](how-tos/text-analytics-how-to-keyword-extraction.md)
