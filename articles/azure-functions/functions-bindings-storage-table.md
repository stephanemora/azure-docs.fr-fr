---
title: Liaisons de stockage Table Azure pour Azure Functions
description: Comprendre comment utiliser les liaisons de stockage de table Azure dans Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 70329d07a9966a5cbdafcd64000470c4edcbca9e
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072048"
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

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>Une entité

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui lit une ligne de table unique. Pour chaque message envoyé à la file d'attente, la fonction sera déclenchée.

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

### <a name="iqueryable"></a>IQueryable

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui lit plusieurs lignes de table où la classe `MyPoco` dérive de `TableEntity`.

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

### <a name="cloudtable"></a>CloudTable

`IQueryable` est pris en charge uniquement dans le [runtime Functions v1](functions-versions.md). Une alternative consiste à utiliser un paramètre de méthode `CloudTable` pour lire la table en utilisant le kit SDK Stockage Azure. Voici un exemple d’une fonction qui interroge une table du journal Azure Functions :

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos.Table;
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

Pour plus d’informations sur l’utilisation de CloudTable, consultez [Bien démarrer avec Stockage Table Azure](../cosmos-db/tutorial-develop-table-dotnet.md).

Si vous essayez de lier à `CloudTable` et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

### <a name="one-entity"></a>Une entité

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

### <a name="iqueryable"></a>IQueryable

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
#r "Microsoft.Azure.Cosmos"
using Microsoft.Azure.Cosmos.Table;
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

### <a name="cloudtable"></a>CloudTable

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

Pour plus d’informations sur l’utilisation de CloudTable, consultez [Bien démarrer avec Stockage Table Azure](../cosmos-db/tutorial-develop-table-dotnet.md).

