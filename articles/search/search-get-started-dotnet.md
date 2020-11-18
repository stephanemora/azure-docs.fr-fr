---
title: 'Démarrage rapide : Créer un index de recherche en .NET'
titleSuffix: Azure Cognitive Search
description: Ce guide de démarrage rapide C# explique comment créer un index, charger des données et exécuter des requêtes à l’aide de la bibliothèque de client Azure.Search.Documents.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f3e43a6b72d8de25de3220a9a6ac4e0b3986a467
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701804"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Démarrage rapide : Créer un index de recherche à l’aide de la bibliothèque de client Azure.Search.Documents

Utilisez la nouvelle [bibliothèque de client Azure.Search.Documents (version 11)](/dotnet/api/overview/azure/search.documents-readme) pour créer une application console .NET Core en C#, qui crée, charge et interroge un index de recherche.

[Téléchargez le code source](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) pour commencer avec un projet terminé ou suivez les étapes décrites dans cet article pour créer votre propre projet.

> [!NOTE]
> Vous recherchez une version antérieure ? Consultez [Créer un index de recherche à l’aide de Microsoft.Azure.Search v10](search-get-started-dotnet-v10.md) à la place.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez disposer des outils et services suivants :

+ Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/).

+ Service Recherche cognitive Azure. [Créez un service](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Vous pouvez utiliser un service gratuit pour ce guide de démarrage rapide. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), toute édition. L’exemple de code a été testé sur l’édition Community gratuite de Visual Studio 2019.

+ [Package NuGet Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/)

## <a name="set-up-your-project"></a>Configuration de votre projet

Rassemblez les informations de connexion du service, puis démarrez Visual Studio pour créer un projet d’application console qui peut s’exécuter sur .NET Core.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Copier une clé et un point de terminaison

Les appels au service nécessitent un point de terminaison d’URL et une clé d’accès pour chaque requête. Dans un premier temps, recherchez la clé et l’URL de l’API à ajouter à votre projet. Vous allez spécifier les deux valeurs lors de la création du client dans une étape ultérieure.

