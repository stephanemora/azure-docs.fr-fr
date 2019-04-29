---
title: Configurer des applications ASP.NET Core - Azure App Service | Microsoft Docs
description: Découvrez comment configurer les applications ASP.NET Core fonctionne dans Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: e203877b2bc939c1d7fb9390df39f3e2451d12d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852310"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Configurer une Linux application ASP.NET Core pour Azure App Service

Les applications ASP.NET Core doivent être déployées en tant que fichiers binaires compilés. L’outil de publication de Visual Studio génère la solution et déploie les fichiers binaires compilés directement, alors que le moteur de déploiement App Service déploie en premier le référentiel de code et compile ensuite les fichiers binaires.

Ce guide fournit des concepts clés et des instructions pour ASP.NET Core les développeurs qui utilisent un conteneur Linux intégré dans App Service. Si vous n’avez jamais utilisé Azure App Service, suivez les [Guide de démarrage rapide ASP.NET Core](quickstart-dotnetcore.md) et [ASP.NET Core avec le didacticiel de base de données SQL](tutorial-dotnetcore-sqldb-app.md) première.

## <a name="show-net-core-version"></a>Affiche la version de .NET Core

Pour afficher la version actuelle de .NET Core, exécutez la commande suivante le [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Pour afficher la prise en charge toutes les versions de .NET Core, exécutez la commande suivante le [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Définir la version .NET Core

Exécutez la commande suivante le [Cloud Shell](https://shell.azure.com) pour définir la version de .NET Core 2.1 :

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Accéder aux variables d’environnement

Dans App Service, vous pouvez [définir les paramètres de l’application](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) en dehors de votre code d’application. Vous pouvez ensuite accéder à l’aide du modèle ASP.NET standard :

```csharp
include Microsoft.Extensions.Configuration;
// retrieve App Service app setting
System.Configuration.ConfigurationManager.AppSettings["MySetting"]
// retrieve App Service connection string
Configuration.GetConnectionString("MyDbConnection")
```

Si vous configurez un paramètre d’application portant le même nom dans App Service et dans *Web.config*, la valeur de l’App Service est prioritaire sur la valeur de Web.config. La valeur de Web.config vous permet de déboguer l’application localement, mais la valeur de l’App Service permet à votre série de l’application dans le produit avec les paramètres de production. Chaînes de connexion fonctionnent de la même façon. De cette façon, vous pouvez conserver les secrets de votre application en dehors de votre référentiel de code et les valeurs appropriées d’accès sans modifier votre code.

## <a name="get-detailed-exceptions-page"></a>Obtenir la page des exceptions détaillées

Lorsque votre application ASP.NET génère une exception dans le débogueur Visual Studio, le navigateur affiche une page d’exception détaillées, mais dans App Service est remplacée par un générique **HTTP 500** erreur ou **une erreur s’est produite lors traitement de votre demande.** . Pour afficher la page d’exception détaillées dans App Service, ajoutez le `ASPNETCORE_ENVIRONMENT` paramètre d’application à votre application en exécutant la commande suivante dans le <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Détecter une session HTTPS

Dans App Service, une [terminaison SSL](https://wikipedia.org/wiki/TLS_termination_proxy) se produit au niveau des équilibreurs de charge réseau. Toutes les requêtes HTTPS accèdent donc à votre application en tant que requêtes HTTP non chiffrées. Si votre logique d’application a besoin de savoir si l’utilisateur demande sont chiffrés ou non, configurez l’intergiciel des en-têtes transférés dans *Startup.cs*:

- Configurer l’intergiciel (middleware) avec [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) pour transférer le `X-Forwarded-For` et `X-Forwarded-Proto` en-têtes dans `Startup.ConfigureServices`.
- Ajouter des plages d’adresses IP privées pour les réseaux connus, afin que l’intergiciel (middleware) peut faire confiance à l’équilibreur de charge App Service.
- Appeler le [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) méthode dans `Startup.Configure` avant d’appeler d’autres intergiciels.

Assembler les trois éléments, votre code ressemble à l’exemple suivant :

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

Pour plus d’informations, consultez [configurer ASP.NET Core pour travailler avec des serveurs proxy et équilibreurs de charge](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Déployer des solutions à projets multiples

Lorsque vous déployez un référentiel ASP.NET sur le moteur de déploiement avec un *.csproj* fichier dans le répertoire racine, le moteur déploie le projet. Lorsque vous déployez un référentiel ASP.NET avec un *.sln* fichier dans le répertoire racine, le moteur récupère le premier Site Web ou un projet d’Application Web qu’il trouve que l’application App Service. Il est possible pour le moteur ne pas à choisir le projet.

Pour déployer une solution à projets multiples, vous pouvez spécifier le projet à utiliser dans App Service de deux manières différentes :

### <a name="using-deployment-file"></a>À l’aide du fichier .deployment

Ajouter un *.deployment* fichier à la racine du référentiel, puis ajoutez le code suivant :

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>À l’aide des paramètres de l’application

Dans le <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>, ajoutez un paramètre d’application à votre application app Service en exécutant la commande CLI suivante. Remplacez  *\<nom de l’application >*,  *\<resource-group-name >*, et  *\<nom_projet >* avec les valeurs appropriées .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Accéder aux journaux de diagnostic

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Ouvrir une session SSH dans un navigateur

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : application ASP.NET Core avec SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Questions fréquentes (FAQ) sur App Service sur Linux](app-service-linux-faq.md)