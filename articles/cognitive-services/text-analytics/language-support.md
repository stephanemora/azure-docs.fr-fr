---
title: Prise en charge linguistique - API Analyse de texte
titleSuffix: Azure Cognitive Services
description: "Liste des langages naturels pris en charge par l’API Analyse de texte. Cet article explique quelles langues sont prises en charge pour chaque opération : analyse des sentiments, extraction de phrases clés, détection de langue et reconnaissance d'entité."
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: ec1858256c942fd50818a214de426fd00c3c2c88
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216929"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Langues et régions prises en charge par l’API Analyse de texte

Cet article explique quelles langues sont prises en charge pour chaque opération : analyse des sentiments, extraction de phrases clés et détection de langue.

## <a name="language-detection"></a>Détection de la langue

L’API Analyse de texte peut détecter jusqu'à 120 langues différentes. La fonction de détection de la langue renvoie le « script » d’une langue. Par exemple, pour la phrase « I have a dog », elle renvoie `en` à la place de `en-US`. Le chinois est un cas spécial unique : la fonction de détection de la langue renvoie `zh_CHS` ou `zh_CHT` si elle parvient à déterminer le script dans le texte fourni. Dans les situations où un script spécifique n’est pas identifié dans un document chinois, elle renvoie simplement `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-recognition"></a>Analyse des sentiments, extraction de phrases clés et reconnaissance d’entité

Pour l’analyse des sentiments, l’extraction de phrases clés et la reconnaissance d’entité, la liste des langues prises en charge est plus sélective, car les analyseurs sont optimisés pour prendre en compte les règles linguistiques des langues supplémentaires.

## <a name="language-list-and-status"></a>Liste et état des langues

Les langues prises en charge sont initialement déployées en préversion avant d’évoluer vers l’état de disponibilité générale, indépendamment les unes des autres et du service Analyse de texte global. Il est possible que les langues restent disponibles en préversion, même si l’API Analyse de texte est rendue globalement disponible.

| Langage    | Code de langue | Sentiments | Expressions clés | Reconnaissance d’entité |   Notes  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Danois      | `da`          | ✔ \*     | ✔           |             |     |
| Néerlandais       | `nl`          | ✔ \*     | ✔          |             |     |
| Anglais     | `en`          | ✔        | ✔           |  ✔ \*   |      |
| Finnois     | `fi`          | ✔ \*     | ✔           |             |     |
| Anglais      | `fr`          | ✔        | ✔           |             |     |
| Allemand      | `de`          | ✔ \*     | ✔           |            |     |
| Grec       | `el`          | ✔ \*     |             |            |     |
| Italien     | `it`          | ✔ \*     | ✔           |             |     |
| Japonais    | `ja`          |          | ✔           |            |     |
| Coréen      | `ko`          |          | ✔           |            |     |
| Norvégien (Bokmål) | `no`          | ✔ \*     |  ✔          |             |     |
| Polonais      | `pl`          | ✔ \*     |  ✔          |             |     |
| Portugais (Portugal) | `pt-PT`| ✔        |  ✔          |       |`pt` également accepté|
| Portugais (Brésil)   | `pt-BR`|          |  ✔   |         |     |
| Russe     | `ru`          | ✔ \*     | ✔           |             |     |
| Espagnol     | `es`          | ✔        | ✔           |   ✔ \*\*      |     |
| Suédois     | `sv`          | ✔ \*     | ✔           |             |     |
| Turc     | `tr`          | ✔ \*     |             |             |  |

\* indique la prise en charge des langues dans la préversion

\*\* Pour l’espagnol, l'extraction d’entité est uniquement disponible dans [(version 2.1 - préversion)](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634)

## <a name="see-also"></a>Voir aussi

[Page Documentation Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)   
[Page de produits Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