Si vous essayez de lier à `CloudTable` et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](#azure-storage-sdk-version-in-functions-1x).


# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

Ligne de table unique 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

# <a name="java"></a>[Java](#tab/java)

L’exemple suivant illustre une fonction déclenchée par HTTP qui retourne une liste d’objets Person qui se trouvent dans une partition spécifiée du stockage de table. Dans l’exemple, la clé de partition est extraite de l’itinéraire http, tandis que le nom de table (tableName) et la connexion proviennent des paramètres de fonction. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

L’annotation TableInput peut également extraire les liaisons du corps JSON de la requête, comme le montre l’exemple suivant.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

Les exemples suivants utilisent le filtre pour interroger des personnes portant un nom spécifique dans une table Azure, et limiter le nombre de correspondances possibles à 10 résultats.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

---

## <a name="input---attributes-and-annotations"></a>Sortie – Attributs et annotations

# <a name="c"></a>[C#](#tab/csharp)

 Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez les attributs suivants pour configurer un déclencheur d’entrée de table :

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Le constructeur de l’attribut prend le nom de la table, la clé de partition et la clé de ligne. L’attribut peut être utilisé sur un paramètre `out` ou sur la valeur de retour de la fonction, comme dans l’exemple suivant :

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

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Les attributs ne sont pas pris en charge par le script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Les attributs ne sont pas pris en charge par JavaScript.

# <a name="python"></a>[Python](#tab/python)

Les attributs ne sont pas pris en charge par Python.

# <a name="java"></a>[Java](#tab/java)

Dans la [bibliothèque du runtime des fonctions Java](/java/api/overview/azure/functions/runtime), utilisez l’annotation `@TableInput` sur les paramètres dont la valeur proviendrait du Stockage Table.  Vous pouvez utiliser cette annotation avec des types Java natifs, des objets POJO ou des valeurs Null à l’aide de `Optional<T>`.

---

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
|**connection** |**Connection** | Nom d’un paramètre d’application comportant la chaîne de connexion de stockage à utiliser pour cette liaison. Le paramètre peut être le nom d’un paramètre d’application préfixé « AzureWebJobs » ou un nom de chaîne de connexion. Par exemple, si le nom de votre paramètre est « AzureWebJobsMyStorage », vous pouvez spécifier « MyStorage » ici. Le runtime Functions recherche automatiquement un paramètre d’application nommé « AzureWebJobsMyStorage ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion de stockage par défaut dans le paramètre d’application nommé `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrée - utilisation

# <a name="c"></a>[C#](#tab/csharp)

* **Lire une ligne dans**

  Définissez `partitionKey` et `rowKey`. Accédez aux données de la table à l’aide d’un paramètre de méthode `T <paramName>`. Dans Script C#, `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. `T` est généralement un type qui implémente `ITableEntity` ou est dérivé de `TableEntity`. Les propriétés `filter` et `take` ne sont pas utilisées dans ce scénario.

* **Lire une ou plusieurs lignes**

  Accédez aux données de la table à l’aide d’un paramètre de méthode `IQueryable<T> <paramName>`. Dans Script C#, `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. `T` doit être généralement un type qui implémente `ITableEntity` ou est dérivé de `TableEntity`. Vous pouvez utiliser les méthodes `IQueryable` pour effectuer le filtrage voulu. Les propriétés `partitionKey`, `rowKey`, `filter` et `take` ne sont pas utilisées dans ce scénario.  

  > [!NOTE]
  > `IQueryable` n’est pas pris en charge dans le [runtime Functions v2](functions-versions.md). Une alternative consiste à utiliser un [paramètre de méthode CloudTable paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) pour lire la table à l’aide du SDK Stockage Azure. Si vous essayez de lier à `CloudTable` et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

* **Lire une ligne dans**

  Définissez `partitionKey` et `rowKey`. Accédez aux données de la table à l’aide d’un paramètre de méthode `T <paramName>`. Dans Script C#, `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. `T` est généralement un type qui implémente `ITableEntity` ou est dérivé de `TableEntity`. Les propriétés `filter` et `take` ne sont pas utilisées dans ce scénario.

* **Lire une ou plusieurs lignes**

  Accédez aux données de la table à l’aide d’un paramètre de méthode `IQueryable<T> <paramName>`. Dans Script C#, `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. `T` doit être généralement un type qui implémente `ITableEntity` ou est dérivé de `TableEntity`. Vous pouvez utiliser les méthodes `IQueryable` pour effectuer le filtrage voulu. Les propriétés `partitionKey`, `rowKey`, `filter` et `take` ne sont pas utilisées dans ce scénario.  

  > [!NOTE]
  > `IQueryable` n’est pas pris en charge dans le [runtime Functions v2](functions-versions.md). Une alternative consiste à utiliser un [paramètre de méthode CloudTable paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) pour lire la table à l’aide du SDK Stockage Azure. Si vous essayez de lier à `CloudTable` et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Définissez les propriétés `filter` et `take`. Ne définissez pas `partitionKey` ni `rowKey`. Accédez à l’entité (ou les entités) de table d’entrée à l’aide de `context.bindings.<BINDING_NAME>`. Les objets désérialisés ont des propriétés `RowKey` et `PartitionKey`.

# <a name="python"></a>[Python](#tab/python)

Les données de table sont passées à la fonction sous la forme d’une chaîne JSON. Désérialisez le message en appelant `json.loads` comme dans l’[exemple](#input) d’entrée.

# <a name="java"></a>[Java](#tab/java)

L’attribut [TableInput](/java/api/com.microsoft.azure.functions.annotation.tableinput) vous donne accès à la ligne de table qui ayant déclenché la fonction.

---

## <a name="output"></a>Output

Utilisez une liaison de sortie de stockage de table Azure pour écrire des entités dans une table d’un compte de Stockage Azure.

> [!NOTE]
> Cette liaison de sortie ne prend pas en charge la mise à jour d’entités existantes. Utilisez l’opération `TableOperation.Replace`[à partir du Kit de développement logiciel (SDK) Stockage Azure](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) pour mettre à jour une entité existante.

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

L’exemple suivant montre comment utiliser la liaison de sortie de stockage de table. La liaison `table` est configurée dans le fichier *function.json* en assignant des valeurs à `name`, `tableName`, `partitionKey`et `connection` :

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

La fonction suivante génère un UUI unique pour la valeur `rowKey` et conserve le message dans le stockage de table.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

# <a name="java"></a>[Java](#tab/java)

L’exemple suivant illustre une fonction Java utilisant un déclencheur HTTP pour écrire une seule ligne de table.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

L’exemple suivant illustre une fonction Java utilisant un déclencheur HTTP pour écrire plusieurs lignes de table.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

---

## <a name="output---attributes-and-annotations"></a>Sortie – Attributs et annotations

# <a name="c"></a>[C#](#tab/csharp)

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Le constructeur de l’attribut prend le nom de la table. L’attribut peut être utilisé sur un paramètre `out` ou sur la valeur de retour de la fonction, comme dans l’exemple suivant :

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

Pour obtenir un exemple complet, consultez [Sortie - exemple C#](#output).

Vous pouvez utiliser l’attribut `StorageAccount` pour spécifier le compte de stockage au niveau de la classe, de la méthode ou du paramètre. Pour plus d’informations, consultez [Entrée - attributs](#input---attributes-and-annotations).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Les attributs ne sont pas pris en charge par le script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Les attributs ne sont pas pris en charge par JavaScript.

# <a name="python"></a>[Python](#tab/python)

Les attributs ne sont pas pris en charge par Python.

# <a name="java"></a>[Java](#tab/java)

Dans la [bibliothèque runtime de fonctions Java](/java/api/overview/azure/functions/runtime), utilisez l’annotation [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) sur les paramètres pour écrire des valeurs dans le stockage de table.

Voir l’[exemple pour plus de détails](#output).

---

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
|**connection** |**Connection** | Nom d’un paramètre d’application comportant la chaîne de connexion de stockage à utiliser pour cette liaison. Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom ici. Par exemple, si vous définissez `connection` sur « MonStockage », le runtime Functions recherche un paramètre d’application qui nommé « MonStockage ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion de stockage par défaut dans le paramètre d’application nommé `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Sortie - utilisation

# <a name="c"></a>[C#](#tab/csharp)

Accédez à l’entité de table de sortie à l’aide d’un paramètre de méthode `ICollector<T> paramName` ou `IAsyncCollector<T> paramName`, où `T` contient les propriétés `PartitionKey` et `RowKey`. Ces propriétés sont souvent accompagnées de l’implémentation de `ITableEntity` ou de l’héritage de `TableEntity`.

Une autre solution consiste à utiliser un paramètre de méthode `CloudTable` pour écrire dans la table en utilisant le Kit de développement logiciel (SDK) Stockage Azure. Si vous essayez de lier à `CloudTable` et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Accédez à l’entité de table de sortie à l’aide d’un paramètre de méthode `ICollector<T> paramName` ou `IAsyncCollector<T> paramName`, où `T` contient les propriétés `PartitionKey` et `RowKey`. Ces propriétés sont souvent accompagnées de l’implémentation de `ITableEntity` ou de l’héritage de `TableEntity`. La valeur `paramName` est spécifiée dans la propriété `name` de *function.json*.

Une autre solution consiste à utiliser un paramètre de méthode `CloudTable` pour écrire dans la table en utilisant le Kit de développement logiciel (SDK) Stockage Azure. Si vous essayez de lier à `CloudTable` et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Accédez à l’événement de sortie à l’aide de `context.bindings.<name>` où `<name>` est la valeur spécifiée dans la propriété `name` de *function.json*.

# <a name="python"></a>[Python](#tab/python)

Il existe deux options pour générer en sortie un message de ligne de stockage de table à partir d’une fonction :

- **Valeur de retour** : Définissez la propriété `name` dans *function.json* sur `$return`. Avec cette configuration, la valeur de retour de la fonction est conservée en tant que ligne de stockage de table.

- **Impératif** : Passez une valeur à la méthode [set](/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) du paramètre déclaré en tant que type [Out](/python/api/azure-functions/azure.functions.out?view=azure-python). La valeur transmise à `set` est conservée en tant que message d’Event Hub.

# <a name="java"></a>[Java](#tab/java)

Il existe deux options pour produire en sortie une ligne de stockage de table à partir d’une fonction en utilisant l’annotation [TableStorageOutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet) :

- **Valeur de retour** : En appliquant l’annotation à la fonction elle-même, la valeur de retour de la fonction est conservée sous la forme d’une ligne de stockage de table.

- **Impératif** : Pour définir explicitement la valeur du message, appliquez l’annotation à un paramètre spécifique du type [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding), où `T` inclut les propriétés `PartitionKey` et `RowKey`. Ces propriétés sont souvent accompagnées de l’implémentation de `ITableEntity` ou de l’héritage de `TableEntity`.

---

## <a name="exceptions-and-return-codes"></a>Exceptions et codes de retour

| Liaison | Informations de référence |
|---|---|
| Table de charge de travail | [Codes d’erreur de table](/rest/api/storageservices/fileservices/table-service-error-codes) |
| Objet blob, Table, File d’attente | [Codes d’erreur de stockage](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objet blob, Table, File d’attente | [Dépannage](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
