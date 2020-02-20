---
title: Prise en charge linguistique - API Analyse de texte
titleSuffix: Azure Cognitive Services
description: "Liste des langages naturels pris en charge par l’API Analyse de texte. Cet article explique quelles langues sont prises en charge pour chaque opération : analyse des sentiments, extraction de phrases clés, détection de langue et reconnaissance d'entité."
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: c5a413a4fe8d9ac9b7aac59ca78cedc6d5a7a313
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77206172"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Langues et régions prises en charge par l’API Analyse de texte

Cet article explique quelles langues sont prises en charge pour chaque opération : analyse des sentiments, extraction de phrases clés, détection de langue et reconnaissance d’entité nommée.

## <a name="language-detection"></a>Détection de la langue

L’API Analyse de texte peut détecter une grande variété de langues, variantes, dialectes et certaines langues régionales/culturelles.  La fonction de détection de la langue renvoie le « script » d’une langue. Par exemple, pour la phrase « I have a dog », elle renvoie `en` à la place de `en-US`. Le chinois est un cas spécial unique : la fonction de détection de la langue renvoie `zh_CHS` ou `zh_CHT` si elle parvient à déterminer le script dans le texte fourni. Dans les situations où un script spécifique n’est pas identifié dans un document chinois, elle renvoie simplement `zh`.

Nous ne publions pas la liste exacte des langues pour cette fonctionnalité, mais elle peut détecter une grande variété de langues, de variantes, de dialectes, et de certaines langues régionales/de culture. 

Si vous avez du contenu exprimé dans une langue moins fréquemment utilisée, vous pouvez essayer Détection de langue pour voir si elle retourne un code. La réponse pour les langues qui ne peuvent pas être détectées est `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Analyse des sentiments, extraction de phrases clés et reconnaissance d’entité nommée

Pour l’analyse des sentiments, l’extraction de phrases clés et la reconnaissance d’entité, la liste des langues prises en charge est plus sélective, car les analyseurs sont optimisés pour prendre en compte les règles linguistiques des langues supplémentaires. Dans la reconnaissance d’entité nommée v2, la prise en charge de l’ensemble complet de [types d’entité](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) est actuellement limitée aux langues suivantes : 
* Anglais
* Chinois simplifié
* Français
* Allemand
* Espagnol

Seules les entités nommées `Person`, `Location` et `Organization` sont retournées pour les autres langues.

## <a name="language-list-and-status"></a>Liste et état des langues

Les langues prises en charge sont initialement déployées en préversion avant d’évoluer vers l’état de disponibilité générale, indépendamment les unes des autres et du service Analyse de texte global. Il est possible que les langues restent disponibles en préversion, même si l’API Analyse de texte est rendue globalement disponible.

> [!NOTE]
> Pour connaître précisément les langues prises en charge pour la préversion publique v3 de la reconnaissance d’entité nommée, consultez [Types d’entités nommées](named-entity-types.md).

| Langage              | Code langue | Sentiments | Expressions clés | Reconnaissance d’entité nommée | Liaison d’entités |       Notes        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| Arabe                |     `ar`      |           |             |           ✔ \*           |                |                    |
| Tchèque                 |     `cs`      |           |             |           ✔ \*           |                |                    |
| Chinois simplifié    |   `zh-hans`   |  ✔ \*\*   |             |            ✔             |                | `zh` également accepté                   |
| Chinois traditionnel   |   `zh-hant`   |  ✔ \*\*   |             |                          |                |                    |
| Danois                |     `da`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Néerlandais                 |     `nl`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Anglais               |     `en`      |   ✔ \**   |      ✔      |          ✔ \*\*          |     ✔ \**      |                    |
| Finnois               |     `fi`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Français                |     `fr`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| Allemand                |     `de`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| Grec                 |     `el`      |   ✔ \*    |             |                          |                |                    |
| Hongrois             |     `hu`      |           |             |           ✔ \*           |                |                    |
| Italien               |     `it`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Japonais              |     `ja`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Coréen                |     `ko`      |   ✔ \*\*  |      ✔      |           ✔ \*           |                |                    |
| Norvégien (Bokmål)   |     `no`      |   ✔ \*    |      ✔      |           ✔ \*           |                | `nb` également accepté                   |
| Polonais                |     `pl`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Portugais (Portugal) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔ \*           |                | `pt` également accepté |
| Portugais (Brésil)   |    `pt-BR`    |           |      ✔      |           ✔ \*           |                |                    |
| Russe               |     `ru`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Espagnol               |     `es`      |   ✔\**    |      ✔      |           ✔ \*           |     ✔ \**      |                    |
| Suédois               |     `sv`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Turc               |     `tr`      |   ✔ \*    |             |           ✔ \*           |                |                    |

\* Prise en charge linguistique disponible en préversion

\** Également disponible dans les préversions publiques [Analyse des sentiments v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-versions-and-features) et/ou [Reconnaissance d’entité nommée v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).

## <a name="see-also"></a>Voir aussi

[Page Documentation Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)   
[Page de produits Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
