---
title: Prise en charge des langues dans l’API Analyse de texte - Azure Cognitive Services | Microsoft Docs
description: Liste des langues prises en charge dans les versions mises à la disposition générale et dans les préversions pour les opérations de l’API Analyse de texte. S’applique à l’analyse des sentiments, à l’extraction de phrases clés et à la détection de langue.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: ashmaka
ms.openlocfilehash: 2d341cfaf261bea6367bb55dd5d322f419e22d34
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370377"
---
# <a name="supported-languages-in-the-text-analytics-api"></a>Langues prises en charge dans l’API Analyse de texte

Cet article explique quelles langues sont prises en charge pour chaque opération : analyse des sentiments, extraction de phrases clés et détection de langue.

## <a name="language-detection"></a>Détection de la langue

L’API Analyse de texte peut détecter jusqu'à 120 langues différentes. La fonction de détection de la langue renvoie le « script » d’une langue. Par exemple, pour la phrase « I have a dog », elle renvoie `en` à la place de `en-US`. Le chinois est un cas spécial unique : la fonction de détection de la langue renvoie `zh_CHS` ou `zh_CHT` si elle parvient à déterminer le script dans le texte fourni. Dans les situations où un script spécifique n’est pas identifié dans un document chinois, elle renvoie simplement `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-linking"></a>Analyse des sentiments, extraction de phrases clés et liaison d’entités

Pour l’analyse des sentiments, l’extraction de phrases clés et la liaison d’entités, la liste des langues prises en charge est plus sélective, car les analyseurs sont optimisés pour prendre en compte les règles linguistiques des langues supplémentaires.

## <a name="language-list-and-status"></a>Liste et état des langues

Les langues prises en charge sont initialement déployées en préversion avant d’évoluer vers l’état de disponibilité générale, indépendamment les unes des autres et du service Analyse de texte global. Il est possible que les langues restent disponibles en préversion, même si l’API Analyse de texte est rendue globalement disponible.

| Langage    | Code de langue | Sentiments | Expressions clés | Liaison d’entités |   Notes  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Danois      | `da`          | ✔ \*     | ✔           |             |     |
| Néerlandais       | `nl`          | ✔ \*     | ✔          |             |     |
| Français     | `en`          | ✔        | ✔           |  ✔ \*   |      |
| Finnois     | `fi`          | ✔ \*     | ✔           |             |     |
| Français      | `fr`          | ✔        | ✔           |             |     |
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
| Espagnol     | `es`          | ✔        | ✔           |     |     |
| Suédois     | `sv`          | ✔ \*     | ✔           |             |     |
| Turc     | `tr`          | ✔ \*     |             |             |     |

\* indique la prise en charge des langues dans la préversion

## <a name="see-also"></a>Voir aussi

[Page Documentation Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)   
[Page de produits Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
