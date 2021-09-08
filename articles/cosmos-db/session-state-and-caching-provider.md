---
title: Utiliser Azure Cosmos DB comme fournisseur d’état de session et de mise en cache ASP.NET
description: Découvrez comment utiliser Azure Cosmos DB comme fournisseur d’état de session et de mise en cache ASP.NET
author: StefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/15/2021
ms.openlocfilehash: c55428557e17615ed58140cbeab5cbe0b5450608
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290382"
---
# <a name="use-azure-cosmos-db-as-an-aspnet-session-state-and-caching-provider"></a>Utiliser Azure Cosmos DB comme fournisseur d’état de session et de mise en cache ASP.NET

La session et le fournisseur de cache de Azure Cosmos DB vous permettent d’utiliser Azure Cosmos DB et de tirer parti de ses capacités de faible latence et de mise à l’échelle globale pour le stockage des données d’état de session et comme un cache distribué au sein de votre application.

## <a name="what-is-session-state"></a>Qu’est-ce que l’état de session ?

L'[État de session](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#configure-session-state&preserve-view=true) est une donnée utilisateur qui effectue le suivi d’un utilisateur qui parcourt une application Web pendant un certain temps, au sein du même navigateur. L’état de session expire et se limite aux interactions qu’un navigateur particulier a et qui ne s’étend pas entre les navigateurs. Elles sont considérées comme des données éphémères, si elles ne sont pas présentes, elles ne perturberont pas l’application. Toutefois, lorsqu’elles existent, l’expérience est plus rapide pour l’utilisateur, car l’application Web n’a pas besoin de les extraire à chaque demande du navigateur pour le même utilisateur.

Elles sont souvent sauvegardées par un mécanisme de stockage, qui peut, dans certains cas, être externe au serveur Web actuel et permettre l’équilibrage de charge des requêtes du même navigateur sur plusieurs serveurs Web pour obtenir une plus grande évolutivité.

Le fournisseur d’état de session le plus simple est le fournisseur en mémoire qui stocke uniquement les données sur la mémoire du serveur Web local et requiert que l’application utilise le[Routage des demandes d’applications](/iis/extensions/planning-for-arr/using-the-application-request-routing-module). Cela rend la session de navigateur rémanente sur un serveur Web particulier (toutes les demandes pour ce navigateur doivent toujours se trouver sur le même serveur Web). Le fournisseur fonctionne bien sur des scénarios simples, mais l’exigence d’adhérence peut poser des problèmes d’équilibrage de charge lorsque les applications Web sont mises à l’échelle.

De nombreux fournisseurs de stockage externes sont disponibles, qui peuvent stocker les données de session d’une manière qui peut être lue et accessible par plusieurs serveurs Web sans nécessiter d’adhérence de session et permettre une mise à l’échelle supérieure.

## <a name="session-state-scenarios"></a>Scénarios d’état de session

Cosmos DB peut être utilisé comme fournisseur d’état de session par le biais du package d’extension [Microsoft. Extensions. Caching. Cosmos](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Cosmos) utilise le [kit de développement logiciel (SDK) .net Azure Cosmos DB](sql-api-sdk-dotnet-standard.md), en utilisant un Conteneur comme stockage de session effectif basé sur une approche clé/valeur où la clé est l’identificateur de session.

