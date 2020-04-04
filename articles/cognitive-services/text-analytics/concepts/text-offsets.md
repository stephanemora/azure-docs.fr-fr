---
title: Décalages de texte dans l’API Analyse de texte
titleSuffix: Azure Cognitive Services
description: Apprenez-en davantage sur les décalages provoqués par des encodages multilingues et d’emojis.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: 6e404c710a244f06676edf50c3f5c95a7d681e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218527"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Décalages de texte dans la sortie de l’API Analyse de texte

La prise en charge multilingue et d’emojis a conduit à des encodages Unicode qui utilisent plusieurs [points de code](https://wikipedia.org/wiki/Code_point) pour représenter un seul caractère affiché, appelé graphème. Par exemple, des emojis tels que 🌷 et 👍 peuvent utiliser plusieurs caractères pour composer la forme, avec des caractères supplémentaires pour des attributs visuels tels que le teint de la peau. De même, le mot Hindi `अनुच्छेद` est encodé sous la forme de cinq lettres et de trois marques qui se combinent.

En raison des différentes longueurs des codages multilingues et d’emojis possibles, l’API Analyse de texte peut retourner des décalages dans la réponse.

## <a name="offsets-in-the-api-response"></a>Décalages dans la réponse de l’API. 

Chaque fois que des décalages sont renvoyés dans la réponse de l’API, par exemple [Reconnaissance d’entité nommée](../how-tos/text-analytics-how-to-entity-linking.md) ou [Analyse des sentiments](../how-tos/text-analytics-how-to-sentiment-analysis.md), gardez à l’esprit ce qui suit :

* Les éléments de la réponse peuvent être spécifiques du point de terminaison appelé. 
* Les charges utiles HTTP POST/GET sont encodées au format [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), qui peut être ou non l’encodage de caractères par défaut sur votre compilateur ou système d’exploitation côté client.
* Les décalages font référence aux nombres de graphèmes selon la norme [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0), non aux nombres de caractères.

## <a name="extracting-substrings-from-text-with-offsets"></a>Extraction de sous-chaînes d’un texte avec des décalages

Les décalages peuvent occasionner des problèmes lors de l’utilisation de méthodes de sous-chaîne basées sur des caractères, par exemple, la méthode .NET [substring()](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8). Un problème est qu’un décalage peut avoir pour effet qu’une méthode de sous-chaîne se termine au milieu d’un encodage de graphème de plusieurs caractères plutôt qu’à la fin.

Dans .NET, envisagez d’utiliser la classe [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8), qui vous permet d’utiliser une chaîne comme une série d’éléments textuels, plutôt que d’objets caractères individuels. Vous pouvez également rechercher des bibliothèques de fractionnements de graphèmes dans votre environnement logiciel de prédilection. 

L’API Analyse de texte retourne également ces éléments textuels pour des raisons de commodité.

## <a name="see-also"></a>Voir aussi

* [Vue d’ensemble d’Analyse de texte](../overview.md)
* [Analyse des sentiments](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconnaissance d’entités](../how-tos/text-analytics-how-to-entity-linking.md)
* [Détecter la langue](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconnaissance de la langue](../how-tos/text-analytics-how-to-language-detection.md)
