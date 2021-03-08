---
title: Ajouter la vérification orthographique
titleSuffix: Azure Cognitive Search
description: Ajoutez la correction orthographique au pipeline de requête pour corriger les fautes de frappe dans les termes de requête avant d’exécuter la requête.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: da172e9a7605876711e4a4f32bf4fac698b35109
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694799"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Ajouter la vérification orthographique aux requêtes dans le service Recherche cognitive

> [!IMPORTANT]
> La correction orthographique est en préversion publique et disponible uniquement par le biais de l’API REST en préversion. Les fonctionnalités d’évaluation sont proposées telles quelles, dans le cadre de [Conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Pendant le lancement de la préversion initiale, le vérificateur d’orthographe n’est pas facturé. Pour plus d’informations, consultez [Disponibilité et tarifs](semantic-search-overview.md#availability-and-pricing).

Vous pouvez améliorer le rappel en corrigeant l’orthographe de termes de requête de recherche individuels avant qu’ils ne parviennent au moteur de recherche. Le paramètre **speller** est pris en charge pour tous les types de requête : [simple](query-simple-syntax.md), [complète](query-lucene-syntax.md) et [sémantique](semantic-how-to-query-request.md) (nouvelle option actuellement en préversion publique).

## <a name="prerequisites"></a>Conditions préalables requises

+ Un index de recherche existant avec du contenu en anglais

+ Un client de recherche pour l’envoi de requêtes

  Le client de recherche doit prendre en charge les API REST en préversion dans la demande de requête. Vous pouvez utiliser [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md) ou le code que vous avez modifié pour effectuer des appels REST vers les API en préversion.

+ [Une demande de requête](/rest/api/searchservice/preview-api/search-documents) qui utilise la correction orthographique présente les paramètres suivants : « api-version=2020-06-30-Preview », « speller=lexicon » et « queryLanguage=en-us ».

  Le paramètre queryLanguage est requis pour le vérificateur d’orthographe. « en-us » est actuellement la seule valeur valide.

> [!Note]
> Le paramètre speller est disponible sur tous les niveaux, dans les régions offrant la recherche sémantique. Vous ne devez pas vous inscrire pour accéder à cette fonctionnalité d'évaluation. Pour plus d’informations, consultez [Disponibilité et tarifs](semantic-search-overview.md#availability-and-pricing).

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

## <a name="language-considerations"></a>Observations relatives au langage

Le paramètre queryLanguage requis pour le vérificateur d’orthographe doit être cohérent avec tous les [analyseurs linguistiques](index-add-language-analyzers.md) affectés aux définitions de champ dans le schéma d’index. 

+ queryLanguage détermine les lexiques utilisés pour la vérification orthographique. Il est également utilisé comme entrée de l’[algorithme de classement sémantique](semantic-how-to-query-response.md) si vous utilisez « queryType=semanti ».

+ Des analyseurs linguistiques sont utilisés pendant l’indexation et l’exécution de requêtes pour récupérer des documents correspondants dans l’index de recherche. Exemple de définition d’un champ qui utilise un analyseur linguistique : `"name": "Description", "type": "Edm.String", "analyzer": "en.microsoft"`.

Pour des résultats optimaux lors de l’utilisation du correcteur orthographique, si le paramètre queryLanguage a la valeur « en-us », une variante anglaise de l’analyseur linguistique doit également être utilisée (« en.microsoft » ou « en.lucene »).

> [!NOTE]
> Les analyseurs indépendants de la langue (keyword, simple, standard, stop, whitespace, `standardasciifolding.lucene`, etc.) ne génèrent pas de conflit avec les paramètres queryLanguage.

Dans une demande de requête, le paramètre queryLanguage que vous définissez s’applique de la même manière au vérificateur d’orthographe, aux réponses et aux légendes. Aucune partie individuelle n’est remplacée.

Même si le contenu d’un index de recherche peut inclure plusieurs langues, l’entrée de requête est généralement formulée en une langue unique. Le moteur de recherche ne vérifie pas la compatibilité du paramètre queryLanguage, de l’analyseur linguistique et de la langue dans laquelle le contenu est composé. Veillez donc à limiter les requêtes en conséquence pour éviter la production de résultats incorrects.

## <a name="next-steps"></a>Étapes suivantes

+ [Créer une requête sémantique](semantic-how-to-query-request.md)
+ [Créer une requête de base](search-query-create.md)
+ [Utiliser la syntaxe de requête Lucene complète](query-Lucene-syntax.md)
+ [Utiliser la syntaxe de requête simple](query-simple-syntax.md)
+ [Vue d’ensemble de la recherche sémantique](semantic-search-overview.md)