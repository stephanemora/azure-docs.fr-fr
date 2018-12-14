---
title: Créer une application console .NET Core pour gérer des données dans le compte d’API SQL Azure Cosmos DB (kit SDK Version 3 - préversion)
description: Didacticiel qui crée une application de base de données en ligne et de console C# à l’aide du SDK .NET Core de l’API SQL d’Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/01/2018
ms.author: dech
ms.openlocfilehash: 92dcd62fa0079b22ba6a2721959a200c2556a4c1
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852589"
---

# <a name="build-a-net-core-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account-sdk-version-3-preview"></a>Créer une application console .NET Core pour gérer des données dans le compte d’API SQL Azure Cosmos DB (kit SDK Version 3 - préversion)

> [!div class="op_single_selector"]
>
> - [.NET Core (préversion)](sql-api-dotnet-core-get-started-preview.md)
> - [.NET Core](sql-api-dotnetcore-get-started.md)
> - [.NET (préversion)](sql-api-dotnet-get-started-preview.md)
> - [.NET](sql-api-get-started.md)
> - [Java](sql-api-java-get-started.md)
> - [Java asynchrone](sql-api-async-java-get-started.md)
> - [Node.JS](sql-api-nodejs-get-started.md)

Bienvenue dans le tutoriel Bien démarrer avec l’API SQL Azure Cosmos DB et .NET Core ! À la fin de ce tutoriel, vous disposerez d’une application console .NET Core qui crée et interroge des ressources Azure Cosmos DB. Ce tutoriel utilise la [version 3.0+](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) du kit SDK .NET Azure Cosmos DB, qui cible [.NET Standard 2.0.](https://docs.microsoft.com/dotnet/standard/net-standard)

Ce didacticiel contient les sections suivantes :

> [!div class="checklist"]
>
> - Création et connexion à un compte Azure Cosmos
> - Configuration de votre projet dans Visual Studio
> - Création d’une base de données et d’un conteneur
> - Ajout d’éléments au conteneur
> - Interrogation du conteneur
> - Opérations CRUD sur l’élément
> - Suppression de la base de données

Vous n’avez pas le temps de créer l’application ? Ne vous inquiétez pas ! La solution complète est disponible sur [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started). Pour obtenir des instructions rapides, allez à la section [Obtenir la solution complète](#GetSolution) .

Vous voulez créer une application Xamarin iOS, Android ou Forms à l’aide de l’API SQL et du SDK .NET Core ? Consultez la page [Créer des applications mobiles avec Xamarin et Azure Cosmos DB](mobile-apps-with-xamarin.md).

## <a name="prerequisites"></a>Prérequis

- Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [compte gratuit](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

- Si vous n’avez pas encore installé Visual Studio 2017, vous pouvez télécharger et utiliser la version gratuite [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Si vous développez une application de plateforme Windows universelle (UWP), vous devez utiliser **Visual Studio 2017 avec la version 15.4** ou une version ultérieure. Veillez à activer la charge de travail **Développement Azure** lors de l’installation de Visual Studio.

  - Si vous travaillez sur un système Mac OS ou Linux, vous pouvez développer des applications .NET Core à partir de la ligne de commande en installant le [SDK .NET Core](https://www.microsoft.com/net/core#macos) pour la plateforme de votre choix.

  - Si vous travaillez sur un système Windows, vous pouvez développer des applications .NET Core à partir de la ligne de commande en installant le [SDK .NET Core](https://www.microsoft.com/net/core#windows).

  - Vous pouvez utiliser votre propre éditeur ou télécharger [Visual Studio Code](https://code.visualstudio.com/), qui est disponible gratuitement pour les systèmes Windows, Linux et Mac OS.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Étape 1 : créer un compte Azure Cosmos DB

Commençons par créer un compte Azure Cosmos DB. Si vous avez déjà un compte que vous souhaitez utiliser, vous pouvez passer directement à l’étape [Configurer votre solution Visual Studio](#SetupVS). Si vous utilisez l’émulateur Azure Cosmos DB, suivez les étapes de la section [Émulateur Azure Cosmos DB](local-emulator.md) pour le configurer, puis passez directement à l’étape [Configurer votre solution Visual Studio](#SetupVS).

[!INCLUDE [cosmos-db-create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

## <a id="SetupVS"></a>Étape 2 : Configurer votre projet Visual Studio

1. Ouvrez **Visual Studio 2017** sur votre ordinateur.
1. Dans le menu **Fichier**, sélectionnez **Nouveau**, puis choisissez **Projet**.
1. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C#** / **Application console (.NET Core)**, donnez un nom à votre projet, puis cliquez sur **OK**.
   ![Capture d’écran de la fenêtre Nouveau projet](./media/sql-api-dotnetcore-get-started/dotnetcore-tutorial-visual-studio-new-project.png)
1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur votre nouvelle application console, qui se trouve sous votre solution Visual Studio, puis cliquez sur **Gérer les packages NuGet…**

   ![Capture d'écran du menu du clic droit pour le projet](./media/sql-api-dotnetcore-get-started/dotnetcore-tutorial-visual-studio-manage-nuget.png)

1. Dans l’onglet **NuGet**, cliquez sur **Parcourir** et tapez **Microsoft.Azure.Cosmos** dans la zone de recherche.
1. Dans les résultats, trouvez **Microsoft.Azure.Cosmos** et cliquez sur **Installer**.
   L’ID de package de la bibliothèque cliente d’API SQL Azure Cosmos DB est [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Capture d’écran du menu NuGet pour la recherche du Kit de développement logiciel (SDK) client Azure Cosmos DB](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

   Si vous obtenez un message concernant la vérification des modifications apportées à la solution, cliquez sur **OK**. Si vous obtenez un message concernant l’acceptation de la licence, cliquez sur **J’accepte**.

Parfait ! L’installation étant terminée, nous pouvons passer à l’écriture du code. Vous trouverez le projet de code complet de ce didacticiel dans [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started/).

## <a id="Connect"></a>Étape 3 : se connecter à un compte Azure Cosmos DB

1. Remplacez tout d’abord les références situées au début de votre application C#, dans le fichier **Program.cs**, par ces références :

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;
   using System.Collections.Generic;
   using System.Net;
   ```

1. À présent, ajoutez ces variables et ces constantes dans votre classe publique `Program`.

   ```csharp
   public class Program
   {
       // ADD THIS PART TO YOUR CODE

       // The Azure Cosmos DB endpoint for running this sample.
       private static readonly string EndpointUri = "<your endpoint here>";
       // The primary key for the Azure Cosmos account.
       private static readonly string PrimaryKey = "<your primary key>";

       // The Cosmos client instance
       private CosmosClient cosmosClient;

       // The database we will create
       private CosmosDatabase database;

       // The container we will create.
       private CosmosContainer container;

       // The name of the database and container we will create
       private string databaseId = "FamilyDatabase";
       private string containerId = "FamilyContainer";
   }
   ```

   Notez que si vous avez déjà utilisé la version précédente du kit SDK .NET, vous connaissez peut-être les termes « collection » et « document ». Étant donné qu’Azure Cosmos DB prend en charge plusieurs modèles d’API, les versions postérieures à la version 3.0 du kit SDK .NET utilisent les termes génériques « conteneur » et « élément ». Un conteneur peut être une collection, un graphe ou une table. Un élément peut être un document, arête/sommet ou ligne, et correspond au contenu à l’intérieur d’un conteneur. [En savoir plus sur les bases de données, les conteneurs et les éléments.](databases-containers-items.md)

1. Récupérez l’URL du point de terminaison et la clé primaire à partir du [portail Azure](https://portal.azure.com).

   Dans le portail Azure, accédez à votre compte Azure Cosmos DB, puis cliquez sur **Clés**.

   Copiez l’URI à partir du portail et collez-le dans `<your endpoint URL>` dans le fichier `Program.cs`. Copiez la CLÉ PRIMAIRE à partir du portail, puis collez-la dans `<your primary key>`.

   ![Capture d’écran de l’obtention des clés Azure Cosmos DB depuis le portail Azure](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. Nous allons créer ensuite une nouvelle instance de `CosmosClient` et configurer une génération de modèles automatique pour notre programme.

   Sous la méthode **Main**, ajoutez une nouvelle tâche asynchrone appelée **GetStartedDemoAsync** qui va instancier notre nouveau `CosmosClient`. Nous allons utiliser **GetStartedDemoAsync** comme point d’entrée appelant les méthodes qui opèrent sur les ressources Azure Cosmos DB.

   ```csharp
   public static async Task Main(string[] args)
   {
   }

   // ADD THIS PART TO YOUR CODE
   /*
       Entry point to call methods that operate on Azure Cosmos DB resources in this sample
   */
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
   }
   ```

1. Ajoutez le code suivant pour exécuter la tâche asynchrone **GetStartedDemoAsync** à partir de la méthode **Main**. La méthode **Main** va intercepter les exceptions et les consigner dans la console.

   ```csharp
   public static async Task Main(string[] args)
   {
       // ADD THIS PART TO YOUR CODE
       try
       {
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();
       }
       catch (CosmosException de)
       {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
       }
       catch (Exception e)
       {
           Console.WriteLine("Error: {0}\n", e);
       }
       finally
       {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
       }
   }
   ```

1. Sélectionnez **F5** pour exécuter votre application. La sortie de la fenêtre de console affiche le message `End of demo, press any key to exit.` qui confirme que la connexion à Azure Cosmos DB a été établie. Vous pouvez alors fermer la fenêtre de console.

Félicitations ! Vous avez établi une connexion à un compte de base de données Azure Cosmos DB.

## <a name="step-4-create-a-database"></a>Étape 4 : Créer une base de données

Vous pouvez créer une base de données à l’aide de la fonction [**CreateDatabaseIfNotExistsAsync**](https://aka.ms/CosmosDotnetAPIDocs) ou [**CreateDatabaseAsync**](https://aka.ms/CosmosDotnetAPIDocs) de la classe `Databases`. Une base de données est le conteneur logique d’éléments partitionnés entre les conteneurs.

1. Copiez et collez la méthode **CreateDatabase** sous votre méthode **GetStartedDemoAsync**. **CreateDatabase** crée une base de données avec l’ID `FamilyDatabase` s’il n’existe pas déjà, sinon avec l’ID spécifié dans le champ `databaseId`.

   ```csharp
   /*
       Create the database if it does not exist
   */
   private async Task CreateDatabase()
   {
       // Create a new database
       this.database = await this.cosmosClient.Databases.CreateDatabaseIfNotExistsAsync(databaseId);
       Console.WriteLine("Created Database: {0}\n", this.database.Id);
   }
   ```

1. Copiez et collez le code ci-dessous, dans lequel vous avez instancié le CosmosClient pour qu’il appelle la méthode **CreateDatabase** que vous venez d’ajouter.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

       //ADD THIS PART TO YOUR CODE
       await this.CreateDatabase();
   }
   ```

   À ce stade, votre code doit désormais ressembler à ceci, avec votre point de terminaison et la clé primaire renseignés. Notez que votre espace de noms varie en fonction du nom de votre projet.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;
   using System.Collections.Generic;
   using System.Net;

   namespace CosmosGettingStartedDotnetCoreTutorial
   {
       class Program
       {
           // The Azure Cosmos DB endpoint for running this sample.
           private static readonly string EndpointUri = "<your endpoint here>";
           // The primary key for the Azure Cosmos account.
           private static readonly string PrimaryKey = "<your primary key>";

           // The Cosmos client instance
           private CosmosClient cosmosClient;

           // The database we will create
           private CosmosDatabase database;

           // The container we will create.
           private CosmosContainer container;

           // The name of the database and container we will create
           private string databaseId = "FamilyDatabase";
           private string containerId = "FamilyContainer";

           public static async Task Main(string[] args)
           {
               try
               {
                   Console.WriteLine("Beginning operations...");
                   Program p = new Program();
                   await p.GetStartedDemoAsync();
               }
               catch (CosmosException de)
               {
                   Exception baseException = de.GetBaseException();
                   Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
               }
               catch (Exception e)
               {
                   Console.WriteLine("Error: {0}\n", e);
               }
               finally
               {
                   Console.WriteLine("End of demo, press any key to exit.");
                   Console.ReadKey();
               }
           }

           /*
               Entry point to call methods that operate on Azure Cosmos DB resources in this sample
           */
           public async Task GetStartedDemoAsync()
           {
               // Create a new instance of the Cosmos Client
               this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
               await this.CreateDatabase();
           }

           /*
               Create the database if it does not exist
           */
           private async Task CreateDatabase()
           {
               // Create a new database
               this.database = await this.cosmosClient.Databases.CreateDatabaseIfNotExistsAsync(databaseId);
               Console.WriteLine("Created Database: {0}\n", this.database.Id);
           }
       }
   }
   ```

Sélectionnez **F5** pour exécuter votre application.

Félicitations ! Vous avez créé une base de données Azure Cosmos DB.

## <a id="CreateColl"></a>Étape 5 : Créer un conteneur

> [!WARNING]
> L’appel de la méthode **CreateContainerIfNotExistsAsync** crée un conteneur, ce qui a une incidence sur la tarification. Pour plus d'informations, visitez notre [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/).

Vous pouvez créer un conteneur en utilisant la fonction [**CreateContainerIfNotExistsAsync**](https://aka.ms/CosmosDotnetAPIDocs) ou [**CreateContainerAsync**](https://aka.ms/CosmosDotnetAPIDocs) dans la classe **Containers**. Un conteneur est composé d’éléments (des documents JSON dans le cas de l’API SQL) et d’une logique d’application côté serveur JavaScript associée, par exemple des procédures stockées, des fonctions définies par l’utilisateur et des déclencheurs.

1. Copiez et collez la méthode **CreateContainer** sous votre méthode **CreateDatabase**. **CreateContainer** crée un conteneur avec l’ID `FamilyContainer` s’il n’existe pas déjà, sinon avec l’ID spécifié dans le champ `containerId`.

   ```csharp
   /*
       Create the container if it does not exist.
       Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
   */
   private async Task CreateContainer()
   {
       // Create a new container
       this.container = await this.database.Containers.CreateContainerIfNotExistsAsync(containerId, "/LastName");
       Console.WriteLine("Created Container: {0}\n", this.container.Id);
   }
   ```

1. Copiez et collez le code ci-dessous, dans lequel vous avez instancié le CosmosClient pour qu’il appelle la méthode **CreateContainer** que vous venez d’ajouter.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();

       //ADD THIS PART TO YOUR CODE
       await this.CreateContainer();
   }
   ```

Select **F5** to run your application.

Congratulations! You have successfully created an Azure Cosmos DB container.

## <a id="CreateDoc"></a>Step 6: Add items to the container

An item can be created by using the [**CreateItemAsync**](https://aka.ms/CosmosDotnetAPIDocs) function of the **Items** class. When using the SQL API, items are projected as documents, which are user-defined (arbitrary) JSON content. You can now insert an item into your Azure Cosmos DB container.

First, we need to create a **Family** class that will represent objects stored within Azure Cosmos DB in this sample. We will also create **Parent**, **Child**, **Pet**, **Address** subclasses that are used within **Family**. Note that documents must have an **Id** property serialized as **id** in JSON.

1. Select **Ctrl+Shift+A** to open the **Add New Item** dialog. Add a new class **Family.cs** to your project.

   ![Screen shot of adding a new Family.cs class into the project](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. Copy and paste the **Family**, **Parent**, **Child**, **Pet**, and **Address** class into **Family.cs**. Note your namespace will differ based on the name of your project.

   ```csharp
   using Newtonsoft.Json;

   namespace CosmosGettingStartedDotnetCoreTutorial
   {
       public class Family
       {
           [JsonProperty(PropertyName = "id")]
           public string Id { get; set; }
           public string LastName { get; set; }
           public Parent[] Parents { get; set; }
           public Child[] Children { get; set; }
           public Address Address { get; set; }
           public bool IsRegistered { get; set; }
           public override string ToString()
           {
               return JsonConvert.SerializeObject(this);
           }
       }

       public class Parent
       {
           public string FamilyName { get; set; }
           public string FirstName { get; set; }
       }

       public class Child
       {
           public string FamilyName { get; set; }
           public string FirstName { get; set; }
           public string Gender { get; set; }
           public int Grade { get; set; }
           public Pet[] Pets { get; set; }
       }

       public class Pet
       {
           public string GivenName { get; set; }
       }

       public class Address
       {
           public string State { get; set; }
           public string County { get; set; }
           public string City { get; set; }
       }
   }
   ```

1. Revenez à **Program.cs** et ajoutez la méthode **AddItemsToContainer** sous votre méthode **CreateContainer**. Le code procède à une vérification pour s’assurer qu’un élément doté du même ID n’existe pas déjà avant de le créer. Nous allons insérer ensuite deux éléments, un pour la famille Andersen, l’autre pour la famille Wakefield.

   ```csharp
   /*
       Add Family items to the container
   */
   private async Task AddItemsToContainer()
   {
       // Create a family object for the Andersen family
       Family andersenFamily = new Family
       {
           Id = "Andersen.1",
           LastName = "Andersen",
           Parents = new Parent[]
           {
               new Parent { FirstName = "Thomas" },
               new Parent { FirstName = "Mary Kay" }
           },
           Children = new Child[]
           {
               new Child
               {
                   FirstName = "Henriette Thaulow",
                   Gender = "female",
                   Grade = 5,
                   Pets = new Pet[]
                   {
                       new Pet { GivenName = "Fluffy" }
                   }
               }
           },
           Address = new Address { State = "WA", County = "King", City = "Seattle" },
           IsRegistered = true
       };

       // Read the item to see if it exists. Note ReadItemAsync will not throw an exception if an item does not exist. Instead, we check the StatusCode property off the response object.
       CosmosItemResponse<Family> andersenFamilyResponse = await this.container.Items.ReadItemAsync<Family>(andersenFamily.LastName, andersenFamily.Id);

       if (andersenFamilyResponse.StatusCode == HttpStatusCode.NotFound)
       {
           // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen"
           andersenFamilyResponse = await this.container.Items.CreateItemAsync<Family>(andersenFamily.LastName, andersenFamily);

           // Note that after creating the item, we can access the body of the item with the Resource property off the CosmosItemResponse.
           //We can also access the RequestCharge property to see the amount of RUs consumed on this request.
           Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
       }
       else
       {
           Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamilyResponse.Resource.Id);
       }

       // Create a family object for the Wakefield family
       Family wakefieldFamily = new Family
       {
           Id = "Wakefield.7",
           LastName = "Wakefield",
           Parents = new Parent[]
           {
               new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
               new Parent { FamilyName = "Miller", FirstName = "Ben" }
           },
           Children = new Child[]
           {
               new Child
               {
                   FamilyName = "Merriam",
                   FirstName = "Jesse",
                   Gender = "female",
                   Grade = 8,
                   Pets = new Pet[]
                   {
                       new Pet { GivenName = "Goofy" },
                       new Pet { GivenName = "Shadow" }
                   }
               },
               new Child
               {
                   FamilyName = "Miller",
                   FirstName = "Lisa",
                   Gender = "female",
                   Grade = 1
               }
           },
           Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
           IsRegistered = false
       };

       // Read the item to see if it exists
       CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.ReadItemAsync<Family>(wakefieldFamily.LastName, wakefieldFamily.Id);

       if (wakefieldFamilyResponse.StatusCode == HttpStatusCode.NotFound)
       {
           // Create an item in the container representing the Wakefield family. Note we provide the value of the partition key for this item, which is "Wakefield"
           wakefieldFamilyResponse = await this.container.Items.CreateItemAsync<Family>(wakefieldFamily.LastName, wakefieldFamily);

           // Note that after creating the item, we can access the body of the item with the Resource property off the CosmosItemResponse.
           //We can also access the RequestCharge property to see the amount of RUs consumed on this request.
           Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", wakefieldFamilyResponse.Resource.Id, wakefieldFamilyResponse.RequestCharge);
       }
       else
       {
           Console.WriteLine("Item in database with id: {0} already exists\n", wakefieldFamilyResponse.Resource.Id);
       }
   }
   ```

1. Ajoutez un appel à `AddItemsToContainer` dans la méthode `GetStartedDemoAsync`.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();

       //ADD THIS PART TO YOUR CODE
       await this.AddItemsToContainer();
   }
   ```

Sélectionnez **F5** pour exécuter votre application.

Félicitations ! Vous avez créé deux éléments Azure Cosmos DB.

## <a id="Query"></a>Étape 7 : Interroger les ressources Azure Cosmos DB

Azure Cosmos DB prend en charge les [requêtes](sql-api-sql-query.md) enrichies sur les documents JSON stockés dans chaque collection. L’exemple de code suivant montre comment exécuter une requête sur les éléments que nous avons insérés à l’étape précédente.

1. Copiez et collez la méthode **RunQuery** sous votre méthode **AddItemsToContainer**.

   ```csharp
   /*
       Run a query (using Azure Cosmos DB SQL syntax) against the container
   */
   private async Task RunQuery()
   {
       var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";
       var partitionKeyValue = "Andersen";

       Console.WriteLine("Running query: {0}\n", sqlQueryText);

       CosmosSqlQueryDefinition queryDefinition = new CosmosSqlQueryDefinition(sqlQueryText);
       CosmosResultSetIterator<Family> queryResultSetIterator = this.container.Items.CreateItemQuery<Family>(queryDefinition, partitionKeyValue);

       List<Family> families = new List<Family>();

       while (queryResultSetIterator.HasMoreResults)
       {
           CosmosQueryResponse<Family> currentResultSet = await queryResultSetIterator.FetchNextSetAsync();
           foreach (Family family in currentResultSet)
           {
               families.Add(family);
               Console.WriteLine("\tRead {0}\n", family);
           }
       }
   }
   ```

1. Ajoutez un appel à `RunQuery` dans la méthode `GetStartedDemoAsync`.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();

       //ADD THIS PART TO YOUR CODE
       await this.RunQuery();
   }
   ```

Sélectionnez **F5** pour exécuter votre application.

Félicitations ! Vous avez interrogé un conteneur Azure Cosmos DB.

## <a id="ReplaceItem"></a>Étape 8 : Remplacer un élément JSON

À présent, nous allons mettre à jour un élément dans Azure Cosmos DB.

1. Copiez et collez la méthode **ReplaceFamilyItem** sous votre méthode **RunQuery**. Notez que nous modifions deux propriétés, `IsRegistered` de la famille, et `Grade` de l’un des enfants.

   ```csharp
   /*
   Update an item in the container
   */
   private async Task ReplaceFamilyItem()
   {
       CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.ReadItemAsync<Family>("Wakefield", "Wakefield.7");
       var itemBody = wakefieldFamilyResponse.Resource;

       // update registration status from false to true
       itemBody.IsRegistered = true;
       // update grade of child
       itemBody.Children[0].Grade = 6;

       // replace the item with the updated content
       wakefieldFamilyResponse = await this.container.Items.ReplaceItemAsync<Family>(itemBody.LastName, itemBody.Id, itemBody);
       Console.WriteLine("Updated Family [{0},{1}]\n. Body is now: {2}\n", itemBody.LastName, itemBody.Id, wakefieldFamilyResponse.Resource);
   }
   ```

1. Ajoutez un appel à `ReplaceFamilyItem` dans la méthode `GetStartedDemo`.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();

       //ADD THIS PART TO YOUR CODE
       await this.ReplaceFamilyItem();
   }
   ```

Select **F5** to run your application.

Congratulations! You have successfully replaced an Azure Cosmos DB item.

## <a id="DeleteDocument"></a>Step 9: Delete item

Now, we will delete an item in Azure Cosmos DB.

1. Copy and paste the **DeleteFamilyItem** method below your **ReplaceFamilyItem** method.

   ```csharp
   /*
   Delete an item in the container
   */
   private async Task DeleteFamilyItem()
   {
       var partitionKeyValue = "Wakefield";
       var familyId = "Wakefield.7";

       // Delete an item. Note we must provide the partition key value and id of the item to delete
       CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.DeleteItemAsync<Family>(partitionKeyValue, familyId);
       Console.WriteLine("Deleted Family [{0},{1}]\n", partitionKeyValue, familyId);
   }
   ```

1. Ajoutez un appel à `DeleteFamilyItem` dans la méthode `GetStartedDemo`.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();
       await this.ReplaceFamilyItem();

       //ADD THIS PART TO YOUR CODE
       await this.DeleteFamilyItem();
   }
   ```

Sélectionnez **F5** pour exécuter votre application.

Félicitations ! Vous avez supprimé un élément Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Étape 10 : Supprimer la base de données

Maintenant, nous allons supprimer notre base de données. Supprimer la base de données créée revient à supprimer la base de données et toutes les ressources enfants (conteneurs, éléments, procédures stockées, fonctions définies par l’utilisateur et déclencheurs). Nous allons également supprimer l’instance **CosmosClient**.

1. Copiez et collez la méthode **DeleteDatabaseAndCleanup** sous votre méthode **DeleteFamilyItem**.

   ```csharp
   /*
   Delete the database and dispose of the Cosmos Client instance
   */
   private async Task DeleteDatabaseAndCleanup()
   {
       CosmosDatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
       // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

       Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

       //Dispose of CosmosClient
       this.cosmosClient.Dispose();
   }
   ```

1. Ajoutez un appel à `DeleteDatabaseAndCleanup` dans la méthode `GetStartedDemo`.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();
       await this.ReplaceFamilyItem();
       await this.DeleteFamilyItem();

       //ADD THIS PART TO YOUR CODE
       await this.DeleteDatabaseAndCleanup();
   }
   ```

Sélectionnez **F5** pour exécuter votre application.

Félicitations ! Vous avez supprimé une base de données Azure Cosmos DB.

## <a id="Run"></a>Étape 11 : Exécuter votre application de console C

Sélectionnez **F5** dans Visual Studio pour générer l’application en mode débogage.

La sortie de votre application entière de prise en main doit s’afficher dans une fenêtre de console. Celle-ci doit présenter les résultats des requêtes que nous avons ajoutées, qui doivent correspondre au texte d'exemple ci-dessous.

```
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

Félicitations ! Vous avez terminé le didacticiel et vous disposez d’une application de console C# qui fonctionne !

## <a id="GetSolution"></a> Obtenir la solution complète du didacticiel

Pour générer la solution GetStarted qui contient tous les exemples de cet article, vous devez avoir les éléments suivants :

- Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [compte gratuit](https://azure.microsoft.com/free/).
- Un [compte Azure Cosmos DB][cosmos-db-create-account].
- La solution [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started) disponible sur GitHub.

Pour restaurer les références au kit SDK .NET Core API SQL pour Azure Cosmos DB dans Visual Studio, cliquez avec le bouton droit sur la solution **GetStarted** dans l’Explorateur de solutions, puis sélectionnez **Restaurer des packages NuGet**. Ensuite, dans le fichier **Program.cs**, mettez à jour les valeurs `EndpointUri` et `PrimaryKey`, tel que décrit dans [Se connecter à un compte Azure Cosmos DB](#Connect).

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment créer une application .NET Core pour gérer les données de l’API SQL Azure Cosmos DB. Vous pouvez maintenant importer des données supplémentaires à votre compte Cosmos DB.

> [!div class="nextstepaction"][importer des données dans azure cosmos db](import-data.md)

[cosmos-db-create-account]: create-sql-api-dotnet-preview.md#create-account
