---
title: Activer le débogueur de capture instantanée pour les applications .NET dans Azure Service Fabric, Service Cloud et Machines virtuelles | Microsoft Docs
description: Activer le débogueur de capture instantanée pour les applications .NET dans Azure Service Fabric, Service Cloud et Machines virtuelles
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: brahmnes
ms.openlocfilehash: ac937ddb1bcaed6813a0de4d631f820eff01e26f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877735"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Activer le débogueur de capture instantanée pour les applications .NET dans Azure Service Fabric, Service Cloud et Machines virtuelles

Si votre ASP.NET ou un ASP.NET core application est exécuté dans Azure App Service, les instructions ci-dessous peuvent également être utilisées. À moins que votre application nécessite une configuration personnalisée de débogueur de capture instantanée, il est vivement recommandé à [autorisez le débogueur de capture instantanée dans la page du portail Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). Si votre application s’exécute dans Azure Service Fabric, Service Cloud, Machines virtuelles, ou sur des machines locales, vous devraient utiliser les instructions suivantes. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Configurer la collecte de captures instantanées pour les applications ASP.NET

1. Si vous ne l’avez pas encore fait, [Activez Application Insights dans votre application web](../../azure-monitor/app/asp-net.md).

2. Incluez le package NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) dans votre application.

3. Si nécessaire, personnaliser la configuration du débogueur de capture instantanée ajoutée à [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). La configuration du débogueur de capture instantanée par défaut est pratiquement vide et tous les paramètres sont facultatifs. Voici un exemple d’une configuration équivalente à la configuration par défaut :

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Des captures instantanées sont collectées uniquement sur des exceptions signalées à Application Insights. Dans certains cas (par exemple, des versions plus anciennes de la plateforme .NET), il se peut que vous deviez [configurer la collecte des exceptions](../../azure-monitor/app/asp-net-exceptions.md#exceptions) afin de voir celles-ci avec des captures instantanées dans le portail.


## <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Configurer la collecte de captures instantanées pour les applications ASP.NET Core 2.0

1. Si vous ne l’avez pas encore fait, [Activez Application Insights dans votre application web ASP.NET Core](../../azure-monitor/app/asp-net-core.md).

    > [!NOTE]
    > Être sûr que votre application fait référence à la version 2.1.1 ou plus récente, du package Microsoft.ApplicationInsights.AspNetCore.

2. Incluez le package NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) dans votre application.

3. Modifiez la classe `Startup` de votre application pour ajouter et configurer le processeur de télémétrie du collecteur de captures instantanées.

    Ajoutez le code suivant à l’aide des instructions pour `Startup.cs`

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   Ajoutez les paramètres suivants de la classe `SnapshotCollectorTelemetryProcessorFactory` à la classe `Startup`.

   ```csharp
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }
       ...
    ```
    Ajoutez les services `SnapshotCollectorConfiguration` et `SnapshotCollectorTelemetryProcessorFactory` au pipeline de start-up :

    ```csharp
       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. Si nécessaire, personnaliser la configuration du débogueur de capture instantanée en ajoutant une section SnapshotCollectorConfiguration au fichier appsettings.json. Tous les paramètres dans la configuration du débogueur de capture instantanée sont facultatifs. Voici un exemple d’une configuration équivalente à la configuration par défaut :

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Configurer la collecte de captures instantanées pour d’autres applications .NET

1. Si votre application n’est pas instrumentée avec Application Insights, commencez par [activer Application Insights et définir la clé d’instrumentation](../../azure-monitor/app/windows-desktop.md).

2. Incluez le package NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) dans votre application.

3. Des captures instantanées sont collectées uniquement sur des exceptions signalées à Application Insights. Il se peut que vous deviez modifier votre code pour les signaler. Le code de gestion des exceptions dépend de la structure de votre application, mais en voici un exemple :
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>Étapes suivantes

- Générer le trafic vers votre application qui peut déclencher une exception. Puis, attendez 10 à 15 minutes pour les captures instantanées à envoyer à l’instance Application Insights.
- Consultez [instantanés](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json) dans le portail Azure.
- Pour faciliter le dépannage des problèmes de Profiler, consultez [résolution des problèmes du débogueur de capture instantanée](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).
