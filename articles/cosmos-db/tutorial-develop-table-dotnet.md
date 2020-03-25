---
title: API Table Azure Cosmos DB à l’aide du SDK .NET Standard
description: Découvrez comment stocker et interroger les données structurées dans le compte d’API Table Azure Cosmos DB
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 12/03/2019
ms.openlocfilehash: f4d6e1bb0d5db0dbfc30e14abc58321bce8d0baf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79222046"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Bien démarrer avec l’API Table d’Azure Cosmos DB et le stockage Table Azure à l’aide du SDK .NET

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Vous pouvez utiliser l’API Table d’Azure Cosmos DB ou le stockage Table Azure pour stocker des données NoSQL structurées dans le cloud, en fournissant un magasin de clés/attributs avec une conception sans schéma. Comme l’API Table d’Azure Cosmos DB et le stockage Table Azure présentent une conception sans schéma, il est facile d’adapter vos données en fonction des besoins de votre application. Vous pouvez utiliser l’API Table d’Azure Cosmos DB et le stockage Table Azure pour stocker des jeux de données flexibles, comme des données utilisateur pour des applications web, des carnets d’adresses, des informations sur les appareils ou d’autres types de métadonnées requis par votre service. 

Ce tutoriel décrit un exemple qui vous montre comment utiliser la [bibliothèque Microsoft Azure Cosmos DB Table pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) dans des scénarios basés sur l’API Table d’Azure Cosmos DB et le Stockage Table Azure. Vous devez utiliser la connexion propre au service Azure. Ces scénarios sont explorés à l’aide d’exemples en C# qui montrent comment créer des tables, ajouter ou mettre à jour des données, interroger des données et supprimer des tables.

## <a name="prerequisites"></a>Conditions préalables requises

Vous aurez besoin des éléments suivants pour suivre cet exemple :

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Bibliothèque Microsoft Azure CosmosDB Table pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) : cette bibliothèque est actuellement disponible pour l’infrastructure .NET Standard et .NET. 

