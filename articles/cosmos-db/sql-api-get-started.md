---
title: Créer une application console .NET pour gérer des données dans le compte de l’API SQL Azure Cosmos DB
description: Tutoriel qui crée une base de données en ligne et une application console C# à l’aide de l’API SQL.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: sngun
ms.openlocfilehash: 60c7e6b32f60d6f42d706489c41dbeea4af0d15d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342118"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Créer une application console .NET pour gérer des données dans le compte de l’API SQL Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (préversion)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (préversion)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Java asynchrone](sql-api-async-java-get-started.md)
> * [Node.JS](sql-api-nodejs-get-started.md)
> 

Bienvenue dans le tutoriel de prise en main de l’API SQL Azure Cosmos DB. À la fin de ce tutoriel, vous aurez une application console qui crée et interroge des ressources Azure Cosmos DB.

Ce didacticiel vous explique les procédures suivantes :

> [!div class="checklist"]
>
> - Créer un compte Azure Cosmos DB et s’y connecter
> - Configurer une solution Visual Studio
> - Créer une base de données
> - Création d'une collection
> - Créer des documents JSON
> - Interroger la collection
> - Mettre à jour un document JSON
> - Supprimer un document
> - Supprimer la base de données

## <a name="prerequisites"></a>Prérequis

Visual Studio 2017 avec le workflow de développement Azure installé :
- Vous pouvez télécharger et utiliser **gratuitement** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Veillez à activer **le développement Azure** lors de l’installation de Visual Studio. 

Un abonnement Azure ou un compte d’essai de Cosmos DB gratuit :
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  
  
