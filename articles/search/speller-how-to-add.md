---
title: Ajouter la vérification orthographique
titleSuffix: Azure Cognitive Search
description: Ajoutez la correction orthographique au pipeline de requête pour corriger les fautes de frappe dans les termes de requête avant d’exécuter la requête.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/27/2021
ms.custom: references_regions
ms.openlocfilehash: 3da9051a1f089d487be7021bf9341a95bae62b08
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110614368"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Ajouter la vérification orthographique aux requêtes dans le service Recherche cognitive

> [!IMPORTANT]
> La correction orthographique est en préversion publique et soumise à des [conditions d'utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Elle est disponible via le portail Azure et une API REST en préversion.

Vous pouvez améliorer le rappel en corrigeant l’orthographe de termes de requête de recherche individuels avant qu’ils ne parviennent au moteur de recherche. Le paramètre **speller** est pris en charge pour tous les types de requête : [simple](query-simple-syntax.md), [complète](query-lucene-syntax.md) et [sémantique](semantic-how-to-query-request.md) (nouvelle option actuellement en préversion publique).

## <a name="prerequisites"></a>Conditions préalables requises

Le vérificateur d'orthographe a été publié en même temps que la [préversion de la recherche sémantique](semantic-search-overview.md). L'[inscription](https://aka.ms/SemanticSearchPreviewSignup) est obligatoire, mais son utilisation est gratuite et sans restriction de niveau. Le vérificateur d'orthographe est disponible dans les [mêmes régions](semantic-search-overview.md#availability-and-pricing) que la recherche sémantique.

Une fois l'inscription traitée, vous devrez disposer des éléments suivants :

+ Index de recherche existant, avec du contenu dans une [langue prise en charge](#supported-languages). Actuellement, la correction de l’orthographe ne fonctionne pas avec les [synonymes](search-synonyms.md). Évitez de l’utiliser sur des index qui spécifient une carte de synonymes dans une définition de champ.

+ Un client de recherche pour l’envoi de requêtes

  Le client de recherche doit prendre en charge les API REST en préversion dans la demande de requête. Vous pouvez utiliser [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md) ou le code que vous avez modifié pour effectuer des appels REST vers les API en préversion.

+ [Une demande de requête](/rest/api/searchservice/preview-api/search-documents) qui appelle la correction orthographique doit avoir les paramètres « api-version=2020-06-30-Preview », « speller=lexicon » et « queryLanguage » définis sur une [langue prise en charge](#supported-languages).

## <a name="spell-correction-with-simple-search"></a>Correction orthographique avec recherche simple

L’exemple suivant utilise l’index hotels-sample intégré pour illustrer la correction orthographique sur une simple requête de texte de forme libre. Sans correction orthographique, la requête ne retourne aucun résultat. Avec la correction, la requête retourne un résultat pour l’hôtel familial Johnson.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>Correction orthographique avec la syntaxe Lucene complète

La correction orthographique est appliquée aux termes de requête individuels faisant l’objet d’une analyse. C’est pourquoi vous pouvez utiliser le paramètre speller avec certaines requêtes Lucene, mais pas avec d’autres.

+ Certains formulaires de requête incompatibles contournent l’analyse de texte, notamment ceux avec des caractères génériques ou des expressions régulières et ceux portant sur des requêtes approximatives.
+ Les formulaires de requête compatibles sont ceux incluant des recherches par champ, ceux portant sur une recherche de proximité ou ceux avec promotion de termes.

Cet exemple utilise la recherche par champ sur le champ Category, avec la syntaxe Lucene complète et un terme de requête mal orthographié. Quand le vérificateur d’orthographe est inclus, la faute de frappe dans « Suiite » est corrigée et la requête aboutit.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>Correction orthographique avec recherche sémantique

Dans cette requête, un seul terme est correctement orthographié. Elle fait l’objet de corrections orthographiques pour retourner des résultats pertinents. Pour plus d’informations, consultez [Créer une requête sémantique](semantic-how-to-query-request.md).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="supported-languages"></a>Langues prises en charge

Pour la vérification orthographique, les valeurs valides pour queryLanguage sont disponibles dans le tableau suivant. Cette liste est un sous-ensemble des [langues prises en charge (référence d’API REST)](/rest/api/searchservice/preview-api/search-documents#queryLanguage). Si vous utilisez des sous-titres sémantiques et des réponses sans vérification orthographique, vous pouvez choisir la langue dans une liste plus importante de langues et de variantes.

| Langage | queryLanguage |
|----------|---------------|
| Anglais [EN] | EN, EN-US (par défaut) |
| Espagnol [ES] | ES, ES-ES (par défaut)|
| Français [FR] | FR, FR-FR (par défaut) |
| Allemand [DE] | DE, DE-DE (par défaut) |

## <a name="language-considerations"></a>Observations relatives au langage

Le paramètre queryLanguage requis pour le vérificateur orthographique doit être cohérent avec tous les [analyseurs linguistiques](index-add-language-analyzers.md) affectés aux définitions de champ dans l’index. Par exemple, si le contenu d’un champ a été indexé à l’aide de l’analyseur linguistique « fr.microsoft », les requêtes, la vérification orthographique, les légendes sémantiques et les réponses sémantiques doivent toutes utiliser une bibliothèque de langue française d’une certaine variante.

Récapitulatif sur la façon dont les bibliothèques de langages sont utilisées pour la Recherche cognitive :

+ Les analyseurs linguistiques peuvent être appelés pendant l’indexation et l’exécution des requêtes. Il s’agit d’analyseurs avec la syntaxe Lucene complète (par exemple « de.lucene ») ou Microsoft (« de.microsoft »).

+ Les lexiques appelés pendant la vérification orthographique sont spécifiés à l’aide d’un des codes de langue indiqués dans le tableau ci-dessus.

Dans une demande de requête, le paramètre queryLanguage s’applique à la fois au vérificateur d’orthographe, aux [réponses](semantic-answers.md) et aux légendes. Aucune partie individuelle de la réponse sémantique n’est remplacée. 

> [!NOTE]
> La cohérence de la langue entre les différentes valeurs de propriété pose problème uniquement si vous utilisez des analyseurs linguistiques. Si vous utilisez des analyseurs avec la langue non spécifiée (comme mot clé, simple, standard, stop, whitespace ou `standardasciifolding.lucene`), vous pouvez indiquer la valeur queryLanguage que vous souhaitez.

Même si le contenu d’un index de recherche peut inclure plusieurs langues, l’entrée de requête est généralement formulée en une langue unique. Le moteur de recherche ne vérifie pas la compatibilité du paramètre queryLanguage, de l’analyseur linguistique et de la langue dans laquelle le contenu est composé. Veillez donc à limiter les requêtes en conséquence pour éviter la production de résultats incorrects.

## <a name="next-steps"></a>Étapes suivantes

+ [Appeler le classement sémantique et les légendes](semantic-how-to-query-request.md)
+ [Créer une requête de base](search-query-create.md)
+ [Utiliser la syntaxe de requête Lucene complète](query-Lucene-syntax.md)
+ [Utiliser la syntaxe de requête simple](query-simple-syntax.md)
+ [Vue d’ensemble de la recherche sémantique](semantic-search-overview.md)