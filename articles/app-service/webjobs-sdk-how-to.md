---
title: Comment utiliser le Kit de développement logiciel (SDK) Azure WebJobs ?
description: Découvrez comment écrire du code pour le Kit de développement logiciel (SDK) WebJobs. Créez des travaux de traitement en arrière-plan basé sur les événements qui accèdent aux données des services Azure et des services tiers.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: glenga
ms.openlocfilehash: 2266f63f9689ec4d22659eb4a7c4876e25fa08b1
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52335212"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Comment utiliser le Kit de développement logiciel (SDK) Azure WebJobs pour le traitement en arrière-plan basé sur les événements

Cet article offre des conseils sur l’écriture de code pour [le Kit de développement logiciel (SDK) Azure WebJobs](webjobs-sdk-get-started.md). La documentation s’applique aux versions 2.x et 3.x, sauf indication contraire. Le principal changement introduit par la version 3.x est l’utilisation de .NET Core au lieu de .NET Framework.

>[!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) repose sur le Kit de développement logiciel (SDK) WebJobs, et cet article comporte des liens vers la documentation Azure Functions pour certaines rubriques. Notez les différences suivantes entre Functions et le Kit de développement logiciel (SDK) WebJobs :
> * Azure Functions version 1.x correspond au Kit de développement logiciel (SDK) WebJobs version 2.x et Azure Functions 2.x au Kit de développement logiciel (SDK) WebJobs version 3.x. Les référentiels du code source suivent la numérotation du Kit de développement logiciel (SDK) WebJobs, et un grand nombre d’entre eux présentent des branches v2.x, la branche maîtresse contenant elle du code 3.x.
> * Un exemple de code pour les bibliothèques de classes C# Azure Functions est semblable au code du Kit de développement logiciel (SDK) WebJobs, à ceci près qu’un projet du Kit de développement logiciel (SDK) WebJobs ne nécessite pas d’attribut `FunctionName`.
> * Certains types de liaisons, comme HTTP, Webhook et Event Grid (liaison basée sur HTTP), sont uniquement pris en charge dans Functions. 
> 
> Pour plus d’informations, consultez [Comparer Functions et WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs). 

## <a name="prerequisites"></a>Prérequis

Cet article suppose la lecture de l’article [Prise en main du SDK WebJobs](webjobs-sdk-get-started.md).

## <a name="jobhost"></a>JobHost

L’objet `JobHost` est le conteneur d’exécution pour les fonctions : il écoute les déclencheurs et appelle les fonctions. Vous devez créer l’objet `JobHost` dans votre code et écrire du code pour personnaliser son comportement.

C’est une différence importante entre l’utilisation directe du Kit de développement logiciel (SDK) WebJobs et son utilisation indirecte à l’aide d’Azure Functions. Dans Azure Functions, le service contrôle l’objet `JobHost`, et vous ne pouvez pas le personnaliser en écrivant du code. Azure Functions vous permet de personnaliser le comportement de l’hôte via les paramètres du fichier *host.json*. Ces paramètres sont des chaînes, et non du code, ce qui limite les types de personnalisations possibles.

### <a name="jobhost-connection-strings"></a>Chaînes de connexion JobHost

Le Kit de développement logiciel (SDK) WebJobs recherche les chaînes de connexion Stockage et Service Bus dans le fichier *local.settings.json* lors d’une exécution locale ou dans l’environnement WebJob lors d’une exécution dans Azure. Si vous souhaitez utiliser vos propres noms pour ces chaînes de connexion ou les stocker ailleurs, vous pouvez les définir dans le code, comme illustré ici :

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-development-settings"></a>Paramètres de développement JobHost

La classe `JobHostConfiguration` présente une méthode `UseDevelopmentSettings` que vous pouvez appeler pour améliorer l’efficacité du développement local. Voici quelques-uns des paramètres que cette méthode modifie :

| Propriété | Paramètre de développement |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` pour optimiser la sortie de la journalisation. |
| `Queues.MaxPollingInterval`  | Une valeur faible pour garantir le déclenchement immédiat des méthodes de file d’attente.  |
| `Singleton.ListenerLockPeriod` | 15 secondes pour favoriser le développement itératif rapide. |

L’exemple suivant montre comment utiliser les paramètres de développement. Pour que `config.IsDevelopment` renvoie `true` lors d’une exécution locale, définissez une variable d’environnement local nommée `AzureWebJobsEnv` avec la valeur `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>Paramètres ServicePointManager JobHost