- Si vous utilisez l’émulateur Azure Cosmos DB, suivez les étapes de la section [Émulateur Azure Cosmos DB](local-emulator.md) pour le configurer. Ensuite, démarrez le tutoriel à l’étape [Configurer la solution Visual Studio](#SetupVS).
  
## <a name="get-the-completed-solution"></a>Obtenir la solution complète

Si vous n’avez pas le temps de suivre le tutoriel ou si vous avez juste besoin des exemples de code, vous pouvez télécharger la solution complète à partir de [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). 

Pour exécuter la solution complète téléchargée : 

1. Vérifiez que les [prérequis](#prerequisites) sont installés. 
1. Ouvrez le fichier de solution *GetStarted.sln* dans Visual Studio.
1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **GetStarted**, puis sélectionnez **Gérer les packages NuGet**.
1. Sous l’onglet **NuGet**, sélectionnez **Restaurer** pour restaurer les références au SDK .NET Azure Cosmos DB.
1. Dans le fichier *App.config*, mettez à jour les valeurs `EndpointUrl` et `PrimaryKey`, comme décrit dans la section [Se connecter au compte Azure Cosmos DB](#Connect).
1. Sélectionnez **Déboguer** > **Démarrer sans débogage** ou appuyez sur **Ctrl**+**F5** pour générer et exécuter l’application.

## <a name="create-an-azure-cosmos-db-account"></a>Création d’un compte Azure Cosmos DB

Suivez ces instructions pour créer un compte Azure Cosmos DB dans le portail Azure. Si vous avez déjà un compte Azure Cosmos DB à utiliser, passez directement à l’étape [Configurer la solution Visual Studio](#SetupVS). 

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Configurer la solution Visual Studio

1. Dans Visual Studio 2017, sélectionnez **Fichier** > **Nouveau** > **Projet**.
   
1. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C#**  > **Application console (.NET Framework)** , nommez votre projet *AzureCosmosDBApp*, puis sélectionnez **OK**.
   
   ![Capture d’écran de la fenêtre Nouveau projet](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
   
1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **AzureCosmosDBApp**, puis sélectionnez **Gérer les packages NuGet**.
   
   ![Menu contextuel du projet](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
   
1. Sous l’onglet **NuGet**, sélectionnez **Parcourir**, puis entrez *azure documentdb* dans la zone de recherche.
   
1. Recherchez et sélectionnez **Microsoft.Azure.DocumentDB**, puis sélectionnez **Installer** si besoin.
   
   L’ID de package de la bibliothèque cliente d’API SQL Azure Cosmos DB est [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/).

   > [!NOTE]
   > Si vous utilisez .NET Core, consultez [la documentation sur .NET Core](./sql-api-dotnetcore-get-started.md).

   ![Capture d’écran du menu NuGet pour la recherche du SDK client Azure Cosmos DB](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)
   
   Si vous obtenez un message concernant la vérification des modifications apportées à la solution, sélectionnez **OK**. Si vous obtenez un message concernant l’acceptation de la licence, sélectionnez **J’accepte**.

## <a id="Connect"></a>Se connecter au compte Azure Cosmos DB

À présent, commencez à écrire du code. Le fichier *Project.cs* complet pour ce tutoriel se trouve dans [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

1. Dans l’**Explorateur de solutions**, sélectionnez *Program.cs*, puis dans l’éditeur de code, ajoutez les références suivantes au début du fichier :
   
   ```csharp
   using System.Net;
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Newtonsoft.Json;
   ```
   
1. Ensuite, ajoutez les deux constantes suivantes et la variable `client` à `public class Program`.
   
   ```csharp
   
   public class Program
   {
      private const string EndpointUrl = "<your endpoint URL>";
      private const string PrimaryKey = "<your primary key>";
      private DocumentClient client;
   ```
   
1. L’URL du point de terminaison et la clé primaire permettent à votre application de se connecter à votre compte Azure Cosmos DB et au compte Azure Cosmos DB d’approuver la connexion. Copiez les clés à partir du [portail Azure](https://portal.azure.com), puis collez-les dans votre code. 

   
   1. Dans votre compte Azure Cosmos DB, sélectionnez **Clés** sur la gauche.
      
      ![Voir et copier des clés d’accès dans le portail Azure](./media/sql-api-get-started/nosql-tutorial-keys.png)
      
   1. Sous **Clés en lecture-écriture**, copiez la valeur d’**URI** à l’aide du bouton Copier situé à droite, puis collez-la dans `<your endpoint URL>`, dans *Program.cs*. Par exemple : 
      
      `private const string EndpointUrl = "https://mysqlapicosmosdb.documents.azure.com:443/";`
      
   1. Copiez la valeur de **CLÉ PRIMAIRE**, puis collez-la dans `<your primary key>`, dans *Program.cs*. Par exemple : 
      
      `private const string PrimaryKey = "19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==";`
   
1. Après la méthode `Main`, ajoutez une nouvelle tâche asynchrone appelée `GetStartedDemo`, qui instancie un nouveau `DocumentClient` appelé `client`.
   
   ```csharp
      private async Task GetStartedDemo()
      {
        client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
      }
   ```

   Si vous utilisez un objet proxy pour vous connecter à Azure Cosmos DB, vous devez plutôt utiliser le bloc de code suivant pour créer l’objet DocumentClient. L’exemple dans ce document n’utilise pas d’objet proxy. Par conséquent, l’exemple ci-dessous est uniquement proposé à titre de référence :

   ```csharp
   HttpClientHandler handler = new HttpClientHandler()
   {
     Proxy = proxyObject
     UseProxy = true,
   };

   //Pass handler to the constructor of DocumentClient.
   DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey, handler);
   
   ```
   
1. Ajoutez le code suivant à la méthode `Main` pour exécuter la tâche `GetStartedDemo`. La méthode `Main` intercepte les exceptions et les écrit dans la console.
   
   ```csharp
      static void Main(string[] args)
      {
        try
        {
           Program p = new Program();
           p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine($"{de.StatusCode} error occurred: {de.Message}, Message: {baseException.Message}");
        }
        catch (Exception e)
        {
           Exception baseException = e.GetBaseException();
           Console.WriteLine($"Error: {e.Message}, Message: {baseException.Message}");
        }
        finally
        {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
        }
      }
   ```
   
1. Appuyez sur **F5** pour exécuter votre application. 
   
1. Quand vous voyez le message **Fin de la démonstration, appuyez sur une touche pour quitter** dans la fenêtre de console, cela signifie que la connexion a réussi. Appuyez sur une touche pour fermer la fenêtre de console. 

Vous avez correctement établi une connexion à votre compte Azure Cosmos DB. Maintenant, utilisez des ressources Azure Cosmos DB.  

## <a name="create-a-database"></a>Créer une base de données

Une [base de données](databases-containers-items.md#azure-cosmos-databases) Azure Cosmos DB est le conteneur logique de stockage de documents JSON partitionné entre les collections. Vous créez une base de données à l’aide de la méthode [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) de la classe `DocumentClient`. 

1. Avant d'ajouter le code permettant de créer une base de données, ajoutez une méthode d'assistance pour l'écriture sur la console. Copiez et collez la méthode `WriteToConsoleAndPromptToContinue` suivante après la méthode `GetStartedDemo` dans votre code.
   
   ```csharp
   private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
   {
      Console.WriteLine(format, args);
      Console.WriteLine("Press any key to continue...");
      Console.ReadKey();
   }
   ```
   
1. Copiez et collez la ligne suivante dans votre méthode `GetStartedDemo`, après la ligne `client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);`. Ce code crée une base de données nommée `FamilyDB`.
   
   ```csharp
      await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
   ```
   
1. Appuyez sur **F5** pour exécuter votre application.

Vous avez correctement créé une base de données Azure Cosmos DB. Vous pouvez voir la base de données dans le [portail Azure](https://portal.azure.com) en sélectionnant **Explorateur de données** dans le panneau de navigation gauche de votre compte Azure Cosmos DB. 

## <a id="CreateColl"></a>Créer une collection

Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript. Vous créez une collection à l’aide de la méthode [CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync#overloads) de la classe `DocumentClient`. 

> [!IMPORTANT]
> **CreateDocumentCollectionIfNotExistsAsync** crée une collection avec un débit réservé, ce qui génère des frais. Pour plus d’informations, consultez la [page sur les tarifs](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 

1. Copiez et collez le code suivant dans votre méthode `GetStartedDemo`, après la ligne `await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });`. Ce code crée une collection de documents nommée `FamilyCollection`.
   
   ```csharp
      await client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });
   ```
   
1. Appuyez sur **F5** pour exécuter votre application.

Vous avez correctement créé une collection de documents Azure Cosmos DB. Vous pouvez voir la collection sous votre base de données **FamilyDB** dans l’**Explorateur de données** du portail Azure.  

## <a id="CreateDoc"></a>Créer des documents JSON

Les documents correspondent à du contenu JSON arbitraire défini par l’utilisateur. Ils doivent avoir une propriété ID sérialisée en tant que `id` dans JSON. Vous créez des documents à l’aide de la méthode [CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync#overloads) de la classe `DocumentClient`. 

> [!TIP]
> Si vous avez déjà des données à stocker dans votre base de données, vous pouvez utiliser l’[outil de migration de données](import-data.md) Azure Cosmos DB pour les importer.
>

Le code suivant crée et insère deux documents dans votre collection de bases de données. Tout d’abord, vous créez une classe `Family` et des sous-classes `Parent`, `Child`, `Pet` et `Address` à utiliser dans `Family`. Ensuite, vous créez une méthode `CreateFamilyDocumentIfNotExists`, puis vous créez et insérez deux documents. 

1. Copiez et collez les classes `Family`, `Parent`, `Child`, `Pet` et `Address` suivantes après la méthode `WriteToConsoleAndPromptToContinue` dans votre code.
   
   ```csharp
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
   ```
   
1. Copiez et collez la méthode `CreateFamilyDocumentIfNotExists` suivante après la classe `Address` que vous venez d’ajouter.
   
   ```csharp
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            WriteToConsoleAndPromptToContinue($"Found {family.Id}");
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                WriteToConsoleAndPromptToContinue($"Created Family {family.Id}");
            }
            else
            {
                throw;
            }
        }
    }
   ```
   
1. Copiez et collez le code suivant à la fin de votre méthode `GetStartedDemo`, après la ligne `await client.CreateDocumentCollectionIfNotExistsAsync`. Ce code crée et insère deux documents, un pour la famille Andersen, l’autre pour la famille Wakefield.
   
   ```csharp
    Family andersenFamily = new Family
    {
        Id = "AndersenFamily",
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

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

    Family wakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
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

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);
   ```
   
1. Appuyez sur **F5** pour exécuter votre application.

Vous avez correctement créé deux documents Azure Cosmos DB. Vous pouvez voir les documents sous votre base de données **FamilyDB** et la collection **FamilyCollection** dans l’**Explorateur de données** du portail Azure.   

![Diagramme illustrant la relation hiérarchique entre le compte, la base de données en ligne, la collection et les documents](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Interroger les ressources Azure Cosmos DB

Azure Cosmos DB prend en charge les [requêtes](how-to-sql-query.md) avancées sur les documents JSON stockés dans des collections. L’exemple de code suivant utilise la syntaxe LINQ et SQL Azure Cosmos DB pour exécuter une requête sur les exemples de documents.

1. Copiez et collez la méthode `ExecuteSimpleQuery` suivante après la méthode `CreateFamilyDocumentIfNotExists` dans votre code.
   
   ```csharp
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options.
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Find the Andersen family by its LastName.
        IQueryable<Family> familyQuery = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
            .Where(f => f.LastName == "Andersen");

        // Execute the query synchronously. 
        // You could also execute it asynchronously using the IDocumentQuery<T> interface.
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine($"\tRead {family}");
        }

        // Now execute the same query using direct SQL.
        IQueryable<Family> familyQueryInSql = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
            "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
            queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
            Console.WriteLine($"\tRead {family}");
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }
   ```
   
1. Copiez et collez le code suivant à la fin de votre méthode `GetStartedDemo`, après la ligne `await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);`.
   
   ```csharp
      ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Appuyez sur **F5** pour exécuter votre application.

La requête précédente retourne l’élément complet pour la famille Andersen. Vous avez correctement interrogé une collection Azure Cosmos DB.

Le diagramme suivant montre comment la syntaxe de requête SQL Azure Cosmos DB effectue un appel sur la collection. La même logique s’applique à la requête LINQ.

![Diagramme illustrant l’étendue et la signification de la requête utilisée par le didacticiel NoSQL pour créer une application de console C#](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

Le mot clé [FROM](sql-query-from.md) est facultatif dans la requête SQL, car les requêtes Azure Cosmos DB sont déjà limitées à une collection unique. Vous pouvez permuter `FROM Families f` avec `FROM root r` ou tout autre nom de variable de votre choix. Azure Cosmos DB déduit alors que `Families`, `root` ou le nom de variable choisi font référence à la collection actuelle.

## <a id="ReplaceDocument"></a>Mettre à jour un document JSON

L’API SQL Azure Cosmos DB prend en charge la mise à jour et le remplacement des documents JSON.  

1. Copiez et collez la méthode `ReplaceFamilyDocument` suivante après la méthode `ExecuteSimpleQuery` dans votre code.
   
   ```csharp
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
       await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
       WriteToConsoleAndPromptToContinue($"Replaced Family {familyName}");
    }
   ```
   
1. Copiez et collez le code suivant à la fin de votre méthode `GetStartedDemo`, après la ligne `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");`. Le code met à jour les données dans l’un des documents, puis réexécute la requête pour montrer le document modifié.
   
   ```csharp
   // Update the Grade of the Andersen Family child
   andersenFamily.Children[0].Grade = 6;
   await ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily", andersenFamily);
   ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Appuyez sur **F5** pour exécuter votre application.

La sortie de la requête indique que la valeur `Grade` de l’enfant de la famille Andersen est passé de `5` à `6`. Vous avez correctement mis à jour et remplacé un document Azure Cosmos DB. 

## <a id="DeleteDocument"></a>Supprimer un document JSON

L’API SQL Azure Cosmos DB prend en charge la suppression de documents JSON.  

1. Copiez et collez la méthode `DeleteFamilyDocument` suivante après la méthode `ReplaceFamilyDocument`.
   
   ```csharp
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
        Console.WriteLine($"Deleted Family {documentName}");
    }
   ```
   
1. Copiez et collez le code suivant à la fin de votre méthode `GetStartedDemo`, après la deuxième ligne `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");`.
   
   ```csharp
   await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");
   ```
   
1. Appuyez sur **F5** pour exécuter votre application.

Vous avez correctement supprimé un document Azure Cosmos DB. 

## <a id="DeleteDatabase"></a>Supprimer la base de données

Supprimez la base de données que vous avez créée pour l’enlever ainsi que toutes ses ressources enfants, y compris la collection et les documents. 

1. Copiez et collez le code suivant à la fin de votre méthode `GetStartedDemo`, après la ligne `await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");`. 
   
   ```csharp
   // Clean up - delete the database
   await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));
   ```
   
1. Appuyez sur **F5** pour exécuter votre application.

Vous avez correctement supprimé la base de données Azure Cosmos DB. Vous pouvez voir dans l’**Explorateur de données** de votre compte Azure Cosmos DB que la base de données FamilyDB est supprimée. 

## <a id="Run"></a>Exécuter l’application console C# entière

Appuyez sur **F5** dans Visual Studio pour générer et exécuter l’application console C# complète en mode débogage. Vous devez voir la sortie suivante dans la fenêtre de console :

```bash
Created Family AndersenFamily
Press any key to continue ...
 Created Family WakefieldFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Replaced Family AndersenFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Deleted Family AndersenFamily
End of demo, press any key to exit.
```

Félicitations ! Vous avez terminé le tutoriel et vous disposez maintenant d’une application console C# opérationnelle capable de créer, interroger, mettre à jour et supprimer des ressources Azure Cosmos DB.  

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur Azure Cosmos DB, consultez la page [Bienvenue dans Azure Cosmos DB](introduction.md).
* Pour suivre un tutoriel ASP.NET MVC plus complexe, consultez [Tutoriel ASP.NET MVC : développement d’applications web avec Azure Cosmos DB](sql-api-dotnet-application.md).
* Pour tester les performances et la mise à l’échelle avec Azure Cosmos DB, consultez [Test des performances et de la mise à l’échelle avec Azure Cosmos DB](performance-testing.md).
* Pour découvrir comment superviser les requêtes, l’utilisation et le stockage Azure Cosmos DB, consultez [Superviser des comptes](monitor-accounts.md).
* Exécutez des requêtes sur un exemple de dataset dans le [Query Playground](https://www.documentdb.com/sql/demo).

