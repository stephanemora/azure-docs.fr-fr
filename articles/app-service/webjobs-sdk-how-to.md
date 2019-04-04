---
title: Comment utiliser le Kit de développement logiciel (SDK) WebJobs - Azure
description: Découvrez comment écrire du code pour le Kit de développement logiciel (SDK) WebJobs. Créer des travaux de traitement qui accèdent aux données dans les services Azure et des services tiers arrière-plan pilotée par événements.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 38d8bdfcba48d2080b434ebec192b41f3663ae6a
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895205"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Comment utiliser le Kit de développement logiciel (SDK) Azure WebJobs pour le traitement en arrière-plan basé sur les événements

Cet article fournit des conseils sur la façon de travailler avec le SDK Azure WebJobs. Pour commencer avec les tâches Web tout de suite, consultez [bien démarrer avec le SDK Azure WebJobs pour le traitement d’arrière-plan pilotée par événements](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Versions du SDK WebJobs

Voici les principales différences entre la version 3. *x* et la version 2. *x* du SDK WebJobs :

* Version 3. *x* ajoute la prise en charge de .NET Core.
* Dans la version 3. *x*, vous devez explicitement installer l’extension de liaison de stockage requise par le SDK WebJobs. Dans la version 2. *x*, les liaisons de stockage ont été inclus dans le Kit de développement.
* Outils pour .NET Core Visual Studio (3. *x*) projets diffère des outils pour .NET Framework (2. *x*) projets. Pour plus d’informations, consultez [développer et déployer des tâches Web à l’aide de Visual Studio - Azure App Service](webjobs-dotnet-deploy-vs.md).

Dans la mesure du possible, les exemples sont fournis pour version 3. *x* et la version 2. *x*.

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) repose sur le SDK WebJobs, cet article fournit des liens vers la documentation d’Azure Functions pour certaines rubriques. Notez ces différences entre les fonctions et le SDK WebJobs :
> * Azure Functions version 2. *x* correspond au SDK WebJobs version 3. *x*et Azure Functions 1. *x* correspond à 2 du Kit de développement logiciel WebJobs. *x*. Référentiels de code source utilisent le SDK WebJobs numérotation.
> * Exemples de code pour Azure Functions C# bibliothèques de classes est comme le code du SDK WebJobs, sauf que vous n’avez pas besoin un `FunctionName` attribut dans un projet de SDK WebJobs.
> * Certains types de liaison sont pris en charge uniquement dans les fonctions, telles que HTTP (Webhooks) et Event Grid (qui est basé sur HTTP).
>
> Pour plus d’informations, consultez [Comparer Functions et WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Hôte de WebJobs

L’hôte est un conteneur d’exécution pour les fonctions.  Il écoute les déclencheurs et appelle les fonctions. Dans la version 3. *x*, l’hôte est une implémentation de `IHost`. Dans la version 2. *x*, vous utilisez le `JobHost` objet. Vous créez une instance d’hôte dans votre code et écrivez du code pour personnaliser son comportement.

Il s’agit d’une différence essentielle entre à l’aide du SDK WebJobs directement et indirectement à l’aide avec Azure Functions. Dans Azure Functions, le service de contrôle de l’hôte, et vous ne pouvez pas personnaliser l’ordinateur hôte en écrivant du code. Azure Functions vous permet de personnaliser le comportement d’hôte via des paramètres dans le fichier host.json. Ces paramètres sont des chaînes, pas de code, et ce qui limite les types de personnalisations que vous pouvez effectuer.

### <a name="host-connection-strings"></a>Chaînes de connexion de l’hôte

Le SDK WebJobs recherche les chaînes de connexion stockage Azure et Azure Service Bus dans le fichier local.settings.json lorsque vous exécutez localement ou dans l’environnement de la tâche Web lorsque vous exécutez dans Azure. Par défaut, une connexion de stockage chaîne de paramètre nommé `AzureWebJobsStorage` est requis.  

Version 2. *x* du SDK vous permet d’utiliser vos propres noms de ces chaînes de connexion ou les stocker ailleurs. Vous pouvez définir des noms dans le code à l’aide du [ `JobHostConfiguration` ], comme illustré ici :

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Étant donné que la version 3. *x* configuration par défaut .NET Core API, il n’existe aucune API pour modifier les noms de chaîne de connexion.

### <a name="host-development-settings"></a>Paramètres de développement de l’hôte

Vous pouvez exécuter l’hôte en mode de développement pour améliorer l’efficacité du développement local. Voici les paramètres qui sont modifiés lorsque vous exécutez en mode de développement :

| Propriété | Paramètre de développement |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` Pour optimiser la sortie du journal. |
| `Queues.MaxPollingInterval`  | Une valeur faible pour garantir le déclenchement immédiat des méthodes de file d’attente.  |
| `Singleton.ListenerLockPeriod` | 15 secondes pour favoriser le développement itératif rapide. |

Le processus d’activation du mode de développement dépend de la version SDK. 

#### <a name="version-3x"></a>Version 3. *x*

Version 3. *x* utilise les API de Core ASP.NET standard. Appelez le [ `UseEnvironment` ](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) méthode sur le [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instance. Passer une chaîne nommée `development`, comme dans cet exemple :

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Version 2.*x*

La classe `JobHostConfiguration` dispose d’une méthode `UseDevelopmentSettings` qui active le mode de développement.  L’exemple suivant montre comment utiliser les paramètres de développement. Pour rendre `config.IsDevelopment` retourner `true` lorsqu’il s’exécute localement, définissez une variable d’environnement local nommée `AzureWebJobsEnv` avec la valeur `Development`.

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-servicepointmanager-settings"></a>Gestion des connexions simultanées (version 2. *x*)

Dans la version 3. *x*, la limite de connexion par défaut est infinies connexions. Si pour une raison quelconque, vous devez modifier cette limite, vous pouvez utiliser la [ `MaxConnectionsPerServer` ](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) propriété de la [ `WinHttpHandler` ](/dotnet/api/system.net.http.winhttphandler) classe.

Dans la version 2. *x*, vous contrôler le nombre de connexions simultanées à un ordinateur hôte à l’aide de la [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API. 2. *x*, vous devez augmenter cette valeur à partir de la valeur par défaut de 2 avant de commencer votre hôte de tâches Web.

Les requêtes HTTP sortantes que vous apportez à partir d’une fonction à l’aide de `HttpClient` circuler `ServicePointManager`. Après avoir accédé à la valeur définie dans `DefaultConnectionLimit`, `ServicePointManager` démarre les demandes de file d’attente avant de les envoyer. Supposons que votre limite `DefaultConnectionLimit` est définie sur 2 et que votre code effectue 1 000 requêtes HTTP. Au départ, seulement deux requêtes sont autorisées à transiter jusqu’au système d’exploitation. Les 998 autres sont mises en file d’attente jusqu’à ce que de la place se libère pour elles. Cela signifie que votre `HttpClient` peut expirer parce qu’elle semble avoir effectué la demande, mais la requête n’est jamais envoyée par le système d’exploitation pour le serveur de destination. Par conséquent, il se peut que vous observiez un comportement qui semble illogique : votre `HttpClient` local met 10 secondes pour effectuer une requête, mais votre service renvoie chaque requête en 200 ms. 

La valeur par défaut pour les applications ASP.NET est `Int32.MaxValue`, et qui est susceptible de fonctionnent bien pour les tâches Web en cours d’exécution dans une base ou supérieur Plan App Service. WebJobs généralement besoin de ce paramètre Always On, et qui est pris en charge uniquement par la base et versions ultérieures Plans App Service.

Si votre tâche web est exécutée dans un plan App Service Gratuit ou Partagé, votre application est limitée par le bac à sable App Service, qui présente actuellement une [limite de connexion de 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Avec une limite de connexion indépendant dans `ServicePointManager`, il est plus probable que le seuil de connexion de bac à sable sera atteinte et le site s’arrête. Dans ce cas, le fait de définir `DefaultConnectionLimit` sur une valeur inférieure, par exemple 50 ou 100, peut empêcher ce problème tout en assurant un débit suffisant.

Le paramètre doit être configuré avant d’effectuer toute requête HTTP. Pour cette raison, l’hôte de WebJobs ne doit pas ajuster le paramètre automatiquement. Il existe peut-être des requêtes HTTP qui se produisent avant le démarrage de l’hôte, ce qui peut entraîner un comportement inattendu. La meilleure approche consiste à définir la valeur immédiatement dans votre `Main` méthode avant d’initialiser `JobHost`, comme illustré ici :

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Déclencheurs

Fonctions doivent être des méthodes publiques et doit avoir un attribut de déclencheur ou la [ `NoAutomaticTrigger` ](#manual-triggers) attribut.

### <a name="automatic-triggers"></a>Déclencheurs automatiques

Les déclencheurs automatiques appellent une fonction en réponse à un événement. Prenons l’exemple suivant d’une fonction qui est déclenchée par un message ajouté au stockage de file d’attente Azure. Il répond en lisant un objet blob à partir du stockage d’objets Blob Azure :

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Le `QueueTrigger` attribut indique au runtime d’appeler la fonction chaque fois qu’un message de file d’attente s’affiche dans le `myqueue-items` file d’attente. Le `Blob` attribut indique au runtime d’utiliser le message de file d’attente pour lire un objet blob dans le *exemple-workitems* conteneur. Le contenu du message de file d’attente, passé à la fonction dans le `myQueueItem` paramètre, est le nom de l’objet blob.


### <a name="manual-triggers"></a>Déclencheurs manuels

Pour déclencher manuellement une fonction, utilisez le `NoAutomaticTrigger` d’attribut, comme illustré ici :

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

Le processus pour déclencher manuellement la fonction dépend de la version SDK.

#### <a name="version-3x"></a>Version 3. *x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2.*x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Liaisons d’entrée et de sortie

Les liaisons d’entrée fournissent une méthode déclarative pour rendre disponibles les données des services Azure ou tiers pour votre code. Les liaisons de sortie offrent un moyen de mettre à jour des données. Le [prise en main](webjobs-sdk-get-started.md) article montre un exemple de chacun d’eux.

Vous pouvez utiliser une valeur de retour de méthode pour une liaison de sortie en appliquant l’attribut à la valeur de retour de méthode. Consultez l’exemple dans [valeur de retour à l’aide de la fonction Azure](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Types de liaisons

Le processus d’installation et la gestion des types de liaison dépend de si vous utilisez la version 3. *x* ou version 2. *x* du SDK. Vous pouvez trouver le package d’installation pour un type de liaison particulière dans la section « Packages » de Azure Functions de ce type de liaison [article de référence](#binding-reference-information). Une exception est le déclencheur de fichiers et de liaison (pour le système de fichiers local), ce qui n’est pas pris en charge par Azure Functions.

#### <a name="version-3x"></a>Version 3. *x*

Dans la version 3. *x*, les liaisons de stockage sont inclus dans le `Microsoft.Azure.WebJobs.Extensions.Storage` package. Appelez le `AddAzureStorage` méthode d’extension dans le `ConfigureWebJobs` (méthode), comme illustré ici :

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

Pour utiliser d’autres types de liaisons et de déclencheurs, installez le package NuGet qui les contient et appelez la méthode d’extension `Add<binding>` implémentée dans l’extension. Par exemple, si vous souhaitez utiliser une liaison Azure Cosmos DB, installer `Microsoft.Azure.WebJobs.Extensions.CosmosDB` et appelez `AddCosmosDB`, comme suit :

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

Pour utiliser le déclencheur de minuteur ou la liaison Files, qui font partie des services principaux, appelez les méthodes d’extension `AddTimers` ou `AddFiles`, respectivement.

#### <a name="version-2x"></a>Version 2.*x*

Ces types de déclencheur et de liaison sont inclus dans la version 2. *x* de la `Microsoft.Azure.WebJobs` package :

* Stockage d'objets blob
* Stockage de files d'attente
* Stockage de tables

Pour utiliser d’autres types de liaisons et de déclencheurs, installez le package NuGet qui les contient et appelez une méthode `Use<binding>` sur l’objet `JobHostConfiguration`. Par exemple, si vous souhaitez utiliser un déclencheur de minuteur, installer `Microsoft.Azure.WebJobs.Extensions` et appelez `UseTimers` dans le `Main` (méthode), comme illustré ici :

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Pour utiliser la liaison Files, installez `Microsoft.Azure.WebJobs.Extensions` et appelez `UseFiles`.

### <a name="executioncontext"></a>ExecutionContext

WebJobs vous permet de lier à [`ExecutionContext`]. Avec cette liaison, vous pouvez accéder à [`ExecutionContext`] en tant que paramètre dans votre signature de fonction. Par exemple, le code suivant utilise l’objet de contexte pour accéder à l’ID d’appel, ce que vous pouvez utiliser pour mettre en corrélation tous les journaux produits par l’appel d’une fonction donnée.  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

Le processus de liaison à la [ `ExecutionContext` ] dépend de votre version de kit de développement logiciel.

#### <a name="version-3x"></a>Version 3. *x*

Appelez le `AddExecutionContextBinding` méthode d’extension dans le `ConfigureWebJobs` (méthode), comme illustré ici :

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Version 2.*x*

Le package `Microsoft.Azure.WebJobs.Extensions` mentionné précédemment fournit également un type de liaison spécial que vous pouvez enregistrer en appelant la méthode `UseCore`. Cette liaison vous permet de définir un [ `ExecutionContext` ] paramètre dans la signature de fonction, qui est activée comme suit :

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>Configuration de liaison

Vous pouvez configurer le comportement de certains déclencheurs et des liaisons. Le processus de configuration dépend de la version SDK.

* **Version 3. *x*:** Définir la configuration lorsque le `Add<Binding>` méthode est appelée dans `ConfigureWebJobs`.
* **Version 2. *x*:** Configuration du jeu en définissant des propriétés dans un objet de configuration que vous passez à `JobHost`.

Ces paramètres spécifiques d’une liaison sont équivalents aux paramètres dans le [fichier de projet host.json](../azure-functions/functions-host-json.md) dans Azure Functions.

Vous pouvez configurer les liaisons suivantes :

* [Déclencheur COSMOS DB Azure](#azure-cosmosdb-trigger-configuration-version-3x)
* [Déclencheur Event Hubs](#event-hubs-trigger-configuration-version-3x)
* Déclencheur de stockage de file d’attente
* [Liaison SendGrid](#sendgrid-binding-configuration-version-3x)
* [Déclencheur Service Bus](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Configuration du déclencheur Azure COSMOS DB (version 3. *x*)

Cet exemple montre comment configurer le déclencheur Azure Cosmos DB :

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Pour plus d’informations, consultez le [Azure CosmosDB liaison](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) article.

### <a name="event-hubs-trigger-configuration-version-3x"></a>Configuration de déclencheur Event Hubs (version 3. *x*)

Cet exemple montre comment configurer le déclencheur Event Hubs :

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Pour plus d’informations, consultez le [liaison de concentrateurs d’événements](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) article.

### <a name="queue-storage-trigger-configuration"></a>Configuration du déclencheur stockage file d’attente

Ces exemples montrent comment configurer le déclencheur de stockage de file d’attente :

#### <a name="version-3x"></a>Version 3. *x*

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Pour plus d’informations, consultez le [file d’attente de liaison de stockage](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings) article.

#### <a name="version-2x"></a>Version 2.*x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Pour plus d’informations, consultez le [host.json version 1.x référence](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Configuration de liaison SendGrid (version 3. *x*)

Cet exemple montre comment configurer SendGrid liaison de sortie :

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Pour plus d’informations, consultez le [liaison SendGrid](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) article.

### <a name="service-bus-trigger-configuration-version-3x"></a>Configuration du déclencheur Service Bus (version 3. *x*)

Cet exemple montre comment configurer le déclencheur Service Bus :

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Pour plus d’informations, consultez le [liaison de Service Bus](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) article.

### <a name="configuration-for-other-bindings"></a>Configuration pour les autres liaisons

Certains types de déclencheur et la liaison de définissent leurs propres types de configuration personnalisée. Par exemple, le déclencheur de fichier vous permet de spécifier le chemin d’accès racine à surveiller, comme dans les exemples :

#### <a name="version-3x"></a>Version 3. *x*

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

#### <a name="version-2x"></a>Version 2.*x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>Expressions de liaison

Dans les paramètres de constructeur d’attribut, vous pouvez utiliser des expressions qui sont remplacées par des valeurs provenant de diverses sources. Par exemple, dans le code suivant, le chemin d’accès pour l’attribut `BlobTrigger` crée une expression nommée `filename`. Lorsqu’elle est utilisée pour la liaison de sortie, l’expression `filename` est remplacée par le nom de l’objet blob déclencheur.

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

Pour plus d’informations sur les expressions de liaison, consultez [Modèles et expressions de liaison](../azure-functions/functions-bindings-expressions-patterns.md) dans la documentation Azure Functions.

### <a name="custom-binding-expressions"></a>Expressions de liaison personnalisées

Parfois, vous souhaitez spécifier un nom de file d’attente, un nom d’objet blob ou conteneur ou un nom de table dans le code au lieu de coder en dur. Par exemple, il se peut que vous souhaitiez spécifier le nom de la file d’attente de l’attribut `QueueTrigger` dans une variable d’environnement ou un fichier de configuration.

Vous pouvez le faire en passant un `NameResolver` objet dans le `JobHostConfiguration` objet. Incluez des espaces réservés dans les paramètres de constructeur d’attribut pour que le code de votre objet `NameResolver` fournisse les valeurs réelles à utiliser à la place de ces espaces réservés. Identifier les espaces réservés entourés de pourcentage (%) signes, comme illustré ici :

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Ce code vous permet d’utiliser une file d’attente nommée `logqueuetest` dans l’environnement de test, et une autre nommée `logqueueprod` en production. Au lieu d’un nom de file d’attente codé en dur, vous spécifiez le nom d’une entrée dans la collection `appSettings`.

Il existe une valeur par défaut `NameResolver` qui entre en vigueur si vous ne fournissez pas un personnalisé. L’objet par défaut obtient les valeurs à partir des paramètres d’application ou des variables d’environnement.

Votre `NameResolver` classe obtient le nom de la file d’attente à partir de `appSettings`, comme illustré ici :

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Version 3. *x*

Vous configurez le programme de résolution à l’aide de l’injection de dépendances. Ces exemples nécessitent l’instruction `using` suivante :

```cs
using Microsoft.Extensions.DependencyInjection;
```

Vous ajoutez le programme de résolution en appelant le [ `ConfigureServices` ] méthode d’extension sur [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder), comme dans cet exemple :

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2.*x*

Transmettre votre `NameResolver` classe dans le `JobHost` de l’objet, comme illustré ici :

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions implémente `INameResolver` pour obtenir les valeurs à partir des paramètres d’application, comme illustré dans l’exemple. Quand vous utilisez le Kit de développement logiciel (SDK) WebJobs directement, vous pouvez écrire une implémentation personnalisée qui obtient les valeurs de remplacement des espaces réservés à partir de la source de votre choix.

## <a name="binding-at-runtime"></a>Liaison au runtime

Si vous avez besoin d’effectuer une tâche dans votre fonction avant d’utiliser un attribut de liaison comme `Queue`, `Blob`, ou `Table`, vous pouvez utiliser le `IBinder` interface.

L’exemple suivant prend un message en file d’attente d’entrée et crée un message avec le même contenu dans une file d’attente de sortie. Le nom de file d’attente de sortie est défini par le code dans le corps de la fonction.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

Pour plus d’informations, consultez [Liaison au runtime](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) dans la documentation Azure Functions.

## <a name="binding-reference-information"></a>Informations de référence sur les liaisons

La documentation d’Azure Functions fournit des informations de référence sur chaque type de liaison. Vous trouverez les informations suivantes dans chaque article de référence de liaison. (Cet exemple est basé sur la file d’attente de stockage).

* [Packages](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x). Le package, que vous devez installer pour prendre en charge la liaison dans un projet de SDK WebJobs.
* [Exemples](../azure-functions/functions-bindings-storage-queue.md#trigger---example). Exemples de code. Le C# exemple de bibliothèque de classes s’applique au SDK WebJobs. Omettez simplement le `FunctionName` attribut.
* [Attributs](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes). Les attributs à utiliser pour le type de liaison.
* [Configuration](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration). Obtenir une explication sur les propriétés de l’attribut et les paramètres du constructeur.
* [Utilisation](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Vous pouvez lier à des types et les informations sur le fonctionne de la liaison. Exemple : algorithme d’interrogation, traitement de file d’attente de messages incohérents.
  
Pour obtenir la liste des articles de référence de liaison, consultez « Liaisons prises en charge » dans le [déclencheurs et liaisons](../azure-functions/functions-triggers-bindings.md#supported-bindings) article pour Azure Functions. Dans cette liste, les liaisons HTTP, Webhooks et Event Grid sont pris en charge uniquement par Azure Functions, pas par le SDK WebJobs.

## <a name="disable-attribute"></a>Attribut Disable 

Le [ `Disable` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) attribut vous permet de contrôler si une fonction peut être déclenchée. 

Dans l’exemple suivant, si le paramètre d’application `Disable_TestJob` a la valeur `1` ou `True` (sans respect de la casse), la fonction ne s’exécute pas. Dans ce cas, le runtime crée le message de journal *Function ’Functions.TestJob’ is disabled* (La fonction « Functions.TestJob » est désactivée).

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Lorsque vous modifiez les valeurs de paramètre d’application dans le portail Azure, la tâche Web redémarre pour récupérer le nouveau paramètre.

L’attribut peut être déclaré au niveau du paramètre, de la méthode ou de la classe. Le nom du paramètre peut également contenir des expressions de liaison.

## <a name="timeout-attribute"></a>Attribut Timeout

Le [ `Timeout` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) attribut entraîne une fonction soit annulée si elle ne se termine pas dans un laps de temps. Dans l’exemple suivant, la fonction s’exécute pendant une journée, sans l’attribut de délai d’attente. Délai d’attente, la fonction doit être annulée après 15 secondes.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

Vous pouvez appliquer l’attribut de délai d’expiration au niveau de la classe ou la méthode, et vous pouvez spécifier un délai d’expiration global à l’aide de `JobHostConfiguration.FunctionTimeout`. Délais d’expiration de niveau classe ou au niveau de la méthode remplacent les délais d’expiration globaux.

## <a name="singleton-attribute"></a>Attribut Singleton

Le [ `Singleton` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) attribut permet de s’assurer que seule une instance d’une fonction s’exécute, même quand il existe plusieurs instances de l’application web hôte. Pour ce faire à l’aide de [distributed verrouillage](#viewing-lease-blobs).

Dans cet exemple, une seule instance de la `ProcessImage` fonction s’exécute à un moment donné :

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Certains déclencheurs intègrent la prise en charge de la gestion de l’accès concurrentiel :

* **QueueTrigger**. Réglez `JobHostConfiguration.Queues.BatchSize` sur `1`.
* **ServiceBusTrigger**. Réglez `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` sur `1`.
* **FileTrigger**. Réglez `FileProcessor.MaxDegreeOfParallelism` sur `1`.

Vous pouvez utiliser ces paramètres pour vous assurer que votre fonction s’exécute en tant que singleton sur une instance unique. Pour vous assurer qu’une seule instance de la fonction est en cours d’exécution lors de l’application web est étendue à plusieurs instances, appliquer un verrou au niveau de l’écouteur singleton sur la fonction (`[Singleton(Mode = SingletonMode.Listener)]`). Écouteur sont des verrous lorsque le JobHost démarre. Si trois instances scale-out démarrent en même temps, une seule de ces instances acquiert le verrou et un seul écouteur démarre.

### <a name="scope-values"></a>Valeurs d’étendue

Vous pouvez spécifier un *expression/valeur de portée* sur un singleton. La valeur d’expression/garantit que toutes les exécutions de la fonction dans une étendue spécifique seront sérialisées. Implémentation d’un verrouillage plus précis de cette façon permet pour un certain degré de parallélisme pour votre fonction lors de la sérialisation d’autres appels, selon vos besoins. Par exemple, dans le code suivant, l’expression d’étendue lie à la `Region` valeur du message entrant. Lorsqu’elle est la file d’attente contient trois messages dans les régions est, East et West respectivement, les messages qui ont la région est des États-Unis sont exécutés en série lorsque le message avec la région Qu'ouest est exécutée en parallèle avec ceux de l’est des États-Unis.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

L’étendue par défaut pour un verrou est `SingletonScope.Function`, ce qui signifie que la portée du verrou (le chemin de bail blob) est lié au nom de fonction qualifié complet. Pour verrouiller de fonctions, spécifiez `SingletonScope.Host` et utiliser un nom d’ID de portée qui est le même pour toutes les fonctions que vous ne souhaitez pas exécuter simultanément. Dans l’exemple suivant, seule une instance de `AddItem` ou `RemoveItem` est exécutée à la fois :

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>Affichage des baux d’objets blob

Le Kit de développement logiciel (SDK) WebJobs utilise des [baux d’objets blob Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) en arrière-plan pour implémenter le verrouillage distribué. Vous trouverez les objets BLOB de bail utilisés par un Singleton dans le `azure-webjobs-host` conteneur dans le `AzureWebJobsStorage` compte de stockage sous le chemin d’accès « verrous ». Par exemple, le chemin d’accès du bail d’objet blob pour le premier exemple `ProcessImage` illustré précédemment peut être `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Tous les chemins d’accès incluent l’ID JobHost, dans le cas présent 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Fonctions asynchrones

Pour plus d’informations sur la façon de fonctions asynchrones de code, consultez le [documentation Azure Functions](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Jetons d’annulation

Pour plus d’informations sur la gestion des jetons d’annulation, consultez la documentation Azure Functions sur [les jetons d’annulation et l’arrêt approprié](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Instances multiples

Si votre application web s’exécute sur plusieurs instances, une tâche web continue se lance sur chaque instance, écoutant les déclencheurs et appelant les fonctions. Les différentes liaisons de déclencheur sont conçues pour partager efficacement le travail de façon collaborative entre les instances afin que l’extension à d’autres instances vous permette de gérer une charge plus importante.

Les déclencheurs de file d’attente et objets blob automatiquement empêchent une fonction de traitement d’un message de file d’attente ou d’objets blob plusieurs fois ; fonctions n’êtes pas obligé d’être idempotentes.

Le déclencheur timer garantit automatiquement l’exécution d’une seule instance du minuteur de sorte qu’une instance unique de la fonction soit exécutée à une heure planifiée donnée.

Si vous souhaitez vous assurer que seule une instance d’une fonction s’exécute même quand il existe plusieurs instances de l’application web hôte, vous pouvez utiliser la [ `Singleton` ](#singleton-attribute) attribut.

## <a name="filters"></a>Filtres

Les filtres de fonctions (préversion) vous offrent un moyen de personnaliser le pipeline d’exécution de tâches web avec votre propre logique. Les filtres sont similaires aux [filtres ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Vous pouvez les implémenter en tant qu’attributs déclaratifs qui sont appliqués à vos fonctions ou les classes. Pour plus d’informations, consultez [Function Filters](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters) (Filtres de fonctions).

## <a name="logging-and-monitoring"></a>Enregistrement et surveillance

Nous vous recommandons du framework de journalisation qui a été développé pour ASP.NET. Le [prise en main](webjobs-sdk-get-started.md) article explique comment l’utiliser. 

### <a name="log-filtering"></a>Filtrage de journal

Chaque journal créé par une instance `ILogger` présente des attributs `Category` et `Level` associés. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) est une énumération, et le code d’entier indique l’importance relative :

|LogLevel    |Code|
|------------|---|
|Trace       | 0 |
|Déboguer       | 1 |
|Information | 2 |
|Avertissement     | 3 |
|Error       | 4 |
|Critique    | 5. |
|Aucun        | 6. |

Vous pouvez filtrer indépendamment de chaque catégorie pour un particulier [ `LogLevel` ](/dotnet/api/microsoft.extensions.logging.loglevel). Par exemple, il se peut que vous souhaitiez afficher tous les journaux pour le traitement de déclencheurs blob, mais uniquement les niveaux `Error` et plus élevés pour tous les autres éléments.

#### <a name="version-3x"></a>Version 3. *x*

Version 3. *x* du SDK s’appuie sur le filtrage intégré à .NET Core. La classe `LogCategories` vous permet de définir des catégories pour des fonctions, des déclencheurs ou des utilisateurs spécifiques. Il définit également des filtres pour les États de l’ordinateur hôte spécifique, comme `Startup` et `Results`. Cela vous permet de vous permettent d’affiner la sortie de journalisation. Si aucune correspondance n’est trouvée dans les catégories définies, le filtre se replie sur la valeur `Default` pour décider si le message doit être filtré ou non.

`LogCategories` requiert les éléments suivants à l’aide d’instruction :

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

L’exemple suivant crée un filtre qui, par défaut, filtre tous les journaux à le `Warning` niveau. Le `Function` et `results` catégories (équivalent à `Host.Results` dans la version 2. *x*) sont filtrés à le `Error` niveau. Le filtre compare la catégorie actuelle à tous les niveaux enregistrés dans l’instance `LogCategories`, et choisit la correspondance la plus longue. Cela signifie que le `Debug` niveau inscrit pour `Host.Triggers` correspond à `Host.Triggers.Queue` ou `Host.Triggers.Blob`. Cela vous permet de contrôler des catégories plus vastes sans avoir à ajouter chacune d’entre elles.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2.*x*

Dans la version 2. *x* du SDK, vous utilisez la `LogCategoryFilter` classe pour contrôler le filtrage. Le `LogCategoryFilter` a un `Default` propriété avec une valeur initiale de `Information`, ce qui signifie que tous les messages à la `Information`, `Warning`, `Error`, ou `Critical` niveaux sont enregistrés, mais tous les messages à la `Debug` ou `Trace` niveaux sont filtrés.

Comme avec `LogCategories` dans la version 3. *x*, le `CategoryLevels` propriété permet de spécifier les niveaux de journalisation pour les catégories spécifiques afin que vous pouvez affiner la sortie de journalisation. Si aucune correspondance n’est trouvée dans le dictionnaire `CategoryLevels`, le filtre se replie sur la valeur `Default` pour décider si le message doit être filtré ou non.

L’exemple suivant crée un filtre qui filtre par défaut tous les journaux au niveau `Warning`. Le `Function` et `Host.Results` catégories sont filtrés à le `Error` niveau. L’objet `LogCategoryFilter` compare la catégorie actuelle à toutes les propriétés `CategoryLevels` enregistrées et choisit la correspondance la plus longue. Par conséquent, le `Debug` niveau inscrit pour `Host.Triggers` correspondra `Host.Triggers.Queue` ou `Host.Triggers.Blob`. Cela vous permet de contrôler des catégories plus vastes sans avoir à ajouter chacune d’entre elles.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Télémétrie personnalisée pour Application Insights​

Le processus d’implémentation des données de télémétrie personnalisées pour [Application Insights](../azure-monitor/app/app-insights-overview.md) dépend de la version SDK. Pour savoir comment configurer Application Insights, consultez [Ajouter la journalisation Application Insights](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Version 3. *x*

Étant donné que la version 3. *x* du SDK WebJobs s’appuie sur le .NET Core hôte générique, une fabrique de données de télémétrie personnalisées n’est plus disponible. Mais vous pouvez ajouter des données de télémétrie personnalisées au pipeline à l’aide de l’injection de dépendances. Les exemples de cette section nécessitent les instructions `using` suivantes :

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

L’implémentation personnalisée suivante de [`ITelemetryInitializer`] vous permet d’ajouter votre propre [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) à la valeur par défaut de [`TelemetryConfiguration`].

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Appelez [`ConfigureServices`] dans le générateur pour ajouter votre [`ITelemetryInitializer`] personnalisé au pipeline.

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Lorsque [`TelemetryConfiguration`] est construit, tous les types enregistrés de [`ITelemetryInitializer`] sont inclus. Pour plus d’informations, consultez [API Application Insights pour les événements et mesures personnalisés](../azure-monitor/app/api-custom-events-metrics.md).

Dans la version 3. *x*, vous n’avez plus à vider le [ `TelemetryClient` ] lorsque l’hôte s’arrête. Le système d’injection de dépendance de .NET Core supprime automatiquement l’élément `ApplicationInsightsLoggerProvider` enregistré, ce qui vide [`TelemetryClient`].

#### <a name="version-2x"></a>Version 2.*x*

Dans la version 2. *x*, le [ `TelemetryClient` ] créés en interne par le fournisseur d’Application Insights pour le SDK WebJobs utilise [ `ServerTelemetryChannel` ](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Quand le point de terminaison d’Application Insights est non disponible ou limite les requêtes entrantes, ce canal [enregistre les requêtes dans le système de fichiers de l’application web et les soumet à nouveau ultérieurement](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

[`TelemetryClient`] est créé par une classe qui implémente `ITelemetryClientFactory`. Par défaut, il s’agit de [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Si vous souhaitez modifier une partie du pipeline Application Insights, vous pouvez fournir votre propre classe `ITelemetryClientFactory`, qui sera utilisée par l’hôte pour construire un élément [`TelemetryClient`]. Par exemple, ce code remplace `DefaultTelemetryClientFactory` pour modifier une propriété de `ServerTelemetryChannel`:

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

Le `SamplingPercentageEstimatorSettings` objet configure [l’échantillonnage ADAPTATIF](https://docs.microsoft.com/azure/application-insights/app-insights-sampling). Cela signifie que dans certains scénarios à volumes élevés, Applications Insights envoie un sous-ensemble des données de télémétrie sélectionné sur le serveur.

Après avoir créé la fabrique de données de télémétrie, vous passez au fournisseur de journalisation Application Insights :

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Étapes suivantes

Cet article a fourni des extraits de code qui montrent comment gérer des scénarios courants pour travailler avec le SDK WebJobs. Pour obtenir des exemples complets, consultez [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples).

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
