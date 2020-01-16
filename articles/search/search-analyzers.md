---
title: Analyseurs pour la linguistique et le traitement de texte
titleSuffix: Azure Cognitive Search
description: Attribuez des analyseurs aux champs de texte d’un index pouvant faire l’objet d’une recherche, afin de remplacer l’instance de Lucene standard par défaut par des solutions alternatives personnalisées, prédéfinies ou spécifiques à une langue.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 2e4a6ab8825982969ffa4654c2418f7a9d168d2e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460719"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Analyseurs pour le traitement de texte dans la Recherche cognitive Azure

Un *analyseur* est un composant du [moteur de recherche en texte intégral](search-lucene-query-architecture.md) chargé de traiter le texte dans les chaînes de requête et les documents indexés. La façon dont les différents analyseurs manipulent le texte varie en fonction du scénario. Les analyseurs linguistiques traitent le texte en utilisant des règles linguistiques afin d’améliorer la qualité de la recherche, alors que d’autres analyseurs effectuent des tâches plus simples comme la conversion de caractères en minuscules, par exemple. 

Les analyseurs linguistiques sont les plus fréquemment utilisés. D’ailleurs, un analyseur linguistique par défaut est affecté à chaque champ pouvant faire l’objet d’une recherche figurant dans un index de Recherche cognitive Azure. Les transformations linguistiques suivantes sont courantes au cours d’une analyse de texte :

+ Les mots non essentiels (mots vides) et la ponctuation sont supprimés.
+ Les expressions et les mots avec tirets sont segmentés en différents composants.
+ Les mots en majuscules sont mis en minuscules.
+ Seule la racine des mots est conservée pour qu’une correspondance soit trouvée, quels que soient le temps ou la forme utilisés.

Les analyseurs linguistiques convertissent une entrée de texte en forme primitive ou racine plus efficace pour le stockage et la récupération des informations. La conversion se produit lors de l’indexation, lorsque l’index est créé, puis à nouveau lors d’une recherche lorsque l’index est en lecture. Vous avez plus de chances d’obtenir les résultats de recherche que vous attendez si vous utilisez le même analyseur pour les deux opérations.

## <a name="default-analyzer"></a>Analyseur par défaut  

