---
title: Interroger des données dans un index Recherche Azure en C# (SDK .NET) - Recherche Azure
description: Exemple de code C# pour la création d’une requête de recherche dans Recherche Azure. Ajouter des paramètres de recherche pour filtrer et trier les résultats de la recherche.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: 1194407122123797c2564c96ac452b9582b017a4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264945"
---
# <a name="quickstart-3---query-an-azure-search-index-in-c"></a>Démarrage rapide : 3 - Interroger un index Recherche Azure en C#

Cet article vous montre comment interroger [un index Recherche Azure](search-what-is-an-index.md) à l’aide de C# et du [SDK .NET](https://aka.ms/search-sdk). La recherche de documents dans votre index s’effectue en exécutant ces tâches :

> [!div class="checklist"]
> * Créer un objet [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) à connecter à votre index de recherche avec des droits de lecture seule.
> * Créer un objet [`SearchParameters`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet) contenant la définition de la recherche ou du filtre.
> * Appeler la méthode `Documents.Search` sur `SearchIndexClient` pour envoyer des requêtes à un index.

## <a name="prerequisites"></a>Prérequis

[Chargez un index Recherche Azure](search-import-data-dotnet.md) avec les données de l’exemple hotels.

Obtenez une clé de requête utilisée pour l’accès en lecture seule aux documents. Jusqu’à présent, vous avez utilisé une clé API d’administration pour créer des objets et du contenu sur un service de recherche. Toutefois, pour prendre en charge les requêtes dans les applications, nous recommandons fortement d’utiliser une clé de requête. Pour obtenir des instructions, consultez [Créer une clé de requête](search-security-api-keys.md#create-query-keys).

## <a name="create-a-client"></a>Créer un client
Créez une instance de la classe `SearchIndexClient` afin de lui donner une clé de requête pour l’accès en lecture seule (par opposition aux droits d’accès en écriture conférés via le `SearchServiceClient` utilisé dans la leçon précédente).

Cette classe dispose de plusieurs constructeurs. Celui qui vous intéresse prend le nom du service de recherche, le nom de l’index et un objet [`SearchCredentials`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials?view=azure-dotnet) comme paramètres. `SearchCredentials` wrappe votre clé API.

Le code ci-dessous crée un `SearchIndexClient` pour l’index « hotels » à l’aide de valeurs pour le nom du service de recherche et la clé API stockées dans le fichier config de l’application (`appsettings.json` dans le cas de l’[exemple d’application](https://aka.ms/search-dotnet-howto)) :

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` a une propriété [`Documents`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient.documents?view=azure-dotnet). Cette propriété fournit toutes les méthodes dont vous avez besoin pour interroger les index Azure Search.

## <a name="construct-searchparameters"></a>Construire les SearchParameters
La recherche à l’aide du Kit de développement logiciel (SDK) .NET est aussi simple que l’appel de la méthode `Documents.Search` sur votre `SearchIndexClient`. Cette méthode accepte quelques paramètres, notamment le texte de recherche, avec un objet `SearchParameters` qui peut être utilisé pour affiner la requête.

### <a name="types-of-queries"></a>Types de requête
Les deux principaux [types de requête](search-query-overview.md#types-of-queries) que vous utilisez sont `search` et `filter`. Une requête `search` recherche un ou plusieurs termes dans tous les champs de *recherche* de votre index. Une requête `filter` permet d’évaluer une expression booléenne dans tous les champs *filtrables* d’un index. Vous pouvez utiliser des recherches et des filtres conjointement ou séparément.

Les filtres et les recherches sont exécutés à l’aide de la méthode `Documents.Search` . Une requête de recherche peut être transmise dans le paramètre `searchText`, tandis qu’une expression de filtre peut être transmise dans la propriété `Filter` de la classe `SearchParameters`. Pour filtrer sans effectuer de recherche, transmettez simplement `"*"` pour le paramètre `searchText`. Pour effectuer une recherche sans appliquer de filtre, ne définissez pas la propriété `Filter` et ne transmettez pas une instance `SearchParameters`.

### <a name="example-queries"></a>Exemples de requêtes
L’exemple de code suivant montre différentes manières d’interroger l’index « hotels » défini dans [Créer un index Recherche Azure en C#](search-create-index-dotnet.md#DefineIndex). Notez que les documents renvoyés avec les résultats de recherche sont des instances de la classe `Hotel`, qui a été définie dans [Importer des données dans un index Recherche Azure en C#](search-import-data-dotnet.md#construct-indexbatch). L’exemple de code utilise une méthode `WriteDocuments` pour générer les résultats de recherche dans la console. Cette méthode est décrite dans la section suivante.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="handle-search-results"></a>Traiter les résultats de recherche
La méthode `Documents.Search` renvoie un objet `DocumentSearchResult` qui contient les résultats de la requête. L’exemple figurant dans la section précédente utilisait une méthode appelée `WriteDocuments` pour générer les résultats de recherche dans la console :

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Voici les résultats des requêtes de la section précédente, en supposant que l’index « hotels » est rempli avec les données de l’exemple :

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
```

L’exemple de code ci-dessus utilise la console pour générer les résultats de recherche. De même, vous devez afficher les résultats de recherche dans votre propre application. Pour obtenir un exemple du rendu des résultats de la recherche dans une application web MVC, consultez le [projet DotNetSample](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) sur GitHub.

## <a name="next-steps"></a>Étapes suivantes

Si vous ne l’avez pas encore fait, examinez l’exemple de code dans [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) sur GitHub ainsi que [Guide pratique pour utiliser la Recherche Azure à partir d’une application .NET](search-howto-dotnet-sdk.md) pour des descriptions plus détaillées de l’exemple de code. 