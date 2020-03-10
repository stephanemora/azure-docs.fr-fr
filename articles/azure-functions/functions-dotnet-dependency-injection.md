---
title: Utiliser l’injection de dépendances dans .NET Azure Functions
description: Découvrez comment utiliser l’injection de dépendances pour l’inscription et l’utilisation de services dans les fonctions .NET
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 1aff2815144f776b351e92d8945b267d1451f9f6
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77915705"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Utiliser l’injection de dépendances dans .NET Azure Functions

Azure Functions prend en charge le modèle de conception logicielle d’injection de dépendances, qui est une technique pour obtenir une [inversion de contrôle](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.

- L’injection de dépendance dans Azure Functions repose sur les fonctionnalités d’injection de dépendance .Net Core. Une certaine familiarité avec les fonctionnalités d’[injection de dépendance de .Net Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) est recommandée. Il existe cependant des différences dans la façon de modifier les dépendances et la façon dont les valeurs de configuration sont lues avec Azure Functions sur le plan de consommation.

- L’injection de dépendances est prise en charge depuis Azure Functions 2.x.

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir utiliser l’injection de dépendances, vous devez installer les packages NuGet suivants :

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Package Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 1.0.28 ou version ultérieure

## <a name="register-services"></a>Inscrire des services

Pour inscrire des services, créez une méthode pour configurer et ajouter des composants à une instance `IFunctionsHostBuilder`.  L’hôte Azure Functions crée une instance de `IFunctionsHostBuilder` et la passe directement dans votre méthode.

Pour inscrire la méthode, ajoutez l’attribut d’assembly `FunctionsStartup` qui spécifie le nom de type utilisé lors du démarrage.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>Mises en garde

Des étapes d’inscription s’exécutent avant et après le traitement de la classe de démarrage par le runtime. Vous devez donc garder à l’esprit les considérations suivantes :

- *La classe de démarrage est destinée uniquement à la configuration et à l’inscription.* Évitez d’utiliser des services inscrits au cours du processus de démarrage. Par exemple, n’essayez pas de consigner un message dans un enregistreur d’événements inscrit lors du démarrage. Cette étape du processus d’inscription est trop précoce pour que vos services soient disponibles. Une fois la méthode `Configure` exécutée, le runtime Functions continue d’inscrire des dépendances supplémentaires, ce qui peut affecter le fonctionnement de vos services.

- *Le conteneur d’injection de dépendances contient uniquement des types inscrits explicitement*. Les seuls services disponibles en tant que types injectables sont ceux qui sont configurés dans la méthode `Configure`. Par conséquent, des types spécifiques d’Azure Functions, tels que `BindingContext` et `ExecutionContext`, ne sont pas disponibles pendant la configuration ou en tant que types injectables.

## <a name="use-injected-dependencies"></a>Utiliser les dépendances injectées

L’injection de constructeur est utilisée pour rendre vos dépendances disponibles dans une fonction. L’utilisation de l’injection de constructeur requiert que vous n’utilisiez pas de classes statiques.

L’exemple suivant montre comment les dépendances `IMyService` et `HttpClient` sont injectées dans une fonction déclenchée via HTTP. Cet exemple utilise le package [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) requis pour inscrire un `HttpClient` au démarrage.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
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

## <a name="service-lifetimes"></a>Durées de service

Les durées de service des applications Azure Functions sont identiques à celles du service d’[injection de dépendance ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes). Pour une application Azure Functions, les différentes durées de vie de service se comportent comme suit :

- **Temporaire** : Des services temporaires sont créés à chaque demande du service.
- **Inclus dans l’étendue** : La durée de vie du service étendu correspond à celle d’exécution de la fonction. Les services délimités sont créés une fois par exécution. Les demandes ultérieures pour ce service pendant l’exécution réutilisent l’instance de service existante.
- **Singleton** : La durée de vie de service singleton correspond à celle de l’hôte, et est réutilisée entre les exécutions de la fonction sur cette instance. Les services à durée de vie singleton sont recommandés pour des connexions et des clients, par exemple, pour des instances `SqlConnection` ou `HttpClient`.

Affichez ou téléchargez un exemple [des différentes durées de vie de service](https://aka.ms/functions/di-sample) sur GitHub.

## <a name="logging-services"></a>Services de journalisation

Si vous avez besoin de votre propre fournisseur de journalisation, inscrivez un type personnalisé en tant qu’instance `ILoggerProvider`. Application Insights est ajouté automatiquement par Azure Functions.

> [!WARNING]
> - N’ajoutez pas `AddApplicationInsightsTelemetry()` à la collection de services, car il enregistre des services en conflit avec les services fournis par l’environnement.
> - N’inscrivez pas votre propre `TelemetryConfiguration` ou `TelemetryClient` si vous utilisez la fonctionnalité intégrée Application Insights. Si vous devez configurer votre propre instance `TelemetryClient`, créez-en une via la `TelemetryConfiguration` injectée, comme indiqué dans [Superviser Azure Functions](./functions-monitoring.md#version-2x-and-later-2).

## <a name="function-app-provided-services"></a>Services fournis par Function App

L’hôte de la fonction inscrit de nombreux services. Les services suivants peuvent être injectés comme dépendances en toute sécurité dans votre application :

|Type de service|Durée de vie|Description|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Configuration du runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Chargé de fournir l’ID de l’instance d’hôte|

Si vous souhaitez voir d’autres services pris en charge par l’injection de dépendances, [créez un ticket et proposez-les sur GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Remplacement des services de l’hôte

Le remplacement des services fournis par l’hôte n’est pas pris en charge actuellement.  Si vous souhaitez remplacer des services, [créez un ticket et proposez-les sur GitHub](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Utilisation d’options et de paramètres

Des valeurs définies dans les [paramètres de l’application](./functions-how-to-use-azure-function-app-settings.md#settings) sont disponibles dans une instance `IConfiguration`, ce qui vous permet de lire les valeurs de paramètres d’application dans la classe de démarrage.

Vous pouvez extraire des valeurs de l’instance `IConfiguration` dans un type personnalisé. La copie des valeurs de paramètres d’application dans un type personnalisé facilite le test de vos services en rendant ces valeurs injectables. Les paramètres lus dans l’instance de configuration doivent être des paires clé-valeur simples.

Considérons la classe suivante qui comprend une propriété nommée cohérente avec un paramètre d’application :

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

Et un fichier `local.settings.json` qui pourrait structurer le paramètre personnalisé comme suit :
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

À partir de la méthode `Startup.Configure`, vous pouvez extraire des valeurs de l’instance `IConfiguration` dans votre type personnalisé à l’aide du code suivant :

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

L’appel de `Bind` copie des valeurs dont les noms de propriété correspondent de la configuration vers l’instance personnalisée. L’instance options est désormais disponible dans le conteneur IoC pour injection dans une fonction.

L’objet d’options est injecté dans la fonction en tant qu’instance de l’ interface `IOptions` générique. Utilisez la propriété `Value` pour accéder aux valeurs figurant dans votre configuration.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Pour plus d’informations sur l’utilisation des options, voir [Modèle Options dans ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

> [!WARNING]
> Évitez d’essayer de lire les valeurs dans les fichiers comme *local.settings.json* ou *appsettings.{environnement}.json* dans le plan Consommation. Les valeurs lues à partir de ces fichiers associés à des connexions de déclencheurs ne sont pas disponibles à mesure que l’application est mise à l’échelle parce que l’infrastructure d’hébergement n’a pas accès aux informations de configuration.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

- [Surveiller votre application de fonction](functions-monitoring.md)
- [Meilleures pratiques pour Functions](functions-best-practices.md)
