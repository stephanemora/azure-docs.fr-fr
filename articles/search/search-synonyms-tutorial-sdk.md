---
title: Exemple C# pour les synonymes
titleSuffix: Azure Cognitive Search
description: Avec cet exemple C#, découvrez comment ajouter la fonctionnalité des synonymes à un index dans la Recherche cognitive Azure. Une carte de synonymes est une liste de termes équivalents. Les champs avec prise en charge des synonymes étendent les requêtes afin d'inclure le terme fourni par l’utilisateur et tous les synonymes associés.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8cc085fd27004928babd7df305a4452d1b068f6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794234"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Exemple : Ajouter des synonymes pour le service Recherche cognitive Azure en C#

Les synonymes développent une requête en faisant correspondre les termes considérés comme sémantiquement équivalents à l’expression entrée. Par exemple, vous souhaiterez peut-être que le terme « voiture » vous permette de trouver des documents contenant les mots « automobile » ou « véhicule ». 

Dans la Recherche cognitive Azure, les synonymes sont définis dans une *carte de synonymes*, par le biais des *règles de mappage* qui associent des termes équivalents. Cet exemple décrit les étapes essentielles pour l’ajout et l’utilisation de synonymes avec un index existant. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une carte de synonymes en utilisant la classe [SynonymMap](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap?view=azure-dotnet). 
> * Définir la propriété [SynonymMaps](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.synonymmaps?view=azure-dotnet) sur des champs qui doivent prendre en charge l’extension de requête par le biais de synonymes.

Vous pouvez interroger un champ acceptant les synonymes comme vous le faites habituellement. Aucune syntaxe de requête supplémentaire n’est requise pour accéder aux synonymes.

Vous pouvez créer plusieurs cartes de synonymes, les valider en tant que ressources du service disponible pour tout index, et ensuite référencer ceux que vous souhaitez utiliser au niveau du champ. Au moment de la requête, en plus de la recherche dans un index, la Recherche cognitive Azure effectue une recherche dans une carte de synonymes, si une telle carte est spécifiée dans les champs utilisés dans la requête.

> [!NOTE]
> Les synonymes peuvent être créés par programmation, mais pas dans le portail. Si la prise en charge des synonymes par le portail Azure peut vous être utile, donnez-nous votre avis sur [UserVoice](https://feedback.azure.com/forums/263029-azure-search)

## <a name="prerequisites"></a>Prérequis

La configuration requise du didacticiel est la suivante :

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Service Recherche cognitive Azure](search-create-service-portal.md)
* [Bibliothèque .NET Microsoft.Azure.Search](https://aka.ms/search-sdk)
* [Guide pratique pour utiliser la Recherche cognitive Azure à partir d’une application .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Vue d’ensemble

Les requêtes avant et après présentent la valeur des synonymes. Dans cet exemple, vous utilisez un exemple d’application qui exécute des requêtes et retourne des résultats sur un index d’exemples. L’exemple d’application crée un petit index nommé « hotels » comprenant deux documents. L’application exécute des requêtes de recherche à l’aide de termes et d’expressions qui n’apparaissent pas dans l’index, active la fonctionnalité de synonymes, puis lance les mêmes recherches une nouvelle fois. Le code ci-dessous montre le flux global.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
Les étapes pour créer et remplir l’index des exemples sont expliquées dans le [Guide pratique pour utiliser la Recherche cognitive Azure à partir d’une application .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

## <a name="before-queries"></a>Requêtes « avant »

Dans `RunQueriesWithNonExistentTermsInIndex`, émettez des requêtes de recherche avec « five star », « internet » et « economy AND hotel ».
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
Aucun des deux documents indexés ne contient les termes, nous avons donc la sortie suivante à partir du premier `RunQueriesWithNonExistentTermsInIndex`.
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>Activation des synonymes

L’activation des synonymes est un processus en deux étapes. Tout d’abord nous définissons et chargeons les règles de synonymes, puis nous configurons les champs pour les utiliser. Le processus est décrit dans `UploadSynonyms` et `EnableSynonymsInHotelsIndex`.

1. Ajoutez une carte de synonymes à votre service de recherche. Dans `UploadSynonyms`, nous définissons quatre règles de notre carte de synonymes « desc-synonymmap » et effectuons le téléchargement vers le service.
   ```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
   ```
   Une carte de synonymes doit être conforme au format `solr` standard Open Source. Le format est expliqué dans [Synonymes dans la Recherche cognitive Azure](search-synonyms.md) sous la section `Apache Solr synonym format`.

2. Configurez les champs pouvant faire l’objet d’une recherche pour utiliser la carte de synonymes dans la définition d’index. Dans `EnableSynonymsInHotelsIndex`, nous activons les synonymes sur deux champs `category` et `tags` en affectant à la propriété `synonymMaps` le nom de la carte de synonymes qui vient d’être téléchargée.
   ```csharp
   Index index = serviceClient.Indexes.Get("hotels");
   index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
   index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

   serviceClient.Indexes.CreateOrUpdate(index);
   ```
   Lorsque vous ajoutez une carte de synonymes, les reconstructions d’index ne sont pas requises. Vous pouvez ajouter une carte de synonymes à votre service, puis modifier les définitions de champ existantes dans n’importe quel index pour utiliser la nouvelle carte de synonymes. L’ajout de nouveaux attributs n’a aucun impact sur la disponibilité de l’index. Il en va de même pour la désactivation de synonymes pour un champ. Vous pouvez simplement affecter à la propriété `synonymMaps` une liste vide.
   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
   ```

## <a name="after-queries"></a>Requêtes « après »

Une fois que la carte de synonymes est téléchargée et l’index mis à jour pour utiliser la carte de synonymes, le deuxième appel `RunQueriesWithNonExistentTermsInIndex` affiche les éléments suivants :

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
La première requête trouve le document à partir de la règle `five star=>luxury`. La deuxième requête étend la recherche à l’aide de `internet,wifi` et la troisième avec à la fois `hotel, motel` et `economy,inexpensive=>budget` pour trouver les documents en correspondance.

L’ajout de synonymes modifie complètement l’expérience de recherche. Dans cet exemple, les requêtes d’origine n’ont pas pu retourner de résultats significatifs même si les documents dans notre index étaient pertinents. En activant les synonymes, nous pouvons développer un index pour inclure les termes communément utilisés, sans modification de données sous-jacentes dans l’index.

## <a name="sample-application-source-code"></a>Code source de l'exemple d'application
Vous trouverez le code source complet de l’exemple d’application utilisé dans cette procédure sur [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Le moyen le plus rapide de procéder à un nettoyage après un exemple consiste à supprimer le groupe de ressources contenant le service Recherche cognitive Azure. Vous pouvez maintenant supprimer le groupe de ressources pour supprimer définitivement tout ce qu’il contient. Dans le portail, le nom du groupe de ressources figure dans la page Vue d’ensemble du service Recherche cognitive Azure.

## <a name="next-steps"></a>Étapes suivantes

Cet exemple a présenté la fonctionnalité de synonymes en code C# pour créer et publier des règles de mappage, puis appeler la carte de synonymes pour une requête. Vous trouverez des informations supplémentaires dans la documentation de référence du [Kit de développement logiciel (SDK) .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) et de [l’API REST](https://docs.microsoft.com/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Guide pratique pour utiliser des synonymes dans la Recherche cognitive Azure](search-synonyms.md)
