---
title: Communication de service avec ASP.NET Core | Microsoft Docs
description: Découvrez comment utiliser ASP.NET Core dans Reliable Services avec et sans état.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 638c06e1854504dcb7ff34b1d9df56694556c421
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939796"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core dans Azure Service Fabric Reliable Services

ASP.NET Core est une infrastructure open source et multiplateforme. Cette infrastructure est conçue pour générer des applications en nuage, connecté à internet, telles que les applications web, applications IoT, et de backends mobiles.

Cet article constitue un guide détaillé pour héberger des services ASP.NET Core dans Reliable Services Service Fabric à l’aide de la **Microsoft.ServiceFabric.AspNetCore.** ensemble de packages NuGet.

Pour un didacticiel d’introduction sur ASP.NET Core dans Service Fabric et obtenir des instructions sur l’obtention de votre environnement de développement, consultez [didacticiel : Créer et déployer une application avec un service frontal API Web ASP.NET Core et un service principal avec état](service-fabric-tutorial-create-dotnet-app.md).

Le reste de cet article suppose que vous êtes déjà familiarisé avec ASP.NET Core. Dans le cas contraire, veuillez lire toutes les [notions de base ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core dans l’environnement Service Fabric

Les applications ASP.NET Core et Service Fabric peuvent s’exécuter sur .NET Core ou .NET Framework complet. Vous pouvez utiliser ASP.NET Core de deux manières différentes dans Service Fabric :
 - **Hébergée en tant qu’exécutable invité**. De cette façon est principalement utilisée pour exécuter des applications ASP.NET Core existantes sur Service Fabric sans modification du code.
 - **Exécuter à l’intérieur d’un service fiable**. De cette façon permet une meilleure intégration avec le runtime Service Fabric et ASP.NET Core avec état services.

Le reste de cet article explique comment utiliser ASP.NET Core à l’intérieur d’un service fiable, via les composants d’intégration ASP.NET Core fournis avec le SDK Service Fabric.

## <a name="service-fabric-service-hosting"></a>Hébergement du service Service Fabric

Dans Service Fabric, un ou plusieurs instances et/ou les réplicas de votre service s’exécutent dans un *processus hôte de service*: un fichier exécutable qui exécute le code de votre service. Vous, en tant que créateur du service, possédez le processus hôte de service et Service Fabric active et il surveille pour vous.

Traditionnellement, ASP.NET (jusqu’à MVC 5) est étroitement lié à IIS via System.Web.dll. ASP.NET Core fournit une séparation entre le serveur web et votre application web. Cette séparation permet aux applications web soit portable entre différents serveurs web. Il permet également de serveurs web *auto-hébergé*. Cela signifie que vous pouvez démarrer un serveur web dans votre propre processus, par opposition à un processus appartenant à un logiciel de serveur web dédié, par exemple IIS.

Pour combiner un Service service Fabric et ASP.NET, comme un exécutable invité ou dans un service fiable, vous devez être en mesure de démarrer ASP.NET à l’intérieur de votre processus hôte de service. L’auto-hébergement d’ASP.NET Core vous le permet.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hébergement d’ASP.NET Core dans un service fiable
En règle générale, les applications ASP.NET Core auto-hébergées créent une méthode WebHost dans le point d’entrée d’une application, par exemple la méthode `static void Main()` dans `Program.cs`. Dans ce cas, le cycle de vie de la méthode WebHost est lié au cycle de vie du processus.

![Hébergement d’ASP.NET Core dans un processus][0]

Mais le point d’entrée de l’application n’est pas au bon endroit pour créer une méthode WebHost dans un service fiable. C’est parce que le point d’entrée de l’application est uniquement utilisé pour inscrire un type de service avec le runtime Service Fabric, afin qu’il puisse créer des instances de ce type de service. La méthode WebHost doit être créé dans un service fiable lui-même. Dans le processus hôte de service, les instances de service et/ou réplicas peuvent passer par plusieurs cycles de vie. 

Une instance du service Reliable Service est représentée par votre classe de service dérivant de `StatelessService` ou `StatefulService`. La pile de communication d’un service est contenue dans une implémentation `ICommunicationListener` dans votre classe de service. Le `Microsoft.ServiceFabric.AspNetCore.*` les packages NuGet contiennent les implémentations de `ICommunicationListener` qui démarrent et gèrent le WebHost ASP.NET Core pour Kestrel ou HTTP.sys dans un service fiable.

![Diagramme pour l’hébergement ASP.NET Core dans un service fiable][1]

## <a name="aspnet-core-icommunicationlisteners"></a>Écouteurs ICommunicationListeners d’ASP.NET Core
Le `ICommunicationListener` implémentations pour Kestrel et HTTP.sys dans le `Microsoft.ServiceFabric.AspNetCore.*` les packages NuGet ont des modèles d’utilisation similaires. Mais ils effectuent des actions légèrement différentes propres à chaque serveur web. 

Les deux écouteurs de communications fournissent un constructeur qui accepte les arguments suivants :
 - **`ServiceContext serviceContext`** : Il s’agit du `ServiceContext` objet qui contient des informations sur le service en cours d’exécution.
 - **`string endpointName`** : Il s’agit du nom d’un `Endpoint` configuration dans le fichier ServiceManifest.xml. Il est essentiellement là que les deux écouteurs de communication diffèrent. HTTP.sys *requiert* un `Endpoint` configuration, tandis que Kestrel ne.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`** : Il s’agit d’une expression lambda que vous implémentez dans laquelle vous créez et renvoyez un `IWebHost`. Il vous permet de configurer `IWebHost` la façon que vous le feriez normalement dans une application ASP.NET Core. L’expression lambda fournit une URL qui est générée pour vous, selon les options d’intégration de Service Fabric que vous utilisez et le `Endpoint` configuration que vous fournissez. Vous pouvez ensuite modifier ou utiliser cette URL pour démarrer le serveur web.

## <a name="service-fabric-integration-middleware"></a>Intergiciel (middleware) d’intégration à Service Fabric
Le `Microsoft.ServiceFabric.AspNetCore` package NuGet inclut le `UseServiceFabricIntegration` méthode d’extension sur `IWebHostBuilder` qui ajoute l’intergiciel (middleware) Service Fabric – prenant en charge. Cet intergiciel (middleware) configure l’élément Kestrel ou HTTP.sys `ICommunicationListener` pour inscrire une URL de service unique auprès du Service d’affectation de noms de Service Fabric. Ensuite, il valide les demandes des clients pour garantir les clients se connectent au service approprié. 

Cette étape est nécessaire pour empêcher les clients de se connecter par erreur à un service incorrect. C’est parce que, dans un environnement hôte partagé comme Service Fabric, plusieurs applications web peuvent s’exécuter sur la même machine physique ou virtuelle, mais n’utilisent pas les noms d’hôte unique. Ce scénario est décrit plus en détail dans la section suivante.

### <a name="a-case-of-mistaken-identity"></a>Cas d’erreur d’identité
Quel que soit le protocole utilisé, les réplicas de service écoutent sur une combinaison IP:port unique. Une fois qu’un réplica de service a commencé à écouter sur un point de terminaison IP : port, il signale cette adresse de point de terminaison de Service Fabric Naming Service. Là, les clients ou autres services peuvent les découvrir. Si des services utilisent des ports d’application attribué dynamiquement, un réplica de service peut utiliser par hasard le même point de terminaison IP : port d’un autre service précédemment sur le même physique ou machine virtuelle. Cela peut provoquer la connexion d’un client au mauvais service. Ce scénario peut entraîner si la séquence d’événements suivante se produit :

 1. Le service A écoute sur 10.0.0.1:30000 via HTTP. 
 2. Client résout le Service A et obtient l’adresse 10.0.0.1:30000.
 3. Le service A passe à un autre nœud.
 4. Le service B est placé sur 10.0.0.1 et utilise par hasard le même port 30000.
 5. Le client essaie de se connecter au service A avec l’adresse mise en cache 10.0.0.1:30000.
 6. Client est maintenant connecté au service B, sans détecter qu’il est connecté à un service incorrect.

Cela peut provoquer des bogues de façon aléatoire qui peuvent être difficiles à diagnostiquer.

### <a name="using-unique-service-urls"></a>Utilisation d’URL de service uniques
Pour empêcher ces bogues, les services peuvent publier un point de terminaison au Service d’affectation de noms avec un identificateur unique et puis valider cet identificateur lors des demandes de client. Il s’agit d’une action de coopération entre les services dans un environnement approuvé de client non hostile. Il ne fournit pas d’authentification de service sécurisé dans un environnement de client hostile.

Dans un environnement approuvé, l’intergiciel (middleware) qui est ajouté par le `UseServiceFabricIntegration` méthode ajoute automatiquement un identificateur unique à l’adresse validée au Service d’affectation de noms. Il valide le fait que l’identificateur à chaque demande. Si l’identificateur ne correspond pas, l’intergiciel (middleware) retourne immédiatement une réponse HTTP 410 supprimé.

Les services qui utilisent un port attribué dynamiquement doivent avoir recours à cet intergiciel (middleware).

Les services qui utilisent un port unique fixe n’ont pas ce problème dans un environnement coopératif. Un port de ce type est généralement utilisé pour les services externes qui nécessitent un port connu pour que les applications clientes s’y connectent. Par exemple, la plupart des applications de web orienté internet utilisera le port 80 ou 443 pour les connexions de navigateur web. Dans ce cas, l’identificateur unique ne doit pas être activé.

Le diagramme suivant illustre le flux de demande avec l’intergiciel (middleware) activé :

![Intégration d’ASP.NET Core à Service Fabric][2]

Kestrel et HTTP.sys `ICommunicationListener` implémentations utilisent ce mécanisme de la même façon. Bien que HTTP.sys peut différencier en interne les requêtes basées sur les chemins d’accès URL uniques à l’aide de sous-jacent **HTTP.sys** fonctionnalité, qui est de la fonctionnalité de partage de port *pas* utilisé par le HTTP.sys `ICommunicationListener`implémentation. Cela survient car il en résulte dans les codes d’état erreur HTTP 503 et HTTP 404 dans le scénario décrit précédemment. Qui à son tour rend difficile pour les clients déterminer l’intention de l’erreur, comme HTTP 503 et HTTP 404 sont couramment utilisés pour indiquer d’autres erreurs. 

Par conséquent, Kestrel et HTTP.sys `ICommunicationListener` implémentations Standardisez intergiciel (middleware) fourni par le `UseServiceFabricIntegration` méthode d’extension. Par conséquent, les clients n’aient à effectuer une action de nouvelle résolution de point de terminaison de service sur les réponses HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HTTP.sys dans Reliable Services
Vous pouvez utiliser HTTP.sys dans Reliable Services en important le **Microsoft.ServiceFabric.AspNetCore.HttpSys** package NuGet. Ce package contient `HttpSysCommunicationListener`, une implémentation de `ICommunicationListener`. `HttpSysCommunicationListener` vous permet de créer un WebHost ASP.NET Core à l’intérieur d’un service fiable en tant que le serveur web à l’aide de HTTP.sys.

HTTP.sys est basé sur le [API du serveur HTTP Windows](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Cette API utilise le **HTTP.sys** pilote de noyau pour traiter les requêtes HTTP et de les acheminer vers les processus qui exécutent des applications web. Cela permet à plusieurs processus sur la même machine physique ou virtuel pour héberger des applications web sur le même port, éviter toute ambiguïté par soit un unique URL du chemin d’accès ou nom d’hôte. Ces fonctionnalités sont utiles dans Service Fabric pour héberger plusieurs sites web dans un même cluster.

>[!NOTE]
>Implémentation de HTTP.sys fonctionne uniquement sur la plateforme Windows.

Le diagramme suivant illustre comment HTTP.sys utilise le **HTTP.sys** pilote de noyau sur Windows pour le partage de port :

![Diagramme de HTTP.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP.sys dans un service sans état
Pour utiliser `HttpSys` dans un service sans état, remplacez la méthode `CreateServiceInstanceListeners` et retournez une instance `HttpSysCommunicationListener` :

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HTTP.sys dans un service avec état

`HttpSysCommunicationListener` n’est pas actuellement conçu pour une utilisation dans les services avec état en raison de complications liées à sous-jacent **HTTP.sys** fonctionnalité de partage de port. Pour plus d’informations, consultez la section suivante sur l’allocation de port dynamique avec HTTP.sys. Pour les services avec état, Kestrel est le serveur web suggéré.

### <a name="endpoint-configuration"></a>Configuration du point de terminaison

Un `Endpoint` configuration est requise pour les serveurs web qui utilisent l’API de serveur HTTP Windows, y compris de HTTP.sys. Les serveurs Web qui utilisent l’API de serveur HTTP Windows doivent d’abord réserver leur URL avec HTTP.sys (cela se produit généralement avec les [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) outil). 

Cette action requiert des privilèges élevés qui n’ont pas vos services par défaut. Les options « http » ou « https » pour le `Protocol` propriété de la `Endpoint` de configuration dans le fichier ServiceManifest.xml sont utilisées spécifiquement pour indiquer au runtime Service Fabric pour inscrire une URL avec HTTP.sys en votre nom. Pour ce faire à l’aide de la [ *caractère générique fort* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) préfixe d’URL.

Par exemple, pour réserver `http://+:80` pour un service, utilisez la configuration suivante dans le fichier ServiceManifest.xml :

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

Par ailleurs, le nom du point de terminaison doit être transmis au constructeur `HttpSysCommunicationListener` :

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Utiliser HTTP.sys avec un port statique
Pour utiliser un port statique avec HTTP.sys, indiquez le numéro de port dans le `Endpoint` configuration :

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Utiliser HTTP.sys avec un port dynamique
Pour utiliser un port affecté dynamiquement avec HTTP.sys, omettez la `Port` propriété dans le `Endpoint` configuration :

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Un port dynamique alloué par un `Endpoint` configuration fournit uniquement un seul port *par processus hôte*. Le modèle d’hébergement Service Fabric actuel permet plusieurs instances de service et/ou réplicas d’être hébergés dans le même processus. Cela signifie que chacun d’eux partagera le même port lors de l’allocation par le biais du `Endpoint` configuration. Plusieurs **HTTP.sys** instances peuvent partager un port à l’aide de sous-jacent **HTTP.sys** fonctionnalité de partage de port. Mais il n’est pas pris en charge par `HttpSysCommunicationListener` en raison des complications qu’elle induit pour les demandes des clients. Pour l’utilisation des ports dynamiques, Kestrel est le serveur web suggéré.

## <a name="kestrel-in-reliable-services"></a>Kestrel dans Reliable Services
Vous pouvez utiliser Kestrel dans Reliable Services en important le **Microsoft.ServiceFabric.AspNetCore.Kestrel** package NuGet. Ce package contient `KestrelCommunicationListener`, une implémentation de `ICommunicationListener`. `KestrelCommunicationListener` vous permet de créer un WebHost ASP.NET Core à l’intérieur d’un service fiable à l’aide de Kestrel en tant que le serveur web.

Kestrel est un serveur web multiplateforme pour ASP.NET Core basé sur libuv, une bibliothèque d’E/S asynchrone multiplateforme. Contrairement à HTTP.sys, Kestrel n’utilise pas un gestionnaire de points de terminaison centralisé. Également Contrairement à HTTP.sys, Kestrel ne prend en charge le partage de port entre plusieurs processus. Chaque instance de Kestrel doit utiliser un port unique.

![Diagramme de kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel dans un service sans état
Pour utiliser `Kestrel` dans un service sans état, remplacez la méthode `CreateServiceInstanceListeners` et retournez une instance `KestrelCommunicationListener` :

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Kestrel dans un service avec état
Pour utiliser `Kestrel` dans un service avec état, remplacez la méthode `CreateServiceReplicaListeners` et retournez une instance `KestrelCommunicationListener` :

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

Dans cet exemple, une instance singleton de `IReliableStateManager` est fournie au conteneur d’injection de dépendance WebHost. Ce n’est pas strictement nécessaire, mais il vous permet d’utiliser `IReliableStateManager` et Reliable Collections dans vos méthodes d’action de contrôleur MVC.

*Aucun* nom de configuration `Endpoint` n’est fourni à `KestrelCommunicationListener` dans un service avec état. La section suivante contient une explication plus détaillée à ce sujet.

### <a name="configure-kestrel-to-use-https"></a>Configurer Kestrel pour l’utilisation de HTTPS
Lorsque vous activez HTTPS avec Kestrel dans votre service, vous devrez définir plusieurs options à l’écoute. Mise à jour le `ServiceInstanceListener` à utiliser un *EndpointHttps* point de terminaison et qu’il écoute un port spécifique (tel que le port 443). Lors de la configuration de l’hôte web pour utiliser le serveur web Kestrel, vous devez configurer Kestrel pour écouter les adresses IPv6 sur toutes les interfaces réseau : 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Pour obtenir un exemple complet dans un didacticiel, consultez [Kestrel de configurer pour utiliser HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Configuration du point de terminaison
Un `Endpoint` configuration n’est pas obligée d’utiliser Kestrel. 

Kestrel est un serveur web autonome simple. Contrairement à HTTP.sys (ou HttpListener), il n’a pas besoin un `Endpoint` configuration dans le fichier ServiceManifest.xml, car il ne nécessite pas l’inscription d’URL avant de commencer. 

#### <a name="use-kestrel-with-a-static-port"></a>Utiliser Kestrel avec un port statique
Vous pouvez configurer un port statique dans le `Endpoint` configuration du fichier ServiceManifest.xml pour une utilisation avec Kestrel. Bien que cela n’est pas strictement nécessaire, il offre deux avantages potentiels :
 - Si le port ne tombe pas dans la plage de ports d’application, il est ouvert via le pare-feu du système d’exploitation par Service Fabric.
 - L’URL qui vous est fournie via `KestrelCommunicationListener` utilise ce port.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Si un `Endpoint` est configuré, son nom doit être transmis au constructeur `KestrelCommunicationListener` : 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Si le fichier ServiceManifest.xml n’utilise pas un `Endpoint` configuration, omettez le nom dans la `KestrelCommunicationListener` constructeur. Dans ce cas, il utilise un port dynamique. Consultez la section suivante pour plus d’informations à ce sujet.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Utiliser Kestrel avec un port dynamique
Kestrel ne peut pas utiliser l’attribution de port automatique à partir de la `Endpoint` configuration dans le fichier ServiceManifest.xml. C’est parce qu’automatique port affectation à partir d’un `Endpoint` configuration affecte un port unique par *processus hôte*, et un processus hôte unique peut contenir plusieurs instances de Kestrel. Cela ne fonctionne pas avec Kestrel, car il ne prend pas en charge le partage de port. Par conséquent, chaque instance de Kestrel doit être ouvert sur un port unique.

Pour utiliser l’affectation de port dynamique avec Kestrel, omettez la `Endpoint` configuration dans le fichier ServiceManifest.xml entièrement et ne transmettez pas un nom de point de terminaison pour le `KestrelCommunicationListener` constructeur, comme suit :

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Dans cette configuration, `KestrelCommunicationListener` sélectionne automatiquement un port inutilisé dans la plage de ports d’application.

## <a name="service-fabric-configuration-provider"></a>Fournisseur de configuration de service Fabric
Configuration de l’application dans ASP.NET Core est basée sur des paires clé-valeur établies par le fournisseur de configuration. Lecture [Configuration dans ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) pour comprendre plus sur ASP.NET Core configuration prise en charge générale.

Cette section décrit comment le fournisseur de configuration de Service Fabric s’intègre avec la configuration d’ASP.NET Core en important le `Microsoft.ServiceFabric.AspNetCore.Configuration` package NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Extensions de démarrage AddServiceFabricConfiguration
Après avoir importé le `Microsoft.ServiceFabric.AspNetCore.Configuration` package NuGet, vous devez inscrire la source de Configuration de Service Fabric avec l’API de configuration ASP.NET Core. Pour cela, recherchez **AddServiceFabricConfiguration** extensions dans le `Microsoft.ServiceFabric.AspNetCore.Configuration` espace de noms par rapport à `IConfigurationBuilder`.

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

Désormais le service ASP.NET Core peut accéder aux paramètres de configuration de Service Fabric, tout comme les autres paramètres de l’application. Par exemple, vous pouvez utiliser le modèle d’options pour charger les paramètres dans des objets fortement typés.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Mappage de clé par défaut
Par défaut, le fournisseur de configuration de Service Fabric inclut le nom du package, le nom de la section et le nom de propriété. Ensemble, elles forment la clé de configuration ASP.NET Core, comme suit :
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Par exemple, si vous disposez d’un package de configuration nommé `MyConfigPackage` avec le contenu suivant, puis la valeur de configuration seront disponible sur ASP.NET Core `IConfiguration` via *MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Options de configuration de service Fabric
Le fournisseur de configuration de Service Fabric prend également en charge `ServiceFabricConfigurationOptions` pour modifier le comportement par défaut de mappage de clé.

#### <a name="encrypted-settings"></a>Paramètres chiffrés
Service Fabric prend en charge les paramètres chiffrés, comme le fait le fournisseur de configuration de Service Fabric. Les paramètres chiffrés ne sont pas déchiffrés à ASP.NET Core `IConfiguration` par défaut. Les valeurs chiffrées sont stockées à la place. Mais si vous souhaitez déchiffrer la valeur à stocker dans ASP.NET Core IConfiguration, vous pouvez définir le *DecryptValue* indicateur sur false dans le `AddServiceFabricConfiguration` extension, comme suit :

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = true); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Plusieurs packages de configuration
Service Fabric prend en charge plusieurs packages de configuration. Par défaut, le nom du package est inclus dans la clé de configuration. Mais vous pouvez définir le `IncludePackageName` sur false, comme suit :
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Mappage de clés personnalisé, l’extraction de valeur et de remplissage des données
Le fournisseur de configuration de Service Fabric prend également en charge des scénarios plus avancés pour personnaliser le mappage de clés avec `ExtractKeyFunc` et personnalisé extrait les valeurs avec `ExtractValueFunc`. Vous pouvez même modifier l’ensemble du processus de remplissage des données à partir de la configuration de Service Fabric à la configuration d’ASP.NET Core à l’aide de `ConfigAction`.

Les exemples suivants illustrent comment utiliser `ConfigAction` pour personnaliser le remplissage des données :
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>Mises à jour de configuration
Le fournisseur de configuration de Service Fabric prend également en charge les mises à jour de configuration. Vous pouvez utiliser ASP.NET Core `IOptionsMonitor` pour recevoir des notifications de modification, puis utilisez `IOptionsSnapshot` pour recharger les données de configuration. Pour plus d’informations, consultez [options d’ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Ces options sont prises en charge par défaut. Aucune autre codage est nécessaire pour activer les mises à jour de configuration.

## <a name="scenarios-and-configurations"></a>Scénarios et configurations
Cette section fournit la combinaison de serveur web, la configuration de port, options d’intégration de Service Fabric et divers paramètres, que nous vous recommandons de résoudre les scénarios suivants :
 - Services sans état d’ASP.NET Core exposés en externe
 - Interne uniquement les services sans état ASP.NET Core
 - Interne uniquement les services avec état ASP.NET Core

Un **exposé en externe service** expose un point de terminaison qui est appelée depuis l’extérieur du cluster, généralement par le biais d’un équilibreur de charge.

Un **interne uniquement** service est un point de terminaison est appelée uniquement à partir du cluster.

> [!NOTE]
> Généralement, les points de terminaison de service avec état ne doit pas être exposés à internet. Clusters derrière des équilibreurs de charge qui n’ont pas conscience de la résolution de service Service Fabric, tels que Azure Load Balancer, ne pourront pas exposer les services avec état. Voilà, car l’équilibreur de charge n’est pas en mesure de localiser et d’acheminer le trafic vers le réplica de service avec état approprié. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Services sans état d’ASP.NET Core exposés en externe
Kestrel est le serveur web suggéré pour les services frontaux qui exposent des points de terminaison HTTP externes, accessible sur internet. Sur Windows, HTTP.sys peut fournir des fonctionnalités de partage de port, ce qui vous permet d’héberger plusieurs services web sur le même ensemble de nœuds à l’aide de la même port. Dans ce scénario, les services web sont différenciées par nom d’hôte ou le chemin d’accès, sans se baser sur un proxy frontal ou une passerelle pour assurer le routage HTTP.
 
Lorsqu’elle est exposée à internet, un service sans état doit utiliser un point de terminaison connu et stable qui est accessible via un équilibreur de charge. Vous allez fournir cet URL aux utilisateurs de votre application. Nous vous recommandons la configuration suivante :

|  |  | **Remarques** |
| --- | --- | --- |
| Serveur web | Kestrel | Kestrel est le serveur web par défaut, elle est prise en charge sur Windows et Linux. |
| Configuration du port | statique | Un port statique connu doit être défini dans la configuration `Endpoints` du fichier ServiceManifest.xml, par exemple 80 pour HTTP et 443 pour HTTPS. |
| ServiceFabricIntegrationOptions | Aucun | Utilisez la `ServiceFabricIntegrationOptions.None` option lors de la configuration d’intergiciel d’intégration de Service Fabric, afin que le service ne tente pas de valider les demandes entrantes pour un identificateur unique. Les utilisateurs externes de votre application ne sont pas connaître les informations d’identification uniques qui utilise l’intergiciel (middleware). |
| Nombre d'instances | -1 | Dans les scénarios d’utilisation classiques, le paramètre du nombre d’instance doit être défini *-1*. Cela afin qu’une instance soit disponible sur tous les nœuds recevant le trafic provenant d’un équilibreur de charge. |

Si plusieurs services exposés en externe partagent le même ensemble de nœuds, vous pouvez utiliser HTTP.sys avec un chemin d’URL unique, mais stable. Pour cela, vous pouvez modifier l’URL fournie lors de la configuration d’IWebHost. Notez que cela s’applique à HTTP.sys uniquement.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Service d’ASP.NET Core sans état interne uniquement
Les services sans état qui sont appelés uniquement à partir du cluster doivent utiliser des URL uniques et des ports affectés dynamiquement afin d’assurer une coopération entre plusieurs services. Nous vous recommandons la configuration suivante :

|  |  | **Remarques** |
| --- | --- | --- |
| Serveur web | Kestrel | Bien que vous pouvez utiliser HTTP.sys pour les services sans état internes, Kestrel est le meilleur serveur pour permettre à plusieurs instances de service de partager un hôte.  |
| Configuration du port | affecté de manière dynamique | Plusieurs réplicas d’un service avec état susceptibles de partager un processus hôte ou un système d’exploitation hôte et nécessitent donc des ports uniques. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Avec l’affectation de port dynamique, ce paramètre empêche le problème d’erreur d’identité décrit précédemment. |
| InstanceCount | any | Le paramètre du nombre d’instances peut être défini sur toute valeur nécessaire au bon fonctionnement du service. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Service d’ASP.NET Core avec état interne uniquement
Les services avec état qui sont appelés uniquement à partir du cluster doivent utiliser des ports affectés dynamiquement afin d’assurer une coopération entre plusieurs services. Nous vous recommandons la configuration suivante :

|  |  | **Remarques** |
| --- | --- | --- |
| Serveur web | Kestrel | Le `HttpSysCommunicationListener` n’est pas conçu pour être utilisé par les services avec état dans lequel les réplicas partagent un processus hôte. |
| Configuration du port | affecté de manière dynamique | Plusieurs réplicas d’un service avec état susceptibles de partager un processus hôte ou un système d’exploitation hôte et nécessitent donc des ports uniques. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Avec l’affectation de port dynamique, ce paramètre empêche le problème d’erreur d’identité décrit précédemment. |

## <a name="next-steps"></a>Étapes suivantes
[Débogage de votre application Service Fabric à l’aide de Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
