---
title: Créer et exécuter des tests de disponibilité personnalisés avec Azure Functions
description: Cette documentation explique comment créer une fonction Azure avec TrackAvailability() pour qu’elle s’exécute régulièrement selon la configuration spécifiée dans la fonction TimerTrigger. Les résultats de ce test sont envoyés à votre ressource Application Insights, où vous pouvez rechercher et signaler des données sur les résultats de disponibilité. Les tests personnalisés vous permettent d’écrire des tests de disponibilité plus complexes qu’avec l’IU du portail, de superviser une application dans votre réseau virtuel Azure, de changer l’adresse du point de terminaison ou de créer un test de disponibilité, si ce dernier n’est pas disponible dans votre région.
ms.topic: conceptual
ms.date: 05/06/2021
ms.openlocfilehash: 891f69bd42dc228e01e7b341630e7f37522f9828
ms.sourcegitcommit: c1b0d0b61ef7635d008954a0d247a2c94c1a876f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109628152"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Créer et exécuter des tests de disponibilité personnalisés avec Azure Functions

Cet article explique comment créer une fonction Azure avec TrackAvailability() pour qu’elle s’exécute régulièrement selon la configuration spécifiée dans la fonction TimerTrigger avec votre propre logique métier. Les résultats de ce test sont envoyés à votre ressource Application Insights, où vous pouvez rechercher et signaler des données sur les résultats de disponibilité. Cela vous permet de créer des tests personnalisés similaires à ceux que vous pouvez effectuer via la [supervision de la disponibilité](./monitor-web-app-availability.md) dans le portail. Les tests personnalisés vous permettent d’écrire des tests de disponibilité plus complexes qu’avec l’IU du portail, de superviser une application dans votre réseau virtuel Azure, de changer l’adresse du point de terminaison ou de créer un test de disponibilité même si cette fonctionnalité n’est pas disponible dans votre région.

> [!NOTE]
> Cet exemple est conçu uniquement pour vous montrer comment fonctionne l’appel d’API TrackAvailability() au sein d’une fonction Azure. Notez comment écrire le code de test HTTP sous-jacent/la logique métier nécessaires pour convertir ce code en test de disponibilité entièrement fonctionnel. Par défaut, si vous suivez cet exemple, vous allez créer un test HTTP GET de disponibilité de base.

## <a name="create-a-timer-trigger-function"></a>Créer une fonction de déclencheur de minuteur

