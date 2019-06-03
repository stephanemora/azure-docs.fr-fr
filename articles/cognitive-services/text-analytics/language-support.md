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
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 69c14c6b98b572bc413f5a35696269e13344387e
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417314"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Langues et régions prises en charge par l’API Analyse de texte

Cet article explique quelles langues sont prises en charge pour chaque opération : analyse des sentiments, extraction de phrases clés, détection de la langue et reconnaissance d’entité nommée.

## <a name="language-detection"></a>Détection de la langue

L’API d’Analytique de texte peut détecter un large éventail de langages, les variantes, dialectes et certains langages culturelles/régionales.  La fonction de détection de la langue renvoie le « script » d’une langue. Par exemple, pour la phrase « I have a dog », elle renvoie `en` à la place de `en-US`. Le chinois est un cas spécial unique : la fonction de détection de la langue renvoie `zh_CHS` ou `zh_CHT` si elle parvient à déterminer le script dans le texte fourni. Dans les situations où un script spécifique n’est pas identifié dans un document chinois, elle renvoie simplement `zh`.

Nous ne publions pas la liste exacte des langues pour cette fonctionnalité, mais il peut détecter un large éventail de langages, les variantes, dialectes et certains langages culturelles/régionales. 

Si vous avez du contenu exprimée dans un langage moins fréquemment utilisé, vous pouvez essayer pour voir si elle retourne un code de détection de la langue. La réponse pour les langages qui ne peut pas être détecté est `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Analyse des sentiments, Extraction de phrases clés et reconnaissance d’entité nommée

Pour l’analyse des sentiments, l’extraction de phrases clés et la reconnaissance d’entité, la liste des langues prises en charge est plus sélective, car les analyseurs sont optimisés pour prendre en compte les règles linguistiques des langues supplémentaires.

## <a name="language-list-and-status"></a>Liste et état des langues

Les langues prises en charge sont initialement déployées en préversion avant d’évoluer vers l’état de disponibilité générale, indépendamment les unes des autres et du service Analyse de texte global. Il est possible que les langues restent disponibles en préversion, même si l’API Analyse de texte est rendue globalement disponible.

| Langue    | Code de langue | Sentiments | Expressions clés | Reconnaissance d’entité nommée |   Notes  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Arabe      | `ar`          |           |             | ✔ \*                     | |
| Tchèque       | `cs`          |           |             | ✔ \*                     | |
| Chinois simplifié | `zh-CN`|           |             | ✔ \*        |    |
| Danois      | `da`          | ✔ \*     | ✔           | ✔ \*            |     |
| Néerlandais       | `nl`          | ✔ \*     | ✔          |  ✔ \*           |     |
| Anglais     | `en`          | ✔        | ✔           |  ✔ \*\*     |      |
| Finnois     | `fi`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Anglais      | `fr`          | ✔        | ✔           |  ✔ \*           |     |
| Allemand      | `de`          | ✔ \*     | ✔           |  ✔ \*          |     |
| Grec       | `el`          | ✔ \*     |             |            |     |
| Hongrois   | `hu`          |           |             |  ✔ \*          |     | 
| Italien     | `it`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Japonais    | `ja`          |          | ✔           |  ✔ \*          |     |
| Coréen      | `ko`          |          | ✔           |  ✔ \*          |     |
| Norvégien (Bokmål) | `no`  | ✔ \*     |  ✔          | ✔ \*            |     |
| Polonais      | `pl`          | ✔ \*     |  ✔          |  ✔ \*           |     |
| Portugais (Portugal) | `pt-PT`| ✔        |  ✔          | ✔ \*      |`pt` également accepté|
| Portugais (Brésil)   | `pt-BR`|          |  ✔   |  ✔ \*       |     |
| Russe     | `ru`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Espagnol     | `es`          | ✔        |            |   ✔ \*\*      |     | 
| Suédois     | `sv`          | ✔ \*     | ✔           |   ✔ \*          |     |
| Turc     | `tr`          | ✔ \*     |             |   ✔ \*          |  |

\* Prise en charge linguistique est disponible en version préliminaire

\*\* Nommé reconnaissance d’entité et [Entity linking](how-tos/text-analytics-how-to-entity-linking.md) sont tous deux disponibles pour cette langue.    

## <a name="see-also"></a>Voir aussi

[Page Documentation Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)   
[Page de produits Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