* [Compte d’API Table Cosmos DB Azure](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Créer un compte d’API de table Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Créer un projet de console .NET

Dans Visual Studio, créez une application console .NET. Les étapes suivantes vous montrent comment créer une application console dans Visual Studio 2019. Vous pouvez utiliser la bibliothèque Azure Cosmos DB Table dans n’importe quel type d’application .NET, y compris un service cloud Azure, une application web, une application de bureau ou une application mobile. Dans ce guide, nous utilisons une application console pour plus de simplicité.

1. Sélectionnez **Fichier** > **Nouveau** > **Projet**.

1. Choisissez **Application console (.NET Core)** , puis sélectionnez **Suivant**.

1. Dans le champ **Nom de projet**, entrez un nom pour votre application, tel que **CosmosTableSamples**. Vous pouvez fournir un autre nom.

1. Sélectionnez **Create** (Créer).

Tous les exemples de code figurant dans cette démonstration peuvent être ajoutés à la méthode Main() dans le fichier **Program.cs** de votre application console.

## <a name="install-the-required-nuget-package"></a>Installer le package NuGet nécessaire

Pour obtenir le package NuGet, effectuez les étapes suivantes :

1. Cliquez avec le bouton droit sur votre projet dans **l’Explorateur de solutions**, puis sélectionnez **Gérer les packages NuGet**.

1. Recherchez en ligne [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration), [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json), [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder), puis sélectionnez **Installer** pour installer la bibliothèque Microsoft Azure Cosmos DB Table.

## <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage

1. À partir du [Portail Azure](https://portal.azure.com/), accédez à votre compte Azure Cosmos ou au compte Stockage Table. 

1. Ouvrez le volet **Chaîne de connexion** ou **Clés d’accès**. Utilisez les boutons de copie sur le côté droit de la fenêtre pour copier la **CHAÎNE DE CONNEXION PRINCIPALE**.

   ![Afficher et copier la CHAÎNE DE CONNEXION PRINCIPALE dans le volet Chaîne de connexion](./media/create-table-dotnet/connection-string.png)
   
1. Pour configurer votre chaîne de connexion, à partir de Visual Studio, cliquez avec le bouton droit sur votre projet **CosmosTableSamples**.

1. Sélectionnez **Ajouter**, puis **Nouvel élément**. Créez un fichier **Settings.json** avec le type de fichier **config JSON TypeScript**. 

1. Remplacez le code dans le fichier Settings.json par le code suivant et affectez la chaîne de connexion principale :

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Cliquez avec le bouton droit sur votre projet **CosmosTableSamples**. Sélectionnez **Ajouter**, **Nouvel élément** et ajoutez une classe nommée **AppSettings.cs**.

1. Ajoutez le code suivant au fichier AppSettings.cs. Ce fichier lit la chaîne de connexion contenue dans le fichier Settings.json et l’affecte au paramètre de configuration :

   ```csharp
   namespace CosmosTableSamples
   {
    using Microsoft.Extensions.Configuration;
    public class AppSettings
    {
        public string StorageConnectionString { get; set; }
        public static AppSettings LoadAppSettings()
        {
            IConfigurationRoot configRoot = new ConfigurationBuilder()
                .AddJsonFile("Settings.json")
                .Build();
            AppSettings appSettings = configRoot.Get<AppSettings>();
            return appSettings;
        }
    }
   }
   ```

## <a name="parse-and-validate-the-connection-details"></a>Analyser et valider les informations de connexion 

1. Cliquez avec le bouton droit sur votre projet **CosmosTableSamples**. Sélectionnez **Ajouter**, **Nouvel élément** et ajoutez une classe nommée **Common.cs**. Vous allez écrire le code qui valide les informations de connexion et crée une table au sein de cette classe.

1. Définissez une méthode `CreateStorageAccountFromConnectionString` comme indiqué ci-dessous. Cette méthode analyse les informations de la chaîne de connexion et vérifie que le nom et la clé du compte fournis dans le fichier « Settings.json » sont corrects. 

 ```csharp
using System;

namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Microsoft.Azure.Documents;

    public class Common
    {
        public static CloudStorageAccount CreateStorageAccountFromConnectionString(string storageConnectionString)
        {
            CloudStorageAccount storageAccount;
            try
            {
                storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            }
            catch (FormatException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the application.");
                throw;
            }
            catch (ArgumentException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the sample.");
                Console.ReadLine();
                throw;
            }

            return storageAccount;
        }
    }
}
   ```


## <a name="create-a-table"></a>Créer une table 

La classe [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) vous permet de récupérer des tables et entités stockées dans Table Storage. Étant donné qu’il n’y a pas de tables dans le compte d’API Table Cosmos DB, nous allons en créer une en ajoutant la méthode `CreateTableAsync` à la classe **Common.cs** :

```csharp
public static async Task<CloudTable> CreateTableAsync(string tableName)
  {
    string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;

    // Retrieve storage account information from connection string.
    CloudStorageAccount storageAccount = CreateStorageAccountFromConnectionString(storageConnectionString);

    // Create a table client for interacting with the table service
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());

    Console.WriteLine("Create a Table for the demo");

    // Create a table client for interacting with the table service 
    CloudTable table = tableClient.GetTableReference(tableName);
    if (await table.CreateIfNotExistsAsync())
    {
      Console.WriteLine("Created Table named: {0}", tableName);
    }
    else
    {
      Console.WriteLine("Table {0} already exists", tableName);
    }

    Console.WriteLine();
    return table;
}
```

Si vous recevez une erreur « Exception 503 - Service indisponible », il est possible que les ports nécessaires pour le mode de connectivité soient bloqués par un pare-feu. Pour résoudre ce problème, ouvrez les ports nécessaires ou utilisez la connectivité en mode de passerelle, comme indiqué dans le code suivant :

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>Définir l’entité 

Les entités mappent vers les objets C# en utilisant une classe personnalisée dérivée d’une [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Pour ajouter une entité à une table, créez une classe définissant les propriétés de votre entité.

Cliquez avec le bouton droit sur votre projet **CosmosTableSamples**. Sélectionnez **Ajouter**, **Nouveau dossier** et nommez le dossier **Model** (Modèle). Dans le dossier Model, ajoutez une classe nommée **CustomerEntity.cs** et ajoutez-y le code suivant.

```csharp
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

Ce code définit une classe d’entité où le prénom et le nom de famille du client sont utilisés en tant que clé de ligne et clé de partition, respectivement. Ensemble, les clés de partition et de ligne d’une entité l’identifient de façon unique dans la table. Les requêtes sur des entités ayant la même clé de partition sont plus rapides que celles effectuées sur des entités qui n’ont pas la même, mais l’utilisation de clés de partition différentes améliore la scalabilité des opérations parallèles. Les entités à stocker dans des tables doivent être d’un type pris en charge, par exemple dérivé de la classe [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Les propriétés de l’entité à stocker dans une table doivent être des propriétés publiques du type et prendre en charge la récupération et la définition de valeurs. De plus, le type d’entité doit exposer un constructeur sans paramètre.

## <a name="insert-or-merge-an-entity"></a>Insérer ou fusionner une entité

L’exemple de code suivant crée un objet entité et l’ajoute à la table. La méthode InsertOrMerge dans la classe [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) s’utilise pour insérer ou fusionner une entité. La méthode [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) est appelée pour exécuter l’opération. 

Cliquez avec le bouton droit sur votre projet **CosmosTableSamples**. Sélectionnez **Ajouter**, **Nouvel élément** et ajoutez une classe nommée **SamplesUtils.cs**. Cette classe stocke tout le code requis pour effectuer des opérations CRUD sur les entités. 

```csharp
 public static async Task<CustomerEntity> InsertOrMergeEntityAsync(CloudTable table, CustomerEntity entity)
 {
     if (entity == null)
     {
         throw new ArgumentNullException("entity");
     }
     try
     {
         // Create the InsertOrReplace table operation
         TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);

         // Execute the operation.
         TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
         CustomerEntity insertedCustomer = result.Result as CustomerEntity;

         // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure Cosmos DB
         if (result.RequestCharge.HasValue)
         {
             Console.WriteLine("Request Charge of InsertOrMerge Operation: " + result.RequestCharge);
         }

         return insertedCustomer;
     }
     catch (StorageException e)
     {
         Console.WriteLine(e.Message);
         Console.ReadLine();
         throw;
     }
 }
```

### <a name="get-an-entity-from-a-partition"></a>Obtenir une entité à partir d’une partition

Vous pouvez obtenir une entité à partir d’une partition en utilisant la méthode Retrieve dans la classe [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation). L’exemple de code suivant obtient la clé de ligne de la clé de partition, l’e-mail et le numéro de téléphone d’une entité client. Cet exemple affiche également les unités de requête consommées pour obtenir l’entité. Pour rechercher une entité, ajoutez le code suivant au fichier **SamplesUtils.cs** : 

```csharp
public static async Task<CustomerEntity> RetrieveEntityUsingPointQueryAsync(CloudTable table, string partitionKey, string rowKey)
    {
      try
      {
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
        TableResult result = await table.ExecuteAsync(retrieveOperation);
        CustomerEntity customer = result.Result as CustomerEntity;
        if (customer != null)
        {
          Console.WriteLine("\t{0}\t{1}\t{2}\t{3}", customer.PartitionKey, customer.RowKey, customer.Email, customer.PhoneNumber);
        }

        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
        {
           Console.WriteLine("Request Charge of Retrieve Operation: " + result.RequestCharge);
        }

        return customer;
        }
        catch (StorageException e)
        {
           Console.WriteLine(e.Message);
           Console.ReadLine();
           throw;
        }
    }
```

## <a name="delete-an-entity"></a>Suppression d’une entité

Il est facile de supprimer une entité après l’avoir récupérée. Il suffit pour cela d’utiliser la procédure suivie pour la mise à jour d’une entité. Le code suivant extrait et supprime une entité de client. Pour supprimer une entité, ajoutez le code suivant au fichier **SamplesUtils.cs** : 

```csharp
public static async Task DeleteEntityAsync(CloudTable table, CustomerEntity deleteEntity)
   {
     try
     {
        if (deleteEntity == null)
     {
        throw new ArgumentNullException("deleteEntity");
     }

    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    TableResult result = await table.ExecuteAsync(deleteOperation);

    // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
    if (result.RequestCharge.HasValue)
    {
       Console.WriteLine("Request Charge of Delete Operation: " + result.RequestCharge);
    }

    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="execute-the-crud-operations-on-sample-data"></a>Exécuter les opérations CRUD sur les exemples de données

Après avoir défini les méthodes pour créer une table et ajouter ou fusionner des entités, exécutez-les sur les exemples de données. Pour ce faire, cliquez avec le bouton droit sur votre projet **CosmosTableSamples**. Sélectionnez **Ajouter**, **Nouvel élément** et ajoutez une classe nommée **BasicSamples.cs**, puis ajoutez-y le code suivant. Ce code crée une table et y ajoute des entités. Si vous souhaitez supprimer l’entité et la table à la fin du projet, supprimez les commentaires des méthodes `table.DeleteIfExistsAsync()` et `SamplesUtils.DeleteEntityAsync(table, customer)` dans le code suivant :

```csharp
using System;
namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Model;

    class BasicSamples
    {
        public async Task RunSamples()
        {
            Console.WriteLine("Azure Cosmos DB Table - Basic Samples\n");
            Console.WriteLine();

            string tableName = "demo" + Guid.NewGuid().ToString().Substring(0, 5);

            // Create or reference an existing table
            CloudTable table = await Common.CreateTableAsync(tableName);

            try
            {
                // Demonstrate basic CRUD functionality 
                await BasicDataOperationsAsync(table);
            }
            finally
            {
                // Delete the table
                // await table.DeleteIfExistsAsync();
            }
        }

        private static async Task BasicDataOperationsAsync(CloudTable table)
        {
            // Create an instance of a customer entity. See the Model\CustomerEntity.cs for a description of the entity.
            CustomerEntity customer = new CustomerEntity("Harp", "Walter")
            {
                Email = "Walter@contoso.com",
                PhoneNumber = "425-555-0101"
            };

            // Demonstrate how to insert the entity
            Console.WriteLine("Insert an Entity.");
            customer = await SamplesUtils.InsertOrMergeEntityAsync(table, customer);

            // Demonstrate how to Update the entity by changing the phone number
            Console.WriteLine("Update an existing Entity using the InsertOrMerge Upsert Operation.");
            customer.PhoneNumber = "425-555-0105";
            await SamplesUtils.InsertOrMergeEntityAsync(table, customer);
            Console.WriteLine();

            // Demonstrate how to Read the updated entity using a point query 
            Console.WriteLine("Reading the updated Entity.");
            customer = await SamplesUtils.RetrieveEntityUsingPointQueryAsync(table, "Harp", "Walter");
            Console.WriteLine();

            // Demonstrate how to Delete an entity
            //Console.WriteLine("Delete the entity. ");
            //await SamplesUtils.DeleteEntityAsync(table, customer);
            //Console.WriteLine();
        }
    }
}
```

Le code précédent crée une table dont le nom est composé du préfixe « demo », suivi du GUID généré. Il ajoute ensuite une entité client avec le nom « Harp » et le prénom « Walter », puis met à jour le numéro de téléphone de cet utilisateur. 

Dans ce tutoriel, vous avez créé le code nécessaire pour effectuer des opérations CRUD de base sur les données stockées dans un compte d’API Table. Vous pouvez aussi effectuer diverses opérations avancées, par exemple, ajouter des données par lot, interroger toutes les données d’une partition, interroger une plage de données d’une partition et lister les tables du compte dont les noms commencent par le préfixe spécifié. Vous pouvez télécharger l’exemple complet à partir du dépôt [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) sur GitHub. La classe [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) permet d’effectuer davantage d’opérations sur les données.  

## <a name="run-the-project"></a>Exécuter le projet

À partir de votre projet **CosmosTableSamples**. Ouvrez la classe nommée **Program.cs** et ajoutez-lui le code suivant pour appeler BasicSamples quand le projet s’exécute.

```csharp
using System;

namespace CosmosTableSamples
{
    class Program
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Azure Cosmos Table Samples");
            BasicSamples basicSamples = new BasicSamples();
            basicSamples.RunSamples().Wait();
           
            Console.WriteLine();
            Console.WriteLine("Press any key to exit");
            Console.Read();
        }
    }
}
```

Maintenant, générez la solution et appuyez sur F5 pour exécuter le projet. Après l’exécution du projet, vous voyez la sortie suivante dans l’invite de commandes :

![Sortie de l’invite de commandes](./media/tutorial-develop-table-standard/output-from-sample.png)

Si vous voyez une erreur indiquant qu’un fichier Settings.json n’a pas été trouvé pendant l’exécution du projet, vous pouvez la résoudre en ajoutant l’entrée XML suivante dans les paramètres du projet. Cliquez avec le bouton droit sur CosmosTableSamples, sélectionnez Modifier CosmosTableSamples.csproj et ajoutez l’élément itemGroup suivant : 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Vous pouvez maintenant vous connecter au portail Azure et vérifier que les données existent bien dans la table. 

![Résultats dans le portail](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant passer au tutoriel suivant pour découvrir comment migrer les données vers un compte d’API Table Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Interroger les données](../cosmos-db/table-import.md)
