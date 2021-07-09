---
title: Questions fréquentes (FAQ) - Translator
titleSuffix: Azure Cognitive Services
description: Obtenez des réponses aux questions fréquentes concernant l’API Translator dans Azure Cognitive Services.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/18/2021
ms.author: lajanuar
ms.openlocfilehash: db1d981305ee62267f72f3050475c88dbcd5cc54
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110116535"
---
# <a name="frequently-asked-questionstranslator-api"></a>Questions fréquentes (FAQ) - API Translator

## <a name="how-does-translator-count-characters"></a>Comment Translator compte-t-il les caractères ?

Translator compte chaque point de code qui est défini en Unicode comme un caractère. Chaque traduction est comptabilisée comme une traduction distincte, même si la requête a été effectuée dans un même appel d’API effectuant une traduction dans plusieurs langues. La longueur de la réponse n’a pas d’importance, et le nombre de requêtes, de mots, d’octets ou de phrases n’est pas pris en compte dans le calcul du nombre de caractères.

Translator compte l’entrée suivante :

* Texte transmis à Translator dans le corps d’une requête.
  * `Text` lorsque vous utilisez les méthodes [Translate](reference/v3-0-translate.md), [Transliterate](reference/v3-0-transliterate.md) et [Dictionary Lookup](reference/v3-0-dictionary-lookup.md)
  * `Text` et `Translation` lorsque vous utilisez la méthode [Dictionary Examples](reference/v3-0-dictionary-examples.md).

* Toutes les balises : balises HTML, XML, etc. dans le champ de texte du corps de la requête. La notation JSON utilisée pour générer la requête (par exemple, la clé « Text: ») n’est **pas** prise en compte.
* Une lettre seule.
* La ponctuation.
* Un espace, une tabulation, une balise ou tout espace blanc.
* Une répétition de traduction, même si vous avez déjà traduit ce même texte. Chaque caractère envoyé à la fonction de traduction est compté, même lorsque le contenu reste identique ou que les langues source et cible sont les mêmes.

Pour les scripts basés sur des symboles graphiques tels que les idéogrammes chinois et les kanji japonais, le service Translator compte le nombre de points de code Unicode, soit un caractère par symbole. Exception : les paires de substitution Unicode comptent comme deux caractères.

Les appels aux méthodes **Detect** et **BreakSentence** ne sont pas comptés dans l’utilisation de caractères. Toutefois, le nombre attendu d’appels aux méthodes Detect et BreakSentence doit être raisonnablement proportionnel à l’utilisation des autres fonctions comptabilisées. Si le nombre d’appels Detect ou BreakSentence dépasse le nombre des autres méthodes comptabilisées multiplié par 100, Microsoft se réserve le droit de limiter votre utilisation des méthodes Detect et BreakSentence.

## <a name="where-can-i-see-my-monthly-usage"></a>Où voir mon utilisation mensuelle ?

Utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour estimer les coûts. Vous pouvez également superviser, examiner et ajouter des alertes Azure pour vos services Azure dans votre compte d’utilisateur à partir du portail Azure :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à la page Vue d’ensemble de votre ressource Translator.
1. Sélectionnez l’**abonnement** associé à votre ressource Translator.

    :::image type="content" source="media/azure-portal-overview.png" alt-text="Capture d’écran du lien de l’abonnement dans la page Vue d’ensemble du portail Azure.":::

2. Dans le rail de gauche, effectuez votre sélection sous **Cost Management** :

    :::image type="content" source="media/azure-portal-cost-management.png" alt-text="Capture d’écran des liens des ressources Cost Management dans le portail Azure.":::

## <a name="is-attribution-required-when-using-translator"></a>L’attribution est-elle obligatoire lors de l’utilisation de Translator ?

L’attribution n’est pas obligatoire si vous utilisez Translator pour la traduction vocale et de texte. Il est recommandé d’informer les utilisateurs que le contenu qui leur est présenté provient d’une traduction automatique.

Si l’attribution est utilisée, elle doit être conforme aux [règles d’attribution du service Translator](https://www.microsoft.com/translator/business/attribution/).

## <a name="is-translator-a-replacement-for-human-translator"></a>Le service Translator peut-il remplacer un traducteur humain ?

Non, les deux ont toute leur place et jouent un rôle essentiel dans la communication. Utilisez la traduction automatique là où le volume du contenu, les délais et les contraintes budgétaires rendent impossible la traduction par quelqu’un.

La traduction automatique a été essayée par plusieurs de nos partenaires [LSP (Language Service Provider)](https://www.microsoft.com/translator/business/partners/), avant d’utiliser la traduction manuelle, et a permis d’améliorer la productivité jusqu’à 50 %. Pour obtenir la liste des partenaires LSP, visitez la page des partenaires de Translator.

---
> [!TIP]
> Si vous ne trouvez pas les réponses à vos questions dans ce FAQ, essayez d’interroger la communauté de l’API Translator sur [StackOverflow](https://stackoverflow.com/search?q=%5Bmicrosoft-cognitive%5D+or+%5Bmicrosoft-cognitive%5D+translator&s=34bf0ce2-b6b3-4355-86a6-d45a1121fe27).