1. Créez une ressource Azure Functions.
    - Si vous avez déjà une ressource Application Insights :
        - Par défaut, Azure Functions crée une ressource Application Insights, mais si vous souhaitez utiliser l’une de vos ressources déjà créées, vous devez le spécifier durant la création.
        - Suivez les instructions de [création d'une ressource Azure Functions](../../azure-functions/functions-create-scheduled-function.md#create-a-function-app) en intégrant la modification suivante :
            - Sous l'onglet **Supervision**, sélectionnez la zone déroulante Application Insights, puis entrez ou sélectionnez le nom de votre ressource.
                :::image type="content" source="media/availability-azure-functions/app-insights-resource.png" alt-text="Sous l'onglet Supervision, sélectionnez votre ressource Application Insights existante.":::
    - Si vous n’avez pas encore créé de ressource Application Insights pour votre fonction déclenchée par un minuteur :
        - Par défaut, quand vous créez votre application Azure Functions, une ressource Application Insights est créée automatiquement. Suivez les instructions de [création d'une ressource Azure Functions](../../azure-functions/functions-create-scheduled-function.md#create-a-function-app).
    > [!NOTE]
    > Vous pouvez héberger vos fonctions sur un plan Consommation, Premium ou App Service. Si vous effectuez un test derrière un réseau virtuel ou si vous testez des points de terminaison non publics, vous devrez utiliser le plan Premium à la place du plan Consommation. Sélectionnez votre plan sous l'onglet **Hébergement**.
2. Créez une fonction de déclencheur de minuteur.
    1. Sous votre application de fonction, sélectionnez l'onglet **Fonction**.
    1. Sélectionnez **Ajouter** et, sous l'onglet Ajouter une fonction, sélectionnez les configurations suivantes :
        1. Environnement de développement : *Développer sur le portail*
        1. Sélectionnez un modèle : *Déclencheur de minuteur*
    1. Sélectionnez **Ajouter** pour créer la fonction de déclencheur de minuteur.

    :::image type="content" source="media/availability-azure-functions/add-function.png" alt-text="Capture d'écran montrant comment ajouter une fonction de déclencheur de minuteur à votre application de fonction." lightbox="media/availability-azure-functions/add-function.png":::

## <a name="add-and-edit-code-in-the-app-service-editor"></a>Ajouter et modifier le code dans l'Éditeur App Service

Accédez à l'application de fonction déployée et, sous *Outils de développement*, sélectionnez l'onglet **Éditeur App Service**.

Pour créer un fichier, cliquez avec le bouton droit sous votre fonction de déclencheur de minuteur (par exemple « TimerTrigger1 ») et sélectionnez **Nouveau fichier**. Entrez ensuite le nom du fichier et appuyez sur Entrée.

1. Créez un fichier appelé « function.proj » et collez-y le code suivant :

    ```xml
    <Project Sdk="Microsoft.NET.Sdk"> 
        <PropertyGroup> 
            <TargetFramework>netstandard2.0</TargetFramework> 
        </PropertyGroup> 
        <ItemGroup> 
            <PackageReference Include="Microsoft.ApplicationInsights" Version="2.15.0" /> <!-- Ensure you’re using the latest version --> 
        </ItemGroup> 
    </Project> 
    
    ```

     :::image type="content" source="media/availability-azure-functions/function-proj.png" alt-text="Capture d'écran du fichier function.proj dans l'Éditeur App Service." lightbox="media/availability-azure-functions/function-proj.png":::

2. Créez un fichier nommé « runAvailabilityTest.csx » et collez-y le code suivant :

    ```csharp
    using System.Net.Http; 
    
    public async static Task RunAvailabilityTestAsync(ILogger log) 
    { 
        using (var httpClient = new HttpClient()) 
        { 
            // TODO: Replace with your business logic 
            await httpClient.GetStringAsync("https://www.bing.com/"); 
        } 
    } 
    ```

3. Copiez le code ci-dessous dans le fichier run.csx (cette opération remplace le code préexistant) :

    ```csharp
    #load "runAvailabilityTest.csx" 
    
    using System; 
    
    using System.Diagnostics; 
    
    using Microsoft.ApplicationInsights; 
    
    using Microsoft.ApplicationInsights.Channel; 
    
    using Microsoft.ApplicationInsights.DataContracts; 
    
    using Microsoft.ApplicationInsights.Extensibility; 
    
    private static TelemetryClient telemetryClient; 
    
    // ============================================================= 
    
    // ****************** DO NOT MODIFY THIS FILE ****************** 
    
    // Business logic must be implemented in RunAvailabilityTestAsync function in runAvailabilityTest.csx 
    
    // If this file does not exist, please add it first 
    
    // ============================================================= 
    
    public async static Task Run(TimerInfo myTimer, ILogger log, ExecutionContext executionContext) 
    
    { 
        if (telemetryClient == null) 
        { 
            // Initializing a telemetry configuration for Application Insights based on connection string 
    
            var telemetryConfiguration = new TelemetryConfiguration(); 
            telemetryConfiguration.ConnectionString = Environment.GetEnvironmentVariable("APPLICATIONINSIGHTS_CONNECTION_STRING"); 
            telemetryConfiguration.TelemetryChannel = new InMemoryChannel(); 
            telemetryClient = new TelemetryClient(telemetryConfiguration); 
        } 
    
        string testName = executionContext.FunctionName; 
        string location = Environment.GetEnvironmentVariable("REGION_NAME"); 
        var availability = new AvailabilityTelemetry 
        { 
            Name = testName, 
    
            RunLocation = location, 
    
            Success = false, 
        }; 
    
        availability.Context.Operation.ParentId = Activity.Current.SpanId.ToString(); 
        availability.Context.Operation.Id = Activity.Current.RootId; 
        var stopwatch = new Stopwatch(); 
        stopwatch.Start(); 
    
        try 
        { 
            using (var activity = new Activity("AvailabilityContext")) 
            { 
                activity.Start(); 
                availability.Id = Activity.Current.SpanId.ToString(); 
                // Run business logic 
                await RunAvailabilityTestAsync(log); 
            } 
            availability.Success = true; 
        } 
    
        catch (Exception ex) 
        { 
            availability.Message = ex.Message; 
            throw; 
        } 
    
        finally 
        { 
            stopwatch.Stop(); 
            availability.Duration = stopwatch.Elapsed; 
            availability.Timestamp = DateTimeOffset.UtcNow; 
            telemetryClient.TrackAvailability(availability); 
            telemetryClient.Flush(); 
        } 
    } 
    
    ```

## <a name="check-availability"></a>Vérifier la disponibilité

Pour vérifier que tout fonctionne correctement, vous pouvez consulter le graphe sous l’onglet Disponibilité de votre ressource Application Insights.

> [!NOTE]
> Les tests créés à l'aide de TrackAvailability() apparaissent avec la mention **CUSTOM** en regard du nom du test.

 :::image type="content" source="media/availability-azure-functions/availability-custom.png" alt-text="Onglet Disponibilité avec des résultats réussis." lightbox="media/availability-azure-functions/availability-custom.png":::

Pour voir les détails de la transaction de bout en bout, sélectionnez **Réussite** ou **Échec** dans Explorer, puis sélectionnez un exemple. Vous pouvez également accéder aux détails de la transaction de bout en bout en sélectionnant un point de données sur le graphe.

:::image type="content" source="media/availability-azure-functions/sample.png" alt-text="Sélectionner un exemple de test de disponibilité.":::

:::image type="content" source="media/availability-azure-functions/end-to-end.png" alt-text="Détails de la transaction de bout en bout." lightbox="media/availability-azure-functions/end-to-end.png":::

## <a name="query-in-logs-analytics"></a>Interroger les journaux (Analytics)

Vous pouvez utiliser les journaux (Analytics) pour voir les résultats de la disponibilité, les dépendances, etc. Pour en savoir plus sur les journaux, consultez [Vue d’ensemble des requêtes de journal](../logs/log-query-overview.md).

:::image type="content" source="media/availability-azure-functions/availabilityresults.png" alt-text="Résultats de disponibilité." lightbox="media/availability-azure-functions/availabilityresults.png":::

:::image type="content" source="media/availability-azure-functions/dependencies.png" alt-text="Screenshot shows New Query tab with dependencies limited to 50." lightbox="media/availability-azure-functions/dependencies.png":::

## <a name="next-steps"></a>Étapes suivantes

- [Plan de l’application](./app-map.md)
- [Diagnostics de transaction](./transaction-diagnostics.md)
