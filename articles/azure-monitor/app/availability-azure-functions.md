---
title: Créer et exécuter des tests de disponibilité personnalisés avec Azure Functions
description: Cette documentation explique comment créer une fonction Azure avec TrackAvailability() pour qu’elle s’exécute régulièrement selon la configuration spécifiée dans la fonction TimerTrigger. Les résultats de ce test sont envoyés à votre ressource Application Insights, où vous pouvez rechercher et signaler des données sur les résultats de disponibilité. Les tests personnalisés vous permettent d’écrire des tests de disponibilité plus complexes qu’avec l’IU du portail, de superviser une application dans votre réseau virtuel Azure, de changer l’adresse du point de terminaison ou de créer un test de disponibilité, si ce dernier n’est pas disponible dans votre région.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 10/11/2019
ms.openlocfilehash: a2b29f2c24a3244cdc800d882e7c2ed8f943eabe
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677584"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Créer et exécuter des tests de disponibilité personnalisés avec Azure Functions

Cet article explique comment créer une fonction Azure avec TrackAvailability() pour qu’elle s’exécute périodiquement selon la configuration spécifiée dans la fonction TimerTrigger. Les résultats de ce test sont envoyés à votre ressource Application Insights, où vous pouvez rechercher et signaler des données sur les résultats de disponibilité. Cela vous permet de créer des tests personnalisés similaires à ceux que vous pouvez effectuer via la [supervision de la disponibilité](../../azure-monitor/app/monitor-web-app-availability.md) dans le portail. Les tests personnalisés vous permettent d’écrire des tests de disponibilité plus complexes qu’avec l’IU du portail, de superviser une application dans votre réseau virtuel Azure, de changer l’adresse du point de terminaison ou de créer un test de disponibilité même si cette fonctionnalité n’est pas disponible dans votre région.


## <a name="create-timer-triggered-function"></a>Créer une fonction déclenchée par un minuteur

