---
title: Utiliser l’injection de dépendances dans .NET Azure Functions
description: Découvrez comment utiliser l’injection de dépendances de l’inscription et à l’aide des services dans les fonctions .NET
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: fonctions azure, fonctions, architecture sans serveur
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2019
ms.author: jehollan
ms.openlocfilehash: 1ef688dff65dc11f875f76e2f9127bf89af2f2b9
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074589"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Utiliser l’injection de dépendances dans .NET Azure Functions

Azure Functions prend en charge le modèle de conception logicielle dépendance (DI) de l’injection de code, qui est une technique permettant d’atteindre [Inversion de contrôle (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.

Azure Functions s’appuie sur les fonctionnalités de l’Injection de dépendances ASP.NET Core.  Vous devez comprendre les services, les durées de vie et les modèles de conception de [l’injection de dépendances ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) avant de les utiliser dans les fonctions.

## <a name="installing-dependency-injection-packages"></a>Installation des packages de l’injection de dépendance

Pour utiliser les fonctionnalités de l’injection de dépendance, vous devez inclure le package NuGet qui expose ces API.

```powershell
Install-Package Microsoft.Azure.Functions.Extensions
```

## <a name="registering-services"></a>Enregistrement des services

Pour inscrire les services, vous pouvez créer une méthode configure et ajouter des composants à un `IFunctionsHostBuilder` instance.  L’hôte Azure Functions crée un `IFunctionsHostBuilder` et le passe directement dans votre méthode configurée.

Pour inscrire votre configurer la méthode, vous devez ajouter un attribut d’assembly qui spécifie le type de votre configuration à l’aide de la méthode le `FunctionsStartup` attribut.

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

## <a name="service-lifetimes"></a>Durées de vie de service

Les applications de fonction Azure fournissent les mêmes durées de vie de service en tant que [l’Injection de dépendances ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes), temporaires, délimitées et singleton.

Dans une application de fonction, une durée de vie du service délimité correspond à une durée de vie de l’exécution de fonction. Services délimités sont créés une fois par exécution.  Les demandes ultérieures pour ce service pendant l’exécution réutiliser cette instance.  Une durée de vie du service singleton correspond à la durée de vie d’hôte et est réutilisée entre les exécutions de la fonction sur cette instance.

Services de durée de vie singleton sont recommandés pour les connexions et les clients, par exemple un `SqlConnection`, `CloudBlobClient`, ou `HttpClient`.

Affichez ou téléchargez un [exemple des durées de vie de service différents](http://aka.ms/functions/di-sample).

## <a name="logging-services"></a>Services de journalisation

Si vous avez besoin de votre propre fournisseur de journalisation, la méthode recommandée consiste à inscrire une `ILoggerProvider`.  Pour Application Insights, Functions ajoute automatiquement Application Insights pour vous.  

> [!WARNING]
> N’ajoutez pas `AddApplicationInsightsTelemetry()` aux services collection car elle enregistre les services qui seront en conflit avec ce qui est fournie par l’environnement. 
 
## <a name="function-app-provided-services"></a>Services d’application fournie (fonction)

L’hôte de la fonction s’inscrit de nombreux services.  Vous trouverez ci-dessous des services qui prennent une dépendance sur en toute sécurité.  Autres services de l’hôte ne sont pas pris en charge pour inscrire ou dépendent.  Si d’autres services que vous souhaitez prendre une dépendance, veuillez [créer un problème et la discussion sur GitHub](https://github.com/azure/azure-functions-host).

|Type de service|Durée de vie|Description|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|singleton|Configuration du runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|singleton|Chargé de fournir l’ID de l’instance d’hôte|

### <a name="overriding-host-services"></a>Substitution des Services de l’hôte

Remplacement des services fournis par l’hôte n’est pas pris en charge actuellement.  Si vous souhaitez substituer des services, consultez [créer un problème et la discussion sur GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Guide pratique pour surveiller votre application de fonction](functions-monitoring.md)
* [Meilleures pratiques pour les fonctions](functions-best-practices.md)