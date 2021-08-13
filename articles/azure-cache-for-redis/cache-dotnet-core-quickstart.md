---
title: 'Démarrage rapide : Utiliser Azure Cache pour Redis dans .NET Core'
description: Dans ce guide de démarrage rapide, apprenez à accéder au cache Azure pour Redis dans vos applications .NET Core
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: dotnet
ms.custom: devx-track-csharp, mvc
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: 8b2909d608578cf2ceff926bba5f3aa1b569b025
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136622"
---
# <a name="quickstart-use-azure-cache-for-redis-in-net-core"></a>Démarrage rapide : Utiliser Azure Cache pour Redis dans .NET Core

Dans ce guide de démarrage rapide, vous allez incorporer le cache Azure pour Redis dans une application .NET Core pour avoir accès à un cache sécurisé et dédié accessible à partir de n’importe quelle application dans Azure. Vous utiliserez spécifiquement le client [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) avec du code C# dans une application console .NET Core.

## <a name="skip-to-the-code-on-github"></a>Passer au code sur GitHub

Si vous souhaitez passer directement au code, consultez le [démarrage rapide .NET Core](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/dotnet-core) sur GitHub.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- [Kit de développement logiciel (SDK) .NET Core](https://dotnet.microsoft.com/download)

## <a name="create-a-cache"></a>Création d'un cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Prenez note du **NOM D’HÔTE** et de la clé d’accès **PRINCIPALE**. Vous utiliserez ces valeurs ultérieurement pour construire le secret *CacheConnection*.



## <a name="create-a-console-app"></a>Créer une application console

Ouvrez une nouvelle fenêtre de commande et exécutez la commande suivante pour créer une nouvelle application console .NET Core :

```
dotnet new console -o Redistest
```

Dans votre fenêtre de commande, remplacez par le nouveau répertoire de projet *Redistest*.



## <a name="add-secret-manager-to-the-project"></a>Ajouter Secret Manager au projet

Dans cette section, vous allez ajouter l’outil [Secret Manager](/aspnet/core/security/app-secrets) à votre projet. L’outil Secret Manager stocke les données sensibles pour les travaux de développement à l’extérieur de l’arborescence de votre projet. Cette approche empêche le partage accidentel des secrets d’une application au sein du code source.

Ouvrez votre fichier *Redistest.csproj*. Ajoutez un élément `DotNetCliToolReference` afin d’inclure *Microsoft.Extensions.SecretManager.Tools*. Ajoutez également un élément `UserSecretsId`, comme indiqué ci-dessous, puis enregistrez le fichier.

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>net5.0</TargetFramework>
        <UserSecretsId>Redistest</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
</Project>
```

Exécutez la commande suivante pour ajouter le package *Microsoft.Extensions.Configuration.UserSecrets* au projet :

```
dotnet add package Microsoft.Extensions.Configuration.UserSecrets
```

Exécutez la commande suivante pour restaurer vos packages :

```
dotnet restore
```

Dans la fenêtre de commande, exécutez la commande suivante pour stocker un nouveau secret nommé *CacheConnection*, après avoir remplacé les espaces réservés (y compris les crochets pointus) pour le nom de cache et la clé d’accès principale :

```
dotnet user-secrets set CacheConnection "<cache name>.redis.cache.windows.net,abortConnect=false,ssl=true,allowAdmin=true,password=<primary-access-key>"
```

Ajoutez l’instruction `using` suivante à *Program.cs* :

```csharp
using Microsoft.Extensions.Configuration;
```

Ajoutez les membres suivants à la classe `Program` dans *Program.cs*. Ce code initialise une configuration permettant d’accéder au secret de l’utilisateur pour la chaîne de connexion du cache Azure pour Redis.

```csharp
private static IConfigurationRoot Configuration { get; set; }
const string SecretName = "CacheConnection";

private static void InitializeConfiguration()
{
    var builder = new ConfigurationBuilder()
        .AddUserSecrets<Program>();

    Configuration = builder.Build();
}
```

## <a name="configure-the-cache-client"></a>Configuration du client de cache

Dans cette section, vous allez configurer l’application console pour utiliser le client [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) pour .NET.

Dans la fenêtre de commande, exécutez la commande suivante dans le répertoire du projet *Redistest* :

```
dotnet add package StackExchange.Redis
```

Une fois l’installation terminée, le client de cache *StackExchange.Redis* est disponible pour être utilisé avec votre projet.


## <a name="connect-to-the-cache"></a>Connexion au cache

Ajoutez l’instruction `using` suivante à *Program.cs* :

```csharp
using StackExchange.Redis;
```

La connexion au cache Azure pour Redis est gérée par la classe `ConnectionMultiplexer`. Cette classe doit être partagée et réutilisée dans votre application cliente. Ne créez pas une nouvelle connexion pour chaque opération. 

Dans *Program.cs*, ajoutez les membres suivants à la classe `Program` de votre application console :

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = CreateConnection();

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}

private static Lazy<ConnectionMultiplexer> CreateConnection()
{
    return new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = Configuration[SecretName];
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
}
```

Cette approche pour partager une instance `ConnectionMultiplexer` dans votre application utilise une propriété statique qui renvoie une instance connectée. Ce code fournit une méthode thread-safe permettant d’initialiser une seule instance `ConnectionMultiplexer` connectée. `abortConnect` a la valeur false, ce qui signifie que l’appel réussit même si aucune connexion au cache Azure pour Redis n’est établie. Une fonctionnalité clé de `ConnectionMultiplexer` est qu’il restaure automatiquement la connectivité au cache une fois que le problème réseau ou d’autres causes sont résolus.

La valeur du secret *CacheConnection* est accessible à l’aide du fournisseur de configuration Secret Manager et utilisée en tant que paramètre du mot de passe.

## <a name="handle-redisconnectionexception-and-socketexception-by-reconnecting"></a>Gérer RedisConnectionException et SocketException en rétablissant la connexion

Une bonne pratique recommandée lors de l’appel de méthodes sur `ConnectionMultiplexer` consiste à tenter de résoudre les exceptions `RedisConnectionException` et `SocketException` automatiquement en fermant la connexion et en la rétablissant.

Ajoutez les instructions `using` ci-après à *Program.cs* :

```csharp
using System.Net.Sockets;
using System.Threading;
```

Dans *Program.cs*, ajoutez les membres suivants à la classe `Program` :

```csharp
private static long lastReconnectTicks = DateTimeOffset.MinValue.UtcTicks;
private static DateTimeOffset firstErrorTime = DateTimeOffset.MinValue;
private static DateTimeOffset previousErrorTime = DateTimeOffset.MinValue;

private static readonly object reconnectLock = new object();

// In general, let StackExchange.Redis handle most reconnects,
// so limit the frequency of how often ForceReconnect() will
// actually reconnect.
public static TimeSpan ReconnectMinFrequency => TimeSpan.FromSeconds(60);

// If errors continue for longer than the below threshold, then the
// multiplexer seems to not be reconnecting, so ForceReconnect() will
// re-create the multiplexer.
public static TimeSpan ReconnectErrorThreshold => TimeSpan.FromSeconds(30);

public static int RetryMaxAttempts => 5;

private static void CloseConnection(Lazy<ConnectionMultiplexer> oldConnection)
{
    if (oldConnection == null)
        return;

    try
    {
        oldConnection.Value.Close();
    }
    catch (Exception)
    {
        // Example error condition: if accessing oldConnection.Value causes a connection attempt and that fails.
    }
}

/// <summary>
/// Force a new ConnectionMultiplexer to be created.
/// NOTES:
///     1. Users of the ConnectionMultiplexer MUST handle ObjectDisposedExceptions, which can now happen as a result of calling ForceReconnect().
///     2. Don't call ForceReconnect for Timeouts, just for RedisConnectionExceptions or SocketExceptions.
///     3. Call this method every time you see a connection exception. The code will:
///         a. wait to reconnect for at least the "ReconnectErrorThreshold" time of repeated errors before actually reconnecting
///         b. not reconnect more frequently than configured in "ReconnectMinFrequency"
/// </summary>
public static void ForceReconnect()
{
    var utcNow = DateTimeOffset.UtcNow;
    long previousTicks = Interlocked.Read(ref lastReconnectTicks);
    var previousReconnectTime = new DateTimeOffset(previousTicks, TimeSpan.Zero);
    TimeSpan elapsedSinceLastReconnect = utcNow - previousReconnectTime;

    // If multiple threads call ForceReconnect at the same time, we only want to honor one of them.
    if (elapsedSinceLastReconnect < ReconnectMinFrequency)
        return;

    lock (reconnectLock)
    {
        utcNow = DateTimeOffset.UtcNow;
        elapsedSinceLastReconnect = utcNow - previousReconnectTime;

        if (firstErrorTime == DateTimeOffset.MinValue)
        {
            // We haven't seen an error since last reconnect, so set initial values.
            firstErrorTime = utcNow;
            previousErrorTime = utcNow;
            return;
        }

        if (elapsedSinceLastReconnect < ReconnectMinFrequency)
            return; // Some other thread made it through the check and the lock, so nothing to do.

        TimeSpan elapsedSinceFirstError = utcNow - firstErrorTime;
        TimeSpan elapsedSinceMostRecentError = utcNow - previousErrorTime;

        bool shouldReconnect =
            elapsedSinceFirstError >= ReconnectErrorThreshold // Make sure we gave the multiplexer enough time to reconnect on its own if it could.
            && elapsedSinceMostRecentError <= ReconnectErrorThreshold; // Make sure we aren't working on stale data (e.g. if there was a gap in errors, don't reconnect yet).

        // Update the previousErrorTime timestamp to be now (e.g. this reconnect request).
        previousErrorTime = utcNow;

        if (!shouldReconnect)
            return;

        firstErrorTime = DateTimeOffset.MinValue;
        previousErrorTime = DateTimeOffset.MinValue;

        Lazy<ConnectionMultiplexer> oldConnection = lazyConnection;
        CloseConnection(oldConnection);
        lazyConnection = CreateConnection();
        Interlocked.Exchange(ref lastReconnectTicks, utcNow.UtcTicks);
    }
}

// In real applications, consider using a framework such as
// Polly to make it easier to customize the retry approach.
private static T BasicRetry<T>(Func<T> func)
{
    int reconnectRetry = 0;
    int disposedRetry = 0;

    while (true)
    {
        try
        {
            return func();
        }
        catch (Exception ex) when (ex is RedisConnectionException || ex is SocketException)
        {
            reconnectRetry++;
            if (reconnectRetry > RetryMaxAttempts)
                throw;
            ForceReconnect();
        }
        catch (ObjectDisposedException)
        {
            disposedRetry++;
            if (disposedRetry > RetryMaxAttempts)
                throw;
        }
    }
}

public static IDatabase GetDatabase()
{
    return BasicRetry(() => Connection.GetDatabase());
}

public static System.Net.EndPoint[] GetEndPoints()
{
    return BasicRetry(() => Connection.GetEndPoints());
}

public static IServer GetServer(string host, int port)
{
    return BasicRetry(() => Connection.GetServer(host, port));
}
```

## <a name="executing-cache-commands"></a>Exécution des commandes de cache

Dans *Program.cs*, ajoutez le code suivant pour la procédure `Main` de la classe `Program` pour votre application console :

```csharp
static void Main(string[] args)
{
    InitializeConfiguration();

    IDatabase cache = GetDatabase();

    // Perform cache operations using the cache object...

    // Simple PING command
    string cacheCommand = "PING";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

    // Simple get and put of integral data types into the cache
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    cacheCommand = "SET Message \"Hello! The cache is working from a .NET Core console app!\"";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
    Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET Core console app!").ToString());

    // Demonstrate "SET Message" executed as expected...
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    // Get the client list, useful to see if connection list is growing...
    // Note that this requires allowAdmin=true in the connection string
    cacheCommand = "CLIENT LIST";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    var endpoint = (System.Net.DnsEndPoint)GetEndPoints()[0];
    IServer server = GetServer(endpoint.Host, endpoint.Port);
    ClientInfo[] clients = server.ClientList();

    Console.WriteLine("Cache response :");
    foreach (ClientInfo client in clients)
    {
        Console.WriteLine(client.Raw);
    }

    CloseConnection(lazyConnection);
}
```

Enregistrez *Program.cs*.

Le cache Azure pour Redis dispose d’un nombre configurable de bases de données (16 par défaut) pouvant être utilisées pour séparer de manière logique les données dans un cache Azure pour Redis. Le code se connecte à la base de données par défaut, DB 0. Pour plus d’informations, consultez les sections [What are Redis databases?](cache-development-faq.yml#what-are-redis-databases-) (Que sont les bases de données Redis ?) et [Configuration du serveur Redis par défaut](cache-configure.md#default-redis-server-configuration).

Les éléments de cache peuvent être stockés et extraits en utilisant les méthodes `StringSet` et `StringGet`.

Redis stocke la plupart des données sous la forme de chaînes Redis, mais ces chaînes peuvent contenir de nombreux types de données, notamment des données binaires sérialisées, qui peuvent être utilisées lors du stockage d'objets .NET dans le cache.

Exécutez la commande suivante dans la fenêtre de commande pour générer l’application :

```
dotnet build
```

Exécutez ensuite l’application avec la commande suivante :

```
dotnet run
```

Dans l’exemple ci-dessous, vous pouvez voir que la clé `Message` présentait auparavant une valeur mise en cache, qui avait été définie à l’aide de la console Redis du portail Azure. L’application a mis à jour cette valeur mise en cache. Elle a également exécuté les commandes `PING` et `CLIENT LIST`.

![Application console partielle](./media/cache-dotnet-core-quickstart/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>Utilisation des objets .NET dans le cache

Le cache Azure pour Redis peut mettre en cache des objets .NET et des types de données primitifs, mais avant qu’un objet .NET puisse être mis en cache, il doit être sérialisé. La sérialisation d’objet .NET échoit au développeur d’applications, qui a toute latitude pour choisir le sérialiseur.

Une méthode simple pour sérialiser des objets consiste à utiliser les méthodes de sérialisation `JsonConvert` dans [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) et à sérialiser vers et à partir de JSON. Dans cette section, vous allez ajouter un objet .NET dans le cache.

Exécutez la commande suivante pour ajouter le package *Newtonsoft.json* à l’application :

```
dotnet add package Newtonsoft.json
```

Ajoutez l’instruction `using` suivante au début de *Program.cs* :

```csharp
using Newtonsoft.Json;
```

Ajoutez la définition de classe `Employee` suivante à *Program.cs* :

```csharp
class Employee
{
    public string Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }

    public Employee(string employeeId, string name, int age)
    {
        Id = employeeId;
        Name = name;
        Age = age;
    }
}
```

Au bas de la procédure `Main()` dans *Program.cs* et avant l’appel à `CloseConnection()`, ajoutez les lignes de code suivantes à mettre en cache et récupérez un objet .NET sérialisé :

```csharp
    // Store .NET object to cache
    Employee e007 = new Employee("007", "Davide Columbo", 100);
    Console.WriteLine("Cache response from storing Employee .NET object : " + 
    cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

    // Retrieve .NET object from cache
    Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
    Console.WriteLine("Deserialized Employee .NET object :\n");
    Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
    Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
    Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

Enregistrez *Program.cs* et régénérez l’application avec la commande suivante :

```
dotnet build
```

Exécutez l’application avec la commande suivante pour tester la sérialisation d’objets .NET :

```
dotnet run
```

![Application console terminée](./media/cache-dotnet-core-quickstart/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez d’exécuter le didacticiel suivant, vous pouvez conserver les ressources créées dans le cadre de ce guide de démarrage rapide afin de les réutiliser.

Sinon, si l’exemple d’application de démarrage rapide était votre dernière opération, vous pouvez supprimer les ressources Azure créées dans ce démarrage rapide afin d’éviter tout frais. 

> [!IMPORTANT]
> La suppression d’un groupe de ressources est définitive ; le groupe de ressources et l’ensemble des ressources qu’il contient sont supprimés de manière permanente. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects. Si vous avez créé les ressources pour l'hébergement de cet exemple dans un groupe de ressources existant contenant des ressources que vous souhaitez conserver, vous pouvez supprimer chaque ressource individuellement sur la gauche, au lieu de supprimer l'intégralité du groupe de ressources.
>

Connectez-vous au [portail Azure](https://portal.azure.com), puis sélectionnez **Groupes de ressources**.

Dans la zone de texte **Filtrer par nom.** , saisissez le nom de votre groupe de ressources. Les instructions de cet article ont utilisé un groupe de ressources nommé *TestResources*. Sur votre groupe de ressources dans la liste des résultats, cliquez sur **...** , puis sur **Supprimer le groupe de ressources**.

![DELETE](./media/cache-dotnet-core-quickstart/cache-delete-resource-group.png)

Il vous sera demandé de confirmer la suppression du groupe de ressources. Saisissez le nom de votre groupe de ressources à confirmer, puis sélectionnez **Supprimer**.

Après quelques instants, le groupe de ressources et toutes les ressources qu’il contient sont supprimés.



<a name="next-steps"></a>

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à utiliser le cache Azure pour Redis à partir d’une application .NET Core. Passez au guide de démarrage rapide suivant pour utiliser le cache Azure pour Redis avec une application web ASP.NET.

> [!div class="nextstepaction"]
> [Créer une application web ASP.NET qui utilise un cache Azure pour Redis.](./cache-web-app-howto.md)

Vous souhaitez optimiser et réduire vos coûts de cloud ?

> [!div class="nextstepaction"]
> [Démarrer l’analyse des coûts avec Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)