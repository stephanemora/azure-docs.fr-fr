---
title: Utiliser l’injection de dépendances dans .NET Azure Functions
description: Découvrez comment utiliser l’injection de dépendances pour l’inscription et l’utilisation de services dans les fonctions .NET
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: fonctions azure, fonctions, architecture sans serveur
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: jehollan, cshoe
ms.openlocfilehash: 781bcdc158cb362b7c46e1ba9771b6a92ebc56a8
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67479623"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Utiliser l’injection de dépendances dans .NET Azure Functions

Azure Functions prend en charge le modèle de conception logicielle d’injection de dépendances, qui est une technique pour obtenir une [inversion de contrôle](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.

Azure Functions s’appuie sur les fonctionnalités d’injection de dépendances ASP.NET Core. Il est recommandé de connaître les services, les durées de vie et les modèles de conception des fonctions [d’injection de dépendances ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) avant d’utiliser les fonctionnalités d’injection de dépendances dans une application Azure Functions.

L’injection de dépendances est prise en charge depuis Azure Functions 2.x.

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir utiliser l’injection de dépendances, vous devez installer les packages NuGet suivants :

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Package Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 1.0.28 ou version ultérieure

- Facultatif : [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) Uniquement requis pour l’inscription de HttpClient au démarrage

## <a name="register-services"></a>Inscrire des services

Pour inscrire des services, vous pouvez créer une méthode pour configurer et ajouter des composants à une instance `IFunctionsHostBuilder`.  L’hôte Azure Functions crée une instance de `IFunctionsHostBuilder` et la passe directement dans votre méthode.

Pour inscrire la méthode, ajoutez l’attribut d’assembly `FunctionsStartup` qui spécifie le nom de type utilisé lors du démarrage. De même, du code fait référence à une version préliminaire de [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) sur Nuget.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos;

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

## <a name="use-injected-dependencies"></a>Utiliser les dépendances injectées

ASP.NET Core utilise l’injection de constructeurs pour rendre vos dépendances accessibles à votre fonction. L’exemple suivant montre comment les dépendances `IMyService` et `HttpClient` sont injectées dans une fonction déclenchée via HTTP.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();;
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

Si vous utilisez l’injection de constructeurs, n’ayez pas recours à des fonctions statiques si vous souhaitez tirer parti de l’injection de dépendances.

## <a name="service-lifetimes"></a>Durées de service

Les applications Azure Functions ont les durées de service définies par [l’injection de dépendances ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): temporaires, délimitées et singleton.

Dans une application Funtions, une durée de service délimitée correspond à la durée de vie de l’exécution d’une fonction. Les services délimités sont créés une fois par exécution. Les demandes ultérieures pour ce service pendant l’exécution réutilisent l’instance de service existante. Une durée de service singleton correspond à la durée de vie de l’hôte. Il est réutilisé entre les exécutions de la fonction sur cette instance.

Les services à durée de vie singleton sont recommandés pour les connexions et les clients, par exemple pour les instances `SqlConnection`, `CloudBlobClient` ou `HttpClient`.

Affichez ou téléchargez un exemple [des différentes durées de vie de service](https://aka.ms/functions/di-sample) sur GitHub.

## <a name="logging-services"></a>Services de journalisation

Si vous avez besoin de votre propre fournisseur de journalisation, la méthode recommandée consiste à inscrire une instance `ILoggerProvider`. Application Insights est ajouté automatiquement par Azure Functions.

> [!WARNING]
> N’ajoutez pas `AddApplicationInsightsTelemetry()` à la collection de services, car il enregistre des services en conflit avec les services fournis par l’environnement.

## <a name="function-app-provided-services"></a>Services fournis par Function App

L’hôte de la fonction inscrit de nombreux services. Les services suivants peuvent être injectés comme dépendances en toute sécurité dans votre application :

|Type de service|Durée de vie|Description|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Configuration du runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Chargé de fournir l’ID de l’instance d’hôte|

Si vous souhaitez voir d’autres services pris en charge par l’injection de dépendances, [créez un ticket et proposez-les sur GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Remplacement des services de l’hôte

Le remplacement des services fournis par l’hôte n’est pas pris en charge actuellement.  Si vous souhaitez remplacer des services, [créez un ticket et proposez-les sur GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

- [Surveiller votre application de fonction](functions-monitoring.md)
- [Meilleures pratiques pour Functions](functions-best-practices.md)
