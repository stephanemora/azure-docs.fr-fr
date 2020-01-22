---
title: Critères et caractères spéciaux
titleSuffix: Azure Cognitive Search
description: Utilisez des requêtes de caractères génériques et de préfixes pour faire correspondre des termes entiers ou partiels dans une demande de requête Recherche cognitive Azure. Les critères difficiles à mettre en correspondance qui incluent des caractères spéciaux peuvent être résolus à l’aide de la syntaxe complète de la requête et des analyseurs personnalisés.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ec1422d03cce78bdd8206f6687a78b63ddf989dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75984925"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>Critères et caractères spéciaux (tirets)

Pour les requêtes qui incluent des caractères spéciaux (`-, *, (, ), /, \, =`) ou pour les critères de requête basés sur des termes partiels dans un terme plus large, des étapes de configuration supplémentaires sont généralement nécessaires pour garantir que l’index contient le contenu attendu, au format approprié. 

Par défaut, un numéro de téléphone comme `+1 (425) 703-6214` est segmenté en jetons `"1"`, `"425"`, `"703"`, `"6214"`. Comme vous pouvez l’imaginer, la recherche sur `"3-62"`, des termes partiels incluant un tiret, échoue, car ce contenu n’existe pas réellement dans l’index. 

Lorsque vous devez effectuer une recherche sur des chaînes partielles ou des caractères spéciaux, vous pouvez remplacer l’analyseur par défaut par un analyseur personnalisé qui fonctionne sous des règles plus simples de segmentation du texte en unités lexicales, en préservant les termes entiers, nécessaires lorsque les chaînes de requête incluent des parties d’un terme ou des caractères spéciaux. En prenant un peu de recul, l’approche ressemble à ceci :

+ Choisir un analyseur prédéfini ou définir un analyseur personnalisé qui produit la sortie souhaitée
+ Attribuer l’analyseur au champ
+ Générer l’index et tester

Cet article vous guide tout au long de ces tâches. L’approche décrite ici est utile dans d’autres scénarios : les requêtes de caractères génériques et d’expressions régulières requièrent également des termes entiers comme base pour les critères spéciaux. 

