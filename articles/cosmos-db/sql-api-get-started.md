---
title: Créer une application console .NET pour gérer des données dans le compte de l’API SQL Azure Cosmos DB
description: Découvrez comment créer des ressources d’API SQL Azure Cosmos DB à l’aide d’une application console C#.
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: kirankk
ms.openlocfilehash: 36d172daed487372401691c7046215fb6c4a63ee
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384942"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Créer une application console .NET pour gérer des données dans le compte de l’API SQL Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Java asynchrone](sql-api-async-java-get-started.md)
> * [Node.JS](sql-api-nodejs-get-started.md)
>

Bienvenue dans le tutoriel de prise en main de l’API SQL Azure Cosmos DB. À la fin de ce didacticiel, vous disposerez d’une application de console qui crée et interroge des ressources Azure Cosmos DB. Ce tutoriel utilise la [Version 3.0+](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) du SDK .NET Azure Cosmos DB, qui peut être ciblé sur le [.NET Framework](https://dotnet.microsoft.com/download) ou [.NET Core](https://dotnet.microsoft.com/download).

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
1. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C#**  / **Application console (.NET Framework)** , donnez un nom à votre projet, puis cliquez sur **OK**.
    ![Capture d’écran de la fenêtre Nouveau projet](./media/sql-api-get-started/dotnet-tutorial-visual-studio-new-project.png)

    > [!NOTE]
    > Pour la cible .NET Core, dans la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C#**  / **Application console (.NET Core)** , donnez un nom à votre projet, puis cliquez sur **OK**.

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur votre nouvelle application console, qui se trouve sous votre solution Visual Studio, puis cliquez sur **Gérer les packages NuGet…**

    ![Capture d'écran du menu du clic droit pour le projet](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. Sous l’onglet **NuGet**, cliquez sur **Parcourir** et tapez **Microsoft.Azure.Cosmos** dans la zone de recherche.
1. Dans les résultats, recherchez **Microsoft.Azure.Cosmos** et cliquez sur **Installer**.
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
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
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
        private Database database;

        // The container we will create.
        private Container container;

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

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Sélectionnez **F5** pour exécuter votre application. La sortie de la fenêtre de console affiche le message `End of demo, press any key to exit.` qui confirme que la connexion à Azure Cosmos DB a été établie. Vous pouvez alors fermer la fenêtre de console.

Félicitations ! Vous avez établi une connexion à un compte de base de données Azure Cosmos DB. 

## <a name="step-4-create-a-database"></a>Étape 4 : Créer une base de données
Vous pouvez créer une base de données à l’aide de la fonction [**CreateDatabaseIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) ou [**CreateDatabaseAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) de la classe ``CosmosClient``. Une base de données est le conteneur logique d’éléments partitionnés entre les conteneurs.

1. Copiez et collez la méthode **CreateDatabaseAsync** sous votre méthode **GetStartedDemoAsync**. **CreateDatabaseAsync** crée une base de données avec l’ID ``FamilyDatabase`` s’il n’existe pas, sinon avec l’ID spécifié dans le champ ``databaseId``. 

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

1. Copiez et collez le code ci-dessous, dans lequel vous avez instancié le CosmosClient pour qu’il appelle la méthode **CreateDatabaseAsync** que vous venez d’ajouter.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    À ce stade, votre code doit désormais ressembler à ceci, avec vos point de terminaison et clé primaire renseignés.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

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
            private Database database;

            // The container we will create.
            private Container container;

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

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
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

Vous pouvez créer un conteneur en utilisant la fonction [**CreateContainerIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) ou [**CreateContainerAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) dans la classe **CosmosDatabase**. Un conteneur est composé d’éléments (documents JSON dans le cas de l’API SQL) et d’une logique d’application côté serveur en JavaScript, par exemple des procédures stockées, des fonctions définies par l’utilisateur et des déclencheurs.

1. Copiez et collez la méthode **CreateContainerAsync** sous votre méthode **CreateDatabaseAsync**. **CreateContainerAsync** crée un conteneur avec l’ID ``FamilyContainer`` s’il n’existe pas, avec l’ID spécifié dans le champ ``containerId`` partitionné par la propriété ``LastName``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Copiez et collez le code ci-dessous, dans lequel vous avez instancié le CosmosClient pour qu’il appelle la méthode **CreateContainer** que vous venez d’ajouter.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

   Sélectionnez **F5** pour exécuter votre application.

Félicitations ! Vous avez créé un conteneur Azure Cosmos DB.  

## <a id="CreateDoc"></a>Étape 6 : Ajouter des éléments au conteneur
Un élément peut être créé à l’aide de la fonction [**CreateItemAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmoscontainer) de la classe **CosmosContainer**. Lorsque vous utilisez l’API SQL, les éléments sont projetés sous forme de documents, à savoir du contenu JSON (arbitraire) défini par l’utilisateur. Vous pouvez maintenant insérer un élément dans votre conteneur Azure Cosmos DB.

Tout d’abord, nous allons créer une classe **Family** représentant les objets stockés dans Azure Cosmos DB dans cet exemple. Nous allons également créer les sous-classes **Parent**, **Child**, **Pet** et **Address** qui seront utilisées dans **Family**. Notez que l’élément doit avoir une propriété **Id** sérialisée comme **id** dans JSON.

1. Appuyez sur **Ctrl + Maj + A** pour afficher la boîte de dialogue **Ajouter un nouvel élément**. Ajoutez une nouvelle classe **Family.cs** à votre projet.

    ![Capture d’écran de l’ajout d’une nouvelle classe Family.cs dans le projet](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. Copiez et collez les classes **Family**, **Parent**, **Child**, **Pet** et **Address** dans **Family.cs**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. Revenez à **Program.cs** et ajoutez la méthode **AddItemsToContainerAsync** sous votre méthode **CreateContainerAsync**.
Le code procède à une vérification pour s’assurer qu’il n’existe pas d’élément doté du même ID avant de le créer. Nous allons insérer ensuite deux éléments, un pour la famille Andersen, l’autre pour la famille Wakefield.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]

1. Ajoutez un appel à ``AddItemsToContainerAsync`` dans la méthode ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

Sélectionnez **F5** pour exécuter votre application.

Félicitations ! Vous avez créé deux éléments Azure Cosmos DB.  

## <a id="Query"></a>Étape 7 : Interroger les ressources Azure Cosmos DB
Azure Cosmos DB prend en charge les [requêtes](sql-api-sql-query.md) enrichies sur les documents JSON stockés dans chaque collection. L’exemple de code suivant montre comment exécuter une requête sur les éléments que nous avons insérés à l’étape précédente.

1. Copiez et collez la méthode **QueryItemsAsync** sous votre méthode **AddItemsToContainerAsync**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Ajoutez un appel à ``QueryItemsAsync`` dans la méthode ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

Sélectionnez **F5** pour exécuter votre application.

Félicitations ! Vous avez interrogé un conteneur Azure Cosmos DB.

## <a id="ReplaceItem"></a>Étape 8 : Remplacer un élément JSON
À présent, nous allons mettre à jour un élément dans Azure Cosmos DB.

1. Copiez et collez la méthode **ReplaceFamilyItemAsync** sous votre méthode **QueryItemsAsync**. Notez que nous modifions deux propriétés, ``IsRegistered`` de la famille, et ``Grade`` de l’un des enfants.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Ajoutez un appel à ``ReplaceFamilyItemAsync`` dans la méthode ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

   Sélectionnez **F5** pour exécuter votre application.

Félicitations ! Vous avez remplacé un élément Azure Cosmos DB.

## <a id="DeleteDocument"></a>Étape 9 : Delete item (Supprimer un élément)
À présent, nous allons supprimer un élément dans Azure Cosmos DB.

1. Copiez et collez la méthode **DeleteFamilyItemAsync** sous votre méthode **ReplaceFamilyItemAsync**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Ajoutez un appel à ``DeleteFamilyItemAsync`` dans la méthode ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

Sélectionnez **F5** pour exécuter votre application.

Félicitations ! Vous avez supprimé un élément Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Étape 10 : Supprimer la base de données
Maintenant, nous allons supprimer notre base de données. Supprimer la base de données créée revient à supprimer la base de données et toutes les ressources enfants (conteneurs, éléments, procédures stockées, fonctions définies par l’utilisateur et déclencheurs). Nous allons également supprimer l’instance **CosmosClient**.

1. Copiez et collez la méthode **DeleteDatabaseAndCleanupAsync** sous votre méthode **DeleteFamilyItemAsync**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Ajoutez un appel à ``DeleteDatabaseAndCleanupAsync`` dans la méthode ``GetStartedDemoAsync``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

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

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