Une fois le package ajouté, vous pouvez l’utiliser `AddCosmosCache` dans le cadre de votre processus de démarrage (services. AddSession et aapp.UseSession sont des étapes [d’initialisation courantes](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#configure-session-stat&preserve-view=true) requises pour tout fournisseur d’état de session) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
  /* Other service configurations */
  services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
  {
      CosmosClientBuilder clientBuilder = new CosmosClientBuilder("myConnectionString")
        .WithApplicationRegion("West US");
      cacheOptions.ContainerName = "myContainer";
      cacheOptions.DatabaseName = "myDatabase";
      cacheOptions.ClientBuilder = clientBuilder;
      /* Creates the container if it does not exist */
      cacheOptions.CreateIfNotExists = true; 
  });

  services.AddSession(options =>
  {
      options.IdleTimeout = TimeSpan.FromSeconds(3600);
      options.Cookie.IsEssential = true;
  });
  /* Other service configurations */
}

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    /* Other configurations */

    app.UseSession();

    /* app.UseEndpoints and other configurations */
}
```

Emplacement que vous spécifiez à la base de données et au conteneur pour stocker l’état de session et éventuellement, que vous créez s’il n’existe pas.

Vous pouvez personnaliser votre configuration client du kit de développement logiciel (SDK) à l’aide de `CosmosClientBuilder` ou, si votre application utilise déjà un `CosmosClient` pour d’autres opérations avec Cosmos DB, vous pouvez également l’injecter dans le fournisseur :

```csharp
services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.ContainerName = "myContainer";
    cacheOptions.DatabaseName = "myDatabase";
    cacheOptions.CosmosClient = preExistingClient;
    /* Creates the container if it does not exist */
    cacheOptions.CreateIfNotExists = true; 
});
```

Après cela, vous pouvez utiliser des sessions ASP.NET Core comme avec n’importe quel autre fournisseur et utiliser l’objet HttpContext. Session. N’oubliez pas de toujours essayer de charger vos informations de session de manière asynchrone, conformément aux [recommandations ASP.NET ](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#load-session-state-asynchronously&preserve-view=true).

##  <a name="distributed-cache-scenarios"></a>Scénarios de cache distribué

Comme le fournisseur de Cosmos DB implémente l'[interface IDistributedCache pour agir en tant que fournisseur de cache distribué](/aspnet/core/performance/caching/distributed?view=aspnetcore-5.0&preserve-view=true), il peut également être utilisé pour n’importe quelle application qui requiert un cache distribué, et pas uniquement pour une application Web qui requiert un fournisseur d’état de session performant et distribué.

Les caches distribués nécessitent une cohérence des données pour fournir aux instances indépendantes le pouvoir de partager ces données mises en cache. Lorsque vous utilisez le fournisseur Cosmos DB, vous pouvez :

- Utiliser votre compte Cosmos DB dans la **Cohérence de session** si vous pouvez activer le [Routage des demandes d'applications](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) et formuler des requêtes rémanentes à une instance particulière.
- Utiliser votre compte Cosmos DB en **fonction de l'obsolescence ou de la cohérence forte** sans avoir recours à l’adhérence des requêtes. Cela fournit la plus grande échelle en termes de répartition de la charge entre vos instances.

Pour utiliser le fournisseur Cosmos DB comme cache distribué, il doit être enregistré dans `ConfiguredService` avec l’appel `services.AddCosmosCache`. Une fois effectué, tout constructeur dans l’application peut demander au cache en référençant `IDistributedCache` et il recevra une instance injectée par [injection de dépendance](/dotnet/core/extensions/dependency-injection) à utiliser :

```csharp
public class MyBusinessClass
{
    private readonly IDistributedCache cache;

    public MyBusinessClass(IDistributedCache cache)
    {
        this.cache = cache;
    }
    
    public async Task SomeOperationAsync()
    {
        string someCachedValue = await this.cache.GetStringAsync("someKey");
        /* Use the cache */
    }
}
```

## <a name="troubleshooting-and-diagnosing"></a>Résolution et diagnostic des problèmes

Dans la mesure où le fournisseur Cosmos DB utilise le .NET SDK sous-jacent, l’ensemble des [directives de performance](performance-tips-dotnet-sdk-v3-sql.md) et [guides de résolution des problèmes](troubleshoot-dot-net-sdk.md)existants s’appliquent à comprendre les éventuels problèmes. Notez qu’il existe un moyen distinct d’accéder aux Diagnostics à partir des opérations de Cosmos DB sous-jacentes, car elles ne peuvent pas être exposées via les API IDistributedCache.

L’inscription du délégué de diagnostics facultatif vous permet de capturer et de journaliser de manière conditionnelle les diagnostics pour résoudre les problèmes liés à une latence élevée :

```csharp
void captureDiagnostics(CosmosDiagnostics diagnostics)
{
    if (diagnostics.GetClientElapsedTime() > SomePredefinedThresholdTime)
    {
        Console.WriteLine(diagnostics.ToString());
    }
}

services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.DiagnosticsHandler = captureDiagnostics;
    /* other options */
});
```

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur la session Azure Cosmos DB et le fournisseur de caches, consultez le [code source sur GitHub](https://github.com/Azure/Microsoft.Extensions.Caching.Cosmos/).
- [Essayez](https://github.com/Azure/Microsoft.Extensions.Caching.Cosmos/tree/master/sample) la session Azure Cosmos DB et le fournisseur de cache en explorant un exemple d’application web ASP.NET Core.
- En savoir plus sur les[caches distribués](/aspnet/core/performance/caching/distributed?view=aspnetcore-5.0&preserve-view=true) sur .NET.
