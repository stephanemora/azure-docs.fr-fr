---
title: 'Nombre de caractères : API de traduction de texte Translator Text'
titlesuffix: Azure Cognitive Services
description: Comment l’API de traduction de texte Translator Text compte les caractères.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: 2164070c8f0efe2898e3780cac30a80c9f1ca986
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874411"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Comment l’API de traduction de texte Translator Text compte les caractères

L’API de traduction de texte Translator Text comptabilise chaque point de code Unicode du texte entré comme un caractère. Chaque traduction d’un texte dans une autre langue est comptabilisée comme une seule traduction, même si la requête a été effectuée dans un même appel d’API effectuant une traduction dans plusieurs langues. La longueur de la réponse n’a pas d’importance.

Voici ce qui est pris en compte :

* Le texte passé à l’API de traduction de texte Translator Text dans le corps de la requête
   * `Text` lorsque vous utilisez les méthodes Translate, Transliterate et Dictionary Lookup
   * `Text` et `Translation` lorsque vous utilisez la méthode Dictionary Examples
* Toutes les balises : balises HTML, XML, etc. dans le champ de texte du corps de la requête. La notation JSON utilisée pour générer la requête (par exemple « Text: ») n’est pas prise en compte.
* Une lettre seule
* Ponctuation
* Un espace, une tabulation, une balise et tout type d’espace blanc
* Chaque point de code défini au format Unicode
* Une répétition de traduction, même si vous avez traduit ce même texte précédemment

Pour les scripts basés sur les idéogrammes, tels que les idéogrammes chinois et les kanji japonais, l’API de traduction de texte Translator Text comptabilise toujours le nombre de points de code Unicode, en comptant un caractère par idéogramme. Exception : Les substituts Unicode comptent comme deux caractères.

Le nombre de requêtes, de mots, d’octets ou de phrases est sans importance dans la comptabilisation des caractères. 

Les appels aux méthodes Detect et BreakSentence ne sont pas comptés dans l’utilisation de caractères. Toutefois, nous nous attendons à ce que la quantité d’appels aux méthodes Detect et BreakSentence soit raisonnable par rapport à l’utilisation des autres fonctions comptées. Si le nombre d’appels Detect ou BreakSentence que vous effectuez dépasse le nombre des autres méthodes comptabilisées multiplié par 100, Microsoft se réserve le droit de limiter votre utilisation des méthodes Detect et BreakSentence.


Vous trouverez plus d’informations sur les comptes de caractères dans la [FAQ de Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