Le .NET Framework contient une API appelée [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) qui contrôle le nombre de connexions simultanées à un hôte. Nous vous recommandons d’augmenter la valeur par défaut de 2 avant de démarrer votre hôte WebJobs.

Toutes les requêtes HTTP sortantes que vous effectuez à partir d’une fonction à l’aide de `HttpClient` transitent par le `ServicePointManager`. Une fois que vous avez atteint la limite `DefaultConnectionLimit`, le `ServicePointManager` commence à mettre les requêtes en file d’attente avant de les envoyer. Supposons que votre limite `DefaultConnectionLimit` est définie sur 2 et que votre code effectue 1 000 requêtes HTTP. Au départ, seulement 2 requêtes sont autorisées à transiter jusqu’au système d’exploitation. Les 998 autres sont mises en file d’attente jusqu’à ce que de la place se libère pour elles. Votre `HttpClient` risque alors d’expirer, car il *pense* avoir effectué la requête, mais celle-ci n’a jamais été envoyée au serveur de destination par le système d’exploitation. Par conséquent, il se peut que vous observiez un comportement qui semble illogique : votre `HttpClient` local met 10 secondes pour effectuer une requête, mais votre service renvoie chaque requête en 200 ms. 

Pour les applications ASP.NET, la valeur par défaut est `Int32.MaxValue`, une valeur qui a de grandes chances de convenir aux tâches web exécutées dans un plan App Service De base ou supérieur. Les tâches web ont généralement besoin du paramètre Always On setting, qui est uniquement pris en charge par les plans App Service De base et supérieurs. 

