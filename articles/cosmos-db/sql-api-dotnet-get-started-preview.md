---
title: Créer une application console .NET pour gérer des données dans le compte de l’API SQL Azure Cosmos DB (kit SDK Version 3 - préversion)
description: Un didacticiel qui crée une application de base de données en ligne et de console C# à l’aide de l’API SQL.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/01/2018
ms.author: dech
ms.openlocfilehash: a942f91dfa03eea2d9dc14b4b44e2ef5ee57c1ba
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58078633"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account-sdk-version-3-preview"></a>Créer une application console .NET pour gérer des données dans le compte de l’API SQL Azure Cosmos DB (kit SDK Version 3 - préversion)

> [!div class="op_single_selector"]
> * [.NET (préversion)](sql-api-dotnet-get-started-preview.md)
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (préversion)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Java asynchrone](sql-api-async-java-get-started.md)
> * [Node.JS](sql-api-nodejs-get-started.md)
> 

Bienvenue dans le didacticiel sur la prise en main de l’API SQL Azure Cosmos DB ! À la fin de ce didacticiel, vous disposerez d’une application de console qui crée et interroge des ressources Azure Cosmos DB. Ce tutoriel utilise la [version 3.0+](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) du kit SDK .NET Azure Cosmos DB, qui cible [.NET Standard 2.0.](https://docs.microsoft.com/dotnet/standard/net-standard)

Ce didacticiel contient les sections suivantes :

> [!div class="checklist"]
> * Création et connexion à un compte Azure Cosmos
> * Configuration de votre projet dans Visual Studio
> * Création d’une base de données et d’un conteneur
> * Ajout d’éléments au conteneur
> * Interrogation du conteneur
> * Opérations CRUD sur l’élément
> * Suppression de la base de données

Vous n’avez pas le temps ? Ne vous inquiétez pas ! La solution complète est disponible sur [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Pour obtenir des instructions rapides, allez à la section [Obtenir la solution complète du tutoriel](#GetSolution).

Commençons dès maintenant !

## <a name="prerequisites"></a>Prérequis

* Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [compte gratuit](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Étape 1 : Création d’un compte Azure Cosmos DB
Commençons par créer un compte Azure Cosmos DB. Si vous avez déjà un compte que vous souhaitez utiliser, vous pouvez passer directement à l’étape [Configurer votre solution Visual Studio](#SetupVS). Si vous utilisez l’émulateur Azure Cosmos DB, suivez les étapes de la section [Émulateur Azure Cosmos DB](local-emulator.md) pour le configurer, puis passez directement à l’étape [Configurer votre projet Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

## <a id="SetupVS"></a>Étape 2 : Configurer votre projet Visual Studio
1. Ouvrez **Visual Studio 2017** sur votre ordinateur.
1. Dans le menu **Fichier**, sélectionnez **Nouveau**, puis choisissez **Projet**.
1. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C#** / **Application console (.NET Framework)**, donnez un nom à votre projet, puis cliquez sur **OK**.
    ![Capture d’écran de la fenêtre Nouveau projet](./media/sql-api-get-started/dotnet-tutorial-visual-studio-new-project.png)
1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur votre nouvelle application console, qui se trouve sous votre solution Visual Studio, puis cliquez sur **Gérer les packages NuGet…**
    
    ![Capture d'écran du menu du clic droit pour le projet](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. Sous l’onglet **NuGet**, cliquez sur **Parcourir** et tapez **Microsoft.Azure.Cosmos** dans la zone de recherche. Assurez-vous de cocher *Inclure la préversion* pour obtenir la préversion.
1. Dans les résultats, trouvez **Microsoft.Azure.Cosmos** et cliquez sur **Installer**.
   L’ID de package de la bibliothèque cliente d’API SQL Azure Cosmos DB est [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Capture d’écran du menu NuGet pour la recherche du SDK client Azure Cosmos DB](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    Si vous obtenez un message concernant la vérification des modifications apportées à la solution, cliquez sur **OK**. Si vous obtenez un message concernant l’acceptation de la licence, cliquez sur **J’accepte**.

Parfait ! L’installation étant terminée, nous pouvons passer à l’écriture du code. Vous trouverez le projet de code complet de ce didacticiel dans [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a id="Connect"></a>Étape 3 : Se connecter à un compte Azure Cosmos DB
1. Remplacez tout d’abord les références situées au début de votre application C#, dans le fichier **Program.cs**, par ces références :

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using Microsoft.Azure.Cosmos;
   using System.Collections.Generic;
   using System.Net;
   ```

1. À présent, ajoutez ces variables et ces constantes dans votre classe publique ``Program``.
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
    Notez que si vous avez déjà utilisé la version précédente du kit SDK .NET, vous connaissez peut-être les termes « collection » et « document ». Étant donné qu’Azure Cosmos DB prend en charge plusieurs modèles d’API, les versions postérieures à la version 3.0 du kit SDK .NET utilisent les termes génériques « conteneur » et « élément ». Un conteneur peut être une collection, un graphique ou une table. Un élément peut être un document, arête/sommet ou ligne, et correspond au contenu à l’intérieur d’un conteneur. [En savoir plus sur les bases de données, les conteneurs et les éléments.](databases-containers-items.md)

1. Récupérez l’URL du point de terminaison et la clé primaire à partir du [portail Azure](https://portal.azure.com).

    Dans le portail Azure, accédez à votre compte Azure Cosmos DB, puis cliquez sur **Clés**.

    Copiez l’URI à partir du portail et collez-le dans `<your endpoint URL>` dans le fichier ```Program.cs```. Copiez la CLÉ PRIMAIRE à partir du portail, puis collez-la dans `<your primary key>`.

   ![Capture d’écran de l’obtention des clés Azure Cosmos DB à partir du portail Azure](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. Nous allons créer ensuite une nouvelle instance de ```CosmosClient``` et configurer une génération de modèles automatique pour notre programme.

    Sous la méthode **Main**, ajoutez une nouvelle tâche asynchrone appelée **GetStartedDemoAsync** qui va instancier notre nouveau ```CosmosClient```. Nous allons utiliser **GetStartedDemoAsync** comme point d’entrée appelant les méthodes qui opèrent sur les ressources Azure Cosmos DB.

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

## <a name="step-4-create-a-database"></a>Étape 4 : Créer une base de données
Vous pouvez créer une base de données à l’aide de la fonction [**CreateDatabaseIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabases) ou [**CreateDatabaseAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabases) de la classe ``CosmosDatabases``. Une base de données est le conteneur logique d’éléments partitionnés entre les conteneurs.
    
1. Copiez et collez la méthode **CreateDatabase** sous votre méthode **GetStartedDemoAsync**. **CreateDatabase** crée une base de données avec l’ID ``FamilyDatabase`` s’il n’existe pas déjà, sinon avec l’ID spécifié dans le champ ``databaseId``. 

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

    À ce stade, votre code doit désormais ressembler à ceci, avec vos point de terminaison et clé primaire renseignés.
    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using Microsoft.Azure.Cosmos;
    using System.Collections.Generic;
    using System.Net;

    namespace CosmosGettingStarted
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

## <a id="CreateColl"></a>Étape 5 : Créez un conteneur.
> [!WARNING]
> L’appel de la méthode **CreateContainerIfNotExistsAsync** crée un conteneur, ce qui a une incidence sur la tarification. Pour plus d'informations, visitez notre [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

Vous pouvez créer un conteneur en utilisant la fonction [**CreateContainerIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmoscontainers) ou [**CreateContainerAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmoscontainers) dans la classe **CosmosContainers**. Un conteneur est composé d’éléments (des documents JSON dans le cas de l’API SQL) et d’une logique d’application côté serveur JavaScript associée, par exemple des procédures stockées, des fonctions définies par l’utilisateur et des déclencheurs.

1. Copiez et collez la méthode **CreateContainer** sous votre méthode **CreateDatabase**. **CreateContainer** crée un conteneur avec l’ID ``FamilyContainer`` s’il n’existe pas déjà, sinon avec l’ID spécifié dans le champ ``containerId``. 

    ```csharp
    /*
        Create the container if it does not exist. 
        Specify "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
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
   Sélectionnez **F5** pour exécuter votre application.

Félicitations ! Vous avez créé un conteneur Azure Cosmos DB.  

## <a id="CreateDoc"></a>Étape 6 : Ajouter des éléments au conteneur
Un élément peut être créé à l’aide de la fonction [**CreateItemAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmositems) de la classe **CosmosItems**. Lorsque vous utilisez l’API SQL, les éléments sont projetés sous forme de documents, à savoir du contenu JSON (arbitraire) défini par l’utilisateur. Vous pouvez maintenant insérer un élément dans votre conteneur Azure Cosmos DB.

Nous devons tout d’abord créer une classe **Family** représentant les objets stockés dans Azure Cosmos DB dans cet exemple. Nous allons également créer les sous-classes **Parent**, **Child**, **Pet** et **Address** qui seront utilisées dans **Family**. Notez que les documents doivent avoir une propriété **Id** sérialisée comme **id** dans JSON. 
1. Appuyez sur **Ctrl + Maj + A** pour afficher la boîte de dialogue **Ajouter un nouvel élément**. Ajoutez une nouvelle classe **Family.cs** à votre projet. 

    ![Capture d’écran de l’ajout d’une nouvelle classe Family.cs dans le projet](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. Copiez et collez les classes **Family**, **Parent**, **Child**, **Pet** et **Address** dans **Family.cs**. 
    ```csharp
    using Newtonsoft.Json;

    namespace CosmosGettingStarted
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
1. Revenez à **Program.cs** et ajoutez la méthode **AddItemsToContainer** sous votre méthode **CreateContainer**. Le code procède à une vérification pour s’assurer qu’un élément doté du même ID n’existe pas déjà avant qu’il le crée. Nous allons insérer ensuite deux éléments, un pour la famille Andersen, l’autre pour la famille Wakefield.

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
1. Ajoutez un appel à ``AddItemsToContainer`` dans la méthode ``GetStartedDemoAsync``.

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


## <a id="Query"></a>Étape 7 : Interroger les ressources Azure Cosmos DB
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
1. Ajoutez un appel à ``RunQuery`` dans la méthode ``GetStartedDemoAsync``.

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

## <a id="ReplaceItem"></a>Étape 8 : Remplacer un élément JSON
À présent, nous allons mettre à jour un élément dans Azure Cosmos DB.

1. Copiez et collez la méthode **ReplaceFamilyItem** sous votre méthode **RunQuery**. Notez que nous modifions deux propriétés, ``IsRegistered`` de la famille, et ``Grade`` de l’un des enfants.
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
1. Ajoutez un appel à ``ReplaceFamilyItem`` dans la méthode ``GetStartedDemo``.

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
   Sélectionnez **F5** pour exécuter votre application.

Félicitations ! Vous avez remplacé un élément Azure Cosmos DB.

## <a id="DeleteDocument"></a>Étape 9 : Delete item (Supprimer un élément)
À présent, nous allons supprimer un élément dans Azure Cosmos DB.

1. Copiez et collez la méthode **DeleteFamilyItem** sous votre méthode **ReplaceFamilyItem**.
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
1. Ajoutez un appel à ``DeleteFamilyItem`` dans la méthode ``GetStartedDemo``.

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
1. Ajoutez un appel à ``DeleteDatabaseAndCleanup`` dans la méthode ``GetStartedDemo``.
    
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

## <a id="Run"></a>Étape 11 : Exécuter votre application de console C#
Sélectionnez F5 dans Visual Studio pour générer et exécuter l’application en mode débogage.

La sortie de votre application entière doit s’afficher dans une fenêtre de console. Celle-ci doit présenter les résultats des requêtes que nous avons ajoutées, qui doivent correspondre au texte d'exemple ci-dessous.

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
Si vous n’avez pas le temps de suivre les étapes de ce didacticiel, ou que vous voulez simplement télécharger les exemples de code, vous pouvez les obtenir à partir de [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). 

Pour générer la solution GetStarted, vous aurez besoin des éléments suivants :

* Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [compte gratuit](https://azure.microsoft.com/free/).
* Un [compte Azure Cosmos DB][cosmos-db-create-account].
* La solution [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) disponible sur GitHub.

Pour restaurer les références au kit SDK .NET Azure Cosmos DB dans Visual Studio, cliquez avec le bouton droit sur la solution **GetStarted** dans l’Explorateur de solutions, puis cliquez sur **Restaurer des packages NuGet**. Ensuite, dans le fichier App.config, mettez à jour les valeurs EndPointUri et PrimaryKey, comme décrit dans [Se connecter à un compte Azure Cosmos DB](#Connect).

Voilà, générez l’élément et c’est parti !


## <a name="next-steps"></a>Étapes suivantes
* Vous voulez un didacticiel ASP.NET MVC plus complexe ? Consultez [Didacticiel ASP.NET MVC : développement d’applications web avec Azure Cosmos DB](sql-api-dotnet-application-preview.md).
* Vous voulez effectuer un test des performances et de la mise à l’échelle avec Azure Cosmos DB ? Consultez la page [Test des performances et de la mise à l’échelle avec Azure Cosmos DB](performance-testing.md).
* Découvrez comment [surveiller les requêtes, l’utilisation et le stockage Azure Cosmos DB](monitor-accounts.md).
* Exécutez des requêtes sur notre exemple de dataset dans le [Query Playground](https://www.documentdb.com/sql/demo).
* Pour en savoir plus sur Azure Cosmos DB, consultez la page [Bienvenue dans Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-dotnet-preview.md#create-account