1. [Connectez-vous au portail Azure](https://portal.azure.com/), puis dans la page **Vue d’ensemble** du service de recherche, récupérez l’URL. Voici un exemple de point de terminaison : `https://mydemo.search.windows.net`.

2. Dans **Paramètres** > **Clés**, procurez-vous une clé d’administration pour disposer de droits complets sur le service, indispensables pour créer ou supprimer des objets. Il existe deux clés, primaire et secondaire, interchangeables. Vous pouvez utiliser celle de votre choix.

   ![Obtenir un point de terminaison et une clé d’accès HTTP](media/search-get-started-rest/get-url-key.png "Obtenir un point de terminaison et une clé d’accès HTTP")

Toutes les demandes nécessitent une clé API sur chaque demande envoyée à votre service. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

### <a name="install-the-nuget-package"></a>Installez le package NuGet

Une fois le projet créé, ajoutez la bibliothèque de client. Le [package Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) consiste en une bibliothèque de client qui fournit toutes les API utilisées pour utiliser un service de recherche dans .NET.

1. Dans **Outils** > **Gestionnaire de package NuGet**, sélectionnez **Gérer les packages NuGet pour la solution...** . 

1. Cliquez sur **Parcourir**.

1. Recherchez `Azure.Search.Documents` et sélectionnez la version 11.0 ou ultérieure.

1. Cliquez sur **Installer** à droite pour ajouter l’assembly à vos projet et solution.

### <a name="create-a-search-client"></a>Créer un client de recherche

1. Dans **Program.cs**, remplacez l’espace de noms par `AzureSearch.SDK.Quickstart.v11`, puis ajoutez les directives `using` suivantes.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Créez deux clients : [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) crée l’index et [SearchClient](/dotnet/api/azure.search.documents.searchclient) charge et interroge un index existant. Tous deux ont besoin du point de terminaison de service et d’une clé API d’administration pour l’authentification avec des droits de création/suppression.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart-v11";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

       // Create a SearchIndexClient to send create/delete index commands
       Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
       AzureKeyCredential credential = new AzureKeyCredential(apiKey);
       SearchIndexClient idxclient = new SearchIndexClient(serviceEndpoint, credential);

       // Create a SearchClient to load and query documents
       SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 – Créer un index

Ce guide de démarrage rapide crée un index Hotels que vous allez charger avec des données sur des hôtels et sur lequel vous allez effectuer des requêtes. Dans cette étape, définissez les champs de l’index. Chaque définition de champ comprend un nom, un type de données et des attributs qui déterminent la façon dont le champ est utilisé.

Dans cet exemple, des méthodes synchrones de la bibliothèque Azure.Search.Documents sont utilisées à des fins de simplicité et de lisibilité. En revanche, dans des scénarios de production, vous devez utiliser des méthodes asynchrones pour maintenir la scalabilité et la réactivité de votre application. Par exemple, vous pouvez utiliser [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) au lieu de [CreateIndex.](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex)

1. Ajoutez une définition de classe vide à votre projet : **Hotel.cs**

1. Dans **Hotel.cs**, définissez la structure d’un document d’hôtel.

    ```csharp
    using System;
    using System.Text.Json.Serialization;

    namespace AzureSearch.SDK.Quickstart.v11
    {
        public class Hotel
        {
            [JsonPropertyName("hotelId")]
            public string Id { get; set; }

            [JsonPropertyName("hotelName")]
            public string Name { get; set; }

            [JsonPropertyName("hotelCategory")]
            public string Category { get; set; }

            [JsonPropertyName("baseRate")]
            public Int32 Rate { get; set; }

            [JsonPropertyName("lastRenovationDate")]
            public DateTime Updated { get; set; }
        }
    }
    ```

1. Dans **Program.cs**, créez un objet [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex), puis appelez la méthode [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) pour exprimer l’index dans votre service de recherche.

   ```csharp
    // Define an index schema using SearchIndex
    // Create the index using SearchIndexClient
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };

    Console.WriteLine("{0}", "Creating index...\n");
    idxclient.CreateIndex(index);
   ```

Les attributs du champ déterminent la façon dont il est utilisé dans une application. Par exemple, l’attribut `IsFilterable` doit être assigné à chaque champ qui prend en charge une expression de filtre.

Dans la bibliothèque de client Azure.Search.Documents, vous pouvez utiliser [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) et [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) pour simplifier les définitions de champs. Les deux sont des dérivés d’un [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) et peuvent potentiellement simplifier votre code :

+ `SimpleField` peut être n’importe quel type de données, ne peut jamais faire l’objet d’une recherche (il est ignoré pour les requêtes de recherche de texte intégral) et peut être récupéré (il n’est pas masqué). Les autres attributs sont désactivés par défaut, mais peuvent être activés. Vous pouvez utiliser un `SimpleField` pour les ID de document ou les champs utilisés seulement dans des filtres, des facettes ou des profils de scoring. Dans ce cas, veillez à appliquer tous les attributs nécessaires pour le scénario, comme `IsKey = true` pour un ID de document. Pour plus d’informations, consultez [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) dans le code source.

+ `SearchableField` doit être une chaîne, et peut toujours faire l’objet d’une recherche et d’une récupération. Les autres attributs sont désactivés par défaut, mais peuvent être activés. Comme ce type de champ peut faire l’objet d’une recherche, il prend en charge les synonymes et l’ensemble complet des propriétés de l’analyseur. Pour plus d’informations, consultez [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) dans le code source.

Que vous utilisiez l’API `SearchField` de base ou un des modèles d’assistance, vous devez activer explicitement les attributs de filtre, de facette et de tri. Par exemple, les attributs [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) et [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) doivent être explicitement attribués, comme illustré dans l’exemple ci-dessus. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – Charger des documents

Le service Recherche cognitive Azure recherche dans le contenu stocké dans le service. Au cours de cette étape, vous allez charger des documents JSON conformes à l’index de l’hôtel que vous venez de créer.

Dans Recherche cognitive Azure, les documents de recherche sont des structures de données qui sont à la fois des entrées pour l’indexation et des sorties de requêtes. Selon une source de données externe, les entrées de documents peuvent être des lignes dans une base de données, des objets blob dans le Stockage Blob ou des documents JSON sur le disque. Dans cet exemple, nous prenons un raccourci et incorporons des documents JSON pour cinq hôtels dans le code lui-même. 

Lors du chargement de documents, vous devez utiliser un objet [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1). Un objet `IndexDocumentsBatch` contient une collection d’[Actions](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions), chacune contenant un document et une propriété qui indiquent à Recherche cognitive Azure l’action à effectuer ([upload, merge, delete et mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. Dans **Program.cs**, créez un tableau des documents et des actions d’index, puis passez le tableau à `IndexDocumentsBatch`. Les documents ci-dessous sont conformes à l’index hotels-quickstart-v11, tel que défini par la classe hotel.

    ```csharp
    // Load documents (using a subset of fields for brevity)
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(new Hotel { Id = "78", Name = "Upload Inn", Category = "hotel", Rate = 279, Updated = new DateTime(2018, 3, 1, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "54", Name = "Breakpoint by the Sea", Category = "motel", Rate = 162, Updated = new DateTime(2015, 9, 12, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "39", Name = "Debug Motel", Category = "motel", Rate = 159, Updated = new DateTime(2016, 11, 11, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "48", Name = "NuGet Hotel", Category = "hotel", Rate = 238, Updated = new DateTime(2016, 5, 30, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "12", Name = "Renovated Ranch", Category = "motel", Rate = 149, Updated = new DateTime(2020, 1, 24, 7, 0, 0) }));

    IndexDocumentsOptions idxoptions = new IndexDocumentsOptions { ThrowOnAnyError = true };

    Console.WriteLine("{0}", "Loading index...\n");
    srchclient.IndexDocuments(batch, idxoptions);
    ```

    Une fois que vous avez initialisé l’objet [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1), vous pouvez l’envoyer à l’index en appelant [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) sur votre objet [SearchClient](/dotnet/api/azure.search.documents.searchclient).

1. Étant donné qu’il s’agit d’une application console qui exécute toutes les commandes de manière séquentielle, ajoutez un délai d’attente de 2 secondes entre l’indexation et les requêtes.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    Le retard de 2 secondes compense l’indexation, qui est asynchrone, afin que tous les documents puissent être indexés avant l’exécution des requêtes. Le codage dans un retard n’est nécessaire que dans les démonstrations, les tests et les exemples d’applications.

## <a name="3---search-an-index"></a>3 – Rechercher dans un index

Vous pouvez obtenir les résultats de la requête dès que le premier document est indexé, mais les tests réels de votre index doivent attendre que tous les documents soient indexés.

Cette section ajoute deux éléments de fonctionnalité : logique de requête et résultats. Pour les requêtes, utilisez la méthode [Search](/dotnet/api/azure.search.documents.searchclient.search). Cette méthode prend le texte de recherche (la chaîne de requête) ainsi que d’autres [options](/dotnet/api/azure.search.documents.searchoptions).

La classe [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) représente les résultats.

1. Dans **Program.cs**, créez une méthode WriteDocuments qui affiche les résultats de la recherche sur la console.

    ```csharp
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> response in searchResults.GetResults())
        {
            Hotel doc = response.Document;
            var score = response.Score;
            Console.WriteLine($"Name: {doc.Name}, Type: {doc.Category}, Rate: {doc.Rate}, Last-update: {doc.Updated}, Score: {score}");
        }

        Console.WriteLine();
    }
    ```

1. Créez une méthode RunQueries pour exécuter des requêtes et retourner des résultats. Les résultats sont des objets Hotel.

    ```csharp
    private static void RunQueries(SearchClient srchclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on the term 'motel' and list the relevance score for each match...\n");

        options = new SearchOptions()
        {
            Filter = "",
            OrderBy = { "" }
        };

        response = srchclient.Search<Hotel>("motel", options);
        WriteDocuments(response);

        Console.WriteLine("Query #2: Find hotels where 'type' equals hotel...\n");

        options = new SearchOptions()
        {
            Filter = "hotelCategory eq 'hotel'",
        };

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);

        Console.WriteLine("Query #3: Filter on rates less than $200 and sort by when the hotel was last updated...\n");

        options = new SearchOptions()
        {
            Filter = "baseRate lt 200",
            OrderBy = { "lastRenovationDate desc" }
        };

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    }
    ```

1. Ajoutez RunQueries à `Main()`.

    ```csharp
    Console.WriteLine("Starting queries...\n");
    RunQueries(srchclient);
    ```

Cet exemple montre les deux [façons de mettre en correspondance des termes dans une requête](search-query-overview.md#types-of-queries) : la recherche en texte intégral et les filtres.

+ Une recherche en texte intégral effectue une requête sur un ou plusieurs termes dans des champs de votre index offrant une possibilité de recherche. La première requête est une recherche en texte intégral. La recherche en texte intégral produit des scores de pertinence utilisés pour classer les résultats.

+ Un filtre est une expression booléenne évaluée sur des champs [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) dans un index. Les requêtes de filtre incluent ou excluent des valeurs. Par conséquent, aucune note de pertinence n’est associée à une requête de filtre. Les deux dernières requêtes illustrent une recherche de filtre.

Vous pouvez utiliser la recherche en texte intégral et les filtres conjointement ou séparément.

Les recherches et les filtres sont effectués à l’aide de la méthode [SearchClient. Search](/dotnet/api/azure.search.documents.searchclient.search). Une requête de recherche peut être transmise dans la chaîne `searchText`, tandis qu’une expression de filtre peut être transmise dans la propriété [Filter](/dotnet/api/azure.search.documents.searchoptions.filter) de la classe [SearchOptions](/dotnet/api/azure.search.documents.searchoptions). Pour filtrer sans effectuer de recherche, transmettez simplement `"*"` pour le paramètre `searchText` de la méthode [Search](/dotnet/api/azure.search.documents.searchclient.search). Pour effectuer une recherche sans filtrage, ne définissez pas la propriété `Filter` et ne transmettez aucune instance `SearchOptions`.

## <a name="run-the-program"></a>Exécuter le programme

Appuyez sur F5 pour regénérer l’application et exécuter le programme dans son intégralité. 

La sortie comprend des messages de la méthode [Console.WriteLine](/dotnet/api/system.console.writeline), avec en plus des informations et des résultats de requête.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous travaillez dans votre propre abonnement, il est recommandé, à la fin de chaque projet, de déterminer si vous avez toujours besoin des ressources que vous avez créées. Les ressources laissées en cours d’exécution peuvent vous coûter de l’argent. Vous pouvez supprimer les ressources une par une, ou choisir de supprimer le groupe de ressources afin de supprimer l’ensemble des ressources.

Vous pouvez rechercher et gérer les ressources dans le portail à l’aide des liens **Toutes les ressources** ou **Groupes de ressources** situés dans le volet de navigation de gauche.

Si vous utilisez un service gratuit, n’oubliez pas que vous êtes limité à trois index, indexeurs et sources de données. Vous pouvez supprimer des éléments un par un dans le portail pour ne pas dépasser la limite. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide C#, vous avez effectué un ensemble de tâches pour créer un index, le charger avec des documents et exécuter des requêtes. À différents stades, nous avons pris des raccourcis afin de simplifier le code pour une meilleure lisibilité et compréhension. Si vous êtes familiarisé avec les concepts de base, nous vous recommandons l’article suivant pour explorer d’autres approches et concepts afin d’approfondir vos connaissances. 

> [!div class="nextstepaction"]
> [Guide pratique pour développer dans .NET](search-howto-dotnet-sdk.md)

Vous souhaitez optimiser et réduire vos coûts de cloud ?

> [!div class="nextstepaction"]
> [Démarrer l’analyse des coûts avec Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)