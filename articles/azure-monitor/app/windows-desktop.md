---
title: Analyse des performances et de l’utilisation pour les applications de bureau Windows
description: Analysez l’utilisation et les performances de votre application de bureau Windows avec Application Insights.
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: eb9e0fc480098478a3a68265ac85e0d5450e27fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537387"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Analyse des niveaux de performance et de l’utilisation dans les applications de bureau Windows Classic

Les applications hébergées en local, dans Azure, et dans d’autres clouds peuvent toutes tirer profit d’Application Insights. La seule limitation réside dans la nécessité d’[autoriser la communication](../../azure-monitor/app/ip-addresses.md) vers le service Application Insights. Pour analyser des applications de plateforme Windows universelle (UWP), nous vous recommandons [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Pour envoyer la télémétrie à Application Insights à partir d’une application Windows Classic
1. Dans le [portail Azure](https://portal.azure.com), [créez une ressource Application Insights](../../azure-monitor/app/create-new-resource.md ). Choisissez ASP.NET comme type d’application.
2. Copiez la clé d'instrumentation. Recherchez la clé dans la liste déroulante Essentials de la nouvelle ressource que vous venez de créer. 
3. Dans Visual Studio, modifiez les packages NuGet de votre projet d’application et ajoutez Microsoft.ApplicationInsights.WindowsServer. (Ou choisissez Microsoft.ApplicationInsights si vous souhaitez simplement l’API nue, sans modules de collecte de télémétrie standard.)
4. Définissez la clé d’instrumentation dans votre code :
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *votre clé* `";`
   
    ou dans ApplicationInsights.config (si vous avez installé l’un des packages de télémétrie standard) :
   
    `<InstrumentationKey>`*votre clé*`</InstrumentationKey>` 
   
    Si vous utilisez ApplicationInsights.config, assurez-vous que ses propriétés dans l’Explorateur de solutions sont définies sur **Build Action = Content, Copy to Output Directory = Copy**.
5. [Utilisez l’API](../../azure-monitor/app/api-custom-events-metrics.md) pour envoyer les données de télémétrie.
6. Exécutez votre application et consultez la télémétrie dans la ressource que vous avez créée dans le Portail Azure.

## <a name="example-code"></a><a name="telemetry"></a>Exemple de code
```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
        {
            e.Cancel = true;

            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="override-storage-of-computer-name"></a>Remplacer le stockage du nom de l’ordinateur

Par défaut, ce kit de développement logiciel (SDK) collecte et stocke le nom d’ordinateur du système émettant la télémétrie. Pour remplacer la collection, vous devez utiliser un initialiseur de télémétrie :

**Écrire un initialiseur TelemetryInitializer personnalisé comme ci-dessous.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here, you can pass an empty string if needed.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```
Instanciez l’initialiseur dans la méthode `Program.cs` `Main()` ci-dessous, en définissant la clé d’instrumentation :

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
        }
```

## <a name="next-steps"></a>Étapes suivantes
* [Création d’un tableau de bord](../../azure-monitor/app/overview-dashboard.md)
* [Recherche de diagnostic](../../azure-monitor/app/diagnostic-search.md)
* [Exploration des mesures](../../azure-monitor/platform/metrics-charts.md)
* [Écriture de requêtes Analytics](../../azure-monitor/app/analytics.md)

