---
title: 'Tutoriel : Indexer les données en C# de bases de données Azure SQL'
titleSuffix: Azure Cognitive Search
description: Dans ce tutoriel C#, vous vous connectez à une base de données Azure SQL, extrayez des données interrogeables et les chargez dans un index de Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: 978587b68e719b79db31ff25adaf2b38d2235095
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650055"
---
# <a name="tutorial-index-azure-sql-data-in-c-using-azure-cognitive-search-indexers"></a>Tutoriel : Indexer des données Azure SQL en C# avec des indexeurs Recherche cognitive Azure

En utilisant C#, configurez un [indexeur](search-indexer-overview.md) qui extrait des données interrogeables d’une base de données Azure SQL et les envoie à un index de recherche. Dans ce tutoriel, vous allez effectuer les tâches suivantes en utilisant les [bibliothèques de client .NET Recherche cognitive Azure](https://aka.ms/search-sdk) et une application console .NET Core :

> [!div class="checklist"]
> * Créer une source de données qui se connecte à une base de données Azure SQL
> * Configurer un indexeur
> * Exécuter un indexeur pour charger des données dans un index
> * Interroger un index dans le cadre d’une étape de vérification

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Créer](search-create-service-portal.md) ou [rechercher un service de recherche existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Vous pouvez utiliser le service gratuit pour ce tutoriel. Avec un service de recherche gratuit, vous êtes limité à trois index, trois indexeurs et trois sources de données. Ce didacticiel crée une occurrence de chaque élément. Avant de commencer, veillez à disposer de l’espace suffisant sur votre service pour accepter les nouvelles ressources.

## <a name="download-source-code"></a>Téléchargement du code source

Le code source pour ce tutoriel se trouve dans le dossier [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) du dépôt GitHub [Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started).

## <a name="get-a-key-and-url"></a>Obtenir une clé et une URL

Les appels d’API nécessitent l’URL du service et une clé d’accès. Un service de recherche est créé avec les deux. Ainsi, si vous avez ajouté la Recherche cognitive Azure à votre abonnement, effectuez ce qui suit pour obtenir les informations nécessaires :

1. [Connectez-vous au portail Azure](https://portal.azure.com/), puis dans la page **Vue d’ensemble** du service de recherche, récupérez l’URL. Voici un exemple de point de terminaison : `https://mydemo.search.windows.net`.

1. Dans **Paramètres** > **Clés**, obtenez une clé d’administration pour avoir des droits d’accès complets sur le service. Il existe deux clés d’administration interchangeables, fournies pour assurer la continuité de l’activité au cas où vous deviez en remplacer une. Vous pouvez utiliser la clé primaire ou secondaire sur les demandes d’ajout, de modification et de suppression d’objets.

   ![Obtenir un point de terminaison et une clé d’accès HTTP](media/search-get-started-postman/get-url-key.png "Obtenir un point de terminaison et une clé d’accès HTTP")

## <a name="set-up-connections"></a>Configurer les connexions

1. Démarrez Visual Studio et ouvrez le fichier **DotNetHowToIndexers.sln**.

1. Dans l’Explorateur de solutions, ouvrez **appsettings.json** et remplacez les valeurs d’espace réservé par les informations de connexion à votre service de recherche. Si l’URL complète est « https://my-demo-service.search.windows.net  », vous devez spécifier le nom de service « my-demo-service ».

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
    }
    ```

La dernière entrée nécessite une base de données existante. Vous allez la créer au cours de la prochaine étape.

## <a name="prepare-sample-data"></a>Préparer l’exemple de données

Au cours de cette étape, vous allez créer, sur Azure SQL Database, une source de données externe qu’un indexeur peut analyser. Vous pouvez utiliser le portail Azure et le fichier *hotels.sql* à partir de l’exemple pour créer le jeu de données dans Azure SQL Database. Recherche cognitive Azure utilise des ensembles de lignes aplatis, comme celui généré à partir d’une vue ou d’une requête. Le fichier SQL de l’exemple de solution crée et remplit une table unique.

Si vous disposez d’une ressource Azure SQL Database, vous pouvez y ajouter la table hotels en commençant à l’étape 4.

1. [Connectez-vous au portail Azure](https://portal.azure.com/).

1. Recherchez ou créez une **base de données SQL**. Vous pouvez utiliser les valeurs par défaut et le niveau de tarification le moins élevé. Un des avantages lié à la création d’un serveur est de pouvoir spécifier un nom d’utilisateur administrateur et le mot de passe nécessaire pour la création et le chargement des tables lors d’une étape ultérieure.

   ![Page de création de base de données](./media/search-indexer-tutorial/indexer-new-sqldb.png "Nouvelle page de base de données")

1. Cliquez sur **Vérifier + créer** pour déployer le nouveau serveur et la nouvelle base de données. Patientez jusqu’au déploiement du serveur et de la base de données.

1. Dans le volet de navigation, cliquez sur **Éditeur de requêtes (préversion)** et entrez le nom d’utilisateur et le mot de passe de l’administrateur du serveur. 

   Si l’accès est refusé, copiez l’adresse IP du client à partir du message d’erreur, puis cliquez sur le lien **Définir le pare-feu du serveur** pour ajouter une règle autorisant l’accès à partir de votre ordinateur client, en utilisant l’adresse IP de votre client pour la plage. La prise en compte de la règle peut prendre plusieurs minutes.

1. Dans l’Éditeur de requête, cliquez sur **Ouvrir la requête** et accédez à l’emplacement du fichier *hotels.sql* sur votre ordinateur local. 

1. Sélectionnez le fichier et cliquez sur **Ouvrir**. Le script doit ressembler à la capture d’écran suivante :

   ![Script SQL](./media/search-indexer-tutorial/sql-script.png "Script SQL")

1. Cliquez sur **Exécuter** pour exécuter la requête. Dans le volet Résultats, vous devez voir un message de réussite de la requête pour 3 lignes.

1. Pour renvoyer un ensemble de lignes de cette table, vous pouvez exécuter la requête suivante comme étape de vérification :

    ```sql
    SELECT * FROM Hotels
    ```

1. Copiez la chaîne de connexion ADO.NET pour la base de données. Sous **Paramètres** > **Chaînes de connexion**, copiez la chaîne de connexion ADO.NET, similaire à celle présentée dans l’exemple ci-dessous.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

1. Collez la chaîne de connexion « AzureSqlConnectionString » comme troisième entrée du fichier **appsettings.json** dans Visual Studio.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

1. Entrez votre mot de passe dans la chaîne de connexion dans le fichier **appsettings.json**. Les noms d’utilisateur et de base de données sont copiés dans votre chaîne de connexion, mais le mot de passe doit être entré manuellement.

## <a name="build-the-solution"></a>Générez la solution.

Appuyez sur F5 pour générer la solution. Le programme s’exécute en mode débogage. Une fenêtre de console signale l’état de chaque opération.

   ![Sortie de la console](./media/search-indexer-tutorial/console-output.png "Sortie de la console")

Votre code s’exécute localement dans Visual Studio en se connectant à votre service de recherche sur Azure qui, à son tour, se connecte à Azure SQL Database et récupère le jeu de données. Étant donné le grand nombre d’opérations, il existe plusieurs points de défaillance potentiels. Si une erreur survient, commencez par vérifier les conditions suivantes :

+ Les informations de connexion du service de recherche que vous fournissez se limitent au nom du service de ce didacticiel. Si vous avez entré l’URL complète, les opérations s’arrêtent à la création d’index avec une erreur signalant un échec de connexion.

+ Informations de connexion à la base de données dans **appsettings.json**. Il doit s’agir de la chaîne de connexion ADO.NET obtenue à partir du portail et modifiée pour inclure un nom d’utilisateur et un mot de passe valides pour votre base de données. Le compte d’utilisateur doit avoir l’autorisation de récupérer des données. L’accès à partir de l’adresse IP du client local doit être autorisé.

+ Limites des ressources. N’oubliez pas que le niveau Gratuit a une limite de 3 index, indexeurs et sources de données. Un service ayant atteint la limite maximale ne peut pas créer de nouveaux objets.

## <a name="check-results"></a>Vérifier les résultats

Utilisez le portail Azure pour vérifier la création des objets, puis utilisez l’**Explorateur de recherche** pour interroger l’index.

1. [Connectez-vous au portail Azure](https://portal.azure.com/). Dans la page **Vue d’ensemble** de votre service de recherche, ouvrez chaque liste successivement pour vérifier que l’objet est créé. Sous **Index**, **Indexeurs** et **Sources de données**, vous devez voir « hotels », « azure-sql-indexer » et « azure-sql » respectivement.

   ![Vignettes d’indexeur et de source de données](./media/search-indexer-tutorial/tiles-portal.png)

1. Sélectionnez l’index hotels. Dans la page hotels, le premier onglet est celui de l’**Explorateur de recherche**. 

1. Cliquez sur **Rechercher** pour émettre une requête vide. 

   Les trois entrées de votre index sont renvoyées en tant que documents JSON. L’explorateur de recherche renvoie des documents au format JSON afin que vous puissiez afficher l’ensemble de la structure.

   ![Interroger un index](./media/search-indexer-tutorial/portal-search.png "Interroger un index")
   
1. Ensuite, entrez une chaîne de recherche : `search=river&$count=true`. 

   Cette requête appelle la recherche en texte intégral sur le terme `river` et le résultat inclut le nombre de documents correspondants. Connaître le nombre de documents correspondants est utile dans les scénarios de test lorsque vous avez un index de grande taille comportant des milliers, voire des millions de documents. Dans ce cas, un seul document correspond à la requête.

1. Enfin, entrez une chaîne de recherche qui limite la sortie JSON aux champs pertinents : `search=river&$count=true&$select=hotelId, baseRate, description`. 

   La réponse à la requête se réduit aux champs sélectionnés, ce qui entraîne une sortie plus concise.

## <a name="explore-the-code"></a>Explorer le code

Vous avez compris ce que crée l’exemple de code. Revenons à présent à la solution pour revoir le code. Le code approprié se trouve dans deux fichiers :

  + **hotel.cs**, contenant un schéma qui définit l’index
  + **Program.cs**, contenant les fonctions permettant de créer et de gérer des structures dans votre service

### <a name="in-hotelcs"></a>Dans Hotel.cs

Le schéma d’index définit la collection de champs, y compris les attributs spécifiant les opérations autorisées, pour savoir, par exemple, si un champ peut faire l’objet d’une interrogation en texte intégral, d’un filtrage ou d’un tri comme indiqué dans la définition de champ suivante pour HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Un schéma peut également inclure d’autres éléments, y compris des profils de notation pour améliorer un score de recherche, des analyseurs personnalisés et d’autres constructions. Toutefois, en ce qui nous concerne, le schéma est peu défini et comporte uniquement des champs trouvés dans les exemples de jeux de données.

### <a name="in-programcs"></a>Dans Program.cs

Le programme principal inclut une logique pour la création d’un client, d’un index, d’une source de données et d’un indexeur. Le code recherche et supprime les ressources existantes du même nom, en supposant que vous pouvez exécuter ce programme plusieurs fois.

L’objet de source de données est configuré avec des paramètres propres aux ressources de base de données Azure SQL, notamment l’[indexation partielle ou incrémentielle](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows), pour tirer parti des [fonctionnalités de détection des modifications](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) d’Azure SQL. La base de données des hôtels de démonstration dans Azure SQL a une colonne « suppression réversible » nommée **IsDeleted**. Quand cette colonne est définie sur true dans la base de données, l’indexeur supprime le document correspondant dans l’index de Recherche cognitive Azure.

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

Un objet de l’indexeur est indépendant de la plateforme, où la configuration, la planification et l’appel sont les mêmes quelle que soit la source. Cet exemple d’indexeur inclut une planification, une option de réinitialisation qui efface l’historique de l’indexeur, et appelle une méthode pour créer et exécuter immédiatement l’indexeur.

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous travaillez dans votre propre abonnement, il est judicieux à la fin d’un projet de supprimer les ressources dont vous n’avez plus besoin. Les ressources laissées en cours d’exécution peuvent vous coûter de l’argent. Vous pouvez supprimer les ressources une par une, ou choisir de supprimer le groupe de ressources afin de supprimer l’ensemble des ressources.

Vous pouvez rechercher et gérer les ressources dans le portail à l’aide des liens Toutes les ressources ou Groupes de ressources situés dans le volet de navigation de gauche.

Si vous utilisez un service gratuit, n’oubliez pas que vous êtes limité à trois index, indexeurs et sources de données. Vous pouvez supprimer des éléments un par un dans le portail pour ne pas dépasser la limite.

## <a name="next-steps"></a>Étapes suivantes

Le service Recherche cognitive Azure propose des indexeurs pour plusieurs sources de données Azure. Votre prochaine étape consiste à en apprendre davantage sur l’indexeur pour le Stockage Blob Azure.

> [!div class="nextstepaction"]
> [Indexation de documents dans Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md)