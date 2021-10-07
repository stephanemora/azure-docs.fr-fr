---
title: Bien démarrer avec Stockage Table à l’aide de Visual Studio (services cloud)
description: Comment prendre en main le stockage de tables Azure dans un projet service cloud dans Visual Studio après s’être connecté à un compte de stockage à l’aide des services connectés de Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3cad09a5359bd5d9bd2041eb92d0e994adf80080
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823080"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Prendre en main le stockage de tables Azure et les services connectés de Visual Studio (projets services cloud)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d’ensemble

[!INCLUDE [Cloud Services (classic) deprecation announcement](../cloud-services/includes/deprecation-announcement.md)]


Cet article décrit comment prendre en main Azure Table Storage dans Visual Studio après avoir créé ou référencé un compte de stockage Azure dans un projet de services cloud via la boîte de dialogue **Ajouter des services connectés** de Visual Studio. L’opération **Ajouter des services connectés** installe les packages NuGet appropriés pour accéder au stockage Azure de votre projet et ajoute la chaîne de connexion pour le compte de stockage aux fichiers de configuration de votre projet.

Le service de stockage de tables Azure vous permet de stocker de grandes quantités de données structurées. Il s'agit d'une banque de données NoSQL qui accepte les appels authentifiés provenant de l'intérieur et de l'extérieur du cloud Azure. Les tables Azure sont idéales pour le stockage des données structurées non relationnelles.

Pour commencer, vous devez créer une table dans votre compte de stockage. Nous vous montrerons comment créer une table Azure dans le code et effectuer des opérations de base sur les tables et les entités, comme l’ajout, la modification et la lecture d’entités de table. Les exemples sont écrits en code C\# et utilisent la [bibliothèque cliente Stockage Microsoft Azure pour .NET](/previous-versions/azure/dn261237(v=azure.100)).

**REMARQUE :** parmi les API qui appellent le stockage Azure, certaines sont asynchrones. Pour plus d’informations, consultez l’article [Programmation asynchrone avec Async et Await](/previous-versions/hh191443(v=vs.140)) . Le code ci-dessous suppose que les méthodes de programmation asynchrone sont utilisées.

