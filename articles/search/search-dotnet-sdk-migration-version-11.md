---
title: Mettre à niveau vers la version 11 du Kit de développement logiciel (SDK) .NET
titleSuffix: Azure Cognitive Search
description: Migrez le code vers le Kit de développement logiciel (SDK) .NET Recherche cognitive Azure version 11 à partir de versions antérieures. Découvrez les nouveautés et les modifications de code nécessaires.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: c5f070f59df69bb186041af450e6ca922469d960
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98043742"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Mettre à niveau vers la version 11 du Kit de développement logiciel (SDK) .NET Recherche cognitive Azure

Si vous utilisez la version 10.0 ou une version antérieure du [Kit SDK .NET](/dotnet/api/overview/azure/search), cet article vous aidera à effectuer une mise à niveau pour utiliser la version 11 et la bibliothèque de client **Azure.Search.Documents**.

La version 11 est une bibliothèque de client entièrement repensée, publiée par l’équipe de développement de Kits de développement logiciel (SDK) d’Azure (les versions précédentes ont été produites par l’équipe de développement de Recherche cognitive Azure). La bibliothèque a été repensée pour une plus grande cohérence avec les autres bibliothèques de client Azure, en dépendant d’[Azure.Core](/dotnet/api/azure.core) et de [System.Text.Json](/dotnet/api/system.text.json) et en implémentant des approches familières pour les tâches courantes.

Voici quelques-unes des principales différences que vous noterez dans la nouvelle version :

+ Un package et une bibliothèque au lieu de plusieurs
+ Un nouveau nom du package : `Azure.Search.Documents` au lieu de `Microsoft.Azure.Search`
+ Trois clients au lieu de deux : `SearchClient`, `SearchIndexClient`, `SearchIndexerClient`
+ Des différences d’affectation de noms dans une plage d’API et de petites différences structurelles qui simplifient certaines tâches

En plus de cet article, vous pouvez passer en revue le [journal des modifications](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) pour obtenir la liste des modifications apportées au Kit de développement logiciel (SDK) .NET version 11.

## <a name="package-and-library-consolidation"></a>Consolidation des packages et des bibliothèques

La version 11 regroupe plusieurs packages et bibliothèques en un seul package et une seule bibliothèque. Après la migration, vous aurez moins de bibliothèques à gérer.

