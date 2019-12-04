---
title: 'Didacticiel : Indexer les données en C# de bases de données Azure SQL'
titleSuffix: Azure Cognitive Search
description: Dans ce tutoriel C#, vous vous connectez à une base de données Azure SQL, extrayez des données interrogeables et les chargez dans un index de Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 36215403f99cc86ab4fb111ce95a6b3190063d7b
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406709"
---
# <a name="tutorial-import-azure-sql-database-in-c-using-azure-cognitive-search-indexers"></a>Didacticiel : Importer une base de données Azure SQL en C# à l’aide d’indexeurs de Recherche cognitive Azure

Découvrez comment configurer un indexeur pour extraire des données pouvant faire l’objet d’une recherche à partir d’un exemple de base de données Azure SQL. [Les indexeurs](search-indexer-overview.md) sont un composant de Recherche cognitive Azure qui analyse les sources de données externes tout en alimentant un [index de recherche](search-what-is-an-index.md) avec le contenu. De tous les indexeurs, l’indexeur correspondant à Azure SQL Database est le plus couramment utilisé. 

Il est utile de maîtriser la configuration de l’indexeur dans la mesure où cela simplifie la quantité de code à écrire et à gérer. Au lieu de préparer et d’envoyer un jeu de données conformes au schéma JSON, vous pouvez attacher un indexeur à une source de données, faire en sorte que l’indexeur extrait des données et les insère dans un index, et vous pouvez également exécuter l’indexeur selon une planification périodique pour récupérer les modifications dans la source sous-jacente.

Dans ce tutoriel, vous allez effectuer les tâches suivantes en utilisant les [bibliothèques de client .NET Recherche cognitive Azure](https://aka.ms/search-sdk) et une application console .NET Core :

> [!div class="checklist"]
> * Ajouter des informations de service de recherche aux paramètres d’application
> * Préparer un jeu de données externe dans la base de données Azure SQL 
> * Passer en revue les définitions d’index et d’indexeur dans l’exemple de code
> * Exécuter le code de l’indexeur pour importer des données
> * Rechercher l’index
> * Afficher la configuration de l’indexeur dans le portail

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Voici les services, outils et données utilisés dans ce guide de démarrage rapide. 

[Créez un service Recherche cognitive Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce tutoriel.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) stocke la source de données externes utilisée par un indexeur. L’exemple de solution fournit un fichier de données SQL pour créer la table. Les étapes de création du service et de la base de données sont fournies dans ce tutoriel.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), quelle que soit l’édition, peut être utilisé pour exécuter l’exemple de solution. L’exemple de code et les instructions ont été testés dans l’édition Communauté gratuite.

[Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) fournit l’exemple de solution situé dans le dépôt GitHub des exemples Azure. Téléchargez et extrayez la solution. Par défaut, les solutions sont en lecture seule. Cliquez avec le bouton droit sur la solution et désélectionnez l’attribut de lecture seule pour pouvoir modifier les fichiers.

> [!Note]
> Si vous utilisez le service Recherche cognitive Azure gratuit, vous êtes limité à trois index, trois indexeurs et trois sources de données. Ce didacticiel crée une occurrence de chaque élément. Assurez-vous de disposer de l’espace suffisant sur votre service pour accepter les nouvelles ressources.

## <a name="get-a-key-and-url"></a>Obtenir une clé et une URL

Les appels REST requièrent l’URL du service et une clé d’accès et ce, sur chaque demande. Un service de recherche est créé avec les deux. Ainsi, si vous avez ajouté la Recherche cognitive Azure à votre abonnement, effectuez ce qui suit pour obtenir les informations nécessaires :

