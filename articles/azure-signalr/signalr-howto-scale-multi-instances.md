---
title: Mise à l’échelle avec plusieurs instances de Service Azure SignalR
description: Dans de nombreux scénarios de mise à l’échelle, le client doit souvent gérer plusieurs instances et configurer pour les utiliser ensemble, pour créer un déploiement à grande échelle. Par exemple, partitionnement nécessite que la prise en charge de plusieurs instances.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: e284a0492774e02cab79db6d9006c1718a7fcfc9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809130"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Mise à l’échelle SignalR Service avec plusieurs instances ?
La dernière version du SDK de Service de SignalR prend en charge plusieurs points de terminaison pour les instances de SignalR Service. Vous pouvez utiliser cette fonctionnalité à l’échelle les connexions simultanées, ou utiliser pour la messagerie entre les régions.

## <a name="for-aspnet-core"></a>Pour ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Comment ajouter plusieurs points de terminaison à partir de la configuration ?

Configuration avec la clé `Azure:SignalR:ConnectionString` ou `Azure:SignalR:ConnectionString:` pour la chaîne de connexion de SignalR Service.

Si la clé commence par `Azure:SignalR:ConnectionString:`, il doit être au format `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, où `Name` et `EndpointType` sont des propriétés de la `ServiceEndpoint` de l’objet et sont accessibles à partir du code.

Vous pouvez ajouter plusieurs chaînes de connexion d’instance à l’aide de ce qui suit `dotnet` commandes :

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Comment ajouter plusieurs points de terminaison à partir du code ?

Un `ServicEndpoint` classe est introduite pour décrire les propriétés d’un point de terminaison de Service Azure SignalR.
Vous pouvez configurer plusieurs points de terminaison instance lors de l’utilisation du SDK de Service Azure SignalR via :
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>Comment personnaliser le routeur de point de terminaison ?

Par défaut, le SDK utilise le [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) pour sélectionner des points de terminaison.

#### <a name="default-behavior"></a>Comportement par défaut 
1. Routage des demandes de client

    Lorsque client `/negotiate` avec le serveur d’application. Par défaut, le Kit de développement logiciel **sélectionne de manière aléatoire** un point de terminaison de l’ensemble de points de terminaison de service disponibles.

2. Routage des messages de serveur

    Lors de la * envoi de message à un spécifique ** connexion *** et la connexion cible est acheminé vers le serveur actuel, le message est envoyé directement à ce point de terminaison connecté. Sinon, les messages sont diffusées à chaque point de terminaison Azure SignalR.

#### <a name="customize-routing-algorithm"></a>Personnaliser l’algorithme de routage
Vous pouvez créer votre propre routeur lorsque vous avez des connaissances spéciales pour identifier les messages doivent accéder à quels points de terminaison.

Un routeur personnalisé est défini ci-dessous, par exemple lorsque groupes commençant par `east-` toujours accéder au point de terminaison nommé `east`:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

Un autre exemple ci-dessous, qui remplace la valeur par défaut négocier le comportement, pour sélectionner les points de terminaison dépend où se trouve le serveur d’applications.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

N’oubliez pas d’inscrire le routeur pour le conteneur d’injection de dépendances à l’aide :

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>Pour ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Comment ajouter plusieurs points de terminaison à partir de la configuration ?

Configuration avec la clé `Azure:SignalR:ConnectionString` ou `Azure:SignalR:ConnectionString:` pour la chaîne de connexion de SignalR Service.

Si la clé commence par `Azure:SignalR:ConnectionString:`, il doit être au format `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, où `Name` et `EndpointType` sont des propriétés de la `ServiceEndpoint` de l’objet et sont accessibles à partir du code.

Vous pouvez ajouter plusieurs chaînes de connexion d’instance pour `web.config`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Comment ajouter plusieurs points de terminaison à partir du code ?

Un `ServicEndpoint` classe est introduite pour décrire les propriétés d’un point de terminaison de Service Azure SignalR.
Vous pouvez configurer plusieurs points de terminaison instance lors de l’utilisation du SDK de Service Azure SignalR via :

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>Comment personnaliser le routeur ?

La seule différence entre ASP.NET SignalR et ASP.NET Core SignalR est le type de contexte http pour `GetNegotiateEndpoint`. Pour ASP.NET SignalR, il s’agit de [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) type.

Voici personnalisé négocier exemple SignalR ASP.NET :

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

N’oubliez pas d’inscrire le routeur pour le conteneur d’injection de dépendances à l’aide :

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>Configuration dans les scénarios entre les régions

Le `ServiceEndpoint` objet possède un `EndpointType` propriété avec la valeur `primary` ou `secondary`.

`primary` points de terminaison sont des points de terminaison préférés pour recevoir le trafic de client et sont considérés comme ayant des connexions réseau plus fiables ; `secondary` points de terminaison sont considérés comme ayant des connexions réseau moins fiables et sont utilisés uniquement pour le serveur prise au trafic de client, par exemple, diffusion des messages, mais pas pour le client prise pour le trafic du serveur.

Dans le cas de plusieurs régions, le réseau peut être instable. Pour le serveur une seule application situé dans *est des États-Unis*, le point de terminaison de SignalR Service situé dans le même *est des États-Unis* région peut être configurée en tant que `primary` et points de terminaison dans d’autres régions marquées comme `secondary`. Dans cette configuration, les points de terminaison de service dans d’autres régions peuvent **réception** messages à partir de ce *est des États-Unis* du serveur d’applications, mais il n’y aura aucune **inter-régions** clients acheminé vers ce serveur d’applications. L’architecture est illustrée dans le diagramme ci-dessous :

![Géo-Infra](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Quand un client tente de `/negotiate` avec le serveur d’application, avec le routeur par défaut, Kit de développement logiciel **sélectionne de manière aléatoire** un point de terminaison de l’ensemble de disponible `primary` points de terminaison. Lorsque le point de terminaison est disponible, puis SDK **sélectionne de manière aléatoire** à partir de tous disponibles `secondary` points de terminaison. Le point de terminaison est marqué comme **disponible** lorsque la connexion entre le serveur et le point de terminaison de service est active.

Dans le scénario entre les régions, quand un client tente de `/negotiate` avec le serveur d’application hébergé dans *est des États-Unis*, par default il retourne toujours le `primary` point de terminaison situés dans la même région. Lorsque tous les *est des États-Unis* points de terminaison ne sont pas disponibles, le client est redirigé vers les points de terminaison dans d’autres régions. Section de basculement ci-dessous décrit le scénario en détail.

![Négocier normal](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Basculement

Lorsque tous les `primary` points de terminaison ne sont pas disponibles, de client `/negotiate` sélectionne parmi les `secondary` points de terminaison. Ce mécanisme de basculement requiert que chaque point de terminaison doit servir `primary` du serveur d’applications au moins un point de terminaison.

![Basculement](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez appris comment configurer plusieurs instances dans la même application pour la mise à l’échelle, partitionnement et les scénarios entre les régions.

Prend en charge de plusieurs points de terminaison permettre également servir dans les scénarios de récupération d’urgence et disponibilité élevées.

> [!div class="nextstepaction"]
> [Le programme d’installation SignalR Service pour la récupération d’urgence et haute disponibilité](./signalr-concept-disaster-recovery.md)
