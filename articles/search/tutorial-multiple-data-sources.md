---
title: 'Tutoriel C# : Indexer plusieurs sources de données'
titleSuffix: Azure Cognitive Search
description: Découvrez comment importer des données à partir de plusieurs sources de données dans un seul index Recherche cognitive Azure à l’aide d’indexeurs. Ce didacticiel et cet exemple de code utilisent le langage C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/23/2019
ms.openlocfilehash: aac5dc300009ec682ef1599ad654415f5c4ad190
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495057"
---
# <a name="c-tutorial-combine-data-from-multiple-data-sources-in-one-azure-cognitive-search-index"></a>Tutoriel C# : Combiner des données provenant de plusieurs sources de données dans un index Recherche cognitive Azure

La Recherche cognitive Azure peut importer, analyser et indexer des données à partir de plusieurs sources de données dans un index de recherche combiné unique. Cette fonctionnalité prend en charge les situations où des données structurées sont agrégées avec des données moins structurées, voire de texte brut, provenant d’autres sources, telles que des documents de texte, HTML ou JSON.

Ce tutoriel explique comment indexer des données d’hôtels provenant d’une source de données Azure Cosmos DB et les fusionner avec des détails sur les chambres d’hôtel tirés de documents Stockage Blob Azure. Le résultat est un index de recherche d’hôtel combiné contenant des types de données complexes.

Ce tutoriel utilise le langage C#, le SDK .NET pour la Recherche cognitive Azure et le portail Azure pour effectuer les tâches suivantes :

> [!div class="checklist"]
> * Charger des exemples de données et créer des sources de données
> * Identifier la clé de document
> * Définir et créer l’index
> * Indexer les données d’hôtels issues d’Azure Cosmos DB
> * Fusionner les données des chambres d’hôtel provenant du stockage d’objets blob

## <a name="prerequisites"></a>Conditions préalables requises

Voici les services, outils et données utilisés dans ce guide de démarrage rapide. 