1. [Connectez-vous au portail Azure](https://portal.azure.com/), puis dans la page **Vue d’ensemble** du service de recherche, récupérez l’URL. Voici un exemple de point de terminaison : `https://mydemo.search.windows.net`.

1. Dans **Paramètres** > **Clés**, obtenez une clé d’administration pour avoir des droits d’accès complets sur le service. Il existe deux clés d’administration interchangeables, fournies pour assurer la continuité de l’activité au cas où vous deviez en remplacer une. Vous pouvez utiliser la clé primaire ou secondaire sur les demandes d’ajout, de modification et de suppression d’objets.

![Obtenir un point de terminaison et une clé d’accès HTTP](media/search-get-started-postman/get-url-key.png "Obtenir une clé d’accès et un point de terminaison HTTP")

Toutes les demandes nécessitent une clé API sur chaque demande envoyée à votre service. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

## <a name="set-up-connections"></a>Configurer les connexions
Les informations de connexion aux services requis sont spécifiées dans le fichier **appsettings.json** de la solution. 

1. Dans Visual Studio, ouvrez le fichier **DotNetHowToIndexers.sln**.

1. Dans l’Explorateur de solutions, ouvrez **appsettings.json** pour pouvoir renseigner chaque paramètre.  

Vous pouvez renseigner les deux premières entrées maintenant, en utilisant l’URL et les clés d’administration de votre service Recherche cognitive Azure. Dans la mesure où le point de terminaison est `https://mydemo.search.windows.net`, le nom du service à fournir est `mydemo`.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

La dernière entrée nécessite une base de données existante. Vous allez la créer au cours de la prochaine étape.

## <a name="prepare-sample-data"></a>Préparer l’exemple de données

Au cours de cette étape, créez une source de données externe qu’un indexeur peut analyser. Vous pouvez utiliser le portail Azure et le fichier *hotels.sql* à partir de l’exemple pour créer le jeu de données dans Azure SQL Database. Recherche cognitive Azure utilise des ensembles de lignes aplatis, comme celui généré à partir d’une vue ou d’une requête. Le fichier SQL de l’exemple de solution crée et remplit une table unique.

L’exercice suivant suppose l’absence de serveur ou de base de données existante et vous invite à créer les deux lors de l’étape 2. Éventuellement, si vous disposez d’une ressource, vous pouvez y ajouter la table hôtels, en commençant à l’étape 4.

1. [Connectez-vous au portail Azure](https://portal.azure.com/). 

2. Recherchez ou créez une **base de données Azure SQL** pour créer une base de données, un serveur et un groupe de ressources. Vous pouvez utiliser les valeurs par défaut et le niveau de tarification le moins élevé. Un des avantages lié à la création d’un serveur est de pouvoir spécifier un nom d’utilisateur administrateur et le mot de passe nécessaire pour la création et le chargement des tables lors d’une étape ultérieure.

   ![Nouvelle page de base de données](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Cliquez sur **Créer** pour déployer le nouveau serveur et la base de données. Patientez jusqu’au déploiement du serveur et de la base de données.

4. Ouvrez la page Base de données SQL de votre nouvelle base de données, si elle n’est pas encore ouverte. Le nom de la ressource doit indiquer *Base de données SQL* et non *SQL Server*.

   ![Page de la base de données SQL](./media/search-indexer-tutorial/hotels-db.png)

4. Dans le volet de navigation, cliquez sur **Éditeur de requêtes (préversion)** .

5. Cliquez sur **Connexion** et entrez le nom d’utilisateur et un mot de passe d’administrateur du serveur.

6. Cliquez sur **Ouvrir la requête** et accédez à l’emplacement d’*hotels.sql*. 

7. Sélectionnez le fichier et cliquez sur **Ouvrir**. Le script doit ressembler à la capture d’écran suivante :

   ![Script SQL](./media/search-indexer-tutorial/sql-script.png)

8. Cliquez sur **Exécuter** pour exécuter la requête. Dans le volet Résultats, vous devez voir un message de réussite de la requête pour 3 lignes.

9. Pour renvoyer un ensemble de lignes de cette table, vous pouvez exécuter la requête suivante comme étape de vérification :

    ```sql
    SELECT HotelId, HotelName, Tags FROM Hotels
    ```
    Le prototype de requête `SELECT * FROM Hotels` ne fonctionne pas dans l’éditeur de requête. L’exemple de données inclut les coordonnées géographiques du champ Emplacement qui n’est pas géré dans l’éditeur pour l’instant. Pour obtenir la liste des autres colonnes de la requête, vous pouvez exécuter l’instruction suivante : `SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. Maintenant que vous avez un jeu de données externe, copiez la chaîne de connexion ADO.NET pour la base de données. Sur la page Base de données SQL de votre base de données, accédez à **Paramètres** > **Chaînes de connexion**, puis copiez la chaîne de connexion ADO.NET.
 
    Une chaîne de connexion ADO.NET ressemble à l’exemple suivant, modifié pour utiliser un nom de base de données, un nom d’utilisateur et un mot de passe valides.

    ```sql
    Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```
11. Collez la chaîne de connexion « AzureSqlConnectionString » comme troisième entrée du fichier **appsettings.json** dans Visual Studio.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-the-code"></a>Comprendre le code

Une fois que les données et les paramètres de configuration sont en place, l’exemple de programme dans **DotNetHowToIndexers.sln** est prêt à créer et à exécuter. Avant cela, prenez une minute pour étudier les définitions d’index et d’indexeur de cet exemple. Le code qui convient se trouve dans deux fichiers :

  + **Hotel.cs**, contenant le schéma qui définit l’index
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

Dans ce didacticiel, l’indexeur extrait les données d’une source de données. Dans la pratique, vous pouvez attacher plusieurs indexeurs au même index en créant un index consolidé pouvant faire l’objet de recherches à partir de plusieurs sources de données. Vous pouvez utiliser la même paire index/indexeur en variant juste les sources de données ou un index avec diverses combinaisons d’indexeurs et de sources de données, en fonction de vos besoins en matière de flexibilité.

### <a name="in-programcs"></a>Dans Program.cs

Le programme principal inclut une logique pour la création d’un client, d’un index, d’une source de données et d’un indexeur. Le code recherche et supprime les ressources existantes du même nom, en supposant que vous pouvez exécuter ce programme plusieurs fois.

L’objet de source de données est configuré avec des paramètres spécifiques aux ressources de base de données SQL Azure, notamment [l’indexation incrémentielle](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows), pour tirer parti des [fonctionnalités de détection des modifications](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) d’Azure SQL. La base de données des hôtels de démonstration dans Azure SQL a une colonne « suppression réversible » nommée **IsDeleted**. Quand cette colonne est définie sur true dans la base de données, l’indexeur supprime le document correspondant dans l’index de Recherche cognitive Azure.

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



## <a name="run-the-indexer"></a>Exécuter l’indexeur

Dans cette étape, compilez et exécutez le programme. 

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **DotNetHowToIndexers** et sélectionnez **Build**.
2. Là encore, avec le bouton droit cliquez sur **DotNetHowToIndexers**, puis sur **Déboguer** > **Démarrer une nouvelle instance**.

Le programme s’exécute en mode débogage. Une fenêtre de console signale l’état de chaque opération.

  ![Script SQL](./media/search-indexer-tutorial/console-output.png)

Votre code s’exécute localement dans Visual Studio en se connectant à votre service de recherche sur Azure, qui à son tour utilise la chaîne de connexion pour se connecter à Azure SQL Database et récupérer le jeu de données. Ces nombreuses opérations sont source de plusieurs points de défaillance potentiels. Si vous obtenez une erreur, vérifiez tout d’abord les conditions suivantes :

+ Les informations de connexion du service de recherche que vous fournissez se limitent au nom du service de ce didacticiel. Si vous avez entré l’URL complète, les opérations s’arrêtent à la création d’index avec une erreur signalant un échec de connexion.

+ Informations de connexion à la base de données dans **appsettings.json**. Il doit s’agir de la chaîne de connexion ADO.NET obtenue à partir du portail et modifiée pour inclure un nom d’utilisateur et un mot de passe valides pour votre base de données. Le compte d’utilisateur doit avoir l’autorisation de récupérer des données.

+ Limites des ressources. N’oubliez pas que le niveau Gratuit a une limite de 3 index, indexeurs et sources de données. Un service ayant atteint la limite maximale ne peut pas créer de nouveaux objets.

## <a name="search-the-index"></a>Rechercher l’index 

Dans le portail Azure, dans la page de vue d’ensemble du service de recherche, cliquez sur **Explorateur de recherche** en haut pour soumettre des requêtes sur le nouvel index.

1. Cliquez sur **Modifier l’index** en haut pour sélectionner l’index *hotels*.

2. Cliquez sur le bouton **Rechercher** pour lancer une recherche vide. 

   Les trois entrées de votre index sont renvoyées en tant que documents JSON. L’explorateur de recherche renvoie des documents au format JSON afin que vous puissiez afficher l’ensemble de la structure.

3. Ensuite, entrez une chaîne de recherche : `search=river&$count=true`. 

   Cette requête appelle la recherche en texte intégral sur le terme `river` et le résultat inclut le nombre de documents correspondants. Connaître le nombre de documents correspondants est utile dans les scénarios de test lorsque vous avez un index de grande taille comportant des milliers, voire des millions de documents. Dans ce cas, un seul document correspond à la requête.

4. Enfin, entrez une chaîne de recherche qui limite la sortie JSON aux champs pertinents : `search=river&$count=true&$select=hotelId, baseRate, description`. 

   La réponse à la requête se réduit aux champs sélectionnés, ce qui entraîne une sortie plus concise.

## <a name="view-indexer-configuration"></a>Afficher la configuration de l’indexeur

Tous les indexeurs, y compris celui que vous venez de créer par programme, sont répertoriés dans le portail. Vous pouvez ouvrir une définition d’indexeur et afficher sa source de données ou configurer une planification d’actualisation pour sélectionner les lignes nouvelles et modifiées.

1. [Connectez-vous au portail Azure](https://portal.azure.com/) et, à la page **Vue d’ensemble** de votre service de recherche, cliquez sur les liens **Index**, **Indexeurs** et **Sources de données**.
3. Sélectionnez des objets individuels pour afficher ou modifier les paramètres de configuration.

   ![Vignettes d’indexeur et de source de données](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Le moyen le plus rapide de procéder à un nettoyage à la fin d’un tutoriel consiste à supprimer le groupe de ressources contenant le service Recherche cognitive Azure. Vous pouvez maintenant supprimer le groupe de ressources pour supprimer définitivement tout ce qu’il contient. Dans le portail, le nom du groupe de ressources figure dans la page Vue d’ensemble du service Recherche cognitive Azure.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez attacher un enrichissement intégrant l’intelligence artificielle à un pipeline d’indexeur. Comme prochaine étape, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Indexation de documents dans Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md)