- Si vous avez une ressource Application Insights :
    - Par défaut, Azure Functions crée une ressource Application Insights, mais si vous souhaitez utiliser l’une de vos ressources déjà créées, vous devez le spécifier durant la création.
    - Suivez les instructions permettant de [créer une ressource Azure Functions et une fonction déclenchée par un minuteur](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (arrêter avant le nettoyage) avec les choix suivants.
        -  Cliquez sur la section Application Insights avant de sélectionner **Créer**.

            ![ Créer une application Azure Functions avec votre propre ressource App Insights](media/availability-azure-functions/create-function-app.png)

        - Cliquez sur **Sélectionner une ressource existante**, puis tapez le nom de votre ressource. Sélectionnez **Appliquer**

            ![Sélection d’une ressource Application Insights existante](media/availability-azure-functions/app-insights-resource.png)

        - Sélectionnez **Créer**
- Si vous n’avez pas encore créé de ressource Application Insights pour votre fonction déclenchée par un minuteur :
    - Par défaut, quand vous créez votre application Azure Functions, une ressource Application Insights est créée automatiquement.
    - Suivez les instructions permettant de [créer une ressource Azure Functions et une fonction déclenchée par un minuteur](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (arrêter avant le nettoyage).

## <a name="sample-code"></a>Exemple de code

Copiez le code ci-dessous dans le fichier run.csx (cette opération remplace le code préexistant). Pour ce faire, accédez à votre application Azure Functions, puis sélectionnez votre fonction avec déclenchement par minuteur sur la gauche.

![Fichier run.csx de la fonction Azure dans le portail Azure](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Pour l’adresse du point de terminaison, utilisez `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. Si votre ressource se trouve dans une région comme Azure Government ou Azure Chine, consultez cet article sur le [remplacement des points de terminaison par défaut](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification), puis sélectionnez le point de terminaison de canal de télémétrie approprié pour votre région.

```C#
using System;
using System.Diagnostics;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

// [CONFIGURATION_REQUIRED] configure test timeout accordingly for which your request should run
private static readonly HttpClient HttpClient = new HttpClient { Timeout = TimeSpan.FromSeconds(30) };

// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
//DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string InstrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");

// [CONFIGURATION_REQUIRED] Configure EndpointAddress
private static readonly TelemetryConfiguration TelemetryConfiguration = new TelemetryConfiguration(InstrumentationKey, new ServerTelemetryChannel() { EndpointAddress = "<EndpointAddress>" });
private static readonly TelemetryClient TelemetryClient = new TelemetryClient(TelemetryConfiguration);

[FunctionName("Function")]
public static async void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");

    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }

    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";

    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");

    // [CONFIGURATION_REQUIRED] configure {uri} and {contentMatch} accordingly for your web app. {uri} is the website that you are testing the availability of, make sure to include http:// ot https:// in your url. If {contentMatch} is present on the page, the test will succeed, otherwise it will fail.  
    await AvailabilityTestRun(
        name: testName,
        location: location,
        uri: "<http://example.com>",
        contentMatch: "<Enter a short string of text that is present  in the body of the page your are testing>",
        log: log
    );
}

private static async Task AvailabilityTestRun(string name, string location, string uri, string contentMatch, ILogger log)
{
    log.LogInformation($"Executing availability test run for {name} at: {DateTime.Now}");

    string operationId = Guid.NewGuid().ToString("N");

    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = name,
        RunLocation = location,
        Success = false
    };

    var stopwatch = new Stopwatch();
    stopwatch.Start();
    bool isMonitoringFailure = false;

    try
    {
        using (var httpResponse = await HttpClient.GetAsync(uri))
        {
            // add test results to availability telemetry property
            availability.Properties.Add("HttpResponseStatusCode", Convert.ToInt32(httpResponse.StatusCode).ToString());

            // check if response content contains specific text
            string content = httpResponse.Content != null ? await httpResponse.Content.ReadAsStringAsync() : "";
            if (httpResponse.IsSuccessStatusCode && content.Contains(contentMatch))
            {
                availability.Success = true;
                availability.Message = $"Test succeeded with response: {httpResponse.StatusCode}";
                log.LogTrace($"[Verbose]: {availability.Message}");
            }
            else if (!httpResponse.IsSuccessStatusCode)
            {
                availability.Message = $"Test failed with response: {httpResponse.StatusCode}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
            else
            {
                availability.Message = $"Test content does not contain: {contentMatch}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
        }
    }
    catch (TaskCanceledException e)
    {
        availability.Message = $"Test timed out: {e.Message}";
        log.LogWarning($"[Warning]: {availability.Message}");
    }
    catch (Exception ex)
    {
        // track exception when unable to determine the state of web app
        isMonitoringFailure = true;
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", name);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        exceptionTelemetry.Properties.Add("TestUri", uri);
        TelemetryClient.TrackException(exceptionTelemetry);
        log.LogError($"[Error]: {ex.Message}");

        // optional - throw to fail the function
        throw;
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;

        // do not make an assumption as to the state of the web app when monitoring failures occur
        if (!isMonitoringFailure)
        {
            TelemetryClient.TrackAvailability(availability);
            log.LogInformation($"Availability telemetry for {name} is sent.");
        }

        // call flush to ensure telemetries are sent
        TelemetryClient.Flush();
    }
}
```

À droite, sous Afficher les fichiers, sélectionnez **Ajouter**. Appelez le nouveau fichier **function.proj** avec la configuration suivante.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
</Project>

```

![À droite, sélectionnez Ajouter. Nommer le fichier function.proj](media/availability-azure-functions/addfile.png)

## <a name="check-availability"></a>Vérifier la disponibilité

Pour vérifier que tout fonctionne correctement, vous pouvez consulter le graphe sous l’onglet Disponibilité de votre ressource Application Insights.

![Onglet Disponibilité avec des résultats réussis](media/availability-azure-functions/availtab.png)

Quand vous configurez votre test à l’aide d’Azure Functions, vous pouvez noter que, contrairement à **Ajouter un test** sous l’onglet Disponibilité, le nom de votre test n’apparaît pas. Vous ne pouvez donc pas interagir avec celui-ci. Les résultats sont visibles, mais vous accédez à une vue récapitulative au lieu de la vue détaillée que vous obtenez quand vous créez un test de disponibilité via le portail.

Pour voir les détails de la transaction de bout en bout, sélectionnez **Réussite** ou **Échec** dans Explorer, puis sélectionnez un exemple. Vous pouvez également accéder aux détails de la transaction de bout en bout en sélectionnant un point de données sur le graphe.

![Sélectionner un exemple de test de disponibilité](media/availability-azure-functions/sample.png)

![Détails de la transaction de bout en bout](media/availability-azure-functions/end-to-end.png)

## <a name="query-in-logs-analytics"></a>Interroger les journaux (Analytics)

Vous pouvez utiliser les journaux (Analytics) pour voir les résultats de la disponibilité, les dépendances, etc. Pour en savoir plus sur les journaux, consultez [Vue d’ensemble des requêtes de journal](../../azure-monitor/log-query/log-query-overview.md).

![Résultats de la disponibilité](media/availability-azure-functions/availabilityresults.png)

![Les dépendances](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Étapes suivantes

- [Plan de l’application](../../azure-monitor/app/app-map.md)
- [Diagnostics de transaction](../../azure-monitor/app/transaction-diagnostics.md)
