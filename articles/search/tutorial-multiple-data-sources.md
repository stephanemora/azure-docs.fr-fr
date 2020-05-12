---
title: Tutoriel C# sur l’indexation de plusieurs sources de données Azure
titleSuffix: Azure Cognitive Search
description: Découvrez comment importer des données à partir de plusieurs sources de données dans un seul index Recherche cognitive Azure à l’aide d’indexeurs. Ce didacticiel et cet exemple de code utilisent le langage C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: eb1824e41fe9fc5185ae4e914b4828cddb2c42db
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780519"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>Tutoriel : Indexer à partir de plusieurs sources de données à l’aide du SDK .NET

Recherche cognitive Azure peut importer, analyser et indexer des données provenant de plusieurs sources de données dans un même index de recherche consolidé. Cette fonctionnalité prend en charge les situations où des données structurées sont agrégées avec des données moins structurées, voire de texte brut, provenant d’autres sources, telles que des documents de texte, HTML ou JSON.

Ce tutoriel explique comment indexer des données d’hôtels provenant d’une source de données Azure Cosmos DB et les fusionner avec des détails sur les chambres d’hôtel tirés de documents Stockage Blob Azure. Le résultat est un index de recherche d’hôtel combiné contenant des types de données complexes.

Ce tutoriel utilise C# et le [SDK .NET](https://aka.ms/search-sdk). Dans ce tutoriel, vous allez effectuer les tâches suivantes :

> [!div class="checklist"]
> * Charger des exemples de données et créer des sources de données
> * Identifier la clé de document
> * Définir et créer l’index
> * Indexer les données d’hôtels issues d’Azure Cosmos DB
> * Fusionner les données des chambres d’hôtel provenant du stockage d’objets blob

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

