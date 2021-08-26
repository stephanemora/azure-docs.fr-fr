---
title: Création d'un index
titleSuffix: Azure Cognitive Search
description: Présente les concepts et les outils d’indexation dans Recherche cognitive Azure, notamment les définitions de schéma et la structure physique des données.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: cdfadc895de3af0f79c30a067f3e5376bfa8873b
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769101"
---
# <a name="creating-search-indexes-in-azure-cognitive-search"></a>Création d’index de recherche dans Recherche cognitive Azure

Recherche cognitive Azure stocke le contenu pouvant faire l’objet d’une recherche utilisé pour le texte intégral et les requêtes filtrées dans un *index de recherche*. Un index est défini par un schéma et enregistré dans le service, l’importation des données se faisant dans un deuxième temps. 

Les index contiennent des *documents de recherche*. Conceptuellement, un document correspond à une unité de données pouvant faire l’objet d’une recherche dans un index. Un détaillant peut posséder un document pour chaque produit, un organisme de presse peut posséder un document par article, et ainsi de suite. Pour comparer avec des éléments de base de données plus familiers, un *index de recherche* correspond à une *table*, et les *documents* équivalent plus ou moins aux *lignes* d’une table.

## <a name="whats-an-index-schema"></a>Qu’est-ce qu’un schéma d’index ?

La structure physique d’un index est déterminée par le schéma. La collection « fields » correspond généralement à la majeure partie de l’index, dans laquelle chaque champ est nommé, se voit attribuer un [type de données](/rest/api/searchservice/Supported-data-types) et est pourvu de comportements autorisés qui déterminent son utilisation.

