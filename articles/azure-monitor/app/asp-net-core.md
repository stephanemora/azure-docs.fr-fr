---
title: Azure Application Insights pour ASP.NET Core | Microsoft Docs
description: Superviser la disponibilité, les performances et l’utilisation des applications web ASP.NET Core.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: ae0d3658d9ae8534b1596fa7363495926cd0dfe7
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520749"
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights pour ASP.NET Core

Azure Application Insights assure une surveillance approfondie de votre application web jusqu’au niveau du code. Azure Application Insights vous permet d’analyser facilement la disponibilité, les performances et l’utilisation. De plus, vous pouvez rapidement identifier et diagnostiquer les erreurs dans votre application sans attendre qu’un utilisateur ne les signale.

Cet article vous guide tout au long des étapes de création d’un exemple d’application de [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) ASP.NET Core dans Visual Studio. Il vous montre également comment commencer la surveillance avec Application Insights.

## <a name="prerequisites"></a>Conditions préalables

- SDK .NET Core 2.0.0 ou version ultérieure
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) version 15.7.3 ou ultérieure avec la charge de travail de développement web et ASP.NET

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Créer un projet ASP.NET Core dans Visual Studio

1. Cliquez avec le bouton droit sur **Visual Studio 2017**, puis sélectionnez **Exécuter en tant qu’administrateur**.
2. Sélectionnez **Fichier** > **Nouveau** > **Projet** (Ctrl+Maj+N).

   ![Capture d’écran du menu Nouveau Projet de Visual Studio](./media/asp-net-core/001-new-project.png)

3. Développez **Visual C#**. Sélectionnez **.Net Core** > **Application web ASP.NET Core**. Entrez un nom de projet et un nom de solution, puis sélectionnez **Créer un dépôt Git**.

   ![Capture d’écran de l’Assistant Nouveau Projet de Visual Studio](./media/asp-net-core/002-asp-net-core-web-application.png)

