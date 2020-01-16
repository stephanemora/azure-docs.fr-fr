---
title: Liaisons de stockage Table Azure pour Azure Functions
description: Comprendre comment utiliser les liaisons de stockage de table Azure dans Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: bca3c246db25e86772a7009bf85e7d302b4b17b2
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562066"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Liaisons de stockage Table Azure pour Azure Functions

Cet article explique comment utiliser les liaisons de stockage de table Azure dans Azure Functions. Azure Functions prend en charge les liaisons d’entrée et de sortie pour Stockage de table Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Packages - Functions 1.x

Les liaisons du Stockage Table sont fournies dans le package NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), version 2.x. Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Packages – Functions 2.x et versions ultérieures

Les liaisons du Stockage Table sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), version 3.x. Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Entrée

La liaison d’entrée de stockage de table Azure permet de lire une table dans un compte de stockage Azure.

## <a name="input---example"></a>Entrée - exemple

Consultez l’exemple propre à un langage particulier :

* [C# - lire une entité](#input---c-example---one-entity)
* [C# - se lier à IQueryable](#input---c-example---iqueryable)
* [C# - se lier à CloudTable](#input---c-example---cloudtable)
* [Script C# - lire une entité](#input---c-script-example---one-entity)
* [Script C# - se lier à IQueryable](#input---c-script-example---iqueryable)
* [Script C# - se lier à CloudTable](#input---c-script-example---cloudtable)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)
* [Java](#input---java-example)

### <a name="input---c-example---one-entity"></a>Entrée - Exemple C# - une seule entité

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui lit une ligne de table unique. 

La valeur de clé de ligne "{queueTrigger}" indique que la clé de ligne provient de la chaîne de message de file d’attente.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="input---c-example---iqueryable"></a>Entrée - Exemple C# - IQueryable

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui lit plusieurs lignes de table. Notez que la classe `MyPoco` est dérivée de `TableEntity`.

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="input---c-example---cloudtable"></a>Entrée - Exemple C# - CloudTable

`IQueryable` n’est pas pris en charge dans le [runtime Functions v2](functions-versions.md). Une alternative consiste à utiliser un paramètre de méthode `CloudTable` pour lire la table en utilisant le kit SDK Stockage Azure. Voici un exemple d’une fonction qui interroge une table du journal Azure Functions :

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

Pour plus d’informations sur l’utilisation de CloudTable, consultez [Bien démarrer avec Stockage Table Azure](../cosmos-db/table-storage-how-to-use-dotnet.md).

Si vous essayez de lier à `CloudTable` et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---c-script-example---one-entity"></a>Entrée - Exemple de script C# - une seule entité

L’exemple suivant montre une liaison d’entrée de table dans un fichier *function.json* et un code [Script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction utilise un déclencheur de file d’attente pour lire une seule ligne du tableau. 

Le fichier *function.json* spécifie une propriété `partitionKey` et une propriété `rowKey`. La valeur `rowKey` "{queueTrigger}" indique que la clé de ligne provient de la chaîne de message de file d’attente.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code Script C# :

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="input---c-script-example---iqueryable"></a>Entrée - Exemple de script C# - IQueryable

L’exemple suivant montre une liaison d’entrée de table dans un fichier *function.json* et un code [Script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction lit les entités d’une clé de partition qui est spécifiée dans un message de file d’attente.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Le code Script C# ajoute une référence au kit SDK Stockage Azure afin que le type d’entité puisse dériver de `TableEntity` :

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="input---c-script-example---cloudtable"></a>Entrée - Exemple de script C# - CloudTable

`IQueryable` n’est pas pris en charge dans le runtime Functions pour les [versions 2.x et ultérieures](functions-versions.md). Une alternative consiste à utiliser un paramètre de méthode `CloudTable` pour lire la table en utilisant le kit SDK Stockage Azure. Voici un exemple d’une fonction qui interroge une table du journal Azure Functions :

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

Pour plus d’informations sur l’utilisation de CloudTable, consultez [Bien démarrer avec Stockage Table Azure](../cosmos-db/table-storage-how-to-use-dotnet.md).

Si vous essayez de lier à `CloudTable` et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---f-example"></a>Entrée - exemple F#

L’exemple suivant montre une liaison d’entrée de table dans un fichier *function.json* et du code [Script F#](functions-reference-fsharp.md) qui utilise la liaison. La fonction utilise un déclencheur de file d’attente pour lire une seule ligne du tableau. 

Le fichier *function.json* spécifie une propriété `partitionKey` et une propriété `rowKey`. La valeur `rowKey` "{queueTrigger}" indique que la clé de ligne provient de la chaîne de message de file d’attente.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code F# :

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.LogInformation(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>Entrée - exemple JavaScript

L’exemple suivant montre une liaison d’entrée de table dans un fichier *function.json* et un [code JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction utilise un déclencheur de file d’attente pour lire une seule ligne du tableau. 

Le fichier *function.json* spécifie une propriété `partitionKey` et une propriété `rowKey`. La valeur `rowKey` "{queueTrigger}" indique que la clé de ligne provient de la chaîne de message de file d’attente.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

### <a name="input---java-example"></a>Entrée - exemple Java

L’exemple suivant illustre une fonction déclenchée par HTTP, qui retourne le nombre total d’éléments dans une partition spécifiée dans le Stockage Table.

```java
@FunctionName("getallcount")
public int run(
   @HttpTrigger(name = "req",
                 methods = {HttpMethod.GET},
                 authLevel = AuthorizationLevel.ANONYMOUS) Object dummyShouldNotBeUsed,
   @TableInput(name = "items",
                tableName = "mytablename",  partitionKey = "myparkey",
                connection = "myconnvarname") MyItem[] items
) {
    return items.length;
}
```


## <a name="input---attributes"></a>Entrée - attributs
 
Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez les attributs suivants pour configurer un déclencheur d’entrée de table :

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Le constructeur de l’attribut prend le nom de la table, la clé de partition et la clé de ligne. Il peut être utilisé sur un paramètre out ou sur la valeur de retour de la fonction, comme indiqué dans l’exemple suivant :

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Vous pouvez définir la propriété `Connection` pour spécifier le compte de stockage à utiliser, comme indiqué dans l’exemple suivant :

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Pour obtenir un exemple complet, consultez Entrée - exemple C#.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Fournit une autre manière de spécifier le compte de stockage à utiliser. Le constructeur prend le nom d’un paramètre d’application comportant une chaîne de connexion de stockage. L’attribut peut être appliqué au niveau du paramètre, de la méthode ou de la classe. L’exemple suivant montre le niveau de la classe et celui de la méthode :

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Le compte de stockage à utiliser est déterminé dans l’ordre suivant :

* La propriété `Connection` de l’attribut `Table`.
* L’attribut `StorageAccount` appliqué au même paramètre que l’attribut `Table`.
* L’attribut `StorageAccount` appliqué à la fonction.
* L’attribut `StorageAccount` appliqué à la classe.
* Le compte de stockage par défaut pour l’application de fonction (paramètre d’application « AzureWebJobsStorage »).

## <a name="input---java-annotations"></a>Entrée - annotations Java

Dans la [bibliothèque du runtime des fonctions Java](/java/api/overview/azure/functions/runtime), utilisez l’annotation `@TableInput` sur les paramètres dont la valeur proviendrait du Stockage Table.  Vous pouvez utiliser cette annotation avec des types Java natifs, des objets POJO ou des valeurs Null à l’aide de Optional\<T>. 

## <a name="input---configuration"></a>Entrée - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `Table`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Cette propriété doit être définie sur `table`. Cette propriété est définie automatiquement lorsque vous créez la liaison dans le portail Azure.|
|**direction** | n/a | Cette propriété doit être définie sur `in`. Cette propriété est définie automatiquement lorsque vous créez la liaison dans le portail Azure. |
|**name** | n/a | Nom de la variable qui représente la table ou l’entité dans le code de la fonction. | 
|**tableName** | **TableName** | Nom de la table.| 
|**partitionKey** | **PartitionKey** |facultatif. Clé de partition de l’entité de table à lire. Consultez la section [utilisation](#input---usage) pour obtenir des conseils sur l’utilisation de cette propriété.| 
|**rowKey** |**RowKey** | facultatif. Clé de ligne de l’entité de table à lire. Consultez la section [utilisation](#input---usage) pour obtenir des conseils sur l’utilisation de cette propriété.| 
|**take** |**Take** | facultatif. Nombre maximal d’entités à lire en JavaScript. Consultez la section [utilisation](#input---usage) pour obtenir des conseils sur l’utilisation de cette propriété.| 
|**filter** |**Filter** | facultatif. Expression de filtre OData pour l’entrée de table dans JavaScript. Consultez la section [utilisation](#input---usage) pour obtenir des conseils sur l’utilisation de cette propriété.| 
|**connection** |**Connection** | Nom d’un paramètre d’application comportant la chaîne de connexion de stockage à utiliser pour cette liaison. Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom ici. Par exemple, si vous définissez `connection` sur « MyStorage », le runtime Functions recherche un paramètre d’application qui est nommé « AzureWebJobsMyStorage ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion de stockage par défaut dans le paramètre d’application nommé `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrée - utilisation

La liaison d’entrée de stockage de table prend en charge les scénarios suivants :

* **Lire une ligne dans C# ou Script C#**

  Définissez `partitionKey` et `rowKey`. Accédez aux données de la table à l’aide d’un paramètre de méthode `T <paramName>`. Dans Script C#, `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. `T` est généralement un type qui implémente `ITableEntity` ou est dérivé de `TableEntity`. Les propriétés `filter` et `take` ne sont pas utilisées dans ce scénario. 

* **Lire une ou plusieurs lignes en C# ou en Script C#**

  Accédez aux données de la table à l’aide d’un paramètre de méthode `IQueryable<T> <paramName>`. Dans Script C#, `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. `T` doit être généralement un type qui implémente `ITableEntity` ou est dérivé de `TableEntity`. Vous pouvez utiliser les méthodes `IQueryable` pour effectuer le filtrage voulu. Les propriétés `partitionKey`, `rowKey`, `filter` et `take` ne sont pas utilisées dans ce scénario.  

  > [!NOTE]
  > `IQueryable` n’est pas pris en charge dans le [runtime Functions v2](functions-versions.md). Une alternative consiste à utiliser un [paramètre de méthode CloudTable paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) pour lire la table à l’aide du SDK Stockage Azure. Si vous essayez de lier à `CloudTable` et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](#azure-storage-sdk-version-in-functions-1x).

* **Lire une ou plusieurs lignes en JavaScript**

  Définissez les propriétés `filter` et `take`. Ne définissez pas `partitionKey` ni `rowKey`. Accédez à l’entité (ou les entités) de table d’entrée à l’aide de `context.bindings.<BINDING_NAME>`. Les objets désérialisés ont des propriétés `RowKey` et `PartitionKey`.

## <a name="output"></a>Output

Utilisez une liaison de sortie de stockage de table Azure pour écrire des entités dans une table d’un compte de Stockage Azure.

> [!NOTE]
> Cette liaison de sortie ne prend pas en charge la mise à jour d’entités existantes. Utilisez la [`TableOperation`](/dotnet/api/microsoft.azure.cosmos.table.tableoperation?view=azure-dotnet) nécessaire qui est disponible dans le [SDK Stockage Azure](/azure/cosmos-db/tutorial-develop-table-dotnet#insert-or-merge-an-entity) afin de mettre à jour une entité existante.   

## <a name="output---example"></a>Sortie - exemple

Consultez l’exemple propre à un langage particulier :

* [C#](#output---c-example)
* [Script C# (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Sortie - exemple C#

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui utilise un déclencheur HTTP pour écrire une ligne de table. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>Sortie - exemple Script C#

L’exemple suivant montre une liaison de sortie de table dans un fichier *function.json* et un code [Script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction écrit plusieurs entités de table.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code Script C# :

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

### <a name="output---f-example"></a>Sortie - exemple F#

L’exemple suivant montre une liaison de sortie de table dans un fichier *function.json* et un code [Script F#](functions-reference-fsharp.md) qui utilise la liaison. La fonction écrit plusieurs entités de table.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code F# :

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: ILogger) =
    for i = 1 to 10 do
        log.LogInformation(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>Sortie - exemple JavaScript

L’exemple suivant montre une liaison de sortie de table dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction écrit plusieurs entités de table.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

## <a name="output---attributes"></a>Sortie - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Le constructeur de l’attribut prend le nom de la table. Il peut être utilisé sur un paramètre `out` ou sur la valeur de retour de la fonction, comme indiqué dans l’exemple suivant :

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Vous pouvez définir la propriété `Connection` pour spécifier le compte de stockage à utiliser, comme indiqué dans l’exemple suivant :

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Pour obtenir un exemple complet, consultez [Sortie - exemple C#](#output---c-example).

Vous pouvez utiliser l’attribut `StorageAccount` pour spécifier le compte de stockage au niveau de la classe, de la méthode ou du paramètre. Pour plus d’informations, consultez [Entrée - attributs](#input---attributes).

## <a name="output---configuration"></a>Sortie - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `Table`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Cette propriété doit être définie sur `table`. Cette propriété est définie automatiquement lorsque vous créez la liaison dans le portail Azure.|
|**direction** | n/a | Cette propriété doit être définie sur `out`. Cette propriété est définie automatiquement lorsque vous créez la liaison dans le portail Azure. |
|**name** | n/a | Nom de variable utilisé dans le code de la fonction qui représente la table ou l’entité. La valeur doit être `$return` pour faire référence à la valeur de retour de la fonction.| 
|**tableName** |**TableName** | Nom de la table.| 
|**partitionKey** |**PartitionKey** | Clé de partition de l’entité de table à écrire. Consultez la section [utilisation](#output---usage) pour obtenir des conseils sur l’utilisation de cette propriété.| 
|**rowKey** |**RowKey** | Clé de ligne de l’entité de table à écrire. Consultez la section [utilisation](#output---usage) pour obtenir des conseils sur l’utilisation de cette propriété.| 
|**connection** |**Connection** | Nom d’un paramètre d’application comportant la chaîne de connexion de stockage à utiliser pour cette liaison. Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom ici. Par exemple, si vous définissez `connection` sur « MyStorage », le runtime Functions recherche un paramètre d’application qui est nommé « AzureWebJobsMyStorage ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion de stockage par défaut dans le paramètre d’application nommé `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Sortie - utilisation

La liaison de sortie de stockage de table prend en charge les scénarios suivants :

* **Écrire une ligne dans un langage**

  Dans C# et Script C#, accédez à l’entité de table de sortie en utilisant un paramètre de méthode comme `out T paramName` ou la valeur de retour de la fonction. Dans Script C#, `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. `T` peut être n’importe quel type sérialisable si la clé de partition et la clé de ligne sont fournies par le fichier *function.json* ou l’attribut `Table`. Sinon, `T` doit être un type qui inclut les propriétés `PartitionKey` et `RowKey`. Dans ce scénario, `T` implémente généralement `ITableEntity` ou est dérivé de `TableEntity`, mais ce n’est pas obligatoire.

* **Écrire une ou plusieurs lignes en C# ou en Script C#**

  Dans C# et Script C#, accédez à l’entité de table de sortie en utilisant un paramètre de méthode `ICollector<T> paramName` ou `IAsyncCollector<T> paramName`. Dans Script C#, `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. `T` spécifie le schéma des entités que vous souhaitez ajouter. En général, `T` est dérivé de `TableEntity` ou implémente `ITableEntity`, mais ce n’est pas obligatoire. Ni les valeurs de clé de partition et de clé de ligne dans *function.json*, ni le constructeur d’attribut `Table` ne sont utilisés dans ce scénario.

  Une autre solution consiste à utiliser un paramètre de méthode `CloudTable` pour écrire la table en utilisant le kit SDK Stockage Azure. Si vous essayez de lier à `CloudTable` et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](#azure-storage-sdk-version-in-functions-1x). Pour obtenir un exemple de code qui se lie à `CloudTable`, consultez les exemples de liaison d’entrée pour [C#](#input---c-example---cloudtable) ou [Script C#](#input---c-script-example---cloudtable) plus haut dans cet article.

* **Écrire une ou plusieurs lignes en JavaScript**

  Dans les fonctions JavaScript, accédez à la sortie de table avec `context.bindings.<BINDING_NAME>`.

## <a name="exceptions-and-return-codes"></a>Exceptions et codes de retour

| Liaison | Informations de référence |
|---|---|
| Table de charge de travail | [Codes d’erreur de table](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Objet blob, Table, File d’attente | [Codes d’erreur de stockage](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objet blob, Table, File d’attente | [Dépannage](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
