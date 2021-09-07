---
title: Utiliser Azure Cosmos DB comme fournisseur d’état de session et de mise en cache ASP.NET
description: Découvrez comment utiliser Azure Cosmos DB comme fournisseur d’état de session et de mise en cache ASP.NET
author: StefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/15/2021
ms.openlocfilehash: 9a7b7ad15d1d4850e910010e4fdc195e474d10af
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116048"
---
# <a name="use-azure-cosmos-db-as-an-aspnet-session-state-and-caching-provider"></a>Utiliser Azure Cosmos DB comme fournisseur d’état de session et de mise en cache ASP.NET
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

La session et le fournisseur de cache de Azure Cosmos DB vous permettent d’utiliser Azure Cosmos DB et de tirer parti de ses capacités de faible latence et de mise à l’échelle globale pour le stockage des données d’état de session et comme un cache distribué au sein de votre application.

## <a name="what-is-session-state"></a>Qu’est-ce que l’état de session ?

L’[état de session](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#configure-session-state&preserve-view=true) est une donnée utilisateur qui effectue le suivi d’un utilisateur qui parcourt une application web pendant un certain temps, au sein du même navigateur. L’état de session expire et se limite aux interactions d’un navigateur particulier. Il ne s’étend pas à d’autres navigateurs. Ces interactions étant considérées comme des données éphémères, si elles ne sont pas présentes, elles ne perturbent pas l’application. Toutefois, lorsqu’elles existent, l’expérience est plus rapide pour l’utilisateur, car l’application web n’a pas besoin de les extraire à chaque demande du navigateur pour le même utilisateur.

Elles sont souvent sauvegardées par un mécanisme de stockage, qui peut, dans certains cas, être externe au serveur web actuel et permettre l’équilibrage de charge des requêtes du même navigateur sur plusieurs serveurs web afin de bénéficier d’une plus grande scalabilité.

Le fournisseur d’état de session le plus simple est le fournisseur en mémoire qui stocke uniquement les données sur la mémoire du serveur web local et requiert que l’application utilise le[Routage des demandes d’applications](/iis/extensions/planning-for-arr/using-the-application-request-routing-module). Cela rend la session de navigateur rémanente sur un serveur web particulier (toutes les demandes pour ce navigateur doivent toujours se trouver sur le même serveur web). Le fournisseur fonctionne bien sur des scénarios simples, mais l’exigence d’adhérence peut poser des problèmes d’équilibrage de charge lorsque les applications web sont mises à l’échelle.

De nombreux fournisseurs de stockage externes sont disponibles, qui peuvent stocker les données de session de manière à ce qu’elles soient lisibles et accessibles par plusieurs serveurs web sans nécessiter d’adhérence de session, et permettre une échelle supérieure.

## <a name="session-state-scenarios"></a>Scénarios d’état de session

Cosmos DB peut être utilisé comme fournisseur d’état de session via le package d’extension [Microsoft.Extensions.Caching.Cosmos](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Cosmos). Il utilise le [kit de développement logiciel (SDK) .NET Azure Cosmos DB](sql-api-sdk-dotnet-standard.md), en se servant d’un conteneur comme stockage de session effectif basé sur une approche clé/valeur où la clé est l’identificateur de session.

Une fois le package ajouté, vous pouvez l’utiliser la commande `AddCosmosCache` dans le cadre de votre processus de démarrage (services.AddSession et aapp.UseSession sont des [étapes d’initialisation courantes](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#configure-session-stat&preserve-view=true) requises pour tout fournisseur d’état de session) :

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

Vous spécifiez la base de données et le conteneur dans lesquels stocker l’état de session, ou les créez s’ils n’existent pas.

Vous pouvez personnaliser la configuration de votre client du kit de développement logiciel (SDK) à l’aide de `CosmosClientBuilder` ou, si votre application utilise déjà un `CosmosClient` pour d’autres opérations avec Cosmos DB, vous pouvez également l’injecter dans le fournisseur :

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

Après cela, vous pouvez utiliser des sessions ASP.NET Core comme avec n’importe quel autre fournisseur, et utiliser l’objet HttpContext. Session. N’oubliez pas de toujours essayer de charger vos informations de session de manière asynchrone, conformément aux [recommandations ASP.NET](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#load-session-state-asynchronously&preserve-view=true).

##  <a name="distributed-cache-scenarios"></a>Scénarios de cache distribué

Comme le fournisseur Cosmos DB implémente l’[interface IDistributedCache pour agir en tant que fournisseur de cache distribué](/aspnet/core/performance/caching/distributed?view=aspnetcore-5.0&preserve-view=true), il peut également être utilisé toute application nécessitant un cache distribué, pas uniquement pour une application web nécessitant un fournisseur d’état de session performant et distribué.

Les caches distribués nécessitent une cohérence des données pour permettre aux instances indépendantes de partager ces données mises en cache. Lorsque vous utilisez le fournisseur Cosmos DB, vous pouvez :

- utiliser votre compte Cosmos DB dans la **Cohérence de session** si vous pouvez activer [Application Request Routing](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) et formuler des requêtes adhérent à une instance particulière ;
- utiliser votre compte Cosmos DB en **obsolescence limitée ou cohérence forte** sans exiger d’adhérence de demande. Cela offre la plus grande échelle en termes de répartition de la charge entre vos instances.

Pour utiliser le fournisseur Cosmos DB comme cache distribué, celui-ci doit être inscrit dans `ConfiguredService`s avec l’appel`services.AddCosmosCache`. Cela fait, tout constructeur dans l’application peut demander le cache en référençant `IDistributedCache`. Il reçoit alors l’instance injectée par [injection de dépendance](/dotnet/core/extensions/dependency-injection) à utiliser :

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
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Dans la mesure où le fournisseur Cosmos DB utilise le .NET SDK sous-jacent, l’ensemble des [recommandations en matière de performances](performance-tips-dotnet-sdk-v3-sql.md) et [guides de résolution des problèmes](troubleshoot-dot-net-sdk.md) existants sont utiles pour comprendre les problèmes potentiels. Notez qu’il existe un moyen distinct d’accéder aux Diagnostics à partir des opérations de Cosmos DB sous-jacentes, car celles-ci ne peuvent pas être exposées via les API IDistributedCache.

L’inscription du délégué de diagnostics facultatif vous permet de capturer et de journaliser les diagnostics de manière conditionnelle afin de résoudre des problèmes tels qu’une latence élevée :

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
- Pour plus d’informations sur la session Azure Cosmos DB et le fournisseur de cache, consultez le [code source sur GitHub](https://github.com/Azure/Microsoft.Extensions.Caching.Cosmos/).
- [Essayez](https://github.com/Azure/Microsoft.Extensions.Caching.Cosmos/tree/master/sample) la session Azure Cosmos DB et le fournisseur de cache en explorant un exemple d’application web ASP.NET Core.
- Apprenez-en davantage sur les [caches distribués](/aspnet/core/performance/caching/distributed?view=aspnetcore-5.0&preserve-view=true) dans .NET.
