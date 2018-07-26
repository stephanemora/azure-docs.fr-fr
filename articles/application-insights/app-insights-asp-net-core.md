---
title: Azure Application Insights pour ASP.NET Core | Microsoft Docs
description: Surveiller la disponibilité, les performances et l’utilisation.des applications Web.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: 6635906b6aae418fa3754f1152fae3e0d8903ffc
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989755"
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights pour ASP.NET Core

Azure Application Insights assure une surveillance approfondie de votre application web jusqu’au niveau du code. Azure Application Insights vous permet d’analyser facilement la disponibilité, les performances et l’utilisation. De plus, vous pouvez rapidement identifier et diagnostiquer les erreurs dans votre application sans attendre qu’un utilisateur ne les signale.

Cet article vous explique comment créer un exemple d’application de [pages Razor](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) ASP.NET Core dans Visual Studio et démarrer la surveillance avec Azure Application Insights.

## <a name="prerequisites"></a>Prérequis

- Kit de développement logiciel (SDK) NET Core 2.0.0 ou ultérieur.
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) version 15.7.3 ou ultérieur avec la charge de travail Développement ASP.NET et web. 

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Créer un projet ASP.NET Core dans Visual Studio

1. Cliquez avec le bouton droit et démarrez **Visual Studio 2017** en tant qu’administrateur.
2. Sélectionnez **Fichier** > **Nouveau** > **Projet** (Ctrl-Shift-N).

   ![Capture d’écran du menu Fichier Nouveau Projet de Visual Studio](./media/app-insights-asp-net-core/001-new-project.png)