+ [Package Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [Informations de référence sur l’API pour la bibliothèque de client](/dotnet/api/overview/azure/search.documents-readme)

## <a name="client-differences"></a>Différences de clients

Le cas échéant, le tableau suivant mappe les bibliothèques de client entre les deux versions.

| Étendue des opérations | Microsoft.Azure.Search&nbsp;(v10) | Azure.Search.Documents&nbsp;(v11) |
|---------------------|------------------------------|------------------------------|
| Client utilisé pour les requêtes et pour remplir un index. | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](/dotnet/api/azure.search.documents.searchclient) |
| Client utilisé pour les index, les analyseurs et les mappages de synonymes | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| Client utilisé pour les indexeurs, les sources de données et les ensembles de compétences | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**nouveau**)](/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient` existe dans les deux versions, mais prend en charge des éléments différents. Dans la version 10, `SearchIndexClient` crée des index et d’autres objets. Dans la version 11, `SearchIndexClient` fonctionne avec les index existants. Pour éviter toute confusion lors de la mise à jour du code, gardez à l’esprit l’ordre dans lequel les références de client sont mises à jour. Le fait de suivre l’ordre des [étapes de mise à niveau](#UpgradeSteps) devrait aider à atténuer les éventuels problèmes de remplacement de chaînes.

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>Affectation de noms et autres différences d’API

Outre les différences de clients (notées précédemment et donc omises ici), plusieurs autres API ont été renommées et, dans certains cas, redéfinies. Les différences entre les noms de classe sont résumées ci-dessous. Cette liste n’est pas exhaustive, mais elle regroupe les modifications d’API par tâche, ce qui peut être utile pour les révisions de blocs de code spécifiques. Pour obtenir la liste détaillée des mises à jour des API, consultez le [journal des modifications ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) pour `Azure.Search.Documents` sur GitHub.

### <a name="authentication-and-encryption"></a>Authentification et chiffrement

| Version 10 | Équivalent dans la version 11 |
|------------|-----------------------|
| [SearchCredentials](/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey` (existait dans le [Kit de développement logiciel (SDK) de préversion](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) en tant que fonctionnalité généralement disponible) | [SearchResourceEncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>Index, analyseurs et mappages de synonymes

| Version 10 | Équivalent dans la version 11 |
|------------|-----------------------|
| [Index](/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [Champ](/dotnet/api/microsoft.azure.search.models.field) | [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analyseur](/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) (ainsi que `AnalyzerName` vers `LexicalAnalyzerName`) |
| [AnalyzeRequest](/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (ainsi que `StandardTokenizerV2` vers `LuceneStandardTokenizerV2`) |
| [TokenInfo](/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Générateur de jetons](/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (ainsi que `TokenizerName` vers `LexicalTokenizerName`) |
| [SynonymMap.Format](/dotnet/api/microsoft.azure.search.models.synonymmap.format) | Aucun. Supprimez les références à `Format`. |

Les définitions de champ sont rationalisées : [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield), [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) et [ComplexField](/dotnet/api/azure.search.documents.indexes.models.complexfield) sont de nouvelles API permettant de créer des définitions de champ.

### <a name="indexers-datasources-skillsets"></a>Indexeurs, sources de données et ensembles de compétences

| Version 10 | Équivalent dans la version 11 |
|------------|-----------------------|
| [Indexeur](/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Compétence](/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Ensemble de compétences](/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [DataSourceType](/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>Importation des données

| Version 10 | Équivalent dans la version 11 |
|------------|-----------------------|
| [IndexAction](/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>Définitions et résultats des requêtes

| Version 10 | Équivalent dans la version 11 |
|------------|-----------------------|
| [DocumentSearchResult](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](/dotnet/api/azure.search.documents.models.searchresult-1) ou [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1), selon que le résultat est un document unique ou plusieurs documents. |
| [DocumentSuggestResult](/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions](/dotnet/api/azure.search.documents.searchoptions)  |

### <a name="json-serialization"></a>Sérialisation JSON

Par défaut, le Kit de développement logiciel (SDK) Azure utilise [System.Text.Json](/dotnet/api/system.text.json) pour la sérialisation JSON, en se basant sur les capacités de ces API pour gérer les transformations de texte précédemment implémentées par le biais d’une classe [SerializePropertyNamesAsCamelCaseAttribute](/dotnet/api/microsoft.azure.search.models.serializepropertynamesascamelcaseattribute) native, qui n’a pas d’équivalent dans la nouvelle bibliothèque.

Pour sérialiser les noms de propriété en camelCase, vous pouvez utiliser l’attribut [JsonPropertyNameAttribute](/dotnet/api/system.text.json.serialization.jsonpropertynameattribute) (semblable à [cet exemple](https://github.com/Azure/azure-sdk-for-net/tree/d263f23aa3a28ff4fc4366b8dee144d4c0c3ab10/sdk/search/Azure.Search.Documents#use-c-types-for-search-results)).

Vous pouvez également définir une stratégie [JsonNamingPolicy](/dotnet/api/system.text.json.jsonnamingpolicy) fournie dans [JsonSerializerOptions](/dotnet/api/system.text.json.jsonserializeroptions). L’exemple de code System.Text.Json suivant, extrait du [fichier Lisez-moi Microsoft.Azure.Core.Spatial](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial/README.md#deserializing-documents), montre l’utilisation de camelCase sans avoir à attribuer une valeur à chaque propriété :

```csharp
// Get the Azure Cognitive Search endpoint and read-only API key.
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
AzureKeyCredential credential = new AzureKeyCredential(Environment.GetEnvironmentVariable("SEARCH_API_KEY"));

// Create serializer options with our converter to deserialize geographic points.
JsonSerializerOptions serializerOptions = new JsonSerializerOptions
{
    Converters =
    {
        new MicrosoftSpatialGeoJsonConverter()
    },
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase
};

SearchClientOptions clientOptions = new SearchClientOptions
{
    Serializer = new JsonObjectSerializer(serializerOptions)
};

SearchClient client = new SearchClient(endpoint, "mountains", credential, clientOptions);
Response<SearchResults<Mountain>> results = client.Search<Mountain>("Rainier");
```

Si vous utilisez Newtonsoft.Json pour la sérialisation JSON, vous pouvez transmettre des stratégies globales d’affectation de noms à l’aide d’attributs similaires ou en utilisant les propriétés sur [JsonSerializerSettings](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonSerializerSettings.htm). Pour obtenir un exemple équivalent à celui ci-dessus, consultez l’[exemple de désérialisation des documents](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md) dans le fichier Lisez-moi de Newtonsoft.Json.


<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>Contenu de la version 11

Chaque version d’une bibliothèque de client Recherche cognitive Azure cible une version correspondante de l’API REST. L’API REST est considérée comme fondamentale pour le service, avec des Kits de développement logiciel (SDK) individuels encapsulant une version de l’API REST. En tant que développeur .NET, il peut être utile de passer en revue la [documentation sur l’API REST](/rest/api/searchservice/) si vous souhaitez plus d’informations sur des objets ou des opérations spécifiques.

La version 11 cible le [service de recherche 2020-06-30](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json). Étant donné que la version 11 est également une nouvelle bibliothèque de client entièrement créée, l’essentiel de l’effort de développement s’est concentré sur l’équivalence avec la version 10, certaines fonctionnalités de l’API REST étant encore en attente de prise en charge.

La version 11.0 prend entièrement en charge les opérations et objets suivants :

+ Création et gestion d’index
+ Création et gestion de mappage de synonymes
+ Tous les types de requêtes et la syntaxe (à l’exception des filtres géospatiaux)
+ Objets et opérations de l’indexeur pour l’indexation des sources de données Azure, y compris les sources de données et ensembles de compétences

La version 11.1 ajoute ce qui suit :

+ [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) (ajouté à la version 11.1)
+ [Propriété du sérialiseur](/dotnet/api/azure.search.documents.searchclientoptions.serializer) (ajoutée à la version 11.1) pour prendre en charge la sérialisation personnalisée

### <a name="pending-features"></a>Fonctionnalités en attente

Les fonctionnalités suivantes de la version 10 ne sont pas encore disponibles dans la version 11. Si vous avez besoin de ces fonctionnalités, reportez la migration jusqu’à ce qu’elles soient prises en charge.

+ Types géospatiaux
+ [Base de connaissances](knowledge-store-concept-intro.md)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Procédure de mise à niveau

Les étapes suivantes vous permettent de commencer une migration de code en parcourant la première série de tâches requises, notamment en ce qui concerne les références de client.

1. Installez le [package Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) en cliquant avec le bouton droit sur les références de votre projet et en sélectionnant « Gérer les packages NuGet… » dans Visual Studio.

1. Remplacez les directives d’utilisation pour Microsoft.Azure.Search par ce qui suit :

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Pour les classes qui requièrent la sérialisation JSON, remplacez `using Newtonsoft.Json` par `using System.Text.Json.Serialization`.

1. Révisez le code d’authentification du client. Dans les versions précédentes, vous deviez utiliser les propriétés sur l’objet client pour définir la clé API (par exemple, la propriété [SearchServiceClient.Credentials](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials)). Dans la version actuelle, utilisez la classe [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) pour transmettre la clé en tant qu’informations d’identification. Ainsi, si nécessaire, vous pouvez mettre à jour la clé API, sans créer de nouveaux objets clients.

   Les propriétés du client ont été rationalisées pour `Endpoint`, `ServiceName`et `IndexName` (le cas échéant). L’exemple suivant utilise la classe [URI](/dotnet/api/system.uri) du système pour fournir le point de terminaison et la classe [Environnement](/dotnet/api/system.environment) pour lire la valeur de clé :

   ```csharp
   Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
   AzureKeyCredential credential = new AzureKeyCredential(
      Environment.GetEnvironmentVariable("SEARCH_API_KEY"));
   SearchIndexClient indexClient = new SearchIndexClient(endpoint, credential);
   ```

1. Ajoutez de nouvelles références de client pour les objets liés à l’indexeur. Si vous utilisez des indexeurs, des sources de source ou des ensembles de compétences, modifiez les références de client en [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). Ce client est nouveau dans la version 11 et n’a pas d’antécédent.

1. Révisez les collections et les listes. Dans le nouveau Kit de développement logiciel (SDK), toutes les listes sont en lecture seule afin d’éviter les problèmes en aval si la liste contient des valeurs NULL. La modification du code consiste à ajouter des éléments à une liste. Par exemple, au lieu d’assigner des chaînes à une propriété Select, vous devez les ajouter comme suit :

   ```csharp
   var options = new SearchOptions
    {
       SearchMode = SearchMode.All,
       IncludeTotalCount = true
    };

    // Select fields to return in results.
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Tags");
    options.Select.Add("Rooms");
    options.Select.Add("Rating");
    options.Select.Add("LastRenovationDate");
   ```

   Select, Facets, SearchFields, SourceFields, ScoringParameters et OrderBy sont toutes des listes qui doivent maintenant être reconstruites.

1. Mettez à jour les références de client pour les requêtes et l’importation de données. Les instances de [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) doivent être remplacées par [SearchClient](/dotnet/api/azure.search.documents.searchclient). Pour éviter toute confusion dans les noms, veillez à intercepter toutes les instances avant de passer à l’étape suivante.

1. Mettez à jour les références de client pour les objets index, mappage de synonymes et analyseur. Les instances de [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) doivent être remplacées par [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient). 

1. Pour le reste de votre code, mettez à jour les classes, les méthodes et les propriétés pour utiliser les API de la nouvelle bibliothèque. La section relative aux [différences d’affectation de noms](#naming-differences) est un point de départ, mais vous pouvez également consulter le [journal des modifications](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md).

   Si vous avez des difficultés à trouver des API équivalentes, nous vous suggérons de signaler le problème sur [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) afin que nous puissions améliorer la documentation ou examiner le problème.

1. Régénérez la solution. Après avoir corrigé les erreurs de build ou les avertissements, vous pouvez apporter des modifications supplémentaires à votre application pour bénéficier des [nouvelles fonctionnalités](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes"></a>Changements cassants

Compte tenu des modifications radicales apportées aux bibliothèques et aux API, une mise à niveau vers la version 11 n’est pas anodine et constitue un changement cassant dans la mesure où votre code ne sera plus rétrocompatible avec la version 10 et les versions antérieures. Pour une analyse approfondie des différences, consultez le [journal des modifications](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) pour `Azure.Search.Documents`.

En termes de mises à jour de version de service, lorsque les modifications de code de la version 11 sont liées à la fonctionnalité existante (et pas simplement à la refactorisation des API), vous noterez les modifications de comportement suivantes :

+ L’[algorithme de classement BM25](index-ranking-similarity.md) remplace l’algorithme de classement précédent par une technologie plus récente. Les nouveaux services utiliseront cet algorithme automatiquement. Pour les services existants, vous devez définir des paramètres pour utiliser le nouvel algorithme.

+ Les [résultats ordonnés](search-query-odata-orderby.md) pour les valeurs Null ont été modifiés dans cette version ; les valeurs Null apparaissent en premier si le tri est `asc` et en dernier si le tri est `desc`. Si vous avez écrit du code pour gérer la manière dont les valeurs Null sont triées, vous devez revoir et éventuellement supprimer ce code s’il n’est plus nécessaire.

## <a name="next-steps"></a>Étapes suivantes

+ [Package Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Exemples sur GitHub](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Informations de référence sur l’API Azure.Search.Document](/dotnet/api/overview/azure/search.documents-readme)