> [!TIP]
> L’évaluation d’analyseurs est un processus itératif qui requiert des régénérations fréquentes d’index. Vous pouvez faciliter cette étape en utilisant Postman, les API REST pour [créer un index](https://docs.microsoft.com/rest/api/searchservice/create-index), [supprimer un index](https://docs.microsoft.com/rest/api/searchservice/delete-index), [charger des documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) et [rechercher des documents](https://docs.microsoft.com/rest/api/searchservice/search-documents). Pour charger des documents, le corps de la requête doit contenir un petit jeu de données représentatif que vous souhaitez tester (par exemple, un champ avec des numéros de téléphone ou des codes de produit). Ces API étant dans la même collection Postman, vous pouvez effectuer rapidement ces étapes.

## <a name="choosing-an-analyzer"></a>Choix d’un analyseur

Lors du choix d’un analyseur qui produit des jetons à terme entier, les analyseurs suivants sont des choix courants :

| Analyseur | Comportements |
|----------|-----------|
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Le contenu du champ entier est segmenté en jetons en tant que terme unique. |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Sépare sur les espaces blancs seulement. Les termes qui incluent des tirets ou d’autres caractères sont traités comme un jeton unique. |
| [analyseur personnalisé](index-add-custom-analyzers.md) | (recommandé) La création d’un analyseur personnalisé vous permet de spécifier à la fois le générateur de jetons et le filtre de jeton. Les analyseurs précédents doivent être utilisés tels quels. Un analyseur personnalisé vous permet de choisir les générateurs et les filtres de jetons à utiliser. <br><br>Une combinaison recommandée est le [générateur de jetons keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) avec un [filtre de jeton lowercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). En soi, l’[analyseur keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) prédéfini ne met pas en minuscules les caractères majuscules, ce qui peut entraîner l’échec des requêtes. Un analyseur personnalisé vous donne un mécanisme pour ajouter le filtre de jeton lowercase. |

Si vous utilisez un outil de test de l’API Web tel que Postman, vous pouvez ajouter l’[appel REST de l’analyseur de test](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) pour inspecter la sortie segmentée en jetons. À partir d’un index existant et d’un champ contenant des tirets ou des termes partiels, vous pouvez essayer différents analyseurs sur des termes spécifiques pour voir quels jetons sont émis.  

1. Vérifiez l’analyseur Standard pour voir comment les termes sont segmentés en jetons par défaut.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Évaluez la réponse pour voir comment le texte est segmenté en jetons dans l’index. Notez la manière dont chaque terme est en minuscules et segmenté.

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. Modifiez la requête pour utiliser l’analyseur `whitespace` ou `keyword` :

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. À présent, la réponse se compose d’un jeton unique, en majuscules, avec des tirets conservés en tant que partie de la chaîne. Si vous devez effectuer une recherche sur un critère spécial ou sur un terme partiel, le moteur de requête dispose maintenant de la base pour trouver une correspondance.


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> N’oubliez pas que les analyseurs de requêtes mettent souvent les termes en minuscules dans une expression de recherche lors de la création de l’arborescence de requêtes. Si vous utilisez un analyseur qui ne met pas les entrées de texte en minuscules et que vous n’obtenez pas les résultats attendus, cela peut être la raison. La solution consiste à ajouter un filtre de jeton lowercase.

## <a name="analyzer-definitions"></a>Définitions des analyseurs
 
Que vous évaluiez des analyseurs ou que vous avanciez avec une configuration spécifique, vous devrez spécifier l’analyseur sur la définition de champ et éventuellement configurer l’analyseur lui-même si vous n’utilisez pas d’analyseur intégré. Lorsque vous changez d’analyseur, vous devez généralement reconstruire l’index (supprimer, recréer et recharger). 

### <a name="use-built-in-analyzers"></a>Utiliser des analyseurs intégrés

Les analyseurs intégrés ou prédéfinis peuvent être spécifiés par leur nom dans la propriété `analyzer` d’une définition de champ, sans qu’une configuration supplémentaire ne soit requise dans l’index. L’exemple suivant montre comment définir l’analyseur `whitespace` sur un champ.

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```
Pour plus d’informations sur tous les analyseurs intégrés disponibles, consultez [Liste des analyseurs prédéfinis](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

### <a name="use-custom-analyzers"></a>Utiliser des analyseurs personnalisés

Si vous utilisez un [analyseur personnalisé](index-add-custom-analyzers.md), définissez-le dans l’index avec une combinaison définie par l’utilisateur de générateur de jetons et de filtre de jeton, avec les paramètres de configuration possibles. Ensuite, référencez-le sur une définition de champ, comme vous le feriez pour un analyseur intégré.

Lorsque l’objectif est la segmentation du texte en termes entiers, un analyseur personnalisé qui se compose d’un **générateur de jetons keyword** et d’un **filtre de jeton lowercase** est recommandé.

+ Le générateur de jetons keyword crée un jeton unique pour tout le contenu d’un champ.
+ Le filtre de jeton lowercase transforme les lettres majuscules en caractères minuscules. Les analyseurs de requêtes mettent généralement en minuscules toutes les entrées de texte qui sont en majuscules. La mise en minuscules homogénéise les entrées avec les termes jetons.

L’exemple suivant illustre un analyseur personnalisé qui fournit le générateur de jetons keyword et un filtre de jetons lowercase.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
]

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> Le générateur de jetons `keyword_v2` et le filtre de jeton `lowercase` sont connus du système et utilisent leurs configurations par défaut, c’est pourquoi vous pouvez les référencer par leur nom sans avoir à les définir au préalable.

## <a name="tips-and-best-practices"></a>Conseils et meilleures pratiques

### <a name="tune-query-performance"></a>Régler les performances des requêtes

Si vous implémentez la configuration recommandée qui comprend le générateur de jetons keyword_v2 et le filtre de jeton lowercase, vous remarquerez peut-être une baisse des performances de requêtes en raison du traitement du filtre de jeton supplémentaire sur les jetons existants dans votre index. 

L’exemple suivant ajoute un [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) pour que les correspondances de préfixe soient plus rapides. Des jetons supplémentaires sont générés pour les combinaisons de 2 à 25 caractères qui incluent des caractères : (pas seulement MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL). Comme vous pouvez l’imaginer, les résultats supplémentaires de segmentation du texte en unités lexicales entraîne un plus grand index.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
]

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>Utiliser des analyseurs différents pour l’indexation et le traitement des requêtes

Les analyseurs sont appelés pendant l’indexation et pendant l’exécution de la requête. Il est courant d’utiliser le même analyseur pour les deux, mais vous pouvez configurer des analyseurs personnalisés pour chaque charge de travail. Les remplacements d’analyseur sont spécifiés dans la [définition de l’index](https://docs.microsoft.com/rest/api/searchservice/create-index) dans la section `analyzers`, puis référencés sur des champs spécifiques. 

Lorsque l’analyse personnalisée est requise uniquement pendant l’indexation, vous pouvez appliquer l’analyseur personnalisé à l’indexation et continuer à utiliser l’analyseur Lucene standard (ou un autre analyseur) pour les requêtes.

Pour spécifier une analyse spécifique au rôle, vous pouvez définir des propriétés sur le champ pour chacun d’eux, en définissant `indexAnalyzer` et `searchAnalyzer` au lieu de la propriété `analyzer` par défaut.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

### <a name="duplicate-fields-for-different-scenarios"></a>Dupliquer des champs pour différents scénarios

Une autre option s’appuie sur l’affectation de l’analyseur par champ pour optimiser les différents scénarios. Plus précisément, vous pouvez définir « featureCode » et « featureCodeRegex » pour prendre en charge une recherche en texte intégral normale sur le premier et critères spéciaux avancés sur le second.

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_customanalyzer"
},
```

## <a name="next-steps"></a>Étapes suivantes

Cet article explique comment les analyseurs contribuent aux problèmes de requête et à les résoudre. À l’étape suivante, examinez de plus près l’impact de l’analyseur sur l’indexation et le traitement des requêtes. En particulier, envisagez d’utiliser l’API Analyze Text pour retourner une sortie segmentée en jetons afin que vous puissiez voir exactement ce qu’un analyseur crée pour votre index.

+ [Analyseurs de langage](search-language-support.md)
+ [Analyseurs pour le traitement de texte dans Recherche cognitive Azure](search-analyzers.md)
+ [API (REST) Analyze Text](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Fonctionnement de la recherche en texte intégral (architecture de requête)](search-lucene-query-architecture.md)