3. Développez **Visual C#** > sélectionnez **.NET Core** > **Application web ASP.NET Core**. Renseignez **Nom** > **Nom de solution** > cochez **Créer un référentiel Git**.

   ![Capture d’écran de l’assistant Fichier Nouveau Projet de Visual Studio](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. Sélectionnez **.Net Core** > **ASP.NET Core 2.0** **Application web** > **OK**.

    ![Capture d’écran du menu Fichier Nouveau Projet Sélection de Visual Studio](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Recherche Application Insights

Par défaut dans Visual Studio version 2015 Update 2 ou version ultérieure avec un projet ASP.NET Core 2+, vous pouvez tirer parti de la [recherche Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio) avant même d’ajouter explicitement Application Insights à votre projet.

Pour tester cette fonctionnalité :

1. Exécutez votre application en cliquant sur IIS Express. ![Capture d’écran de l’icône IIS Express de Visual Studio](./media/app-insights-asp-net-core/004-iis-express.png)

2. Sélectionnez **Affichage** > **Autres fenêtres** > **Recherche Application Insights**.

   ![Capture d’écran des outils de diagnostic de Visual Studio](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. La télémétrie de la session de débogage est actuellement disponible pour une analyse locale uniquement. Pour activer totalement Application Insights, sélectionnez **État de préparation pour la télémétrie** dans le coin supérieur droit, ou suivez les étapes de la section suivante.

   ![Capture d’écran de la recherche Application Insights dans Visual Studio](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> Pour plus d’informations sur la façon dont Visual Studio active des fonctionnalités comme la [recherche Application Insights](app-insights-visual-studio.md) et [CodeLens](app-insights-visual-studio-codelens.md) localement avant que vous ayez ajouté Application Insights à votre projet ASP.NET Core, lisez l’explication à la [fin de cet article.](#Application-Insights-search-continued)

## <a name="add-application-insights-telemetry"></a>Ajouter Application Insights Telemetry

1. Sélectionnez **Projet** > **Ajouter Application Insights Telemetry...**. (Ou cliquez avec le bouton droit sur **Services connectés**, puis sélectionnez Ajouter un service connecté.)

    ![Capture d’écran du menu Fichier Nouveau Projet Sélection de Visual Studio](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. Sélectionnez **Prise en main**. (Selon votre version de Visual Studio, le texte peut varier légèrement. Certaines versions antérieures affichent à la place un bouton **Démarrer gratuitement**.)

    ![Capture d’écran du menu Fichier Nouveau Projet Sélection de Visual Studio](./media/app-insights-asp-net-core/008-get-started.png)

3. Sélectionnez un(e) **abonnement** > **ressource** > **registre** approprié(e).

## <a name="changes-made-to-your-project"></a>Modifications apportées à votre projet

Application Insights a une faible surcharge. Pour passer en revue les modifications apportées à votre projet en ajoutant Application Insights Telemetry :

Sélectionnez **Vue** > **Team Explorer** (Ctrl+\, Ctrl+M) > **Projet** > **Modifications**

- Quatre modifications au total :

  ![Capture d’écran des fichiers modifiés par l’ajout d’Application Insights](./media/app-insights-asp-net-core/009-changes.png)

- Un nouveau fichier est créé :

   _ConnectedService.json_

```json
{
  "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
  "Version": "8.12.10405.1",
  "GettingStartedDocument": {
    "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
  }
}
```

- Trois fichiers sont modifiés : (des commentaires supplémentaires sont ajoutés pour mettre en évidence les modifications)

  _appsettings.json_

```json
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Warning"
    }
  },
// Changes to file post adding Application Insights Telemetry:
  "ApplicationInsights": {
    "InstrumentationKey": "10101010-1010-1010-1010-101010101010"
  }
}
//
```

  _ContosoDotNetCore.csproj_

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp2.0</TargetFramework>
 <!--Changes to file post adding Application Insights Telemetry:-->
    <ApplicationInsightsResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsResourceId>
    <ApplicationInsightsAnnotationResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsAnnotationResourceId>
<!---->
  </PropertyGroup>
  <ItemGroup>
 <!--Changes to file post adding Application Insights Telemetry:-->
    <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.1.1" />
<!---->
    <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.8" />
  </ItemGroup>
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
  </ItemGroup>
<!--Changes to file post adding Application Insights Telemetry:-->
  <ItemGroup>
    <WCFMetadata Include="Connected Services" />
  </ItemGroup>
<!---->
</Project>
```

   _Program.cs_

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace DotNetCore
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
// Change to file post adding Application Insights Telemetry:
                .UseApplicationInsights()
//
                .UseStartup<Startup>()
                .Build();
    }
}
```

## <a name="synthetic-transactions-with-powershell"></a>Transactions synthétiques avec PowerShell

Pour automatiser des requêtes dans votre application avec des transactions synthétiques.

1. Exécutez votre application en cliquant sur IIS Express. ![Capture d’écran de l’icône IIS Express de Visual Studio](./media/app-insights-asp-net-core/004-iis-express.png)

2. Copiez l’URL de la barre d’adresses du navigateur. Son format est `http://localhost:{random port number}`

   ![Capture d’écran de la barre d’adresses URL du navigateur](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Exécutez la boucle PowerShell suivante pour créer 100 transactions synthétiques pour votre application test. Modifiez le numéro de port après **localhost:** pour qu’il corresponde à l’URL copiée à l’étape précédente.

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Ouvrir le portail Application Insights

Après avoir exécuté PowerShell comme expliqué dans la section précédente, démarrez Application Insights pour voir les transactions et confirmer que les données sont collectées. 

Dans le menu Visual Studio, sélectionnez **Projet** > **Application Insights** > **Ouvrir le portail Application Insights**

   ![Capture d’écran de la vue d’ensemble Application Insights](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> Dans la capture d’écran exemple ci-dessus **Flux temps réel**, **Temps de chargement de la page consultée** et **Requêtes échouées** ne sont pas collectés pour le moment. La section suivante vous guide pour les ajouter. Si vous collectez déjà **Flux temps réel** et **Temps de chargement de la page**, suivez uniquement les étapes pour **Requêtes échouées**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Collecter Requêtes échouées, Flux temps réel et Temps de chargement de la page

### <a name="failed-requests"></a>Demandes ayant échoué

Techniquement, les **Requêtes échouées** sont collectées, mais aucun échec ne s’est produit pour le moment. Pour accélérer le processus, vous pouvez ajouter une exception personnalisée au projet existant pour simuler une exception réelle. Si votre application est toujours en cours d’exécution dans Visual Studio, **arrêtez le débogage** (MAJ + F5) avant de continuer.

1. Dans **l’Explorateur de solutions** > développez **Pages** > **About.cshtml** > ouvrez **About.cshtml.cs**.

   ![Capture d’écran de l’Explorateur de solutions de Visual Studio](./media/app-insights-asp-net-core/011-about.png)

2. Ajoutez une exception sous ``Message=``, puis enregistrez la modification dans le fichier.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc.RazorPages;

    namespace DotNetCore.Pages
    {
        public class AboutModel : PageModel
        {
            public string Message { get; set; }

            public void OnGet()
            {
                Message = "Your application description page.";
                throw new Exception("Test Exception");
            }
        }
    }
    ```

### <a name="live-stream"></a>Flux temps réel

Pour accéder à la fonctionnalité Flux temps réel d’Application Insights avec la mise à jour ASP.NET Core pour les packages NuGet **Microsoft.ApplicationInsights.AspNetCore 2.2.0**.

Dans Visual Studio, sélectionnez **Projet** > **Gérer les packages NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > Version **2.2.0** > **Mise à jour**.

  ![Capture d’écran du Gestionnaire de package NuGet](./media/app-insights-asp-net-core/012-nuget-update.png)

Plusieurs invites de confirmation apparaîtront. Lisez et confirmez ces invites si vous acceptez les modifications.

### <a name="page-view-load-time"></a>Temps de chargement de la page consultée

1. Dans Visual Studio, accédez à **l’Explorateur de solutions** > **Pages** > deux fichiers doivent être modifiés : _Layout.cshtml_ et  _ViewImports.cshtml_

2. Dans __ViewImports.cshtml_, ajoutez :

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. Dans **_Layout.cshtml** ajoutez la ligne ci-dessous avant la balise ``</head>``, mais aussi avant tout autre script.

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Tester Requêtes échouées, Temps de chargement de la page, Flux temps réel

Pour tester et vérifier que tout fonctionne :

1. Exécutez votre application en cliquant sur IIS Express. ![Capture d’écran de l’icône IIS Express de Visual Studio](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Accédez à la page **À propos de** pour déclencher l’exception de test. (Si vous êtes en mode débogage, vous devez cliquer sur **Continuer** dans Visual Studio pour afficher l’exception dans Application Insights.)

3. Réexécutez le script de transaction PowerShell simulé précédemment (vous devrez peut-être ajuster le numéro de port dans le script.)

4. Si la vue d’ensemble d’Application Insights n’est pas encore ouverte, à partir du menu Visual Studio, sélectionnez **Projet** > **Application Insights** > **Ouvrir le portail Application Insights**. 

   > [!TIP]
   > Si vous ne voyez pas encore le nouveau trafic, vérifiez **l’intervalle de temps** et cliquez sur **Actualiser**.

   ![Capture d’écran de la fenêtre de vue d’ensemble](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Sélectionnez Flux temps réel.

   ![Capture d’écran de Flux de métriques en temps réel](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Si votre script PowerShell est toujours en cours d’exécution, vous devez voir les métriques en temps réel. S’il est arrêté, réexécutez le script en laissant Flux temps réel ouvert.)

## <a name="app-insights-sdk-comparison"></a>Comparaison des Kits de développement logiciel (SDK) d’Application Insights

Le groupe de produits Application Insights a beaucoup travaillé pour atteindre une parité de fonctionnalité entre le [SDK .NET Framework complet ](https://github.com/Microsoft/ApplicationInsights-dotnet) et le SDK .Net Core. La version 2.2.0 du [SDK ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore) pour Application Insights a largement comblé l’écart de fonctionnalité.

Pour en savoir plus sur les différences et les compromis entre [.NET et .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

   | Comparaison des SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Métriques temps réel**      | **+** |**-** | **+** |
   | **Canal de télémétrie du serveur** | **+** |**-** | **+**|
   |**Échantillonnage adaptatif**| **+** | **-** | **+**|
   | **Appels de dépendance SQL**     | **+** |**-** | **+**|
   | **Compteurs de performance*** | **+** | **-**| **-**|

Dans ce contexte, les _compteurs de performance_ font référence aux [compteurs de performance côté serveur](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) telles que le processeur, la mémoire et l’utilisation du disque.

## <a name="open-source-sdk"></a>Kit de développement logiciel (SDK) open source
[Lire et contribuer au code](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="application-insights-search-continued"></a>Recherche Application Insights (suite)

Pour mieux comprendre comment la recherche Application Insights fonctionne dans Visual Studio pour un projet ASP.NET Core 2, même quand une installation explicite des packages NuGet Application Insights n’a pas encore été effectuée. Il peut être utile d’examiner la sortie de débogage.

Si vous recherchez le mot _insight_, la recherche mettra en surbrillance des résultats semblables à ce qui suit :

```DebugOuput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

CoreCLR charge deux assemblys : 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

Et la mention _unconfigured_ (non configuré) dans chaque instance de télémétrie Application Insights indique que cette application n’est associée à aucune ikey afin que les données générées pendant l’exécution de votre application ne soient pas envoyées à Azure et soient uniquement disponibles pour une analyse et une recherche locales.

Cela est en partie possible car le package NuGet _Microsoft.AspNetCore.All_ accepte la dépendance [ _Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1)

![Capture d’écran d’une dépendance graphique NuGet pour Microsoft.AspNETCore.all](./media/app-insights-asp-net-core/013-dependency.png)

En dehors de Visual Studio, si vous modifiiez un projet ASP.NET Core dans VSCode ou un autre éditeur, ces assemblys ne se chargent pas automatiquement lors du débogage si vous n’avez pas explicitement ajouté Application Insights à votre projet.

Toutefois, dans Visual Studio, cette mise en surbrillance des fonctionnalités Application Insights locales à partir d’assemblys externes est effectuée en utilisant l’[interface IHostingStartup](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1), qui ajoute dynamiquement Application Insights lors du débogage.

Pour en savoir plus sur l’amélioration d’une application à partir d’un [assembly externe dans ASP.NET Core avec IHostingStartup](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="how-to-disable-application-insights-in-visual-studio-net-core-projects"></a>Comment désactiver Application Insights dans les projets Visual Studio .NET Core

La mise en surbrillance de la fonctionnalité de recherche Application Insights peut être utile pour certains, mais une télémétrie de débogage générée quand vous ne l’attendez pas peut prêter à confusion.

Si le simple fait de désactiver la génération de la télémétrie vous suffit, vous pouvez ajouter ce bloc de code à la méthode de configuration de votre fichier _Startup.cs_ :

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

CoreCLR chargera quand même _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ et _Microsoft.ApplicationInsights.AspNetCore.dll_, mais ces éléments resteront inopérants.

Si vous souhaitez désactiver complètement Application Insights dans votre projet Visual Studio .NET Core, la méthode recommandée consiste à sélectionner **Outils** > **Options** > **Projets et solutions** > **Projets web** > puis à cocher la case afin de désactiver localement Application Insights pour les projets web ASP.NET Core. Cette fonctionnalité a été ajoutée dans Visual Studio 15,6.

![Capture d’écran de projets web dans la fenêtre des options de Visual Studio](./media/app-insights-asp-net-core/014-disable.png)

Si vous exécutez une version antérieure de Visual Studio et que vous souhaitez supprimer complètement tous les assemblys chargés via IHostingStartup, vous pouvez soit ajouter :

`.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")`

à _Program.cs_ :

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace DotNetCore
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
                .UseStartup<Startup>()
                .Build();
    }
}
```

Vous pouvez également ajouter ``"ASPNETCORE_preventHostingStartup": "True"`` aux variables d’environnement _launchSettings.json_.

Le problème avec ces méthodes est que l’opération ne désactivera pas seulement Application Insights, elle désactivera toutes les options Visual Studio qui tiraient parti de la fonctionnalité de mise en surbrillance IHostingStartup.

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Étapes suivantes
* [Explorez les flux d’utilisateurs](app-insights-usage-flows.md) pour comprendre comment les utilisateurs naviguent dans l’application.
* [Configurez la collecte de captures instantanées](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) pour voir l’état du code source et des variables au moment où une exception est levée.
* [Utilisez l’API](app-insights-api-custom-events-metrics.md) pour envoyer vos propres événements et mesures pour obtenir une vue plus détaillée des performances et de l’utilisation de votre application.
* [Les tests de disponibilité](app-insights-monitor-web-app-availability.md) vérifient votre application en permanence dans le monde entier.