+ [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)
+ [Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [Créer](search-create-service-portal.md) ou [rechercher un service de recherche existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Vous pouvez utiliser le service gratuit pour ce tutoriel. Avec un service de recherche gratuit, vous êtes limité à trois index, trois indexeurs et trois sources de données. Ce didacticiel crée une occurrence de chaque élément. Avant de commencer, veillez à disposer de l’espace suffisant sur votre service pour accepter les nouvelles ressources.

## <a name="download-files"></a>Télécharger les fichiers

Le code source pour ce tutoriel se trouve dans le dépôt GitHub [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples), dans le dossier [multiple-data-sources](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources).

## <a name="1---create-services"></a>1 - Créer les services

Ce tutoriel utilise Recherche cognitive Azure pour l’indexation et les requêtes, Azure Cosmos DB pour un des jeux de données et Stockage Blob Azure pour le deuxième jeu de données. 

Si possible, créez tous les services dans la même région et le même groupe de ressources pour des raisons de proximité et de facilité de gestion. Dans la pratique, vos services peuvent se trouver dans n’importe quelle région.

Cet exemple utilise deux petits ensembles de données qui décrivent sept hôtels fictifs. Un ensemble décrit les hôtels eux-mêmes et est chargé dans une base de données Azure Cosmos DB. L’autre ensemble contient les détails des chambres d’hôtel et est fourni sous la forme de sept fichiers JSON distincts à charger dans Stockage Blob Azure.

### <a name="start-with-cosmos-db"></a>Démarrer avec Cosmos DB

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis accédez à la page Vue d’ensemble de votre compte Azure Cosmos DB.

1. Sélectionnez **Explorateur de données**, puis sélectionnez **Nouvelle base de données**.

   ![Créer une base de données](media/tutorial-multiple-data-sources/cosmos-newdb.png "Créer une base de données")

1. Entrez le nom **hotel-rooms-db**. Acceptez les valeurs par défaut pour les autres paramètres.

   ![Configurer la base de données](media/tutorial-multiple-data-sources/cosmos-dbname.png "Configurer la base de données")

1. Créez un conteneur. Utilisez la base de données existante que vous venez de créer. Entrez **hotels** comme nom de conteneur et utilisez **/HotelId** comme clé de partition.

   ![Ajouter un conteneur](media/tutorial-multiple-data-sources/cosmos-add-container.png "Ajouter un conteneur")

1. Sélectionnez **Éléments** sous **Hôtels**, puis cliquez sur **Charger un élément** dans la barre de commandes. Accédez au fichier **cosmosdb/HotelsDataSubset_CosmosDb.json** dans le dossier du projet, puis sélectionnez-le.

   ![Charger dans la collection Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-upload.png "Charger dans la collection Azure Cosmos DB")

1. Utilisez le bouton Actualiser pour actualiser l’affichage des éléments dans la collection hotels. Sept nouveaux documents doivent être listés.

### <a name="azure-blob-storage"></a>Stockage Blob Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com), accédez à votre compte de stockage Azure, cliquez sur **Objets blob**, puis sur **+ Conteneur**.

1. [Créez un conteneur d’objets blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) nommé **hotel-rooms** pour stocker les exemples de fichiers JSON des chambres d’hôtel. Vous pouvez définir le niveau d’accès public sur l’une de ses valeurs valides.

   Créer un ![conteneur d’objets blob](media/tutorial-multiple-data-sources/blob-add-container.png "Création d’un conteneur d’objets blob")

1. Une fois le conteneur créé, ouvrez-le et sélectionnez **Charger** dans la barre de commandes. Accédez au dossier contenant les exemples de fichiers. Sélectionnez-les tous, puis cliquez sur **Charger**.

   ![Charger des fichiers](media/tutorial-multiple-data-sources/blob-upload.png "Charger des fichiers")

Une fois le chargement terminé, les fichiers doivent apparaître dans la liste liée au conteneur de données.

### <a name="azure-cognitive-search"></a>Recherche cognitive Azure

Le troisième composant est Recherche cognitive Azure, que vous pouvez [créer dans le portail](search-create-service-portal.md). Vous pouvez utiliser le niveau gratuit pour effectuer cette procédure pas à pas. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obtenir une clé API d’administration et une URL pour Recherche cognitive Azure

Pour interagir avec votre service Recherche cognitive Azure, vous devrez disposer de l’URL du service et d’une clé d’accès. Un service de recherche est créé avec les deux. Ainsi, si vous avez ajouté la Recherche cognitive Azure à votre abonnement, effectuez ce qui suit pour obtenir les informations nécessaires :

1. [Connectez-vous au portail Azure](https://portal.azure.com/), puis dans la page **Vue d’ensemble** du service de recherche, récupérez l’URL. Voici un exemple de point de terminaison : `https://mydemo.search.windows.net`.

1. Dans **Paramètres** > **Clés**, obtenez une clé d’administration pour avoir des droits d’accès complets sur le service. Il existe deux clés d’administration interchangeables, fournies pour assurer la continuité de l’activité au cas où vous deviez en remplacer une. Vous pouvez utiliser la clé primaire ou secondaire sur les demandes d’ajout, de modification et de suppression d’objets.

   Obtenez aussi la clé de requête. Il est recommandé d’émettre des demandes de requête avec un accès en lecture seule.

   ![Obtenir le nom du service, les clés d’administration et les clés de requête](media/search-get-started-nodejs/service-name-and-keys.png)

L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

## <a name="2---set-up-your-environment"></a>2 - Configurer votre environnement

1. Démarrez Visual Studio 2019 puis, dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet**, puis **Gérer les packages NuGet pour la solution...** . 

1. Sous l’onglet **Parcourir**, recherchez et installez **Microsoft.Azure.Search** (version 9.0.1 ou ultérieure). Vous devrez valider une série de boîtes de dialogue supplémentaires pour terminer l’installation.

    ![Utilisation de NuGet pour ajouter des bibliothèques Azure](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Recherchez le package NuGet **Microsoft.Extensions.Configuration.Json** et installez-le également.

1. Ouvrez le fichier solution **AzureSearchMultipleDataSources.sln**.

1. Dans l’Explorateur de solutions, modifiez le fichier **appsettings.json** en y ajoutant les informations de connexion.  

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "BlobStorageAccountName": "Put your Azure Storage account name here",
      "BlobStorageConnectionString": "Put your Azure Blob Storage connection string here",
      "CosmosDBConnectionString": "Put your Cosmos DB connection string here",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

Les deux premières entrées utilisent l’URL et les clés d’administration de votre service Recherche cognitive Azure. Si le point de terminaison est `https://mydemo.search.windows.net`, le nom du service à fournir est `mydemo`.

Les entrées suivantes spécifient les noms de compte et les informations de chaîne de connexion pour les sources de données Stockage Blob Azure et Azure Cosmos DB.

## <a name="3---map-key-fields"></a>3 - Mapper les champs clés

La fusion de contenu nécessite que les deux flux de données ciblent les mêmes documents dans l’index de recherche. 

Dans Recherche cognitive Azure, le champ de clé identifie chaque document de façon univoque. Chaque index de recherche doit comporter exactement un champ de clé de type `Edm.String`. Ce champ de clé doit être présent pour chaque document d’une source de données qui est ajouté à l’index. (il constitue en fait le seul champ obligatoire).

Quand vous indexez des données provenant de plusieurs sources de données, vérifiez que chaque ligne ou document entrant contient une clé de document commune pour les fusionner à partir de deux documents sources physiquement distincts dans un nouveau document de recherche dans l’index combiné. 

Il est souvent nécessaire de passer par une planification préalable pour identifier une clé de document significative pour votre index et vous assurer qu’elle existe dans les deux sources de données. Dans cette démonstration, la clé `HotelId` de chaque hôtel dans Cosmos DB est également présente dans les objets blob JSON des chambres dans Stockage Blob.

Les indexeurs de la Recherche cognitive Azure peuvent utiliser des mappages de champs pour renommer, voire remettre en forme, les champs de données pendant le processus d’indexation, afin que les données sources puissent être redirigées vers le champ de l’index approprié. Par exemple, dans Cosmos DB, l’identificateur d’hôtel est appelé **`HotelId`** . Mais dans les fichiers d’objets blob JSON pour les chambres d’hôtel, l’identificateur d’hôtel est nommé **`Id`** . Le programme gère cette situation en mappant le champ **`Id`** des objets blob au champ de clé **`HotelId`** de l’index.

> [!NOTE]
> Dans la plupart des cas, les clés de document générées automatiquement, comme celles créées par défaut par certains indexeurs, ne constituent pas des clés de document appropriées pour les index combinés. En général, il est préférable d’utiliser une valeur de clé unique explicite qui existe déjà dans vos sources de données ou qui peut y être facilement ajoutée.

## <a name="4---explore-the-code"></a>4 - Explorer le code

Une fois que les données et les paramètres de configuration sont en place, l’exemple de programme dans **AzureSearchMultipleDataSources.sln** doit être opérationnel.

Cette application console C#/.NET simple effectue les tâches suivantes :

* Elle crée un index basé sur la structure de données de la classe Hotel C# (qui référence également les classes Address et Room).
* Elle crée une source de données et un indexeur qui mappe les données Azure Cosmos DB aux champs d’index. Il s’agit de deux objets dans le service Recherche cognitive Azure.
* Elle exécute l’indexeur pour charger les données des hôtels à partir de Cosmos DB.
* Elle crée une seconde source de données et un indexeur qui mappe les données blob JSON aux champs d’index.
* Elle exécute le second indexeur pour charger des données des chambres à partir du stockage d’objets blob.

 Avant d’exécuter le programme, prenez une minute pour étudier le code et les définitions d’index et d’indexeur de cet exemple. Le code qui convient se trouve dans deux fichiers :

  + **Hotel.cs** contient le schéma qui définit l’index
  + **Program.cs** contient des fonctions qui créent l’index, les sources de données et les indexeurs de la Recherche cognitive Azure, et chargent les résultats combinés dans l’index.

### <a name="create-an-index"></a>Création d'un index

Cet exemple de programme utilise le SDK .NET pour définir et créer un index de Recherche cognitive Azure. Il tire parti de la classe [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) pour générer une structure d’index à partir d’une classe de modèle de données C#.

Le modèle de données est défini par la classe Hotel, qui contient également des références aux classes Address et Room. La classe FieldBuilder explore plusieurs définitions de classe pour générer une structure de données complexes pour l’index. Des étiquettes de métadonnées sont utilisées pour définir les attributs de chaque champ, par exemple s’il peut faire l’objet d’une recherche ou d’un tri.

Les extraits de code suivants tirés du fichier **Hotel.cs** montrent comment spécifier un champ unique et une référence à une autre classe de modèle de données.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

Dans le fichier **Program.cs**, l’index est défini avec un nom et une collection de champs générés par la méthode `FieldBuilder.BuildForType<Hotel>()`, puis créé comme suit :

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Créer l’indexeur et la source de données Azure Cosmos DB

Ensuite, le programme principal inclut une logique destinée à créer la source de données Azure Cosmos DB pour les données d’hôtels.

Tout d’abord, il concatène le nom de la base de données Azure Cosmos DB à la chaîne de connexion. Il définit ensuite l’objet de source de données, y compris les paramètres propres aux sources Azure Cosmos DB, tels que la propriété [useChangeDetection].

  ```csharp
private static async Task CreateAndRunCosmosDbIndexer(string indexName, SearchServiceClient searchService)
{
    // Append the database name to the connection string
    string cosmosConnectString = 
        configuration["CosmosDBConnectionString"]
        + ";Database=" 
        + configuration["CosmosDBDatabaseName"];

    DataSource cosmosDbDataSource = DataSource.CosmosDb(
        name: configuration["CosmosDBDatabaseName"], 
        cosmosDbConnectionString: cosmosConnectString,
        collectionName: "hotels",
        useChangeDetection: true);

    // The Azure Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(cosmosDbDataSource);
  ```

Une fois la source de données créée, le programme configure un indexeur Azure Cosmos DB nommé **hotel-rooms-cosmos-indexer**.

```csharp
    Indexer cosmosDbIndexer = new Indexer(
        name: "hotel-rooms-cosmos-indexer",
        dataSourceName: cosmosDbDataSource.Name,
        targetIndexName: indexName,
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
    
    // Indexers keep metadata about how much they have already indexed.
    // If we already ran this sample, the indexer will remember that it already
    // indexed the sample data and not run again.
    // To avoid this, reset the indexer if it exists.
    bool exists = await searchService.Indexers.ExistsAsync(cosmosDbIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(cosmosDbIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(cosmosDbIndexer);
```
Le programme supprime les indexeurs portant le même nom avant de créer le nouveau, au cas où vous souhaiteriez exécuter cet exemple plusieurs fois.

Cet exemple définit une planification pour l’indexeur, afin qu’il s’exécute une fois par jour. Si vous ne souhaitez pas que l’indexeur soit automatiquement réexécuté à l’avenir, vous pouvez supprimer la propriété de planification de cet appel.

### <a name="index-azure-cosmos-db-data"></a>Indexer les données Azure Cosmos DB

Une fois la source de données et l’indexeur créés, le code qui exécute l’indexeur est simple :

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Cet exemple inclut un bloc try-catch simple pour signaler les erreurs susceptibles de se produire pendant l’exécution.

Une fois l’indexeur Azure Cosmos DB exécuté, l’index de recherche contient un ensemble complet d’exemples de documents d’hôtel. Toutefois, le champ des chambres pour chaque hôtel est un tableau vide, car la source de données Azure Cosmos DB ne contenait aucun détail sur les chambres. Ensuite, le programme extrait les données des chambres du stockage d’objets blob et les fusionne.

### <a name="create-blob-storage-data-source-and-indexer"></a>Créer l’indexeur et la source de données du stockage blob

Pour obtenir les détails des chambres, le programme configure tout d’abord une source de données de stockage blob pour référencer un ensemble de fichiers d’objets blob JSON individuels.

```csharp
private static async Task CreateAndRunBlobIndexer(string indexName, SearchServiceClient searchService)
{
    DataSource blobDataSource = DataSource.AzureBlobStorage(
        name: configuration["BlobStorageAccountName"],
        storageConnectionString: configuration["BlobStorageConnectionString"],
        containerName: "hotel-rooms");

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(blobDataSource);
```

Une fois la source de données créée, le programme configure un indexeur d’objets blob nommé **hotel-rooms-blob-indexer**.

```csharp
    // Add a field mapping to match the Id field in the documents to 
    // the HotelId key field in the index
    List<FieldMapping> map = new List<FieldMapping> {
        new FieldMapping("Id", "HotelId")
    };

    Indexer blobIndexer = new Indexer(
        name: "hotel-rooms-blob-indexer",
        dataSourceName: blobDataSource.Name,
        targetIndexName: indexName,
        fieldMappings: map,
        parameters: new IndexingParameters().ParseJson(),
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

    // Reset the indexer if it already exists
    bool exists = await searchService.Indexers.ExistsAsync(blobIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(blobIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(blobIndexer);
```

Les objets blob JSON contiennent un champ de clé nommé **`Id`** au lieu de **`HotelId`** . Le code utilise la classe `FieldMapping` pour indiquer à l’indexeur de rediriger la valeur du champ **`Id`** vers la clé de document **`HotelId`** dans l’index.

Les indexeurs de stockage d’objets blob peuvent utiliser des paramètres qui identifient le mode d’analyse à utiliser. Le mode d’analyse diffère selon que les objets blob représentent un document unique ou plusieurs documents au sein du même objet blob. Dans cet exemple, comme chaque objet blob représente un document d’index unique, le code utilise le paramètre `IndexingParameters.ParseJson()`.

Pour plus d’informations sur les paramètres d’analyse d’indexeur pour les objets blob JSON, consultez [Indexer des objets blob JSON](search-howto-index-json-blobs.md). Pour plus d’informations sur la spécification de ces paramètres à l’aide du SDK .NET, consultez la classe [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions).

Le programme supprime les indexeurs portant le même nom avant de créer le nouveau, au cas où vous souhaiteriez exécuter cet exemple plusieurs fois.

Cet exemple définit une planification pour l’indexeur, afin qu’il s’exécute une fois par jour. Si vous ne souhaitez pas que l’indexeur soit automatiquement réexécuté à l’avenir, vous pouvez supprimer la propriété de planification de cet appel.

### <a name="index-blob-data"></a>Indexer des données blob

Une fois l’indexeur et la source de données de stockage d’objets blob créés, le code qui exécute l’indexeur est simple :

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Étant donné que l’index a déjà été rempli avec les données d’hôtels à partir de la base de données Azure Cosmos DB, l’indexeur d’objets blob met à jour les documents existants dans l’index et ajoute les détails des chambres.

> [!NOTE]
> Si vous avez les mêmes champs non-clé dans vos deux sources de données et que les données au sein de ces champs ne correspondent pas, l’index contient les valeurs de l’indexeur qui a été exécuté le plus récemment. Dans notre exemple, les deux sources de données contiennent un champ **HotelName**. Si, pour une raison quelconque, les données de ce champ sont différentes pour des documents ayant la même valeur de clé, les données **HotelName** de la source de données qui a été indexée le plus récemment sont la valeur stockée dans l’index.

## <a name="5---search"></a>5 - Recherche

Une fois le programme exécuté, vous pouvez explorer l’index de recherche rempli en utilisant l’[**Explorateur de recherche**](search-explorer.md) dans le portail.

Dans le portail Azure, ouvrez la page **Vue d’ensemble** du service de recherche, puis recherchez l’index **hotel-rooms-sample** dans la liste **Index**.

  ![Liste des index de Recherche cognitive Azure](media/tutorial-multiple-data-sources/index-list.png "Liste des index de Recherche cognitive Azure")

Cliquez sur l’index hotel-rooms-sample dans la liste. Vous verrez une interface de l’Explorateur de recherche pour l’index. Entrez une requête portant sur un terme, par exemple « Luxury » (Luxe). Vous devriez voir au moins un document dans les résultats, et ce document doit afficher une liste d’objets de chambre dans son tableau de chambres.

## <a name="reset-and-rerun"></a>Réinitialiser et réexécuter

Dans les premières étapes expérimentales du développement, l’approche la plus pratique pour les itérations de conception consiste à supprimer les objets de Recherche cognitive Azure et à autoriser votre code à les reconstruire. Les noms des ressources sont uniques. La suppression d’un objet vous permet de le recréer en utilisant le même nom.

L’exemple de code pour ce tutoriel recherche les objets existants et les supprime pour vous permettre de réexécuter votre code.

Vous pouvez également utiliser le portail pour supprimer les index, les indexeurs et les sources de données.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous travaillez dans votre propre abonnement, il est judicieux à la fin d’un projet de supprimer les ressources dont vous n’avez plus besoin. Les ressources laissées en cours d’exécution peuvent vous coûter de l’argent. Vous pouvez supprimer les ressources une par une, ou choisir de supprimer le groupe de ressources afin de supprimer l’ensemble des ressources.

Vous pouvez rechercher et gérer les ressources dans le portail à l’aide des liens Toutes les ressources ou Groupes de ressources situés dans le volet de navigation de gauche.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous êtes familiarisé avec le concept d’investigation de données provenant de plusieurs sources, examinons de plus près la configuration de l’indexeur, en commençant par Cosmos DB.

> [!div class="nextstepaction"]
> [Configurer un indexeur Azure Cosmos DB](search-howto-index-cosmosdb.md)