---
title: Analyse des performances et de l’utilisation pour les applications de bureau Windows
description: Analysez l’utilisation et les performances de votre application de bureau Windows avec Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: mbullwin
ms.openlocfilehash: 95ff8d1a70325357fee4bc24fd96c1a1c7a73845
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077604"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Analyse des niveaux de performance et de l’utilisation dans les applications de bureau Windows Classic

Les applications hébergées en local, dans Azure, et dans d’autres clouds peuvent toutes tirer profit d’Application Insights. La seule limitation réside dans la nécessité d’[autoriser la communication](../../azure-monitor/app/ip-addresses.md) vers le service Application Insights. Pour analyser des applications de plateforme Windows universelle (UWP), nous vous recommandons [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Pour envoyer la télémétrie à Application Insights à partir d’une application Windows Classic
1. Dans le [portail Azure](https://portal.azure.com), [créez une ressource Application Insights](../../azure-monitor/app/create-new-resource.md ). Choisissez ASP.NET comme type d’application.
2. Copiez la clé d'instrumentation. Recherchez la clé dans la liste déroulante Essentials de la nouvelle ressource que vous venez de créer. 
3. Dans Visual Studio, modifiez les packages NuGet de votre projet d’application et ajoutez Microsoft.ApplicationInsights.WindowsServer. (Ou choisissez Microsoft.ApplicationInsights si vous souhaitez simplement l’API nue, sans modules de collecte de télémétrie standard.)
4. Définissez la clé d’instrumentation dans votre code :
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*votre clé*`";`
   
    ou dans ApplicationInsights.config (si vous avez installé l’un des packages de télémétrie standard) :
   
    `<InstrumentationKey>`*votre clé*`</InstrumentationKey>` 
   
    Si vous utilisez ApplicationInsights.config, assurez-vous que ses propriétés dans l’Explorateur de solutions sont définies sur **Build Action = Content, Copy to Output Directory = Copy**.
5. [Utilisez l’API](../../azure-monitor/app/api-custom-events-metrics.md) pour envoyer les données de télémétrie.
6. Exécutez votre application et consultez la télémétrie dans la ressource que vous avez créée dans le portail Azure.

## <a name="telemetry"></a>Exemple de code
```csharp

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Étapes suivantes
* [Création d’un tableau de bord](../../azure-monitor/app/app-insights-dashboards.md)
* [Recherche de diagnostic](../../azure-monitor/app/diagnostic-search.md)
* [Exploration des mesures](../../azure-monitor/app/metrics-explorer.md)
* [Écriture de requêtes Analytics](../../azure-monitor/app/analytics.md)

