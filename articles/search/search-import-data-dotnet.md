---
title: Charger des données dans un index Recherche Azure en C# (SDK .NET) - Recherche Azure
description: Découvrez comment charger des données dans un index de recherche de texte intégral au sein de Recherche Azure en utilisant un exemple de code C# et le kit de développement logiciel (SDK) .NET.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: d2d54d1425bbb67a3f5ba1b6081a9f74ff87f4d6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286905"
---
# <a name="quickstart-2---load-data-to-an-azure-search-index-using-c"></a>Démarrage rapide : 2. - Charger des données dans un index Recherche Azure à l’aide de C#

Cet article vous montre comment importer des données dans [un index Recherche Azure](search-what-is-an-index.md) à l’aide de C# et du [SDK .NET](https://aka.ms/search-sdk). L’envoi (push) de documents dans votre index s’effectue en exécutant ces tâches :

> [!div class="checklist"]
> * Créer un objet [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) à connecter à votre index de recherche.
> * Créer un objet [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) contenant les documents à ajouter, modifier ou supprimer.
> * Appeler la méthode `Documents.Index` sur `SearchIndexClient` pour charger des documents dans un index.

## <a name="prerequisites"></a>Prérequis

[Créer un index Recherche Azure](search-create-index-dotnet.md) et un objet `SearchServiceClient`, comme indiqué dans [« Créer un client »](search-create-index-dotnet.md#CreateSearchServiceClient).


## <a name="create-a-client"></a>Créer un client
Pour importer des données, vous avez besoin d’une instance de la classe `SearchIndexClient`. Plusieurs approches permettent de créer cette classe, dont l’utilisation de l’instance `SearchServiceClient` déjà créée. 

Comme l’illustre l’exemple suivant, vous pouvez utiliser l’instance `SearchServiceClient` et appeler sa méthode `Indexes.GetClient`. Cet extrait de code obtient un `SearchIndexClient` pour l’index nommé « hotels » à partir d’un `SearchServiceClient` nommé `serviceClient`.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

`SearchIndexClient` a une propriété `Documents`. Cette propriété fournit toutes les méthodes dont vous avez besoin pour ajouter, modifier, supprimer ou interroger des documents dans l’index.

> [!NOTE]
> Dans une application de recherche classique, l’interrogation et l’indexation sont gérées séparément. Tandis que `Indexes.GetClient` est pratique pour réutiliser des objets comme `SearchCredentials`, vous pouvez utiliser une approche plus robuste pour créer le `SearchIndexClient` directement afin de pouvoir transmettre une clé de requête au lieu d’une clé d’administration. Cette pratique est cohérente avec le [principe du moindre privilège](https://en.wikipedia.org/wiki/Principle_of_least_privilege) et vous permet de mieux sécuriser votre application. Vous construisez un `SearchIndexClient` dans l’exercice suivant. Pour plus d’informations sur les clés, consultez [Créer et gérer des clés API pour un service de Recherche Azure](search-security-api-keys.md).
> 
> 

<a name="construct-indexbatch"></a>

## <a name="construct-indexbatch"></a>Construire IndexBatch

Pour importer des données à l’aide du SDK .NET, empaquetez vos données dans un objet `IndexBatch`. Un `IndexBatch` encapsule une collection d’objets `IndexAction`, chacun d’entre eux contenant un document et une propriété qui indique à la Recherche Azure les actions à effectuer sur ce document (upload, merge, delete et mergeOrUpload). Pour plus d’informations sur les actions d’indexation, consultez [Actions d’indexation : upload, merge, mergeOrUpload, delete](search-what-is-data-import.md#indexing-actions).

En partant du principe que vous connaissez les actions à effectuer sur vos documents, vous êtes prêt à construire le `IndexBatch`. L’exemple ci-dessous montre comment créer un lot avec différentes actions. L’exemple utilise une classe personnalisée appelée `Hotel` qui est mappée à un document dans l’index « hotels ».

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

Dans ce cas, nous utilisons `Upload`, `MergeOrUpload` et `Delete` comme actions de recherche, tel que spécifié par les méthodes appelées sur la classe `IndexAction`.

Supposons que cet exemple d’index « hotels » contient déjà un certain nombre de documents. Notez que nous n’avons pas eu à spécifier tous les champs de document possibles en utilisant `MergeOrUpload` et que nous n’avons fait que spécifier la clé de document (`HotelId`) avec la commande `Delete`.

Notez également que chaque requête d’indexation ne peut contenir que 1 000 documents maximum.

> [!NOTE]
> Dans cet exemple, nous appliquons différentes actions à différents documents. Si vous souhaitez effectuer ces mêmes actions sur tous les documents du lot, au lieu d’appeler `IndexBatch.New`, vous pouvez utiliser les autres méthodes statiques de `IndexBatch`. Par exemple, vous pouvez créer des lots en appelant `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` ou `IndexBatch.Delete`. Ces méthodes prennent une collection de documents (objets de type `Hotel` dans cet exemple) à la place d’objets `IndexAction`.
> 
> 

## <a name="call-documentsindex"></a>Appeler Documents.Index
Maintenant qu’un objet `IndexBatch` a été initialisé, vous pouvez l’envoyer à l’index en appelant `Documents.Index` sur votre objet `SearchIndexClient`. L’exemple suivant illustre l’appel à `Index`, ainsi que des étapes supplémentaires que vous devez effectuer :

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Notez le signe `try`/`catch` entourant l’appel à la méthode `Index`. Le bloc catch gère un cas d’erreur important pour l’indexation. Si votre service Azure Search ne parvient pas à indexer certains documents du lot, `Documents.Index` génère un `IndexBatchException`. Cela peut se produire si vous indexez des documents lorsque votre service est surchargé. **Nous vous recommandons vivement de prendre en charge explicitement ce cas de figure dans votre code.**  Vous pouvez retarder puis relancer l'indexation des documents qui ont échoué, ouvrir une session et continuer comme dans l’exemple, ou faire autre chose selon la cohérence des données requise par votre application.

Enfin, le code de l’exemple ci-dessus attend deux secondes. L'indexation s’exécutant en mode asynchrone dans votre service Azure Search, l'exemple d'application doit attendre quelque temps afin de s'assurer que les documents sont disponibles pour la recherche. Ce genre de retard n’est nécessaire que dans les démonstrations, les tests et les exemples d'applications.

Pour plus d’informations sur le traitement des documents, consultez [« Comment le SDK .NET traite les documents »](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).


## <a name="next-steps"></a>Étapes suivantes
Après avoir rempli votre index Recherche Azure, l’étape suivante est d’envoyer des requêtes pour rechercher des documents. 

> [!div class="nextstepaction"]
> [Interroger un index Recherche Azure en C#](search-query-dotnet.md)
