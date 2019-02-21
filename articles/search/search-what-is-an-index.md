---
title: Créer une définition et des concepts d’index – Recherche Azure
description: Introduction aux termes et aux concepts des index dans la Recherche Azure, y compris les composants et la structure physique.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/01/2019
ms.custom: seodec2018
ms.openlocfilehash: 77f4b597ad4b87db7e720dd57191c6b192a4c93b
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000948"
---
# <a name="create-a-basic-index-in-azure-search"></a>Créer un index de base dans la Recherche Azure

Dans Recherche Azure, un *index* est une banque permanente de *documents* et d'autres éléments utilisés pour la recherche filtrée et en texte intégral sur un service Recherche Azure. Conceptuellement, un document correspond à une unité de données pouvant faire l’objet d’une recherche dans un index. Par exemple, un détaillant de commerce électronique peut posséder un document pour chaque article qu’il vend, un organisme d’information peut posséder un document par article, et ainsi de suite. Pour comparer avec des éléments de base de données plus familiers, d’un point de vue conceptuel, un *index* est similaire à une *table*, et les *documents* équivalent plus ou moins aux *lignes* d’une table.

Lorsque vous ajoutez ou chargez un index, la Recherche Azure crée des structures physiques basées sur le schéma fourni. Par exemple, si l’un des champs de votre index est marqué comme utilisable dans une requête, un index inversé est créé pour ce champ. Lorsque vous ajouterez ou chargerez des documents, ou que vous soumettrez des requêtes de recherche à la Recherche Azure, vous enverrez les demandes à un index spécifique de votre service de recherche. Le processus consistant à charger des champs ayant valeur de documents se nomme *indexation* ou ingestion des données.

Vous pouvez créer un index avec le portail, [l’API REST](search-create-index-rest-api.md) ou le [Kit SDK .NET](search-create-index-dotnet.md).

## <a name="components-of-an-index"></a>Composants d’un index

Schématiquement, un index Recherche Azure se compose des éléments suivants. 