Par défaut, la Recherche cognitive Azure utilise l’[analyseur Apache Lucene Standard (lucene standard)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html), qui décompose le texte en éléments en suivant les règles de la [« Segmentation du texte Unicode »](https://unicode.org/reports/tr29/). Par ailleurs, l'analyseur standard convertit tous les caractères en minuscules. Les documents indexés et les termes de recherche sont analysés pendant l'indexation et le traitement des requêtes.  

Il est utilisé automatiquement sur chaque champ pouvant faire l’objet d’une recherche. Vous pouvez substituer l’analyseur par défaut champ par champ. Ces analyseurs alternatifs peuvent être un [analyseur linguistique](index-add-language-analyzers.md), un [analyseur personnalisé](index-add-custom-analyzers.md) ou un analyseur prédéfini figurant dans la [liste des analyseurs disponibles](index-add-custom-analyzers.md#AnalyzerTable).


## <a name="types-of-analyzers"></a>Types d’analyseurs

La liste suivante décrit les analyseurs disponibles dans la Recherche cognitive Azure.

| Category | Description |
|----------|-------------|
| [Analyseur Lucene standard](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Par défaut. Aucune spécification ou configuration n’est nécessaire. Cet analyseur à usage général est efficace pour la plupart des scénarios et des langues.|
| Analyseurs prédéfinis | Proposés comme produits finis destinés à être utilisés tels quels. <br/>Il en existe deux types : spécialisé et linguistique. Ils sont dits « prédéfinis », car vous les référencez par leur nom, sans aucune configuration ni personnalisation. <br/><br/>Les [analyseurs spécialisés (non dépendants de la langue)](index-add-custom-analyzers.md#AnalyzerTable) sont employés quand les entrées de texte nécessitent un traitement spécialisé ou minimal. Les analyseurs prédéfinis qui ne dépendent pas de la langue sont les suivants : **Asciifolding**, **Keyword**, **Pattern**, **Simple**, **Stop**, **Whitespace**.<br/><br/>Utilisez les [analyseurs linguistiques](index-add-language-analyzers.md) quand vous avez besoin d’une prise en charge linguistique avancée pour différentes langues. La Recherche cognitive Azure prend en charge 35 analyseurs linguistiques Lucene et 50 analyseurs de traitement en langage naturel Microsoft. |
|[Analyseurs personnalisés](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Ils renvoient à une configuration définie par l’utilisateur constituée d’une combinaison d’éléments existants, dont un générateur de jetons (obligatoire) et des filtres facultatifs (caractères ou jetons).|

Certains analyseurs prédéfinis, comme **Pattern** ou **Stop**, prennent en charge un ensemble limité d’options de configuration. Pour définir ces options, vous créez en réalité un analyseur personnalisé constitué de l’analyseur prédéfini et d’une des autres options documentées dans les [informations de référence sur les analyseurs prédéfinis](index-add-custom-analyzers.md#AnalyzerTable). Comme pour toute configuration personnalisée, nommez votre nouvelle configuration (par exemple, *MonAnalyseurSéquencesOctets*) pour la distinguer de l’analyseur Lucene Pattern.

## <a name="how-to-specify-analyzers"></a>Comment spécifier des analyseurs

1. (Pour les analyseurs personnalisés uniquement) Créez une section **analyseur** nommée dans la définition de l’index. Pour plus d’informations, consultez [Créer un index](https://docs.microsoft.com/rest/api/searchservice/create-index) et [Ajouter des analyseurs personnalisés](index-add-custom-analyzers.md).

2. Dans une [définition de champ](https://docs.microsoft.com/rest/api/searchservice/create-index) de l’index, attribuez à la propriété **analyzer** du champ le nom d’un analyseur cible, par exemple, `"analyzer" = "keyword"`. Les valeurs valides sont les suivantes : le nom d’un analyseur prédéfini, d’un analyseur linguistique ou d’un analyseur personnalisé qui a également été défini dans le schéma de l’index. Envisagez d’affecter un analyseur pendant la phase de définition de l’index avant que celui-ci soit créé dans le service.

3. Au lieu de définir une seule propriété **analyzer**, vous pouvez éventuellement définir des analyseurs différents pour l’indexation et l’interrogation à l’aide des paramètres de champ **indexAnalyzer** et **searchAnalyzer**. Vous utiliserez ainsi l’un ou l’autre de ces analyseurs pour la préparation et la récupération des données si l’une de ces activités nécessite une transformation spécifique et l’autre non.

> [!NOTE]
> Il n’est pas possible d’utiliser un autre [analyseur de langage](index-add-language-analyzers.md) au moment de l’indexation qu’au moment de la requête pour un champ. Cette fonctionnalité est réservée aux [analyseurs personnalisés](index-add-custom-analyzers.md). C’est la raison pour laquelle, si vous tentez de définir les propriétés **searchAnalyzer** ou **indexAnalyzer** sur le nom d’un analyseur de langage, l’API REST renvoie une réponse d’erreur. Vous devez utiliser la propriété **analyzer** à la place.

L’affectation de la propriété **analyzer** ou **indexAnalyzer** à un champ qui a déjà été créé physiquement n’est pas autorisée. Si cela n’est pas clair, vous trouverez dans le tableau suivant des détails sur les actions qui nécessitent une regénération et pourquoi.
 
 | Scénario | Impact | Étapes |
 |----------|--------|-------|
 | Ajouter un nouveau champ | minimal | Si le champ n’existe pas encore dans le schéma, il n’y a aucune révision de champ à effectuer, car il n’a pas encore de présence physique dans votre index. Vous pouvez utiliser [Mettre à jour l’index](https://docs.microsoft.com/rest/api/searchservice/update-index) pour ajouter un nouveau champ à un index existant, et [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) pour la remplir.|
 | Ajoutez une propriété **analyzer** ou **indexAnalyzer** à un champ indexé existant. | [regénération](search-howto-reindex.md) | L’index inversé doit être entièrement recréé pour ce champ et le contenu de ces champs doit être réindexé. <br/> <br/>Pour un index en cours de développement, [supprimez](https://docs.microsoft.com/rest/api/searchservice/delete-index) et [créez](https://docs.microsoft.com/rest/api/searchservice/create-index) l’index pour sélectionner la nouvelle définition de champ. <br/> <br/>Pour les index en production, vous devez retarder une regénération en créant un champ pour fournir la définition modifiée et commencer à l’utiliser à la place de l’ancienne. Utilisez [Mettre à jour l’index](https://docs.microsoft.com/rest/api/searchservice/update-index) pour incorporer le nouveau champ et [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) pour le remplir. Par la suite, pendant l’opération de maintenance planifiée de l’index, vous pouvez le nettoyer de façon à supprimer les champs obsolètes. |

## <a name="when-to-add-analyzers"></a>Quand ajouter des analyseurs

La phase de développement actif est le meilleur moment pour ajouter et affecter des analyseurs, là où la suppression et la recréation d’index sont des activités courantes.

À mesure que la définition d’un index se consolide, vous pouvez ajouter de nouvelles constructions d’analyse à l’index, mais vous devez transmettre l’indicateur **allowIndexDowntime** à [Mettre à jour l’index de mise](https://docs.microsoft.com/rest/api/searchservice/update-index) si vous voulez éviter cette erreur :

*« Mise à jour d’index non autorisée, car cette opération pourrait entraîner un temps d’arrêt. Pour ajouter de nouveaux analyseurs, générateurs de jetons, filtres de jetons ou filtres de caractères à un index existant, définissez le paramètre de requête « allowIndexDowntime » sur « true » dans la demande de mise à jour d’index. Notez que cette opération placera votre index hors connexion pendant au moins quelques secondes, ce qui entraînera l’échec de vos demandes d’indexation et de requête. Les performances et la disponibilité en écriture peuvent être altérées pendant plusieurs minutes après la mise à jour de l’index, voire plus longtemps pour les index de très grande taille ».*

Il en va de même au moment d’affecter un analyseur à un champ. Sachant qu’un analyseur fait partie intégrante de la définition du champ, vous pouvez l’ajouter uniquement pendant la création du champ. Si vous souhaitez ajouter des analyseurs à des champs existants, vous devez [supprimer et regénérer](search-howto-reindex.md) l’index ou ajouter un nouveau champ avec l’analyseur souhaité.

Comme indiqué, la variante **searchAnalyzer** constitue une exception. Sur les trois différentes façons de spécifier des analyseurs (**analyzer**, **indexAnalyzer**, **searchAnalyzer**), seul l’attribut **searchAnalyzer** peut être modifié dans un champ existant.

## <a name="recommendations-for-working-with-analyzers"></a>Suggestions pour utiliser des analyseurs

Cette section offre des conseils pour utiliser les analyseurs.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Un même analyseur pour les opérations de lecture-écriture, sauf besoins spécifiques

La Recherche cognitive Azure vous permet de spécifier différents analyseurs pour l’indexation et la recherche par le biais des paramètres de champ supplémentaires **indexAnalyzer** et **searchAnalyzer**. Par défaut, l’analyseur défini avec la propriété **analyzer** est utilisé pour l’indexation et la recherche. Si `analyzer` n’est pas spécifié, l’analyseur Lucene standard est utilisé par défaut.

En règle générale, il est préférable d’utiliser le même analyseur pour l’indexation et l’interrogation, sauf si des besoins spécifiques vous obligent à faire autrement. Veillez à effectuer des tests approfondis. Quand il existe une divergence de traitement de texte pendant la recherche et l’indexation, le risque est que les termes de la requête et les termes indexés ne correspondent pas si la configuration de l’analyseur de recherche et celle de l’analyseur d’indexation ne sont pas conformes.

### <a name="test-during-active-development"></a>Effectuer des tests pendant le développement actif

La substitution de l’analyseur standard nécessite une regénération de l’index. Si possible, choisissez les analyseurs à utiliser pendant le développement actif, avant de déployer l’index dans un environnement de production.

### <a name="inspect-tokenized-terms"></a>Examiner les termes sous forme de jetons

Si une recherche ne renvoie pas les résultats attendus, cela est très probablement dû aux différences de jetons entre les termes entrés dans la requête et les termes sous forme de jetons présents dans l’index. Si les jetons ne sont pas identiques, les correspondances ne sont pas détectées. Pour examiner les résultats du générateur de jetons, nous vous recommandons d’utiliser [l’API d’analyse](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) comme outil d’investigation. La réponse se compose de jetons qui sont générés par un analyseur spécifique.

<a name="examples"></a>

## <a name="rest-examples"></a>Exemples REST

Les exemples ci-dessous montrent des définitions d’analyseur pour quelques scénarios clés.

+ [Exemple d’analyseur personnalisé](#Custom-analyzer-example)
+ [Exemple d’affectation d’analyseurs à un champ](#Per-field-analyzer-assignment-example)
+ [Combinaison d’analyseurs pour l’indexation et la recherche](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Exemple d’analyseur linguistique](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Exemple d’analyseur personnalisé

Cet exemple montre une définition d’analyseur avec des options personnalisées. Les options personnalisées pour les filtres de caractères, les générateurs de jetons et les filtres de jetons sont spécifiées séparément comme des constructions nommées, puis elles sont référencées dans la définition de l’analyseur. Les éléments prédéfinis sont utilisés tels quels et sont référencés par leur nom.

Si nous suivons cet exemple :

* Les analyseurs sont une propriété de la classe d’un champ pouvant faire l’objet d’une recherche.
* Un analyseur personnalisé fait partie d’une définition d’index. Il peut faire l’objet d’une légère personnalisation (par exemple, la personnalisation d’une option d’un filtre) ou d’une personnalisation à plusieurs endroits.
* Dans ce cas, l’analyseur personnalisé est « my_analyzer », qui utilise alors le générateur de jetons standard personnalisé « my_standard_tokenizer », et deux filtres de jetons : le filtre « lowercase » et le filtre asciifolding personnalisé « my_asciifolding ».
* Il définit également les deux filtres de caractères personnalisés « map_dash » et « remove_whitespace ». Le premier remplace tous les tirets par des traits de soulignement et le second supprime tous les espaces. Les espaces doivent être encodés en UTF-8 dans les règles de mappage. Les filtres de caractères sont appliqués avant la segmentation du texte en unités lexicales et affectent les jetons qui en résultent (les tirets et les espaces provoquent l'arrêt du générateur de jetons, mais pas les traits de soulignement).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
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
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
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
~~~~

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Exemple d’affectation d’analyseur par champ

L’analyseur standard est celui qui est utilisé par défaut. Supposons que vous souhaitiez remplacer l’analyseur par défaut par un autre analyseur prédéfini, tel que l’analyseur de pattern. Si vous ne définissez pas d’options personnalisées, vous devez uniquement le spécifier par son nom dans la définition du champ.

L’élément « analyzer » remplace l’analyseur standard champ après champ. Aucun remplacement global n’est possible. Dans cet exemple, `text1` utilise l’analyseur de pattern, et `text2`, qui ne spécifie pas d’analyseur, utilise celui par défaut.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Combinaison d’analyseurs pour les opérations d’indexation et de recherche

Les API comprennent des attributs d’index supplémentaires qui permettent de spécifier des analyseurs différents pour l’indexation et la recherche. Les attributs **searchAnalyzer** et **indexAnalyzer** doivent être spécifiés sous forme de paire, en remplacement de l’attribut **analyzer**.


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Exemple d’analyseur linguistique

Les champs qui contiennent des chaînes dans différentes langues peuvent utiliser un analyseur linguistique, tandis que les autres champs conservent l’analyseur par défaut (ou utilisent un autre analyseur prédéfini ou personnalisé). Si vous utilisez un analyseur linguistique, vous devez l’utiliser à la fois pour les opérations d’indexation et de recherche. Les champs qui utilisent un analyseur linguistique ne peuvent pas avoir des analyseurs différents pour l’indexation et la recherche.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="c-examples"></a>Exemples C#

Si vous utilisez les exemples de code du SDK .NET, vous pouvez ajouter ces exemples pour utiliser ou configurez des analyseurs.

+ [Attribuer un analyseur intégré](#Assign-a-language-analyzer)
+ [Configurer un analyseur](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Attribuer un analyseur de langage

Tout analyseur qui est utilisé tel quel, sans configuration, est spécifié sur une définition de champ. Il n’est pas obligatoire de créer une construction de l’analyseur. 

Cet exemple attribue les analyseurs Anglais et Français de Microsoft aux champs de description. C’est un extrait de code issu d’une définition plus grande de l’index des hôtels, créé à l’aide de la classe Hotel dans le fichier hotels.cs de l’exemple [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Appelez la classe [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) en spécifiant le type [AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) pour fournir un analyseur de texte pris en charge dans la Recherche cognitive Azure.

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Définir un analyseur personnalisé

Lorsqu’une personnalisation ou configuration est requise, vous devez ajouter une construction de l’analyseur à un index. Une fois que vous la définissez, vous pouvez l’ajouter à la définition de champ comme illustré dans l’exemple précédent.

Créez un objet [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet). Pour plus d’exemples, consultez [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>Étapes suivantes

+ Lisez notre explication complète du [fonctionnement de la recherche en texte intégral dans la Recherche cognitive Azure](search-lucene-query-architecture.md). Cet article utilise des exemples pour expliquer les comportements qui, au premier abord, peuvent sembler contraires à la logique.

+ Essayez une syntaxe de requête supplémentaire à partir de la section Exemple [Rechercher des documents](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) ou à partir de la [syntaxe de requête simple](query-simple-syntax.md) dans l’Explorateur de recherche du portail.

+ Découvrez comment appliquer des [analyseurs lexicaux propres au langage](index-add-language-analyzers.md).

+ [Configurez des analyseurs personnalisés](index-add-custom-analyzers.md) pour un traitement minimal ou pour un traitement spécialisé sur certains champs.

## <a name="see-also"></a>Voir aussi

 [API REST de recherche de documents](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Syntaxe de requête simple](query-simple-syntax.md) 

 [Syntaxe de requête complète Lucene](query-lucene-syntax.md) 
 
 [Traiter les résultats de recherche](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