```json
{
  "name": "name_of_index, unique across the service",
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
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

Parmi les autres éléments, citons les [suggesteurs](index-add-suggesters.md), les [profils de scoring](index-add-scoring-profiles.md), les [analyseurs utilisés](search-analyzers.md) pour traiter les chaînes en jetons selon des règles linguistiques ou d’autres caractéristiques prises en charge par l’analyseur et des paramètres [CORS (Cross-Origin Remote Scripting)](#corsoptions).

## <a name="choose-a-client"></a>Choisir un client

Il existe plusieurs façons de créer un index de recherche. Nous vous recommandons le portail Azure ou les kits SDK pour les premières étapes de développement et les tests de preuve de concept.

Pendant le développement, prévoyez des régénérations fréquentes. Comme les structures physiques sont créées dans le service, il est nécessaire de [supprimer et de recréer les index](search-howto-reindex.md) pour la plupart des modifications apportées à une définition de champ existante. Vous pouvez envisager de travailler sur une partie de vos données pour regénérer plus rapidement.

### <a name="permissions"></a>Autorisations

Toutes les opérations liées à un index de recherche, notamment les requêtes GET et leur définition, nécessitent une [clé d’API d’administrateur](search-security-api-keys.md) sur la requête.

### <a name="limits"></a>Limites

Tous les [niveaux de service limitent](search-limits-quotas-capacity.md#index-limits) le nombre d’objets que vous pouvez créer. Si vous expérimentez le niveau Gratuit, vous ne pouvez avoir que trois index à la fois.

### <a name="use-azure-portal-to-create-a-search-index"></a>Utiliser Portail Azure pour créer un index de recherche

Le portail propose deux options pour créer un index de recherche : [**l’Assistant Importer des données**](search-import-data-portal.md) et **Ajouter un index** qui fournit des champs pour spécifier un schéma d’index. L’Assistant permet d’effectuer des opérations supplémentaires en créant également un indexeur, une source de données et en chargeant des données. Si c’est plus qu’il n’en faut, vous pouvez simplement utiliser l’option **Ajouter un index** ou une autre approche.

La capture d’écran suivante montre où vous pouvez trouver **Ajouter un index** dans le portail. **Importer des données** se trouve juste à côté.

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="Add index command" border="true":::

> [!Tip]
> La conception d’index via le portail applique des exigences et des règles de schéma pour des types de données spécifiques, telles que l’interdiction de la recherche en texte intégral sur les champs numériques. Une fois que vous disposez d’un index exploitable, vous pouvez copier le fichier JSON à partir du portail et l’ajouter à votre solution.

### <a name="use-a-rest-client"></a>Utiliser un client REST

Postman et Visual Studio Code (avec une extension pour Recherche cognitive Azure) peuvent tous deux fonctionner comme client d’index de recherche. En utilisant l’un ou l’autre de ces outils, vous pouvez vous connecter à votre service de recherche et envoyer des requêtes [Create index (REST)](/rest/api/searchservice/create-index). Il existe de nombreux tutoriels et exemples qui illustrent le fonctionnement des clients REST pour la création d’objets. 

Commencez par l’un de ces articles pour en savoir plus sur chaque client :

+ [Créer un index de recherche à l’aide de REST et de Postman](search-get-started-rest.md)
+ [Bien démarrer avec Visual Studio Code et Recherche cognitive Azure](search-get-started-vs-code.md)

Reportez-vous aux [opérations d’index (REST)](/rest/api/searchservice/index-operations) pour obtenir de l’aide dans la formulation de requêtes d’index.

### <a name="use-an-sdk"></a>Utiliser un Kit de développement logiciel (SDK)

Pour Recherche cognitive, les Kits de développement logiciel (SDK) Azure implémentent des fonctionnalités généralement disponibles. Ainsi, vous pouvez utiliser n’importe lequel des Kits de développement logiciel (SDK) pour créer un index de recherche. Ils fournissent tous un **SearchIndexClient** qui contient des méthodes pour la création et la mise à jour d’index.

| Azure SDK | Client | Exemples |
|-----------|--------|----------|
| .NET | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [azure-search-dotnet-samples/quickstart/v11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) |
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [Index](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations.py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

## <a name="define-fields"></a>Définir des champs

Un document de recherche est défini par la collection `fields`. Vous aurez besoin de champs pour les requêtes et les clés. Vous aurez probablement également besoin de champs pour prendre en charge les filtres, les facettes et les tris. Vous pouvez également avoir besoin de champs pour des données qu’un utilisateur ne voit jamais, par exemple des champs pour les marges bénéficiaires ou les promotions marketing que vous pouvez utiliser pour modifier le rang de recherche.

Un champ de type Edm.String doit être désigné comme clé de document. Il est utilisé pour identifier de manière unique chaque document de recherche, et respecte la casse. Vous pouvez récupérer un document par sa clé pour remplir une page de détails.

Si les données entrantes sont de nature hiérarchique, attribuez le type de données [Type complexe](search-howto-complex-data-types.md) pour représenter les structures imbriquées. L’exemple de jeu de données intégré, Hotels, illustre des types complexes utilisant une adresse (contenant plusieurs sous-champs) qui entretient une relation un-à-un avec chaque hôtel, et une collection complexe Rooms, où plusieurs chambres sont associées à chaque hôtel. 

Attribuez des analyseurs aux champs de type chaîne avant la création de l’index. Procédez de même pour les suggesteurs si vous souhaitez activer l’autocomplétion sur des champs spécifiques.

<a name="index-attributes"></a>

### <a name="attributes"></a>Attributs

Les attributs d’un champ déterminent son utilisation, par exemple s’il est utilisé dans la recherche en texte intégral, la navigation par facettes, les opérations de tri et ainsi de suite. 

Les champs de type chaîne sont souvent marqués avec les attributs « Possibilité de recherche » et « Récupérable ». Vous pouvez utiliser les champs « Triable », « Filtrable » et « À choix multiple » pour affiner les résultats de la recherche.

|Attribut|Description|  
|---------------|-----------------|  
|« Possibilité de recherche » |Recherche en texte intégral, avec analyse lexicale (césure de mots) lors de l’indexation. Si vous définissez un champ avec possibilité de recherche sur une valeur comme « journée ensoleillée », cette valeur est fractionnée au niveau interne en jetons individuels « journée » et « ensoleillée ». Pour en savoir plus, consultez la rubrique [Fonctionnement de la recherche en texte intégral](search-lucene-query-architecture.md).|  
|« Filtrable » |Référencé dans les requêtes $filter. Les champs filtrables de type `Edm.String` ou `Collection(Edm.String)` ne font pas l’objet d’une analyse lexicale, les comparaisons ne concernent donc que les correspondances exactes. Par exemple, si vous définissez un champ avec la valeur « journée ensoleillée », la requête `$filter=f eq 'sunny'` ne renverra aucune correspondance, contrairement à `$filter=f eq 'sunny day'`. |  
|« Triable » |Le système trie les résultats par score par défaut, mais vous pouvez configurer le tri en fonction des champs des documents. Les champs de type `Collection(Edm.String)` ne sont pas « triables ». |  
|« À choix multiple » |Généralement utilisé dans une présentation des résultats de recherche qui inclut un nombre de correspondances par catégorie (par exemple, les hôtels dans une ville spécifique). Cette option ne peut pas être utilisée avec des champs de type `Edm.GeographyPoint`. Les champs de type `Edm.String` qui sont « filtrables », « triables » ou « à choix multiple » ne peuvent pas dépasser 32 Ko de longueur. Pour plus d’informations, consultez l’article [Créer un index (API REST)](/rest/api/searchservice/create-index).|  
|« Clé » |Identificateur unique des documents dans l’index. Un seul champ doit être choisi comme champ clé et il doit être de type `Edm.String`.|  
|« Récupérable » |Définit si le champ peut être retourné dans un résultat de recherche. Cet attribut est utile quand vous voulez utiliser un champ (comme *profit margin*) comme mécanisme de filtre, de tri ou de score, mais que vous ne voulez pas qu’il soit visible par l’utilisateur final. Il doit être `true` for `key` .|  

Même si vous pouvez ajouter de nouveaux champs à tout moment, les définitions de champ existantes sont verrouillées pour toute la durée de vie de l’index. C’est pourquoi les développeurs utilisent généralement le portail pour créer des index simples, tester des idées ou rechercher une définition de paramètre. Il est plus efficace d’effectuer des itérations fréquentes sur la conception d’un index si vous suivez une approche basée sur du code pour reconstruire l’index facilement.

> [!NOTE]
> Les API que vous utilisez pour créer un index ont différents comportements par défaut. Pour les [API REST](/rest/api/searchservice/Create-Index), la plupart des attributs sont activés par défaut (par exemple, « Possibilité de recherche » et « Récupérable » sont actifs pour les champs de type chaîne) et vous n’avez généralement besoin de les définir que si vous voulez les désactiver. Pour le Kit de développement logiciel (SDK) .NET, l’inverse est vrai. Pour les propriétés que vous ne définissez pas explicitement, l’option par défaut désactive le comportement de recherche correspondante, sauf si vous l’activez de façon spécifique.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Attributs et taille de l’index (implications en matière de stockage)

La taille d’un index est déterminée par la taille des documents que vous chargez, plus la configuration de l’index, par exemple si vous incluez des suggesteurs, et la façon dont vous définissez des attributs sur des champs individuels. 

La capture d’écran suivante illustre les caractéristiques du stockage d’index résultant des différentes combinaisons d’attributs. L’index est basé sur l’**exemple d’index de biens immobiliers**, que vous pouvez créer facilement à l’aide de l’Assistant Importer des données. Bien que les schémas de l’index ne soient pas montrés, vous pouvez en déduire les attributs d’après le nom de l’index. Par exemple, pour l’index *realestate-searchable*, seul l’attribut « Possibilité de recherche » est sélectionné ; pour l’index *realestate-retrievable*, seul l’index « Récupérable » est sélectionné, et ainsi de suite.

![Taille de l’index en fonction de la sélection de l’attribut](./media/search-what-is-an-index/realestate-index-size.png "Taille de l’index en fonction de la sélection de l’attribut")

Bien que ces variantes d’index soient artificielles, nous pouvons nous y reporter pour nous faire une idée de la façon dont les attributs affectent le stockage. Le paramètre « Récupérable » fait-il croître l’index ? Non. L’ajout de champs à un **suggesteur** fait-il croître l’index ? Oui. 

Le fait de rendre un champ filtrable ou triable augmente également la consommation de stockage, car les champs filtrés et triés ne sont pas sous forme de jeton, de sorte que les séquences de caractères peuvent être mises en correspondance textuellement.

L’impact des [analyseurs](search-analyzers.md) n’est pas non plus pris en compte dans le tableau ci-dessus. Si vous utilisez le générateur de jetons edgeNgram pour stocker des séquences de caractères verbatim (a, ab, abc, abcd), la taille de l’index sera supérieure à si vous avez utilisé un analyseur standard.

> [!Note]
> L’architecture de stockage est considérée comme un détail d’implémentation de Recherche cognitive Azure et est susceptible d’évoluer sans préavis. Il n’est pas garanti que le comportement actuel persistera dans l’avenir.

<a name="corsoptions"></a>

## <a name="about-corsoptions"></a>À propos de `corsOptions`

Les schémas d’index incluent une section pour la définition de `corsOptions`. Le code JavaScript côté client ne peut pas appeler d’API par défaut, car le navigateur empêche toutes les requêtes cross-origin. Pour autoriser les requêtes cross-origin dans l'index, activez CORS (partage des ressources cross-origin) en définissant l'attribut **corsOptions**. Pour des raisons de sécurité, seules les API de requête prennent en charge CORS. 

Les options suivantes peuvent être définies pour CORS :

+ **allowedOrigins** (obligatoire) : il s'agit de la liste des origines pouvant accéder à l’index. Cela signifie que le code JavaScript distribué à partir de ces origines est autorisé à interroger l’index (s'il fournit la bonne clé API). Chaque origine se présente généralement sous la forme `protocol://<fully-qualified-domain-name>:<port>`, bien que `<port>` soit souvent omis. Pour plus d'informations, voir [Partage des ressources cross-origin (Wikipédia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing).

  Si vous voulez autoriser l'accès à toutes les origines, incluez uniquement l’élément `*` dans le tableau **allowedOrigins**. Si *cette pratique est déconseillée pour les services de recherche de production*, elle est souvent utile pour le développement et le débogage.

+ **maxAgeInSeconds** (facultatif) : les navigateurs utilisent cette valeur pour déterminer la durée (en secondes) de mise en cache des réponses CORS préliminaires. Il doit s'agir d'un entier non négatif. Plus cette valeur est importante, meilleures sont les performances, mais plus il faut de temps pour que les modifications apportées à la stratégie CORS prennent effet. Si la valeur n'est pas définie, une durée par défaut de 5 minutes est utilisée.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez acquérir de l’expérience en créant un index en utilisant presque n’importe quel exemple ou procédure pas à pas pour Recherche cognitive. Pour commencer, vous pouvez choisir l’un des démarrages rapides à partir de la table des matières.

Mais vous souhaiterez également vous familiariser avec les méthodologies de chargement d’un index avec des données. Les stratégies de définition d’index et d’importation de données sont définies en tandem. Les articles suivants fournissent plus d’informations sur le chargement d’un index.

+ [Vue d’ensemble de l’importation des données](search-what-is-data-import.md)

+ [Ajout, mise à jour ou suppression de documents (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 