La [*collection de champs*](#fields-collection) correspond généralement à la majeure partie de l’index, dans laquelle chaque champ est nommé, tapé et pourvu de comportements autorisés qui déterminent son utilisation. Il existe d’autres éléments, comme les [suggesteurs](#suggesters), les [profils de score](#scoring-profiles), les [analyseurs](#analyzers) avec les composants pour prendre en charge la personnalisation et les options [CORS](#cors).

```json
{  
  "name": (optional on PUT; required on POST) "name_of_index",  
  "fields": [  
    {  
      "name": "name_of_field",  
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",  
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),  
      "filterable": true (default) | false,  
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),  
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),  
      "key": true | false (default, only Edm.String fields can be keys),  
      "retrievable": true (default) | false,  
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }  
  ],  
  "suggesters": [  
    {  
      "name": "name of suggester",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": ["field1", "field2", ...]  
    }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "name of scoring profile",  
      "text": (optional, only applies to searchable fields) {  
        "weights": {  
          "searchable_field_name": relative_weight_value (positive #'s),  
          ...  
        }  
      },  
      "functions": (optional) [  
        {  
          "type": "magnitude | freshness | distance | tag",  
          "boost": # (positive number used as multiplier for raw score != 1),  
          "fieldName": "...",  
          "interpolation": "constant | linear (default) | quadratic | logarithmic",  
          "magnitude": {  
            "boostingRangeStart": #,  
            "boostingRangeEnd": #,  
            "constantBoostBeyondRange": true | false (default)  
          },  
          "freshness": {  
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)  
          },  
          "distance": {  
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)  
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)  
          },  
          "tag": {  
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)  
          }  
        }  
      ],  
      "functionAggregation": (optional, applies only when functions are specified)   
        "sum (default) | average | minimum | maximum | firstMatching"  
    }  
  ],  
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",  
  "corsOptions": (optional) {  
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],  
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)  
  }  
}
```

## <a name="fields-collection-and-attribution"></a>Collection et attribution de champs
Lorsque vous définissez votre schéma, vous devez spécifier le nom, le type et les attributs de chaque champ de votre index. Le type de champ classifie les données stockées dans ce champ. Les attributs sont définis sur des champs individuels pour spécifier la façon dont le champ est utilisé. Les tableaux ci-après énumèrent les types et les attributs que vous pouvez spécifier.

### <a name="data-types"></a>Types de données
| Type | Description |
| --- | --- |
| *Edm.String* |Texte pouvant éventuellement être tokenisé pour la recherche en texte intégral (césure de mots, recherche de radical, etc). |
| *Collection(Edm.String)* |Liste de chaînes pouvant être éventuellement tokenisées pour la recherche en texte intégral. En théorie, il n’existe pas de limite supérieure quant au nombre d’éléments d’une collection, mais la limite supérieure de 16 Mo sur la taille de charge utile s’applique aux collections. |
| *Edm.Boolean* |Contient des valeurs true/false. |
| *Edm.Int32* |Valeurs entières 32 bits. |
| *Edm.Int64* |Valeurs entières 64 bits. |
| *Edm.Double* |Données numériques à double précision. |
| *Edm.DateTimeOffset* |Dates et heures représentées au format OData V4 (par exemple, `yyyy-MM-ddTHH:mm:ss.fffZ` ou `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |Point représentant un emplacement géographique de la planète. |

Pour plus d’informations sur les types de données pris en charge par le service Recherche Azure, consultez [cet article](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types).

### <a name="index-attributes"></a>Attributs d’index
| Attribut | Description |
| --- | --- |
| *Clé* |Chaîne fournissant un ID unique à chaque document utilisé pour rechercher des documents. Chaque index doit avoir une clé. Un seul champ peut être la clé, et son type doit être défini sur Edm.String. |
| *Affichable dans les résultats d’une recherche* |Définit si un champ peut être retourné dans un résultat de recherche. |
| *Filtrable* |Permet d’utiliser le champ dans des requêtes de filtre. |
| *Triable* |Permet à une requête de trier les résultats de recherche à l’aide de ce champ. |
| *À choix multiple* |Permet d’utiliser un champ pour le filtrage autonome dans une structure de [navigation par facettes](search-faceted-navigation.md) par un utilisateur. En général, les champs contenant des valeurs répétitives que vous pouvez utiliser pour regrouper plusieurs documents (par exemple, plusieurs documents appartenant à une seule marque ou catégorie de service) sont les mieux adaptés en tant que facettes. |
| *Possibilité de recherche* |Indique que le champ peut faire l’objet d’une recherche en texte intégral. |

Pour plus d’informations sur les attributs d’index du service Recherche Azure, consultez [cet article](https://docs.microsoft.com/rest/api/searchservice/Create-Index).

## <a name="suggesters"></a>Générateurs de suggestions
Un suggesteur est une section du schéma qui définit quels champs d’un index sont utilisés pour prendre en charge l’autocomplétion et les requêtes prédictives dans les recherches. En général, les chaînes de recherche partielles sont envoyées aux Suggestions (API REST du service Recherche Azure) pendant que l’utilisateur tape une requête de recherche, et l’API retourne un ensemble d’expressions suggérées. Un générateur de suggestions que vous définissez dans l’index détermine quels champs sont utilisés pour générer les termes de recherche type-ahead. Pour plus d’informations sur la configuration, voir [Ajouter des suggesteurs](index-add-suggesters.md).

## <a name="scoring-profiles"></a>Profils de score

Un profil de score est une section du schéma qui définit des comportements de score personnalisés permettant de faire apparaître certains éléments plus haut dans les résultats de la recherche. Les profils de calcul de score sont constitués de pondérations et de fonctions de champ. Pour les utiliser, vous spécifiez un profil par nom dans la chaîne de requête.

Un profil de score par défaut fonctionne en arrière-plan pour calculer un score de recherche pour chaque élément d’un jeu de résultats. Vous pouvez utiliser le profil de score interne et sans nom. Vous pouvez aussi définir defaultScoringProfile pour utiliser par défaut un profil personnalisé, appelé chaque fois qu’aucun profil personnalisé n’est spécifié dans la chaîne de requête.

Pour plus d'informations, voir [Ajouter des profils de score](index-add-scoring-profiles.md) .

## <a name="analyzers"></a>Analyseurs

L’élément analyseurs définit le nom de l’analyseur linguistique à utiliser pour le champ. Pour connaître l'ensemble des valeurs autorisées, voir [Analyseurs linguistiques dans la Recherche Azure](index-add-language-analyzers.md). Cette option n’est utilisable qu’avec les champs pouvant faire l’objet d’une recherche ; elle ne peut être associée ni à **searchAnalyzer** ni à **indexAnalyzer**. Une fois l'analyseur choisi, il ne peut pas être modifié pour le champ.

## <a name="cors"></a>CORS

Le code JavaScript côté client ne peut pas appeler d’API par défaut, car le navigateur empêche toutes les requêtes cross-origin. Pour autoriser les requêtes cross-origin dans l'index, activez CORS (partage des ressources cross-origin) en définissant l'attribut **corsOptions**. Pour des raisons de sécurité, seules les API de requête prennent en charge CORS. 

Les options suivantes peuvent être définies pour CORS :

+ **allowedOrigins** (obligatoire) : il s'agit de la liste des origines pouvant accéder à l’index. Cela signifie que le code JavaScript distribué à partir de ces origines est autorisé à interroger l’index (s'il fournit la bonne clé API). Chaque origine se présente généralement sous la forme `protocol://<fully-qualified-domain-name>:<port>`, bien que `<port>` soit souvent omis. Pour plus d'informations, voir [Partage des ressources cross-origin (Wikipédia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing).

  Si vous voulez autoriser l'accès à toutes les origines, incluez uniquement l’élément `*` dans le tableau **allowedOrigins**. Si *cette pratique est déconseillée pour les services de recherche de production*, elle est souvent utile pour le développement et le débogage.

+ **maxAgeInSeconds** (facultatif) : les navigateurs utilisent cette valeur pour déterminer la durée (en secondes) de mise en cache des réponses CORS préliminaires. Il doit s'agir d'un entier non négatif. Plus cette valeur est importante, meilleures sont les performances, mais plus il faut de temps pour que les modifications apportées à la stratégie CORS prennent effet. Si la valeur n'est pas définie, une durée par défaut de 5 minutes est utilisée.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous comprenez la composition de l’index, vous pouvez créer votre premier index sur le portail.

> [!div class="nextstepaction"]
> [Ajouter un index (portail)](search-create-index-portal.md)