4. Sélectionnez **.NET Core** > **ASP.NET Core 2.0** > **Application web** > **OK**.

    ![Capture d’écran de la sélection Nouveau modèle de projet de Visual Studio](./media/asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Recherche Application Insights

Dans Visual Studio 2015 Update 2 ou une version ultérieure avec un projet ASP.NET Core 2+, vous pouvez tirer parti de la [recherche Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio) avant même d’ajouter explicitement Application Insights à votre projet.

Pour tester cette fonctionnalité :

1. Exécutez votre application. Pour exécuter votre application, sélectionnez l’icône **IIS Express** (![Capture d’écran de l’icône IIS Express de Visual Studio](./media/asp-net-core/004-iis-express.png)).

2. Sélectionnez **Affichage** > **Autres fenêtres** > **Recherche Application Insights**.

   ![Capture d’écran de la sélection d’outils de diagnostic de Visual Studio](./media/asp-net-core/005-view-other-windows-search.png)

3. Actuellement, la télémétrie de la session de débogage est disponible uniquement pour une analyse locale. Pour activer totalement Application Insights, sélectionnez **État de préparation pour la télémétrie** dans l’angle supérieur droit, ou effectuez les étapes répertoriées dans la section suivante.

   ![Capture d’écran de la recherche Application Insights dans Visual Studio](./media/asp-net-core/006-search.png)

> [!NOTE]
> Pour plus d’informations sur la façon dont Visual Studio active des fonctionnalités comme la [Recherche Application Insights](../../azure-monitor/app/visual-studio.md) et [CodeLens](../../azure-monitor/app/visual-studio-codelens.md) localement avant que vous ajoutiez Application Insights à votre projet ASP.NET Core, consultez [Recherche Application Insights (suite)](#application-insights-search-continued).

## <a name="add-application-insights-telemetry"></a>Ajouter Application Insights Telemetry

1. Sélectionnez **Projet** > **Ajouter Application Insights Telemetry**. (Vous pouvez également cliquer avec le bouton droit sur **Services connectés**, puis sélectionner **Ajouter un service connecté**.)

    ![Capture d’écran du menu de sélection Nouveau projet de Visual Studio](./media/asp-net-core/007-project-add-telemetry.png)

2. Sélectionnez **Prise en main**. (Selon votre version de Visual Studio, le texte peut varier légèrement. Certaines versions antérieures affichent un bouton **Démarrer gratuitement** à la place.)

    ![Capture d’écran du bouton de prise en main d’Application Insights](./media/asp-net-core/008-get-started.png)

3. Sélectionnez votre abonnement, puis sélectionnez **Ressource** > **Inscrire**.

## <a name="changes-made-to-your-project"></a>Changements apportés à votre projet

Application Insights a une faible surcharge. Pour passer en revue les modifications apportées à votre projet en ajoutant Application Insights Telemetry :

Sélectionnez **Vue** > **Team Explorer** (Ctrl+\, Ctrl+M) > **Projet** > **Modifications**

- Quatre changements apparaissent au total :

  ![Capture d’écran des fichiers modifiés par l’ajout d’Application Insights](./media/asp-net-core/009-changes.png)

- Un nouveau fichier est créé :

  - _ConnectedService.json_

    ```json
    {
     "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
     "Version": "8.12.10405.1",
     "GettingStartedDocument": {
       "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
     }
    }
    ```

- Trois fichiers sont modifiés (des commentaires supplémentaires sont ajoutés pour mettre en évidence les changements) :

  - _appsettings.json_ :

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

  - _ContosoDotNetCore.csproj_ :

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

  -  _Program.cs_ :

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

## <a name="send-ilogger-logs-to-application-insights"></a>Envoyer les journaux ILogger à Application Insights

Application Insights prend en charge la capture de journaux envoyés via ILogger. Configuration de journalisation checkout les exemples de code [ici](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

## <a name="synthetic-transactions-with-powershell"></a>Transactions synthétiques avec PowerShell

Pour automatiser des requêtes dans votre application à l’aide de transactions synthétiques :

1. Pour exécuter votre application, sélectionnez l’icône ![Capture d’écran de l’icône IIS Express de Visual Studio](./media/asp-net-core/004-iis-express.png) .

2. Copiez l’URL de la barre d’adresses du navigateur. L’URL est au format suivant `http://localhost:<port number>`.

   ![Capture d’écran de l’URL du navigateur dans la barre d’adresses](./media/asp-net-core/0013-copy-url.png)

3. Exécutez la boucle PowerShell suivante pour créer 100 transactions synthétiques à l’aide de votre application de test. Modifiez le numéro de port après `localhost:` pour qu’il corresponde à l’URL copié à l’étape précédente. Par exemple : 

   ```powershell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-the-application-insights-portal"></a>Ouvrir le portail Application Insights

Après avoir exécuté les commandes PowerShell dans la section précédente, ouvrez Application Insights pour voir les transactions et vérifier que les données sont collectées. 

Dans le menu Visual Studio, sélectionnez **Projet** > **Application Insights** > **Ouvrir le portail Application Insights**.

   ![Capture d’écran de la vue d’ensemble Application Insights](./media/asp-net-core/010-portal.png)

> [!NOTE]
> Dans l’exemple de capture d’écran précédent, les valeurs **Flux temps réel**, **Temps de chargement de la page consultée** et **Requêtes ayant échoué** ne sont pas collectées. La section suivante vous guide tout au long des étapes d’ajout de chacune d’elles. Si vous collectez déjà **Flux temps réel** et **Temps de chargement de la page**, effectuez les étapes uniquement pour **Requêtes ayant échoué**.

## <a name="collect-failed-requests-live-stream-and-page-view-load-time"></a>Collecter Requêtes ayant échoué, Flux temps réel et Temps de chargement de la page

### <a name="failed-requests"></a>Demandes ayant échoué

Techniquement, les requêtes ayant échoué sont collectées, mais aucune requête n’a encore échoué pour le moment. Pour accélérer le processus, vous pouvez ajouter une exception personnalisée au projet existant pour simuler une exception réelle. Si votre application est toujours en cours d’exécution dans Visual Studio, sélectionnez **Arrêter le débogage** (MAJ+F5) avant de continuer.

1. Dans l’**Explorateur de solutions**, développez **Pages** > **About.cshtml**, puis ouvrez *About.cshtml.cs*.

   ![Capture d’écran de l’Explorateur de solutions de Visual Studio](./media/asp-net-core/011-about.png)

2. Ajoutez une exception sous ``Message=``, puis enregistrez le changement dans le fichier.

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

Pour accéder à la fonctionnalité Flux temps réel d’Application Insights avec ASP.NET Core, mettez à jour les packages NuGet Microsoft.ApplicationInsights.AspNetCore 2.2.0.

Dans Visual Studio, sélectionnez **Projet** > **Gérer les packages NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > (version) **2.2.0** > **Mise à jour**.

  ![Capture d’écran du Gestionnaire de package NuGet](./media/asp-net-core/012-nuget-update.png)

Plusieurs invites de confirmation s’affichent. Lisez et confirmez ces invites si vous acceptez les modifications.

### <a name="page-view-load-time"></a>Temps de chargement de la page consultée

1. Dans Visual Studio, accédez à **Explorateur de solutions** > **Pages**. Vous devez modifier deux fichiers : *Layout.cshtml* et *ViewImports.cshtml*.

2. Dans *ViewImports.cshtml*, ajoutez le code suivant :

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. Dans *Layout.cshtml*, ajoutez le code suivant avant la balise ``</head>`` et avant tous les autres scripts :

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-and-live-stream"></a>Tester Requêtes ayant échoué, Temps de chargement de la page et Flux temps réel

Pour tester et vérifier que tout fonctionne :

1. Exécutez votre application. Pour exécuter votre application, sélectionnez l’icône ![Capture d’écran de l’icône IIS Express de Visual Studio](./media/asp-net-core/004-iis-express.png) .

2. Accédez à la page **À propos de** pour déclencher l’exception de test. (Si vous êtes en mode débogage, dans Visual Studio, sélectionnez **Continuer** pour afficher l’exception dans Application Insights.)

3. Réexécutez le script de transaction PowerShell simulé que vous avez utilisé précédemment. (Vous devrez peut-être ajuster le numéro de port dans le script.)

4. Si la page **Vue d’ensemble** dans Application Insights n’est pas encore ouverte, dans le menu Visual Studio, sélectionnez **Projet** > **Application Insights** > **Ouvrir le portail Application Insights**. 

   > [!TIP]
   > Si vous ne voyez pas votre nouveau trafic, vérifiez la valeur **Intervalle de temps**, puis sélectionnez **Actualiser**.

   ![Capture d’écran de la fenêtre de vue d’ensemble](./media/asp-net-core/0019-overview-updated.png)

5. Sélectionnez **Flux temps réel**.

   ![Capture d’écran de Flux de métriques en temps réel](./media/asp-net-core/0020-live-metrics-stream.png)

   (Si votre script PowerShell est toujours en cours d’exécution, vous devez voir des métriques en temps réel. Si son exécution est terminée, réexécutez le script en laissant Flux temps réel ouvert.)

## <a name="application-insights-sdk-comparison"></a>Comparaison des SDK Application Insights

Le groupe de produits Application Insights a beaucoup travaillé pour atteindre une parité des fonctionnalités entre le [SDK .NET Framework complet](https://github.com/Microsoft/ApplicationInsights-dotnet) et le SDK .NET Core. La version 2.2.0 du [SDK ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore) pour Application Insights comble largement l’écart de fonctionnalités.

Le tableau suivant décrit plusieurs des différences et compromis entre [.NET et .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server) :

   | Comparaison des SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Métriques temps réel**      | **+** |**-** | **+** |
   | **Canal de télémétrie du serveur** | **+** |**-** | **+**|
   |**Échantillonnage adaptatif**| **+** | **-** | **+**|
   | **Appels de dépendance SQL**     | **+** |**-** | **+**|
   | **Compteurs de performance*** | **+** | **-**| **-**|

Dans ce contexte, les compteurs de performance font référence aux [compteurs de performance côté serveur](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) comme le processeur, la mémoire et l’utilisation du disque.

## <a name="open-source-sdk"></a>Kit de développement logiciel (SDK) open source
[Lisez et contribuez au code](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="application-insights-search-continued"></a>Recherche Application Insights (suite)

Cette section peut vous aider à mieux comprendre le fonctionnement de la recherche Application Insights dans Visual Studio pour un projet ASP.NET Core 2. Elle fonctionne comme indiqué même quand vous n’avez pas encore explicitement installé les packages NuGet Application Insights. Il peut également être utile d’examiner la sortie de débogage.

Si vous recherchez le mot _insight_ dans la sortie, des résultats semblables à ce qui suit sont mis en surbrillance :

```DebugOutput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

Dans la sortie, CoreCLR charge deux assemblys : 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

La référence _unconfigured_ dans chaque instance de télémétrie Application Insights indique que cette application n’est associée à aucun ikey. Les données qui sont générées pendant l’exécution de votre application ne sont pas envoyées à Azure. Elles sont disponibles uniquement pour une analyse et une recherche locales.

La fonctionnalité est possible en partie car le package NuGet _Microsoft.AspNetCore.All_ accepte [ _Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1) comme dépendance.

![Capture d’écran du graphique de dépendance NuGet de Microsoft.AspNETCore.all](./media/asp-net-core/013-dependency.png)

En dehors de Visual Studio, si vous avez modifiée d’un projet ASP.NET Core dans VSCode ou un autre éditeur, ces assemblys ne chargement automatiquement durant le débogage si vous n’avez pas explicitement ajouté Application Insights à votre projet.

Toutefois, dans Visual Studio, cette mise en surbrillance des fonctionnalités Application Insights locales à partir d’assemblys externes est effectuée en utilisant l’[interface IHostingStartup](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1). Cette interface ajoute de manière dynamique Application Insights pendant le débogage.

Découvrez plus en détail l’amélioration d’une application à partir d’un [assembly externe dans ASP.NET Core avec IHostingStartup](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="disable-application-insights-in-visual-studio-net-core-projects"></a>Désactiver Application Insights dans les projets Visual Studio .NET Core

Même si la mise en surbrillance automatique de la fonctionnalité de recherche Application Insights peut être utile, l’affichage d’une télémétrie de débogage générée quand vous ne l’attendez pas peut prêter à confusion.

Si le simple fait de désactiver la génération de la télémétrie suffit, vous pouvez ajouter ce bloc de code à la méthode **configure** de votre fichier _Startup.cs_ :

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

CoreCLR charge quand même _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ et _Microsoft.ApplicationInsights.AspNetCore.dll_, mais les fichiers restent inopérants.

Si vous souhaitez désactiver complètement Application Insights dans votre projet Visual Studio .NET Core, la méthode recommandée consiste à sélectionner **Outils** > **Options** > **Projets et solutions** > **Projets web**. Cochez la case **Désactiver l’instance locale d’Application Insights pour les projets web ASP.NET Core**. Cette fonctionnalité a été ajoutée dans Visual Studio 15,6.

![Capture d’écran de projets web dans la fenêtre des options de Visual Studio](./media/asp-net-core/014-disable.png)

Si vous exécutez une version antérieure de Visual Studio et que vous souhaitez supprimer complètement tous les assemblys qui ont été chargés via *IHostingStartup*, deux options s’offrent à vous :

* Ajoutez `.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")` à _Program.cs_ :

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

* Ajoutez ``"ASPNETCORE_preventHostingStartup": "True"`` aux variables d’environnement _launchSettings.json_.

Le problème avec ces méthodes est qu’elles ne désactivent pas seulement Application Insights. Elles désactivent également toutes les options Visual Studio qui utilisaient la fonctionnalité de mise en surbrillance *IHostingStartup*.

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Étapes suivantes
* [Explorez les flux d’utilisateurs](../../azure-monitor/app/usage-flows.md) pour comprendre comment les utilisateurs naviguent dans votre application.
* [Configurez la collecte de captures instantanées](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) pour voir l’état du code source et des variables au moment où une exception est levée.
* [Utilisez l’API](../../azure-monitor/app/api-custom-events-metrics.md) pour envoyer vos propres événements et mesures pour obtenir une vue plus détaillée des performances et de l’utilisation de votre application.
* Utilisez des [tests de disponibilité](../../azure-monitor/app/monitor-web-app-availability.md) pour vérifier votre application en permanence dans le monde entier.
