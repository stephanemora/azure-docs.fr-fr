---
title: Configurer des applications Windows ASP.NET Core
description: Découvrez comment configurer une application ASP.NET Core dans les instances Windows natives d’App Service. Cet article présente les tâches de configuration les plus courantes.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 5819fc5b2d6e64d1812dacd88a2a4f840f6e03c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907927"
---
# <a name="configure-a-windows-aspnet-core-app-for-azure-app-service"></a>Configurer une application ASP.NET Core Windows pour Azure App Service

> [!NOTE]
> Pour ASP.NET dans .NET Framework, consultez [Configurer une application ASP.NET pour Azure App Service](configure-language-dotnet-framework.md)

Les applications ASP.NET Core doivent être déployées pour Azure App Service en tant que fichiers binaires compilés. L’outil de publication de Visual Studio génère la solution, puis déploie directement les fichiers binaires compilés, alors que le moteur de déploiement d’App Service déploie en premier le référentiel de code, puis compile les fichiers binaires. Pour plus d’informations sur les applications Linux, consultez [Configurer une application ASP.NET Core Linux pour Azure App Service](containers/configure-language-dotnetcore.md).

Ce guide fournit des concepts clés et des instructions pour les développeurs ASP.NET Core. Si vous n’avez jamais utilisé Azure App Service, commencez par suivre le [Guide de démarrage rapide d’ASP.NET](app-service-web-get-started-dotnet.md) et le [Didacticiel sur ASP.NET Core avec SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md).

## <a name="show-supported-net-core-runtime-versions"></a>Afficher les versions du runtime .NET Core prises en charge

Dans App Service, toutes les versions de .NET Core prises en charge sont déjà installées sur les instances Windows. Pour afficher le runtime .NET Core et les versions du Kit de développement logiciel (SDK) disponibles, accédez à `https://<app-name>.scm.azurewebsites.net/DebugConsole` et exécutez la commande suivante dans la console basée sur navigateur :

```azurecli-interactive
dotnet --info
```

## <a name="set-net-core-version"></a>Définir la version de .NET Core

Définissez la version cible de .NET Framework dans le fichier projet de votre projet ASP.NET Core. Pour plus d’informations, consultez [Sélectionner la version .NET Core à utiliser](https://docs.microsoft.com/dotnet/core/versions/selection) dans la documentation .NET Core.

## <a name="access-environment-variables"></a>Accéder aux variables d’environnement

Dans App Service, vous pouvez [définir les paramètres de l’application](configure-common.md#configure-app-settings) en dehors de votre code d’application. Vous pouvez ensuite y accéder dans n’importe quelle classe en utilisant le modèle d’injection de dépendances ASP.NET Core standard :

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Si vous configurez un paramètre d’application portant le même nom dans App Service et dans *appsettings.json*, la valeur d’App Service est prioritaire sur la valeur de *appsettings.json*. La valeur de locale de *appsettings.json* vous permet de déboguer l’application localement, tandis que la valeur d’App Service vous permet d’exécuter l’application dans le produit avec les paramètres de production. Les chaînes de connexion fonctionnent de la même façon. De cette façon, vous pouvez conserver les secrets de votre application en dehors de votre référentiel de code et accéder aux valeurs appropriées sans modifier votre code.

> [!NOTE]
> Notez que l’accès aux [données de configuration hiérarchiques](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data) dans *appsettings.json* se fait à l’aide du délimiteur `:` qui est standard pour .NET Core. Pour remplacer un paramètre de configuration hiérarchique spécifique dans App Service, définissez le nom du paramètre d’application avec le même format délimité dans la clé. Vous pouvez exécuter l’exemple suivant dans le service [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>Déployer des solutions à projets multiples

Quand une solution Visual Studio comprend plusieurs projets, le processus de publication de Visual Studio comprend déjà la sélection du projet à déployer. Lorsque vous déployez vers le moteur de déploiement App Service, comme c’est le cas avec Git ou Zip Deploy, avec l’automation de la génération activée, le moteur de déploiement App Service sélectionne le premier site web ou projet d’application web qu’il trouve en tant qu’application App Service. Vous pouvez spécifier le projet qu’App Service doit utiliser en spécifiant le paramètre d’application `PROJECT`. Par exemple, exécutez la commande suivante dans le service [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Accéder aux journaux de diagnostic

ASP.NET Core comprend un [fournisseur de journalisation intégré pour App Service](https://docs.microsoft.com/aspnet/core/fundamentals/logging/#azure-app-service). Dans le fichier *Program.cs* de votre projet, ajoutez le fournisseur à votre application à l’aide de la méthode d’extension `ConfigureLogging`, comme dans l’exemple suivant :

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Vous pouvez ensuite configurer et générer des journaux avec le [modèle .NET Core standard](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Pour plus d’informations sur la résolution des problèmes des applications ASP.NET Core dans App Service, consultez [Résolution des problèmes ASP.NET Core sur Azure App Service et IIS](https://docs.microsoft.com/aspnet/core/test/troubleshoot-azure-iis)

## <a name="get-detailed-exceptions-page"></a>Accéder à la page d’exceptions détaillées

Lorsque votre application ASP.NET Core génère une exception dans le débogueur Visual Studio, le navigateur affiche une page d’exceptions détaillées mais, dans App Service, cette page est remplacée par l’erreur **HTTP 500** générique **Une erreur s’est produite lors du traitement de votre demande**. . Pour afficher la page d’exceptions détaillées dans App Service, ajoutez le paramètre d’application `ASPNETCORE_ENVIRONMENT` à l’application en exécutant la commande suivante dans <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Détecter une session HTTPS

Dans App Service, une [terminaison SSL](https://wikipedia.org/wiki/TLS_termination_proxy) se produit au niveau des équilibreurs de charge réseau. Toutes les requêtes HTTPS accèdent donc à votre application en tant que requêtes HTTP non chiffrées. Si votre logique d’application doit savoir si les demandes utilisateur sont chiffrées ou non, configurez l’intergiciel Forwarded Headers dans *Startup.cs* :

- Configurez l’intergiciel avec [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` dans `Startup.ConfigureServices`.
- Ajoutez des plages d’adresses IP privées aux réseaux connus afin que l’intergiciel puisse approuver l’équilibreur de charge d’App Service.
- Appelez la méthode [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) dans `Startup.Configure` avant les autres intergiciels.

Assemblez les trois éléments ; le code ressemble à l’exemple suivant :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

Pour plus d’informations, consultez l’article [Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : application ASP.NET Core avec SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
