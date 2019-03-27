---
title: Créer un index dans le code à l’aide de l’API .NET - Azure Search
description: Découvrez comment créer un index de recherche de texte intégral à l’aide du kit SDK .NET Azure Search et de l’exemple de code en C#.
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: dbaac1478fdbf1b42fc6b597c3a5c541e007e413
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287143"
---
# <a name="quickstart-1---create-an-azure-search-index-in-c"></a>Démarrage rapide : 1 - Créer un index Recherche Azure en C#

Cet article vous explique comment créer [un index Recherche Azure](search-what-is-an-index.md) à l’aide de C# et du [SDK .NET](https://aka.ms/search-sdk). Il s’agit de la première leçon d’un exercice en 3 parties pour créer, charger et interroger un index. La création d’index s’effectue en exécutant ces tâches :

> [!div class="checklist"]
> * Créer un objet [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) à connecter à un service de recherche.
> * Créer un objet [`Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) à passer comme paramètre à `Indexes.Create`.
> * Appeler la méthode `Indexes.Create` sur `SearchServiceClient` pour envoyer le `Index` à un service.

## <a name="prerequisites"></a>Prérequis

[Créez un service Recherche Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce démarrage rapide.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), n’importe quelle édition. L’exemple de code et les instructions ont été testés dans l’édition Communauté gratuite.

Un point de terminaison d’URL et une clé API d’administration de votre service de recherche. Un service de recherche est créé avec les deux. Ainsi, si vous avez ajouté votre abonnement à la fonction Recherche Azure, procédez comme suit pour obtenir les informations nécessaires :

  1. Dans le portail Azure, dans la page **Vue d’ensemble** de votre service de recherche, obtenez l’URL. Voici un exemple de point de terminaison : `https://mydemo.search.windows.net`.

  2. Dans **Paramètres** > **Clés**, obtenez une clé d’administration pour avoir des droits d’accès complets sur le service. Il existe deux clés d’administration interchangeables, fournies pour assurer la continuité de l’activité au cas où vous deviez en remplacer une. Vous pouvez utiliser la clé primaire ou secondaire sur les demandes d’ajout, de modification et de suppression d’objets.

  ![Obtenir une clé d’accès et un point de terminaison HTTP](media/search-fiddler/get-url-key.png "Obtenir une clé d’accès et un point de terminaison HTTP")

Toutes les demandes nécessitent une clé API sur chaque demande envoyée à votre service. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

## <a name="1---open-the-project"></a>1 - Ouvrir le projet

Téléchargez l’exemple de code [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) à partir de GitHub. 

Dans appsettings.json, remplacez le contenu par défaut par l’exemple ci-dessous, puis indiquez le nom du service et la clé API d’administration de votre service. Pour le nom du service, vous avez simplement besoin du nom lui-même. Par exemple, si votre URL est https://mydemo.search.windows.net, ajoutez `mydemo` au fichier JSON.


```json
{
    "SearchServiceName": "Put your search service name here",
    "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
}
```

Une fois que ces valeurs sont définies, vous pouvez générer (F5) la solution pour exécuter l’application de console. Les étapes restantes de cet exercice et celles qui suivent explorent le fonctionnement de ce code. 

Vous pouvez également consulter [Guide pratique pour utiliser la Recherche Azure à partir d’une application .NET](search-howto-dotnet-sdk.md) pour voir plus en détail les comportements du SDK. 

<a name="CreateSearchServiceClient"></a>

## <a name="2---create-a-client"></a>2 - Créer un client

Pour commencer à utiliser le SDK .NET de la Recherche Azure, créez une instance de la classe `SearchServiceClient`. Cette classe dispose de plusieurs constructeurs. Celle que vous cherchez utilise le nom de votre service de recherche et un objet `SearchCredentials` en tant paramètres. `SearchCredentials` encapsule votre clé API.

Le code suivant se trouve dans le fichier Program.cs. Il crée un `SearchServiceClient` à l’aide de valeurs pour le nom du service de recherche et la clé API stockées dans le fichier config de l’application (appsettings.json).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

`SearchServiceClient` a une propriété `Indexes`. Cette propriété fournit toutes les méthodes dont vous avez besoin pour créer, afficher, mettre à jour ou supprimer des index Azure Search.

> [!NOTE]
> La classe `SearchServiceClient` gère les connexions à votre service de recherche. Pour éviter l'ouverture d'un trop grand nombre de connexions, essayez de partager une seule instance de `SearchServiceClient` dans votre application, si possible. Ses méthodes sont thread-safe pour activer le partage.
> 
> 

<a name="DefineIndex"></a>

## <a name="3---construct-index"></a>3 - Construire l’index
Un seul appel à la méthode `Indexes.Create` crée un index. Cette méthode prend comme paramètre un objet `Index` qui définit un index Recherche Azure. Créez un objet `Index` et initialisez-le comme suit :

1. Définissez la propriété `Name` de l’objet `Index` sur le nom de votre index.

2. Définissez la propriété `Fields` de l’objet `Index` sur l’array d’objets `Field`. Le moyen le plus simple de créer les objets `Field` consiste à appeler la méthode `FieldBuilder.BuildForType` en transmettant une classe de modèle pour le paramètre de type. Une classe de modèle comporte des propriétés qui sont mappées sur les champs de votre index. Cela vous permet de lier des documents à partir de votre index de recherche à des instances de votre classe de modèle.

> [!NOTE]
> Si vous n’envisagez pas d’utiliser une classe de modèle, vous pouvez toujours définir votre index en créant les objets `Field` directement. Vous pouvez fournir le nom du champ au constructeur, ainsi que le type de données (ou un analyseur pour les champs de chaîne). Vous pouvez également définir d’autres propriétés comme `IsSearchable`, `IsFilterable`, etc.
>
>

Quand vous concevez votre index, ne perdez pas de vue l’expérience utilisateur de votre recherche ni vos besoins professionnels. Chaque champ doit avoir les [attributs](https://docs.microsoft.com/rest/api/searchservice/Create-Index) qui contrôlent les fonctionnalités de recherche (filtrage, facettes, tri et ainsi de suite) s’appliquant à chaque champ. Pour les propriétés que vous ne définissez pas explicitement, la classe `Field` désactive par défaut la fonctionnalité de recherche correspondante, sauf si vous l'activez de façon spécifique.

Dans cet exemple, le nom d’index est « hotels » et des champs sont définis à l’aide d’une classe de modèle. Chaque propriété de la classe de modèle comporte des attributs qui déterminent les comportements liés à la recherche du champ d’index correspondant. La classe de modèle est définie comme suit :

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

Nous avons soigneusement choisi les attributs de chaque propriété en fonction de la manière dont ils seront vraisemblablement utilisés dans une application. Par exemple, il est probable que les personnes recherchant des hôtels seront intéressées par les correspondances de mots-clés sur le champ `description`. Ainsi, nous activons la recherche en texte intégral pour ce champ en définissant l’attribut `IsSearchable` sur la propriété `Description`.

Notez que, dans votre index, vous ne devez désigner qu’un seul champ de type `string` en tant que champ *clé* en ajoutant l’attribut `Key` (voir `HotelId` dans l’exemple ci-dessus).

La définition d’index ci-dessus utilise un analyseur de langue pour le champ `description_fr` dans la mesure où il est destiné à stocker du texte en français. Pour plus d’informations, consultez [Ajouter des analyseurs linguistiques à un index Recherche Azure](index-add-language-analyzers.md).

> [!NOTE]
> Par défaut, le nom de chaque propriété de votre classe de modèle correspond au nom du champ dans l’index. Si vous souhaitez mapper tous les noms de propriété sur les noms de champ en casse mixte, marquez la classe avec l’attribut `SerializePropertyNamesAsCamelCase`. Si vous souhaitez mapper les noms de propriété sur un nom différent, vous pouvez utiliser l’attribut `JsonProperty` comme la propriété `DescriptionFr` ci-dessus. L’attribut `JsonProperty` est prioritaire sur l’attribut `SerializePropertyNamesAsCamelCase`.
> 
> 

À présent que nous avons défini une classe de modèle, nous pouvons créer une définition d’index très facilement :

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="4---call-indexescreate"></a>4 - Appeler Indexes.Create
Maintenant que vous avez initialisé un objet `Index`, créez l’index en appelant `Indexes.Create` sur votre objet `SearchServiceClient` :

```csharp
serviceClient.Indexes.Create(definition);
```

Pour une requête réussie, la méthode effectuera un retour normalement. En cas de problème avec la requête, comme un paramètre non valide, la méthode lève `CloudException`.

Pour supprimer un index dont vous n’avez plus besoin, appelez la méthode `Indexes.Delete` sur votre `SearchServiceClient`. Par exemple : 

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> L'exemple de code dans cet article utilise les méthodes synchrones du SDK .NET Azure Search pour plus de simplicité. Nous vous recommandons d'utiliser les méthodes asynchrones dans vos propres applications pour les rendre évolutives et réactives. Par exemple, dans les exemples ci-dessus, vous pouvez utiliser `CreateAsync` et `DeleteAsync` au lieu de `Create` et `Delete`.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez créé un index Recherche Azure vide basé sur un schéma qui définit les comportements et les types de données des champs. Le prochain guide de démarrage rapide de cette série explique comment charger l’index avec du contenu à rechercher.

> [!div class="nextstepaction"]
> [Charger des données dans un index Recherche Azure à l’aide de C#](search-import-data-dotnet.md)