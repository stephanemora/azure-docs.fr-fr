---
title: Utiliser l’injection de dépendances dans .NET Azure Functions
description: Découvrez comment utiliser l’injection de dépendances de l’inscription et à l’aide des services dans les fonctions .NET
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: fonctions azure, fonctions, architecture sans serveur
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: jehollan, glenga, cshoe
ms.openlocfilehash: 2f2e3db47bbd02ed0351033a694aa826e0e3e9f2
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66396698"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Utiliser l’injection de dépendances dans .NET Azure Functions

Azure Functions prend en charge le modèle de conception logicielle dépendance (DI) de l’injection de code, qui est une technique pour obtenir [Inversion de contrôle (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.

Azure Functions s’appuie sur les fonctionnalités de l’Injection de dépendances ASP.NET Core. Connaître les services, les durées de vie et les modèles de conception de [l’injection de dépendances ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) avant d’utiliser les fonctionnalités de l’injection de dépendances dans un Azure Functions app est recommandé.

## <a name="prerequisites"></a>Conditions préalables

Avant de pouvoir utiliser l’injection de dépendances, vous devez installer les packages NuGet suivants :

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Package Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) version 1.0.28 ou version ultérieure

## <a name="register-services"></a>Inscrire des services

Pour inscrire les services, vous pouvez créer une méthode pour configurer et ajouter des composants à un `IFunctionsHostBuilder` instance.  L’hôte Azure Functions crée une instance de `IFunctionsHostBuilder` et le passe directement dans votre méthode.

Pour inscrire la méthode, ajoutez le `FunctionsStartup` attribut d’assembly qui spécifie le nom de type utilisé lors du démarrage.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="use-injected-dependencies"></a>Utiliser les dépendances injectés

ASP.NET Core utilise l’injection de constructeur à disposition de vos dépendances à votre fonction. L’exemple suivant montre comment la `IMyService` et `HttpClient` dépendances sont injectées dans une fonction déclenchée via HTTP.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, HttpClient client)
        {
            _service = service;
            _client = client;
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

L’utilisation de l’injection de constructeur signifie que vous ne devez pas utiliser les fonctions statiques si vous souhaitez tirer parti de l’injection de dépendances.

## <a name="service-lifetimes"></a>Durées de vie de service

Les applications de fonctions Azure offrent les mêmes durées de vie de service en tant que [l’Injection de dépendances ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): temporaires, délimitées et singleton.

Dans une application de fonctions, une durée de vie du service délimité correspond à une durée de vie de l’exécution de fonction. Services délimités sont créés une fois par exécution. Les demandes ultérieures pour ce service pendant l’exécution réutiliser l’instance de service existante. Une durée de vie du service singleton correspond à la durée de vie d’hôte et est réutilisée entre les exécutions de la fonction sur cette instance.

Services de durée de vie singleton sont recommandés pour les connexions et les clients, par exemple `SqlConnection`, `CloudBlobClient`, ou `HttpClient` instances.

Affichez ou téléchargez un [des durées de vie de service différents échantillons](https://aka.ms/functions/di-sample) sur GitHub.

## <a name="logging-services"></a>Services de journalisation

Si vous avez besoin de votre propre fournisseur de journalisation, la méthode recommandée consiste à inscrire une `ILoggerProvider` instance. Application Insights est ajouté automatiquement par Azure Functions.

> [!WARNING]
> N’ajoutez pas `AddApplicationInsightsTelemetry()` à la collection de services tel qu’il enregistre des services en conflit avec les services fournis par l’environnement.

## <a name="function-app-provided-services"></a>Services d’application fournie (fonction)

L’hôte de la fonction enregistre de nombreux services. Les services suivants sont sécurisés à prendre en tant que dépendance dans votre application :

|Type de service|Durée de vie|Description|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|singleton|Configuration du runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|singleton|Chargé de fournir l’ID de l’instance d’hôte|

Si d’autres services que vous souhaitez prendre une dépendance sur, [créer un problème et les proposer sur GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Substitution des services de l’hôte

Remplacement des services fournis par l’hôte n’est pas pris en charge actuellement.  S’il existe des services que vous souhaitez remplacer, [créer un problème et les proposer sur GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

- [Guide pratique pour surveiller votre application de fonction](functions-monitoring.md)
- [Meilleures pratiques pour les fonctions](functions-best-practices.md)
