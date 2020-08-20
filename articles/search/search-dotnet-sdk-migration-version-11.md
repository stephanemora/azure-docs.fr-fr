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
ms.date: 08/05/2020
ms.openlocfilehash: 03d40dcaeaefe01fecbc201cf28dc20c8634af9d
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926669"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Mettre à niveau vers la version 11 du Kit de développement logiciel (SDK) .NET Recherche cognitive Azure

Si vous utilisez la version 10.0 ou une version antérieure du [Kit de développement logiciel (SDK) .NET](https://docs.microsoft.com/dotnet/api/overview/azure/search), cet article vous aidera à le mettre à niveau vers la version 11.

La version 11 est une bibliothèque de client entièrement repensée, publiée par l’équipe de développement de Kits de développement logiciel (SDK) d’Azure (les versions précédentes ont été produites par l’équipe de développement de Recherche cognitive Azure). La bibliothèque a été repensée pour une plus grande cohérence avec les autres bibliothèques de client Azure, en dépendant d’[Azure.Core](https://docs.microsoft.com/dotnet/api/azure.core) et de [System.Text.Json](https://docs.microsoft.com/dotnet/api/system.text.json) et en implémentant des approches familières pour les tâches courantes.

Voici quelques-unes des principales différences que vous noterez dans la nouvelle version :

+ Un package et une bibliothèque au lieu de plusieurs
+ Un nouveau nom du package : `Azure.Search.Documents` au lieu de `Microsoft.Azure.Search`
+ Trois clients au lieu de deux : `SearchClient`, `SearchIndexClient`, `SearchIndexerClient`
+ Des différences d’affectation de noms dans une plage d’API et de petites différences structurelles qui simplifient certaines tâches

## <a name="package-and-library-consolidation"></a>Consolidation des packages et des bibliothèques

La version 11 regroupe plusieurs packages et bibliothèques en un seul package et une seule bibliothèque. Après la migration, vous aurez moins de bibliothèques à gérer.

+ [Package Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [Informations de référence sur l’API pour la bibliothèque de client](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)

## <a name="client-differences"></a>Différences de clients

Le cas échéant, le tableau suivant mappe les bibliothèques de client entre les deux versions.

| Étendue des opérations | Microsoft.Azure.Search&nbsp;(v10) | Azure.Search.Documents&nbsp;(v11) |
|---------------------|------------------------------|------------------------------|
| Client utilisé pour les requêtes et pour remplir un index. | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) |
| Client utilisé pour les index, les analyseurs et les mappages de synonymes | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| Client utilisé pour les indexeurs, les sources de données et les ensembles de compétences | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**nouveau**)](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient` existe dans les deux versions, mais prend en charge des éléments différents. Dans la version 10, `SearchIndexClient` crée des index et d’autres objets. Dans la version 11, `SearchIndexClient` fonctionne avec les index existants. Pour éviter toute confusion lors de la mise à jour du code, gardez à l’esprit l’ordre dans lequel les références de client sont mises à jour. Le fait de suivre l’ordre des [étapes de mise à niveau](#UpgradeSteps) devrait aider à atténuer les éventuels problèmes de remplacement de chaînes.

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>Affectation de noms et autres différences d’API

Outre les différences de clients (notées précédemment et donc omises ici), plusieurs autres API ont été renommées et, dans certains cas, redéfinies. Les différences entre les noms de classe sont résumées ci-dessous. Cette liste n’est pas exhaustive, mais elle regroupe les modifications d’API par tâche, ce qui peut être utile pour les révisions de blocs de code spécifiques. Pour obtenir la liste détaillée des mises à jour des API, consultez le [journal des modifications ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) pour `Azure.Search.Documents` sur GitHub.

### <a name="authentication-and-encryption"></a>Authentification et chiffrement

| Version 10 | Équivalent dans la version 11 |
|------------|-----------------------|
| [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey` (existait dans le [Kit de développement logiciel (SDK) de préversion](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) en tant que fonctionnalité généralement disponible) | [SearchResourceEncryptionKey](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>Index, analyseurs et mappages de synonymes

| Version 10 | Équivalent dans la version 11 |
|------------|-----------------------|
| [Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [Champ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field) | [SearchField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analyseur](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicalanalyze) (ainsi que `AnalyzerName` vers `LexicalAnalyzerName`) |
| [AnalyzeRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (ainsi que `StandardTokenizerV2` vers `LuceneStandardTokenizerV2`) |
| [TokenInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Générateur de jetons](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (ainsi que `TokenizerName` vers `LexicalTokenizerName`) |
| [SynonymMap.Format](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap.format) | Aucun. Supprimez les références à `Format`. |

Les définitions de champ sont rationalisées : [SearchableField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchablefield), [SimpleField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.simplefield) et [ComplexField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.complexfield) sont de nouvelles API permettant de créer des définitions de champ.

### <a name="indexers-datasources-skillsets"></a>Indexeurs, sources de données et ensembles de compétences

| Version 10 | Équivalent dans la version 11 |
|------------|-----------------------|
| [Indexeur](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Compétence](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Ensemble de compétences](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskillse) |
| [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>Importation des données

| Version 10 | Équivalent dans la version 11 |
|------------|-----------------------|
| [IndexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>Définitions et résultats des requêtes

| Version 10 | Équivalent dans la version 11 |
|------------|-----------------------|
| [DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresult-1) ou [SearchResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresults-1), selon que le résultat est un document unique ou plusieurs documents. |
| [DocumentSuggestResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions)  |

<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>Contenu de la version 11

Chaque version d’une bibliothèque de client Recherche cognitive Azure cible une version correspondante de l’API REST. L’API REST est considérée comme fondamentale pour le service, avec des Kits de développement logiciel (SDK) individuels encapsulant une version de l’API REST. En tant que développeur .NET, il peut être utile de passer en revue la [documentation sur l’API REST](https://docs.microsoft.com/rest/api/searchservice/) si vous souhaitez plus d’informations sur des objets ou des opérations spécifiques.

La version 11 cible le [service de recherche 2020-06-30](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json). Étant donné que la version 11 est également une nouvelle bibliothèque de client entièrement créée, l’essentiel de l’effort de développement s’est concentré sur l’équivalence avec la version 10, certaines fonctionnalités de l’API REST étant encore en attente de prise en charge.

La version 11 prend entièrement en charge les opérations et objets suivants :

+ Création et gestion d’index
+ Création et gestion de mappage de synonymes
+ Tous les types de requêtes et la syntaxe (à l’exception des filtres géospatiaux)
+ Objets et opérations de l’indexeur pour l’indexation des sources de données Azure, y compris les sources de données et ensembles de compétences

### <a name="pending-features"></a>Fonctionnalités en attente

Les fonctionnalités suivantes de la version 10 ne sont pas encore disponibles dans la version 11. Si vous utilisez ces fonctionnalités, reportez la migration jusqu’à ce qu’elles soient prises en charge.

+ Types géospatiaux
+ [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) (même si vous pouvez utiliser [cette solution de contournement](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/tests/Samples/FieldBuilder/FieldBuilder.cs)).
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

1. Remplacez [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) par [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential).

1. Mettez à jour les références de client pour les objets liés à l’indexeur. Si vous utilisez des indexeurs, des sources de source ou des ensembles de compétences, modifiez les références de client en [SearchIndexerClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient). Ce client est nouveau dans la version 11 et n’a pas d’antécédent.

1. Mettez à jour les références de client pour les requêtes et l’importation de données. Les instances de [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) doivent être remplacées par [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient). Pour éviter toute confusion dans les noms, veillez à intercepter toutes les instances avant de passer à l’étape suivante.

1. Mettez à jour les références de client pour les objets index, indexeur, mappage de synonymes et analyseur. Les instances de [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) doivent être remplacées par [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchindexclient). 

1. Autant que possible, mettez à jour les classes, les méthodes et les propriétés pour utiliser les API de la nouvelle bibliothèque. La section relative aux [différences d’affectation de noms](#naming-differences) est un point de départ, mais vous pouvez également consulter le [journal des modifications](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md).

   Si vous avez des difficultés à trouver des API équivalentes, nous vous suggérons de signaler le problème sur [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) afin que nous puissions améliorer la documentation ou examiner le problème.

1. Régénérez la solution. Après avoir corrigé les erreurs de build ou les avertissements, vous pouvez apporter des modifications supplémentaires à votre application pour bénéficier des [nouvelles fonctionnalités](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-11"></a>Changements cassants dans la version 11

Compte tenu des modifications radicales apportées aux bibliothèques et aux API, une mise à niveau vers la version 11 n’est pas anodine et constitue un changement cassant dans la mesure où votre code ne sera plus rétrocompatible avec la version 10 et les versions antérieures. Pour une analyse approfondie des différences, consultez le [journal des modifications](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) pour `Azure.Search.Documents`.

En ce qui concerne les versions de service, le passage de la version 10 à la version 11 introduit les changements de comportement suivants : 

+ L’[algorithme de classement BM25](index-ranking-similarity.md) remplace l’algorithme de classement précédent par une technologie plus récente. Les nouveaux services utiliseront cet algorithme automatiquement. Pour les services existants, vous devez définir des paramètres pour utiliser le nouvel algorithme.

+ Les [résultats ordonnés](search-query-odata-orderby.md) pour les valeurs Null ont été modifiés dans cette version ; les valeurs Null apparaissent en premier si le tri est `asc` et en dernier si le tri est `desc`. Si vous avez écrit du code pour gérer la manière dont les valeurs Null sont triées, vous devez revoir et éventuellement supprimer ce code s’il n’est plus nécessaire.

## <a name="next-steps"></a>Étapes suivantes

+ [Package Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Exemples sur GitHub](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Informations de référence sur l’API Azure.Search.Document](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)