* Pour plus d’informations sur la manipulation des tables par programme, consultez la page [Prise en main du stockage de tables Azure à l’aide de .NET](../cosmos-db/tutorial-develop-table-dotnet.md) .
* Pour des informations générales sur Azure Storage, consultez la [documentation relative au stockage](https://azure.microsoft.com/documentation/services/storage/) .
* Pour des informations générales sur les services cloud Azure, consultez la [documentation des services cloud](https://azure.microsoft.com/documentation/services/cloud-services/) .
* Pour plus d’informations sur la programmation des applications ASP.NET, consultez la page [ASP.NET](https://www.asp.net) .

## <a name="access-tables-in-code"></a>Accès aux tables dans le code
Pour accéder aux tables dans les projets de service cloud, vous devez inclure les éléments suivants dans les fichiers sources C# qui accèdent au stockage de tables Azure.

1. Vérifiez que les déclarations d’espace de noms figurant au début du fichier C# incluent ces instructions **using** .
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Azure.Data.Table;
    using System.Collections.Generic
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. Obtenez un objet **AzureStorageConnectionString** pour créer un **TableServiceClient** qui effectue des opérations au niveau du compte, telles que la création et la suppression de tables.
   
    ```csharp
    string storageConnString = "_AzureStorageConnectionString"
    ```

   > [!NOTE]
   > Placez tout le code ci-dessus avant celui des exemples suivants.
   
3. Obtenez un objet **TableServiceClient** pour référencer les objets de table de votre compte de stockage.
   
    ```csharp
    // Create the table service client.
    TableServiceClient tableServiceClient = new TableServiceClient(storageConnString);
    ```

4. Obtenez un objet de référence **TableClient** pour référencer une table et des entités spécifiques :
   
    ```csharp
    // Get a reference to a table named "peopleTable".
    TableClient peopleTable = tableServiceClient.GetTableClient("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Création d'une table dans le code
Pour créer la table Azure, ajoutez simplement un appel à **CreateIfNotExistsAsync** après avoir obtenu un objet **TableClient** comme indiqué dans la section « Accès aux tables dans le code ».

```csharp
// Create the TableClient if it does not exist.
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Ajout d'une entité à une table
Pour ajouter une entité à une table, créez une classe définissant les propriétés de votre entité. Le code suivant définit une classe d’entité nommée **CustomerEntity** , utilisant le prénom du client en tant que clé de ligne et son nom de famille en tant que clé de partition.

```csharp
public class CustomerEntity : ITableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Les opérations AddEntity impliquant des entités sont effectuées en utilisant l'objet **TableClient** créé précédemment dans la section « Accéder aux tables dans le code ». La méthode **AddEntity** représente l'opération à effectuer. L'exemple de code suivant montre comment créer des objets **TableClient** et **CustomerEntity**. Pour préparer l'opération, un **AddEntity** insère l'entité du client dans la table.

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Inserts the customer entity.
peopleTable.AddEntity(customer1)
```


## <a name="insert-a-batch-of-entities"></a>Insertion d'un lot d'entités
Vous pouvez insérer plusieurs entités dans une table en une seule opération d'écriture. L'exemple de code suivant crée deux objets d'entité (« Jeff Smith » et « Ben Smith »), les ajoute à un objet **addEntitiesBatch** en utilisant la méthode AddRange, puis démarre l'opération en appelant **TableClient.SubmitTransactionAsync**.

```csharp
// Create a list of 2 entities with the same partition key.
List<CustomerEntity> entityList = new List<CustomerEntity>
{
    new CustomerEntity("Smith", "Jeff")
    {
        { "Email", "Jeff@contoso.com" },
        { "PhoneNumber", "425-555-0104" }
    },
    new CustomerEntity("Smith", "Ben")
    {
        { "Email", "Ben@contoso.com" },
        { "PhoneNumber", "425-555-0102" }
    },
};

// Create the batch.
List<TableTransactionAction> addEntitiesBatch = new List<TableTransactionAction>();

// Add the entities to be added to the batch.
addEntitiesBatch.AddRange(entityList.Select(e => new TableTransactionAction(TableTransactionActionType.Add, e)));

// Submit the batch.
Response<IReadOnlyList<Response>> response = await peopleTable.SubmitTransactionAsync(addEntitiesBatch).ConfigureAwait(false);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Recherche de toutes les entités d'une partition
Pour interroger une table pour toutes les entités d'une partition, utilisez un objet **Query**. L’exemple de code suivant indique un filtre pour les entités où ’Smith’ est la clé de partition. Il imprime les champs de chaque entité dans les résultats de requête vers la console.

```csharp
Pageable<CustomerEntity> queryResultsFilter = peopleTable.Query<CustomerEntity>(filter: "PartitionKey eq 'Smith'");

// Print the fields for each customer.
foreach (CustomerEntity qEntity in queryResultsFilter)
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", qEntity.PartitionKey, qEntity.RowKey, qEntity.Email, qEntity.PhoneNumber);
}
```


## <a name="get-a-single-entity"></a>Obtention d'une seule entité
Vous pouvez écrire une requête pour obtenir une seule entité. Le code suivant utilise une méthode **GetEntityAsync** pour spécifier le client « Ben Smith ». Cette méthode renvoie une seule entité (et non une collection). De plus, la valeur renvoyée dans **GetEntityAsync.Result** est un objet **CustomerEntity**. La méthode la plus rapide pour extraire une seule entité dans le service de **Table** consiste à spécifier une clé de partition et une clé de ligne.

```csharp
var singleResult = peopleTable.GetEntityAsync<CustomerEntity>("Smith", "Ben");

// Print the phone number of the result.
if (singleResult.Result != null)
    Console.WriteLine(((CustomerEntity)singleResult.Result).PhoneNumber);
else
    Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Suppression d’une entité
Une fois l'entité trouvée, vous pouvez la supprimer. Le code suivant recherche l'entité de client « Ben Smith », puis la supprime.

```csharp
var singleResult = peopleTable.GetEntityAsync<CustomerEntity>("Smith", "Ben");

CustomerEntity deleteEntity = (CustomerEntity)singleResult.Result;

// Delete the entity given the partition and row key.
if (deleteEntity != null)
{
    await peopleTable.DeleteEntity(deleteEntity.PartitionKey, deleteEntity.RowKey);

    Console.WriteLine("Entity deleted.");
}

else
    Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
