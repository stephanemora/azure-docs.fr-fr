---
title: Ajouter la vérification orthographique
titleSuffix: Azure Cognitive Search
description: Ajoutez la correction orthographique au pipeline de requête pour corriger les fautes de frappe dans les termes de requête avant d’exécuter la requête.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/29/2021
ms.custom: references_regions
ms.openlocfilehash: 98aec7fc2a3857ac50125e7e49c5f9ab105a49d7
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535917"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Ajouter la vérification orthographique aux requêtes dans le service Recherche cognitive

> [!IMPORTANT]
> La correction orthographique est en préversion publique et soumise à des [conditions d'utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Elle est disponible via le portail Azure et une API REST en préversion.

Vous pouvez améliorer le rappel en corrigeant l’orthographe de termes de requête de recherche individuels avant qu’ils ne parviennent au moteur de recherche. Le paramètre **speller** est pris en charge pour tous les types de requête : [simple](query-simple-syntax.md), [complète](query-lucene-syntax.md) et [sémantique](semantic-how-to-query-request.md) (nouvelle option actuellement en préversion publique).

Le vérificateur d’orthographe a été publié en tandem avec la préversion de la [recherche sémantique](semantic-search-overview.md) et partage le paramètre queryLanguage, mais il s’agit d’une fonctionnalité indépendante avec ses propres conditions préalables. Il n'y a pas d'inscription ni de frais supplémentaires pour utiliser cette fonction.

## <a name="prerequisites"></a>Prérequis

Pour utiliser la vérification orthographique, vous aurez besoin des éléments suivants :

+ Un service de recherche au niveau De base ou supérieur, dans n’importe quelle région.

+ Un index de recherche existant avec du contenu dans une [langue prise en charge](#supported-languages).

+ [Une demande de requête](/rest/api/searchservice/preview-api/search-documents) avec « speller=lexicon », et « queryLanguage » défini sur une [langue prise en charge](#supported-languages). La vérification orthographique fonctionne sur les chaînes passées dans le paramètre « search ». Elle n'est pas prise en charge pour les filtres.

Utilisez un client de recherche qui prend en charge les API en préversion sur la demande de requête. Pour REST, vous pouvez utiliser [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md) ou le code que vous avez modifié pour effectuer des appels REST vers les API en préversion. Vous pouvez également utiliser les versions bêta des kits de développement logiciel (SDK) Azure.

| Bibliothèque cliente | Versions |
|----------|----------|
| API REST | [2021-04-30-Preview](/rest/api/searchservice/index-preview) ou 2020-06-30-Preview |
| Kit SDK Azure pour .NET | [version 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2) | 
| Kit SDK Azure pour Java |  [version 11.4.0-beta.2](https://search.maven.org/artifact/com.azure/azure-search-documents/11.4.0-beta.2/jar) |
| Kit SDK Azure pour JavaScript | [version 11.2.0-beta.2](https://www.npmjs.com/package/@azure/search-documents/v/11.2.0-beta.2) |
| Kit SDK Azure pour Python | [version 11.2.0b3](https://pypi.org/project/azure-search-documents/11.2.0b3/) |

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

La correction orthographique est appliquée aux termes de requête individuels faisant l’objet d’une analyse de texte. C’est pourquoi vous pouvez utiliser le paramètre speller avec certaines requêtes Lucene, mais pas avec d’autres.

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

Les valeurs valides pour queryLanguage sont répertoriées dans le tableau suivant, copiées à partir de la liste des [Langues prises en charge (référence de l’API REST)](/rest/api/searchservice/preview-api/search-documents#queryLanguage).

| Langage | queryLanguage |
|----------|---------------|
| Anglais [EN] | EN, EN-US (par défaut) |
| Espagnol [ES] | ES, ES-ES (par défaut)|
| Français [FR] | FR, FR-FR (par défaut) |
| Allemand [DE] | DE, DE-DE (par défaut) |
| Néerlandais [NL] | NL, NL-BE, NL-NL (par défaut) |

### <a name="querylanguage-considerations"></a>Considérations relatives à queryLanguage

Comme indiqué ailleurs, une demande de requête ne peut comporter qu’un seul paramètre queryLanguage, mais ce paramètre est partagé par plusieurs fonctionnalités, chacune prenant en charge une cohorte de langages différente. Si vous utilisez simplement la vérification orthographique, la liste des langues prises en charge dans le tableau ci-dessus est la liste complète. 

### <a name="language-analyzer-considerations"></a>Considérations relatives à l’analyseur de langue

Les index qui contiennent du contenu non anglais utilisent souvent des [analyseurs de langue](index-add-language-analyzers.md) sur des champs non anglais pour appliquer les règles linguistiques de la langue native.

Si vous ajoutez maintenant la vérification orthographique à du contenu qui subit également une analyse linguistique, vous obtiendrez de meilleurs résultats si vous utilisez la même langue à chaque étape de l’indexation et du traitement des requêtes. Par exemple, si le contenu d’un champ a été indexé à l’aide de l’analyseur linguistique « fr.microsoft », les requêtes, la vérification orthographique, les légendes sémantiques et les réponses sémantiques doivent toutes utiliser une bibliothèque de lexique français d’une certaine variante.

Récapitulatif sur la façon dont les bibliothèques de langages sont utilisées pour la Recherche cognitive :

+ Les analyseurs linguistiques peuvent être appelés pendant l’indexation et l’exécution des requêtes. Il s’agit d’analyseurs avec la syntaxe Lucene Apache (par exemple « de.lucene ») ou Microsoft (« de.microsoft »).

+ Les lexiques appelés pendant la vérification orthographique sont spécifiés à l’aide d’un des codes de langue indiqués dans le tableau ci-dessus.

Dans une demande de requête, la valeur affectée au paramètre queryLanguage s’applique à la fois au vérificateur d’orthographe, aux [réponses](semantic-answers.md) et aux légendes. 

> [!NOTE]
> La cohérence de la langue entre les différentes valeurs de propriété pose problème uniquement si vous utilisez des analyseurs linguistiques. Si vous utilisez des analyseurs avec la langue non spécifiée (comme mot clé, simple, standard, stop, whitespace ou `standardasciifolding.lucene`), vous pouvez indiquer la valeur queryLanguage que vous souhaitez.

Même si le contenu d’un index de recherche peut inclure plusieurs langues, l’entrée de requête est généralement formulée en une langue unique. Le moteur de recherche ne vérifie pas la compatibilité du paramètre queryLanguage, de l’analyseur linguistique et de la langue dans laquelle le contenu est composé. Veillez donc à limiter les requêtes en conséquence pour éviter la production de résultats incorrects.

## <a name="next-steps"></a>Étapes suivantes

+ [Appeler le classement sémantique et les légendes](semantic-how-to-query-request.md)
+ [Créer une requête de base](search-query-create.md)
+ [Utiliser la syntaxe de requête Lucene complète](query-Lucene-syntax.md)
+ [Utiliser la syntaxe de requête simple](query-simple-syntax.md)
+ [Vue d’ensemble de la recherche sémantique](semantic-search-overview.md)