- [Créez un service Recherche cognitive Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce tutoriel.

- [Créez un compte Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) pour stocker les exemples de données d’hôtels.

- [Créez un compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pour stocker les exemples de données de chambres.

- [Installez Visual Studio 2019](https://visualstudio.microsoft.com/) pour l’utiliser comme IDE.

### <a name="install-the-project-from-github"></a>Installer le projet à partir de GitHub

1. Recherchez l’exemple de dépôt sur GitHub : [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Sélectionnez **Clone or download** (Cloner ou télécharger) et créez votre copie privée locale du dépôt.
1. Ouvrez Visual Studio 2019 et installez le package NuGet de la Recherche cognitive Microsoft Azure, s’il n’est pas déjà installé. Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet**, puis **Gérer les packages NuGet pour la solution...** . Sous l’onglet **Parcourir**, recherchez et installez **Microsoft.Azure.Search** (version 9.0.1 ou ultérieure). Vous devrez valider une série de boîtes de dialogue supplémentaires pour terminer l’installation.

    ![Utilisation de NuGet pour ajouter des bibliothèques Azure](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. À l’aide de Visual Studio, accédez à votre dépôt local, puis ouvrez le fichier solution **AzureSearchMultipleDataSources.sln**.

## <a name="get-a-key-and-url"></a>Obtenir une clé et une URL

Pour interagir avec votre service Recherche cognitive Azure, vous devez disposer de l’URL du service et d’une clé d’accès. Un service de recherche est créé avec les deux. Ainsi, si vous avez ajouté la Recherche cognitive Azure à votre abonnement, effectuez ce qui suit pour obtenir les informations nécessaires :

1. Connectez-vous au [portail Azure](https://portal.azure.com/), puis dans la page **Vue d’ensemble** du service de recherche, récupérez l’URL. Voici un exemple de point de terminaison : `https://mydemo.search.windows.net`.

1. Dans **Paramètres** > **Clés**, obtenez une clé d’administration pour avoir des droits d’accès complets sur le service. Il existe deux clés d’administration interchangeables, fournies pour assurer la continuité de l’activité au cas où vous deviez en remplacer une. Vous pouvez utiliser la clé primaire ou secondaire sur les demandes d’ajout, de modification et de suppression d’objets.

![Obtenir un point de terminaison et une clé d’accès HTTP](media/search-get-started-postman/get-url-key.png "Obtenir un point de terminaison et une clé d’accès HTTP")

Toutes les demandes nécessitent une clé API sur chaque demande envoyée à votre service. Une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

## <a name="prepare-sample-azure-cosmos-db-data"></a>Préparer les exemples de données Azure Cosmos DB

Cet exemple utilise deux petits ensembles de données qui décrivent sept hôtels fictifs. Un ensemble décrit les hôtels eux-mêmes et est chargé dans une base de données Azure Cosmos DB. L’autre ensemble contient les détails des chambres d’hôtel et est fourni sous la forme de sept fichiers JSON distincts à charger dans Stockage Blob Azure.

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

## <a name="prepare-sample-blob-data"></a>Préparer les exemples de données blob

1. Connectez-vous au [portail Azure](https://portal.azure.com), accédez à votre compte de stockage Azure, cliquez sur **Objets blob**, puis sur **+ Conteneur**.

1. [Créez un conteneur d’objets blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) nommé **hotel-rooms** pour stocker les exemples de fichiers JSON des chambres d’hôtel. Vous pouvez définir le niveau d’accès public sur l’une de ses valeurs valides.

   Créer un ![conteneur d’objets blob](media/tutorial-multiple-data-sources/blob-add-container.png "Création d’un conteneur d’objets blob")

1. Une fois le conteneur créé, ouvrez-le et sélectionnez **Charger** dans la barre de commandes. Accédez au dossier contenant les exemples de fichiers. Sélectionnez-les tous, puis cliquez sur **Charger**.

   ![Charger des fichiers](media/tutorial-multiple-data-sources/blob-upload.png "Charger des fichiers")

Une fois le chargement terminé, les fichiers doivent apparaître dans la liste liée au conteneur de données.

## <a name="set-up-connections"></a>Configurer les connexions

Les informations de connexion pour le service de recherche et les sources de données sont spécifiées dans le fichier **appsettings.json** de la solution. 

1. Dans Visual Studio, ouvrez le fichier **AzureSearchMultipleDataSources.sln**.

1. Dans l’Explorateur de solutions, modifiez le fichier **appsettings.json**.  

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

### <a name="identify-the-document-key"></a>Identifier la clé de document

Dans la Recherche cognitive Azure, le champ de clé identifie de façon unique chaque document dans l’index. Chaque index de recherche doit comporter exactement un champ de clé de type `Edm.String`. Ce champ de clé doit être présent pour chaque document d’une source de données qui est ajouté à l’index. (il constitue en fait le seul champ obligatoire).

Lorsque vous indexez des données issues de plusieurs sources de données, utilisez une clé de document commune pour les fusionner depuis deux documents sources physiquement distincts dans un nouveau document de recherche situé dans l’index combiné. Il est souvent nécessaire de passer par une planification préalable pour identifier une clé de document significative pour votre index et vous assurer qu’elle existe dans les deux sources de données. Dans cette démonstration, la clé HotelId de chaque hôtel dans Cosmos DB est également présente dans les objets blob JSON des chambres dans le stockage d’objets blob.

Les indexeurs de la Recherche cognitive Azure peuvent utiliser des mappages de champs pour renommer, voire remettre en forme, les champs de données pendant le processus d’indexation, afin que les données sources puissent être redirigées vers le champ de l’index approprié.

Ainsi, dans notre exemple de données Azure Cosmos DB, l’identificateur d’hôtel est appelé **`HotelId`** . Mais dans les fichiers d’objets blob JSON pour les chambres d’hôtel, l’identificateur d’hôtel est nommé **`Id`** . Le programme gère cette situation en mappant le champ **`Id`** des objets blob au champ de clé **`HotelId`** de l’index.

> [!NOTE]
> Dans la plupart des cas, les clés de document générées automatiquement, telles que celles créées par défaut par certains indexeurs, ne constituent pas des clés de document appropriées pour les index combinés. En général, il est préférable d’utiliser une valeur de clé unique explicite qui existe déjà dans vos sources de données ou qui peut y être facilement ajoutée.

## <a name="understand-the-code"></a>Comprendre le code

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

### <a name="define-the-index"></a>Définir l’index

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

## <a name="search-your-json-files"></a>Rechercher vos fichiers JSON

Une fois le programme exécuté, vous pouvez explorer l’index de recherche rempli en utilisant l’[**Explorateur de recherche**](search-explorer.md) dans le portail.

Dans le portail Azure, ouvrez la page **Vue d’ensemble** du service de recherche, puis recherchez l’index **hotel-rooms-sample** dans la liste **Index**.

  ![Liste des index de Recherche cognitive Azure](media/tutorial-multiple-data-sources/index-list.png "Liste des index de Recherche cognitive Azure")

Cliquez sur l’index hotel-rooms-sample dans la liste. Vous verrez une interface de l’Explorateur de recherche pour l’index. Entrez une requête portant sur un terme, par exemple « Luxury » (Luxe). Vous devriez voir au moins un document dans les résultats, et ce document doit afficher une liste d’objets de chambre dans son tableau de chambres.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Le moyen le plus rapide de procéder à un nettoyage à la fin d’un tutoriel consiste à supprimer le groupe de ressources contenant le service Recherche cognitive Azure. Vous pouvez maintenant supprimer le groupe de ressources pour supprimer définitivement tout ce qu’il contient. Dans le portail, le nom du groupe de ressources figure dans la page Vue d’ensemble du service Recherche cognitive Azure.

## <a name="next-steps"></a>Étapes suivantes

Il existe plusieurs approches et plusieurs options pour l’indexation d’objets blob JSON. Si votre source de données inclut un contenu JSON, vous pouvez passer ces options en revue pour déterminer la mieux adaptée à votre scénario.

> [!div class="nextstepaction"]
> [Guide pratique pour indexer des objets blob JSON avec l’indexeur d’objets blob de la Recherche cognitive Azure](search-howto-index-json-blobs.md)