Si votre tâche web est exécutée dans un plan App Service Gratuit ou Partagé, votre application est limitée par le bac à sable App Service, qui présente actuellement une [limite de connexion de 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Avec une limite de connexion indépendante dans `ServicePointManager`, il y a plus de chances que le seuil de connexion du bac à sable soit atteint et le site arrêté. Dans ce cas, le fait de définir `DefaultConnectionLimit` sur une valeur inférieure, par exemple 50 ou 100, peut empêcher ce problème tout en assurant un débit suffisant.

Le paramètre doit être configuré avant d’effectuer toute requête HTTP. Par conséquent, l’hôte WebJobs ne doit pas essayer d’ajuster le paramètre automatiquement. En effet, il se peut que des requêtes HTTP interviennent avant le démarrage de l’hôte, ce qui risque de donner lieu à un comportement inattendu. La meilleure approche consiste à définir la valeur dans votre méthode `Main` immédiatement avant l’initialisation du `JobHost`, comme illustré dans l’exemple suivant :

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it is used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Déclencheurs

Les fonctions doivent être des méthodes publiques et doivent présenter un attribut de déclencheur ou l’attribut [NoAutomaticTrigger](#manual-trigger).

### <a name="automatic-trigger"></a>Déclencheur automatique

Les déclencheurs automatiques appellent une fonction en réponse à un événement. Pour obtenir un exemple, reportez-vous au déclencheur queue dans l’[article de prise en main](webjobs-sdk-get-started.md).

### <a name="manual-trigger"></a>Déclencheur manuel

Pour déclencher une fonction manuellement, utilisez l’attribut `NoAutomaticTrigger`, comme illustré dans l’exemple suivant :

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
    TextWriter logger,
    string value,
    [Queue("outputqueue")] out string message)
{
    message = value;
    logger.WriteLine("Creating queue message: ", message);
}
```

## <a name="input-and-output-bindings"></a>Liaisons d’entrée et de sortie

Les liaisons d’entrée fournissent une méthode déclarative pour rendre disponibles les données des services Azure ou tiers pour votre code. Les liaisons de sortie offrent un moyen de mettre à jour des données. L’[article de prise en main](webjobs-sdk-get-started.md) propose un exemple pour chaque type de liaison.

Vous pouvez utiliser une valeur de retour de méthode pour une liaison de sortie en appliquant l’attribut à la valeur de retour de méthode. Pour plus d’informations, consultez l’exemple dans l’article sur [les déclencheurs et les liaisons](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value) Azure Functions.

## <a name="binding-types"></a>Types de liaisons

Les types de liaisons et de déclencheurs suivants sont inclus dans le package `Microsoft.Azure.WebJobs` :

* Stockage d'objets blob
* Stockage de files d'attente
* Stockage de tables

Pour utiliser d’autres types de liaisons et de déclencheurs, installez le package NuGet qui les contient et appelez une méthode `Use<binding>` sur l’objet `JobHostConfiguration`. Par exemple, si vous souhaitez utiliser un déclencheur Timer, installez `Microsoft.Azure.WebJobs.Extensions` et appelez `UseTimers` dans la méthode `Main`, comme illustré dans cet exemple :

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Vous pouvez trouver le package à installer pour un type de liaison spécifique dans la section **Packages** de l’[article de référence](#binding-reference-information) de ce type de liaison pour Azure Functions. Une exception est le déclencheur et liaison Files (pour le système de fichiers local), qui n’est pas pris en charge par Azure Functions. Pour utiliser la liaison Files, installez `Microsoft.Azure.WebJobs.Extensions` et appelez `UseFiles`.

### <a name="usecore"></a>UseCore

Le package `Microsoft.Azure.WebJobs.Extensions` mentionné précédemment fournit également un type de liaison spécial que vous pouvez enregistrer en appelant la méthode `UseCore`. Cette liaison vous permet de définir un paramètre [ExecutionContext](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs) dans la signature de votre fonction. L’objet de contexte vous donne accès à l’ID d’appel, que vous pouvez utiliser pour mettre en corrélation tous les journaux produits par l’appel d’une fonction donnée. Voici un exemple :

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

## <a name="binding-configuration"></a>Configuration de liaison

Vous pouvez configurer le comportement de nombreux types de déclencheurs et liaisons en définissant des propriétés dans un objet de configuration que vous transmettez au `JobHost`.

### <a name="queue-trigger-configuration"></a>Configuration d’un déclencheur queue

Les paramètres configurables pour le déclencheur queue Stockage sont expliqués dans les [informations de référence sur le fichier host.json](../azure-functions/functions-host-json.md#queues) pour Azure Functions. L’exemple suivant montre comment les définir dans un projet du Kit de développement logiciel (SDK) WebJobs :

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

### <a name="configuration-for-other-bindings"></a>Configuration pour les autres liaisons

Certains types de déclencheurs et de liaisons définissent leur propre type de configuration personnalisée. Par exemple, le déclencheur Files vous permet de spécifier le chemin d’accès racine à surveiller :

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

Pour plus d’informations sur les expressions de liaison, consultez [Modèles et expressions de liaison](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns) dans la documentation Azure Functions.

### <a name="custom-binding-expressions"></a>Expressions de liaison personnalisées

Dans certains cas, vous souhaitez spécifier dans le code un nom de file d’attente, un nom d’objet blob ou un conteneur, ou encore un nom de table, plutôt que de les coder en dur. Par exemple, il se peut que vous souhaitiez spécifier le nom de la file d’attente de l’attribut `QueueTrigger` dans une variable d’environnement ou un fichier de configuration.

Pour ce faire, vous devez transmettre un objet `NameResolver` à l’objet `JobHostConfiguration`. Incluez des espaces réservés dans les paramètres de constructeur d’attribut pour que le code de votre objet `NameResolver` fournisse les valeurs réelles à utiliser à la place de ces espaces réservés. Les espaces réservés sont identifiés par des signes de pourcentage (%) au début et à la fin, comme illustré dans l’exemple suivant :
 
```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Ce code vous permet d’utiliser une file d’attente nommée logqueuetest dans l’environnement de test et une autre nommée logqueueprod en production. Au lieu d’un nom de file d’attente codé en dur, vous spécifiez le nom d’une entrée dans la collection `appSettings`. 

Si vous ne fournissez pas d’objet NameResolver personnalisé, un objet NameResolver par défaut est utilisé. L’objet par défaut obtient les valeurs à partir des paramètres d’application ou des variables d’environnement.

Votre classe `NameResolver` obtient le nom de la file d’attente à partir de `appSettings`, comme illustré dans l’exemple suivant :

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Transmettez votre classe `NameResolver` à l’objet `JobHost`, comme illustré dans l’exemple suivant :

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

Si vous avez besoin d’effectuer une tâche dans votre fonction avant d’utiliser un attribut de liaison tel que `Queue`, `Blob`, ou `Table`, vous pouvez utiliser l’interface `IBinder`.

L’exemple suivant prend un message en file d’attente d’entrée et crée un message avec le même contenu dans une file d’attente de sortie. Le nom de file d’attente de sortie est défini par le code dans le corps de la fonction.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

Pour plus d’informations, consultez [Liaison au runtime](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) dans la documentation Azure Functions.

## <a name="binding-reference-information"></a>Informations de référence sur les liaisons

Des informations de référence sur chaque type de liaison sont fournies dans la documentation Azure Functions. Avec la file d’attente Stockage utilisée ici à titre d’exemple, vous trouverez les informations suivantes dans l’article de référence de chaque liaison :

* [Packages](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x) : package à installer pour inclure la prise en charge de la liaison dans un projet du Kit de développement logiciel (SDK) WebJobs.
* [Exemples](../azure-functions/functions-bindings-storage-queue.md#trigger---example) : l’exemple de bibliothèque de classes C# s’applique au Kit de développement logiciel (SDK) WebJobs ; omettez simplement l’attribut `FunctionName`.
* [Attributs](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes) : attributs à utiliser pour le type de liaison.
* [Configuration](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration) : explication des propriétés d’attribut et des paramètres de constructeur.
* [Utilisation](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) : types avec lesquels vous pouvez effectuer une liaison et informations sur le fonctionnement de la liaison. Exemple : algorithme d’interrogation, traitement de file d’attente de messages incohérents.
  
Pour obtenir la liste des articles de référence sur les liaisons, consultez la section **Liaisons prises en charge** de l’article [Déclencheurs et liaisons](../azure-functions/functions-triggers-bindings.md#supported-bindings) pour Azure Functions. Dans cette liste, les liaisons HTTP, Webhook et Event Grid sont prises en charge uniquement par Azure Functions, et pas par le Kit de développement logiciel (SDK) WebJobs.

## <a name="disable-attribute"></a>Attribut Disable 

L’attribut [Disable](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) vous permet de définir si une fonction peut être déclenchée. 

Dans l’exemple suivant, si le paramètre d’application `Disable_TestJob` présente la valeur « 1 » ou « True » (casse non prise en compte), la fonction n’est pas exécutée. Dans ce cas, le runtime crée le message de journal *Function ’Functions.TestJob’ is disabled* (La fonction « Functions.TestJob » est désactivée).

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

La modification des valeurs des paramètres d’application dans le portail Azure entraîne le redémarrage de la tâche web, qui récupère les nouveaux paramètres.

L’attribut peut être déclaré au niveau du paramètre, de la méthode ou de la classe. Le nom du paramètre peut également contenir des expressions de liaison.

## <a name="timeout-attribute"></a>Attribut Timeout

L’attribut [Timeout](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) entraîne l’annulation d’une fonction si l’exécution de celle-ci ne se termine dans un laps de temps spécifique. Dans l’exemple suivant, sans l’attribut Timeout, la fonction est exécutée pendant un jour. Avec l’attribut Timeout, la fonction est annulée au bout de 15 secondes.

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

Vous pouvez appliquer l’attribut Timeout au niveau de la classe ou de la méthode, et vous pouvez spécifier un délai d’expiration global à l’aide de `JobHostConfiguration.FunctionTimeout`. Les délais d’expiration au niveau de la classe ou de la méthode priment sur le délai d’attente globale.

## <a name="singleton-attribute"></a>Attribut Singleton

Utilisez l’attribut [Singleton](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) pour qu’une seule instance d’une fonction s’exécute même en cas d’instances multiples de l’application web hôte. Pour cela, le [verrouillage distribué](#viewing-lease-blobs) est implémenté.

Dans l’exemple suivant, une seule instance de la fonction `ProcessImage` est exécutée à un moment donné :

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Certains déclencheurs intègrent la prise en charge de la gestion de l’accès concurrentiel :

* **QueueTrigger** : définissez `JobHostConfiguration.Queues.BatchSize` sur 1.
* **ServiceBusTrigger** : définissez `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` sur 1.
* **FileTrigger** : définissez `FileProcessor.MaxDegreeOfParallelism` sur 1.

Vous pouvez utiliser ces paramètres pour vous assurer que votre fonction s’exécute en tant que singleton sur une instance unique. Pour vous assurer qu’une seule instance de la fonction est exécutée quand l’application web est étendue à plusieurs instances, appliquez un verrou Singleton au niveau de l’écouteur sur la fonction (`[Singleton(Mode = SingletonMode.Listener)]`). Les verrous d’écouteurs sont acquis au démarrage du JobHost. Si trois instances scale-out démarrent en même temps, une seule de ces instances acquiert le verrou et un seul écouteur démarre.

### <a name="scope-values"></a>Valeurs d’étendue

Vous pouvez spécifier une **valeur/expression d’étendue** sur le Singleton pour assurer que toutes les exécutions de la fonction dans cette portée seront sérialisées. L’implémentation d’un verrouillage plus précis peut ainsi permettre un certain niveau de parallélisme pour votre fonction, tout en sérialisant d’autres appels conformément à vos besoins. Par exemple, dans l’exemple suivant l’expression d’étendue est liée à la valeur `Region` du message entrant. Si la file d’attente contient 3 messages dans les régions « Est », « Est » et « Ouest » respectivement, alors les messages pour la région « Est » seront exécutés en série pendant que le message pour la région « Ouest » sera exécuté en parallèle.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item
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

L’étendue par défaut d’un verrou est `SingletonScope.Function`, ce qui signifie que l’étendue du verrouillage (le chemin d’accès du bail des objets blob) est lié au nom de fonction complet. Pour appliquer le verrouillage à différentes fonctions, spécifiez `SingletonScope.Host` et utilisez un nom d’ID d’étendue identique pour toutes les fonctions à ne pas exécuter simultanément. Dans l’exemple suivant, seule une instance de `AddItem` ou `RemoveItem` est exécutée à la fois :

```charp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation
}
```

### <a name="viewing-lease-blobs"></a>Affichage des baux d’objets blob

Le Kit de développement logiciel (SDK) WebJobs utilise des [baux d’objets blob Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) en arrière-plan pour implémenter le verrouillage distribué. Les baux d’objets blob de bail utilisés par l’attribut Singleton se trouvent dans le conteneur `azure-webjobs-host` du compte de stockage `AzureWebJobsStorage`, sous le chemin d’accès « locks ». Par exemple, le chemin d’accès du bail d’objet blob pour le premier exemple `ProcessImage` illustré précédemment peut être `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Tous les chemins d’accès incluent l’ID JobHost, dans le cas présent 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Fonctions asynchrones

Pour plus d’informations sur le codage de fonctions async, consultez la documentation Azure Functions sur les [fonctions Async](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Jetons d’annulation

Pour plus d’informations sur la gestion des jetons d’annulation, consultez la documentation Azure Functions sur [les jetons d’annulation et l’arrêt approprié](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Instances multiples

Si votre application web s’exécute sur plusieurs instances, une tâche web continue se lance sur chaque instance, écoutant les déclencheurs et appelant les fonctions. Les différentes liaisons de déclencheur sont conçues pour partager efficacement le travail de façon collaborative entre les instances afin que l’extension à d’autres instances vous permette de gérer une charge plus importante.

Les déclencheurs queue et blob empêchent automatiquement une fonction de traiter un message de file d’attente ou un objet blob plusieurs fois ; les fonctions ne doivent pas nécessairement être idempotentes.

Le déclencheur timer garantit automatiquement l’exécution d’une seule instance du minuteur de sorte qu’une instance unique de la fonction soit exécutée à une heure planifiée donnée.

Si vous souhaitez vous assurer qu’une seule instance d’une fonction s’exécute même lorsqu’il existe plusieurs instances de l’application web hôte, vous pouvez utiliser l’attribut [Singleton](#singleton).
    
## <a name="filters"></a>Filtres 

Les filtres de fonctions (préversion) vous offrent un moyen de personnaliser le pipeline d’exécution de tâches web avec votre propre logique. Ces filtres sont similaires aux [filtres ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Ils peuvent être implémentés en tant qu’attributs déclaratifs appliqués à vos fonctions ou classes. Pour plus d’informations, consultez [Function Filters](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters) (Filtres de fonctions).

## <a name="logging-and-monitoring"></a>Enregistrement et surveillance

Le framework de journalisation développé pour ASP.NET est recommandé. Pour savoir comment l’utiliser, consultez l’[article de prise en main](webjobs-sdk-get-started.md). 

### <a name="log-filtering"></a>Filtrage de journal

Chaque journal créé par une instance `ILogger` présente des attributs `Category` et `Level` associés. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) est une énumération, et le code d’entier indique l’importance relative :

|LogLevel    |Code|
|------------|---|
|Trace       | 0 |
|Déboguer       | 1 |
|Information | 2 |
|Avertissement     | 3 |
|Error       | 4 |
|Critique    | 5. |
|Aucun        | 6. |

Chaque catégorie peut être filtrée indépendamment en fonction d’un attribut [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) spécifique. Par exemple, il se peut que vous souhaitiez afficher tous les journaux pour le traitement de déclencheurs blob, mais uniquement les niveaux `Error` et plus élevés pour tous les autres éléments.

Pour simplifier la spécification des règles de filtrage, le Kit de développement logiciel (SDK) WebJobs fournit l’objet `LogCategoryFilter`, qui peut être transmis à un grand nombre des fournisseurs de journalisation existants, y compris Application Insights et la console.

L’objet `LogCategoryFilter` présente une propriété `Default` avec la valeur initiale `Information`, ce qui signifie que tous les messages avec les niveaux `Information`, `Warning`, `Error` ou `Critical` sont enregistrés, mais que tous les messages avec les niveaux `Debug` ou `Trace` sont exclus du filtre.

La propriété `CategoryLevels` permet de définir des niveaux de journalisation pour des catégories spécifiques afin d’affiner la sortie de la journalisation. Si aucune correspondance n’est trouvée dans le dictionnaire `CategoryLevels`, le filtre se replie sur la valeur `Default` pour décider si le message doit être filtré ou non.

L’exemple suivant crée un filtre qui filtre par défaut tous les journaux au niveau `Warning`. Les catégories `Function` ou `Host.Results` sont filtrées au niveau `Error`. L’objet `LogCategoryFilter` compare la catégorie actuelle à toutes les propriétés `CategoryLevels` enregistrées et choisit la correspondance la plus longue. Par conséquent, le niveau `Debug` enregistré pour `Host.Triggers` correspondra à `Host.Triggers.Queue` ou `Host.Triggers.Blob`. Cela vous permet de contrôler des catégories plus vastes sans avoir à ajouter chacune d’entre elles.

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

Le client `TelemetryClient` créé par le fournisseur Application Insights pour le Kit de développement logiciel (SDK) WebJobs utilise le canal [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs) de façon interne. Quand le point de terminaison d’Application Insights est non disponible ou limite les requêtes entrantes, ce canal [enregistre les requêtes dans le système de fichiers de l’application web et les soumet à nouveau ultérieurement](http://apmtips.com/blog/2015/09/03/more-telemetry-channels).

Le client `TelemetryClient` est créé par une classe qui implémente `ITelemetryClientFactory`. Par défaut, il s’agit de la classe [DefaultTelemetryClientFactory](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Si vous souhaitez modifier une partie du pipeline Application Insights, vous pouvez fournir votre propre classe `ITelemetryClientFactory`, qui sera utilisée par l’hôte pour construire un client `TelemetryClient`. Par exemple, ce code remplace la classe `DefaultTelemetryClientFactory` pour modifier une propriété du canal `ServerTelemetryChannel` :

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

        // change the default from 30 seconds to 15 seconds
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

L’objet SamplingPercentageEstimatorSettings configure l’[échantillonnage adaptatif](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server). Cela signifie que dans certains scénarios impliquant de grands volumes, Application Insights envoie un sous-ensemble sélectionné de données de télémétrie au serveur.

Une fois que vous avez créé la fabrique de données de télémétrie, transmettez-la au fournisseur de journalisation Application Insights :

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Étapes suivantes

Dans ce guide, vous avez pu découvrir des extraits de code qui illustrent comment gérer des scénarios courants d’utilisation du Kit de développement logiciel (SDK) WebJobs. Pour obtenir des exemples complets, consultez [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples).