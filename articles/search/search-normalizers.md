---
title: Normalisation du texte pour les filtres, les facettes et le tri
titleSuffix: Azure Cognitive Search
description: Spécifiez des normaliseurs pour les champs de texte dans un index afin de personnaliser le comportement de correspondance stricte de mot clé pour le filtrage, les facettes et le tri.
author: IshanSrivastava
manager: jlembicz
ms.author: ishansri
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/23/2021
ms.openlocfilehash: 3e7a33d9213d7af44d2cfc50baa847534618f7e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608494"
---
# <a name="text-normalization-for-case-insensitive-filtering-faceting-and-sorting"></a>Normalisation du texte pour le filtrage, les facettes et le tri sans prise en compte de la casse

 > [!IMPORTANT]
 > Normalizer est disponible en préversion publique, via l’**API REST 2020-06-30-preview**. Les fonctionnalités d’évaluation sont proposées telles quelles, dans le cadre de Conditions d’utilisation supplémentaires.

La recherche et la récupération de documents à partir d’un index Recherche cognitive Azure requiert la mise en correspondance de la requête avec le contenu du document. Le contenu peut être analysé pour produire des jetons pour la correspondance, comme c’est le cas lorsque le paramètre `search` est utilisé, ou il peut être utilisé tel quel pour la correspondance stricte de mot clé, comme avec `$filter`, `facets` et `$orderby`. Cette approche de type « tout ou rien » couvre la plupart des scénarios, mais elle est limitée lorsqu’un prétraitement simple, comme la gestion de la casse, l’élimination des accents, l’asciifolding, etc., est requis sans passer par l’ensemble de la chaîne d’analyse.

Penchez-vous sur les exemples suivants :

+ `$filter=City eq 'Las Vegas'` retourne uniquement les documents qui contiennent le texte exact « Las Vegas » et exclut les documents avec « LAS VEGAS » et « las vegas », ce qui n’est pas approprié lorsque le cas d’usage nécessite tous les documents, indépendamment de la casse.

+ `search=*&facet=City,count:5` retourne « Las Vegas », « LAS VEGAS » et « Las Vegas » en tant que valeurs distinctes, bien qu’elles correspondent à la même ville.

+ `search=usa&$orderby=City` renverra les villes dans l’ordre lexicographique : « Las Vegas », « Seattle », « las vegas », même si l’intention est de classer les mêmes villes ensemble, indépendamment de la casse. 

## <a name="normalizers"></a>Normaliseurs

Un *normaliseur* est un composant du moteur de recherche responsable du prétraitement du texte pour la correspondance de mots clés. Les normaliseurs sont similaires aux analyseurs, sauf qu’ils n’affectent pas de jeton à la requête. Voici quelques-unes des transformations qui peuvent être obtenues à l’aide de normaliseurs :

+ Conversion en minuscules ou en majuscules.
+ Normalisez les accents et les signes diacritiques tels que ö ou ê en caractères ASCII équivalents, « o » et « e » respectivement dans notre exemple.
+ Mappage de caractères tels que `-` et les espaces blancs en un caractère spécifié par l’utilisateur.

Les normaliseurs peuvent être spécifiés sur des champs de texte de l’index et s’appliquent à la fois à l’indexation et lors de l’exécution de la requête.

## <a name="predefined-and-custom-normalizers"></a>Normaliseurs prédéfinis et personnalisés 

Recherche cognitive Azure prend en charge des normaliseurs prédéfinis pour les cas d’utilisation courants, ainsi que la possibilité de les personnaliser en fonction des besoins.

| Category | Description |
|----------|-------------|
| [Normaliseurs prédéfinis](#predefined-normalizers) | Fournis prêts à l’emploi et utilisables sans aucune configuration. |
|[Normaliseurs personnalisés](#add-custom-normalizers) | Pour les scénarios avancés. Requiert la configuration définie par l’utilisateur d’une combinaison d’éléments existants, comprenant de caractères et de jetons. <sup>1</sup>|

<sup>(1)</sup> Les normaliseurs personnalisés ne spécifient pas de générateurs de jetons, car les normaliseurs produisent toujours un jeton unique.

## <a name="how-to-specify-normalizers"></a>Comment spécifier des normaliseurs

Les normaliseurs peuvent être spécifiés par champ sur les champs de texte (`Edm.String` et `Collection(Edm.String)`) qui ont au moins une des propriétés `filterable`, `sortable` ou `facetable` définie sur true. La définition d’un normaliseur est facultative et est réglée sur `null` par défaut. Nous vous recommandons d’évaluer les normaliseurs prédéfinis avant de configurer un normaliseur personnalisé pour plus de simplicité d’utilisation. Essayez un autre normaliseur si les résultats ne sont pas ceux attendus.

Les normaliseurs ne peuvent être spécifiés que lorsqu’un nouveau champ est ajouté à l’index. Il est recommandé d’évaluer les besoins en normalisation initiaux et d’affecter des normaliseurs pendant les étapes initiales du développement, lors de la suppression et de la recréation d’index. Les normaliseurs ne peuvent pas être spécifiés sur un champ qui a déjà été créé.

1. Lors de la création d’une définition de champ dans l’[index](/rest/api/searchservice/create-index), définissez la propriété **normalizer** sur l’un des éléments suivants : un [normaliseur prédéfini](#predefined-normalizers) tel que `lowercase` ou un analyseur personnalisé (défini dans le même schéma d’index).  
 
   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "filterable": true,
      "analyzer": "en.microsoft",
      "normalizer": "lowercase"
      ...
    },
   ```

2. Les normaliseurs personnalisés doivent être définis en premier dans la section **[normalizers]** de l’index, puis assignés à la définition de champ, comme indiqué à l’étape précédente. Pour plus d’informations, consultez [Créer un index](/rest/api/searchservice/create-index) et [Ajouter des normaliseurs personnalisés](#add-custom-normalizers).


   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "normalizer": "my_custom_normalizer"
    },
   ```

 
> [!NOTE]
> Pour modifier le normaliseur d’un champ existant, vous devez recréer entièrement l’index (vous ne pouvez pas recréer des champs individuels).

Une bonne solution pour les index de production, où la reconstruction d’index est coûteuse, consiste à créer un nouveau champ identique à l’ancien, mais avec la nouvelle normalisation, et de l’utiliser à la place de l’ancien. Utilisez [Mettre à jour l’index](/rest/api/searchservice/update-index) pour incorporer le nouveau champ et [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) pour le remplir. Par la suite, pendant l’opération de maintenance planifiée de l’index, vous pouvez le nettoyer de façon à supprimer les champs obsolètes.

## <a name="add-custom-normalizers"></a>Ajouter des normaliseurs personnalisés

Les normaliseurs personnalisés sont définis dans le schéma d’index et peuvent être spécifiés à l’aide de la propriété du champ. La définition de la normalisation personnalisée comprend un nom, un type, et un ou plusieurs filtres de caractères et filtres de jetons. Les filtres de caractères et les filtres de jetons sont les blocs de construction d’un normaliseur personnalisé et sont responsables du traitement du texte. Ces filtres sont appliqués de gauche à droite.

 Le `token_filter_name_1` est le nom d’un filtre de jetons, et `char_filter_name_1` et `char_filter_name_2` sont les noms des filtres de caractères (consultez les tableaux [Filtres de jetons](#supported-token-filters) et Filtres de caractères pris en charge pour accéder aux valeurs valides).

La définition du normaliseur est une partie de l’index. Consultez [API de création d’index](/rest/api/searchservice/create-index) pour plus d’informations sur le reste de l’index.

```
"normalizers":(optional)[
   {
      "name":"name of normalizer",
      "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenFilters":[
         "token_filter_name_1
      ]
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name_1",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name_1",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

Des normaliseurs personnalisés peuvent être ajoutés lors de la création de l’index ou ultérieurement en en mettant un à jour. L’ajout d’un normaliseur personnalisé à un index existant nécessite que l’indicateur **allowIndexDowntime** soit spécifié dans [Index de mise à jour](/rest/api/searchservice/update-index) et entraîne l’indisponibilité de l’index pendant quelques secondes.

## <a name="normalizers-reference"></a>Référence sur les normaliseurs

### <a name="predefined-normalizers"></a>Normaliseurs prédéfinis
|**Nom**|**Description et options**|  
|-|-|  
|standard| Met le texte en minuscules, puis applique l’asciifolding.|  
|minuscules| Convertit des caractères en minuscules.|
|majuscules| Convertit des caractères en majuscules.|
|asciifolding| Transforme les caractères qui ne sont pas dans le bloc Unicode latin de base en leur équivalent ASCII, s’il en existe un. Par exemple en changeant à en a.|
|elision| Supprime l’élision au début des jetons.|

### <a name="supported-char-filters"></a>Filtres de caractères pris en charge
Pour plus d’informations sur les filtres de caractères, consultez la [Référence des filtres de caractères](index-add-custom-analyzers.md#CharFilter).
+ [mapping](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)  
+ [pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)

### <a name="supported-token-filters"></a>Filtres de jetons pris en charge
La liste ci-dessous montre les filtres de jetons pris en charge pour les normaliseurs et, est un sous-ensemble des filtres de jetons globaux impliqués dans l’analyse lexicale. Pour plus d’informations sur les filtres, reportez-vous à la [Référence des filtres de jetons](index-add-custom-analyzers.md#TokenFilters).

+ [arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)
+ [asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)
+ [cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)  
+ [elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)  
+ [german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)
+ [hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)  
+ [indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)
+ [persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)
+ [scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)  
+ [scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)
+ [sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)  
+ [lowercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)
+ [uppercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)


## <a name="custom-normalizer-example"></a>Exemple de normaliseur personnalisé

L’exemple ci-dessous illustre une définition de normalisation personnalisée avec les filtres de caractères et filtres de jetons correspondants. Les options personnalisées pour les filtres de caractères et les filtres de jetons sont spécifiées séparément comme des constructions nommées, puis elles sont référencées dans la définition du normaliseur, comme illustré ci-dessous.

* Une normalisation personnalisée « my_custom_normalizer » est définie dans la section `normalizers` de la définition de l’index.
* La normalisation est composée de deux filtres de caractères et de trois filtres de jetons : élision, mise en minuscules et asciifolding dans le filtre « my_asciifolding ».
* Le premier filtre de caractères « map_dash » remplace tous les tirets par des traits de soulignement tandis que le second « remove_whitespace » supprime tous les espaces.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false,
        },
        {
           "name":"city",
           "type":"Edm.String",
           "filterable": true,
           "facetable": true,
           "normalizer": "my_custom_normalizer"
        }
     ],
     "normalizers":[
        {
           "name":"my_custom_normalizer",
           "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],,
           "tokenFilters":[              
              "my_asciifolding",
              "elision",
              "lowercase",
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

## <a name="see-also"></a>Voir aussi
+ [Analyseurs pour la linguistique et le traitement de texte](search-analyzers.md)

+ [API REST de recherche de documents](/rest/api/searchservice/search-documents) 
