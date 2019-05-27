---
title: Comment exécuter des fonctions durables en tant que tâches Web - Azure
description: Découvrez comment coder et configurer Fonctions durables pour s’exécuter dans WebJobs à l’aide du SDK WebJobs.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 39a757900b4307d702a0ce0ce1c20694418aa8dd
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872835"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Comment exécuter des fonctions durables en tant que tâches Web

Par défaut, les fonctions durables utilise le runtime Azure Functions aux orchestrations de l’hôte. Toutefois, il peut y avoir certains scénarios où vous avez besoin de contrôler davantage le code qui écoute les événements. Cet article vous montre comment implémenter votre orchestration à l’aide du SDK WebJobs. Pour obtenir une comparaison détaillée entre Functions et WebJobs, consultez [comparer les fonctions et tâches Web](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Azure Functions](../functions-overview.md) et l’extension [Fonctions durables](durable-functions-overview.md) reposent sur le [SDK WebJobs](../../app-service/webjobs-sdk-how-to.md). L’hôte de tâche dans le SDK WebJobs est le runtime dans Azure Functions. Si vous avez besoin contrôler le comportement de manières n’est pas possible dans Azure Functions, vous pouvez développer et exécuter des fonctions durables à l’aide du SDK WebJobs vous-même.

Dans la version 3.x du SDK WebJobs, l’hôte est une implémentation de `IHost`et dans la version 2.x, vous utilisez le `JobHost` objet.

L’exemple de fonctions durables chaînage est disponible dans une version 2.x de SDK WebJobs : Téléchargez ou clonez le [référentiel des fonctions durables](https://github.com/azure/azure-functions-durable-extension/)et accédez à la *exemples\\webjobssdk\\dechaînage* dossier.

## <a name="prerequisites"></a>Conditions préalables

Cet article suppose que vous connaissez les principes de base du SDK WebJobs, du développement de bibliothèque de classes C# pour Azure Functions et de l’extension Fonctions durables. Si vous souhaitez consulter une présentation de ces thématiques, accédez aux ressources suivantes :

* [Prise en main du Kit de développement logiciel (SDK) WebJobs](../../app-service/webjobs-sdk-get-started.md)
* [Créer votre première fonction à l’aide de Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Fonctions durables](durable-functions-sequence.md)

Pour accomplir les étapes décrites dans cet article :

* [Installer Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) avec la **développement Azure** charge de travail.

  Si vous disposez déjà Visual Studio, mais n’avez pas cette charge de travail, ajoutez la charge de travail en sélectionnant **outils** > **obtenir les outils et fonctionnalités**.

  (Vous pouvez utilisez en lieu et place [Visual Studio Code](https://code.visualstudio.com/), mais certaines des instructions sont spécifiques à Visual Studio.)

* Installez et exécutez [l’émulateur de stockage Azure](../../storage/common/storage-use-emulator.md) version 5.2 ou plus. Sinon, vous pouvez mettre à jour le fichier *App.config* avec une chaîne de connexion Stockage Azure.

## <a name="webjobs-sdk-versions"></a>Versions du SDK WebJobs

Cet article vous explique comment développer un projet SDK WebJobs 2.x (équivalent à Azure Functions version 1.x). Pour plus d’informations sur la version 3.x, consultez [SDK WebJobs 3.x](#webjobs-sdk-3x) plus bas dans cet article.

## <a name="create-a-console-app"></a>Créer une application console

Pour exécuter des fonctions durables en tant que tâches Web, vous devez d’abord créer une application console. Un projet SDK WebJobs est simplement un projet d’application console avec les packages NuGet appropriés installés.

Dans Visual Studio **nouveau projet** boîte de dialogue, sélectionnez **bureau classique Windows** > **application Console (.NET Framework)**. Dans le fichier de projet, l’élément `TargetFrameworkVersion` doit être défini sur `v4.6.1`.

Visual Studio propose également un modèle de projet de tâche Web, vous pouvez utiliser en sélectionnant **Cloud** > **Azure WebJob (.NET Framework)**. Ce modèle installe de nombreux packages, qui ne vous seront peut-être pas tous utiles.

## <a name="install-nuget-packages"></a>Installer les packages NuGet

Vous avez besoin des packages NuGet pour le SDK WebJobs, les liaisons essentielles, l’infrastructure de journalisation et l’extension Tâche durable. Voici **Console du Gestionnaire de Package** commandes pour ces packages, avec les numéros de version stables les plus récentes à la date de rédaction de cet article :

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Vous avez également besoin de fournisseurs de journalisation. Les commandes suivantes installent le fournisseur Azure Application Insights et le `ConfigurationManager`. L’élément `ConfigurationManager` vous permet de récupérer la clé d’instrumentation Application Insights des paramètres d’application.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

La commande suivante installe le fournisseur de console :

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Code JobHost

Ayant créé l’application de console et installé les packages NuGet vous avez besoin, vous êtes prêt à utiliser les fonctions durables. Pour ce faire à l’aide de code de JobHost.

Pour utiliser l’extension Fonctions durables, appelez `UseDurableTask` sur l’objet `JobHostConfiguration` dans votre méthode `Main` :

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Pour obtenir la liste des propriétés que vous pouvez définir dans l’objet `DurableTaskExtension`, consultez le fichier [host.json](../functions-host-json.md#durabletask).

La méthode `Main` sert également à définir les fournisseurs de journalisation. L’exemple suivant configure la console et les fournisseurs d’Application Insights.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Fonctions

Fonctions durables dans le contexte des tâches Web diffère quelque peu de fonctions durables dans le contexte d’Azure Functions. Il est important de connaître les différences que vous écrivez votre code.

Le SDK WebJobs ne prend pas en charge les fonctions suivantes d’Azure Functions :

* [Attribut FunctionName](#functionname-attribute)
* [Déclencheur HTTP](#http-trigger)
* [API de gestion HTTP de Fonctions durables](#http-management-api)

### <a name="functionname-attribute"></a>Attribut FunctionName

Dans un projet de SDK WebJobs, le nom de la méthode d’une fonction est le nom de la fonction. L’attribut `FunctionName` est utilisé uniquement dans Azure Functions.

### <a name="http-trigger"></a>Déclencheur HTTP

Le SDK WebJobs ne présente pas de déclencheur HTTP. Le client d’orchestration de l’exemple de projet utilise un déclencheur de minuteur :

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>API de gestion HTTP

Dans la mesure où il ne présente aucun déclencheur HTTP, le SDK WebJobs ne possède aucune [API de gestion HTTP](durable-functions-http-api.md).

Dans un projet SDK WebJobs, vous pouvez appeler des méthodes sur l’objet de client d’orchestration, au lieu d’en envoyant des requêtes HTTP. Les méthodes suivantes correspondent aux trois tâches pouvant être effectuées avec l’API de gestion HTTP :

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

La fonction de client d’orchestration dans l’exemple de projet démarre la fonction d’orchestrateur, puis entre dans une boucle qui appelle `GetStatusAsync` toutes les 2 secondes :

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Exécution de l'exemple

Vous avez configuré pour s’exécuter comme une tâche Web de fonctions durables, et vous avez maintenant une compréhension de la façon dont cela différera de fonctions durables en cours d’exécution en mode autonome Azure Functions. À ce stade, le voir fonctionner dans un échantillon peut être utile.

Cette section fournit une vue d’ensemble de l’exécution de l’[exemple de projet](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Pour obtenir des instructions détaillées sur la méthode d’exécution en local du projet SDK WebJobs et son déploiement sur une tâche web Azure, consultez la section [Prise en main du kit de développement logiciel (SDK) WebJobs](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Exécution locale

1. Assurez-vous que l’émulateur de stockage est en cours d’exécution (consultez les [Prérequis](#prerequisites)).

1. Si vous souhaitez afficher les journaux dans Application Insights lorsque vous exécutez le projet localement :

    a. Créer une ressource Application Insights et utiliser le **général** type d’application pour lui.

    b. Enregistrez la clé d’instrumentation dans le fichier *App.config*.

1. Exécutez le projet.

### <a name="run-in-azure"></a>Exécuter dans Azure

1. Créez une application web et un compte de stockage.

1. Dans l’application web, enregistrer la chaîne de connexion de stockage dans le paramètre d’application nommé `AzureWebJobsStorage`.

1. Créer une ressource Application Insights et utiliser le **général** type d’application pour lui.

1. Enregistrer la clé d’instrumentation dans le paramètre d’application nommé `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. Déployer le projet en tant qu’objet WebJob.

## <a name="webjobs-sdk-3x"></a>SDK WebJobs 3.x

Cet article explique comment développer un projet 2.x de SDK WebJobs. Si vous développez un [SDK WebJobs 3.x](../../app-service/webjobs-sdk-get-started.md) projet, cette section vous aidera à comprendre les différences.

Le principal changement introduit est l’utilisation de .NET Core au lieu de .NET Framework. Pour créer un projet de 3.x SDK WebJobs, les instructions sont les mêmes, à ces exceptions près :

1. Créez une application console .NET Core. Dans Visual Studio **nouveau projet** boîte de dialogue, sélectionnez **.NET Core** > **application Console (.NET Core)**. Le fichier de projet spécifie que l’élément `TargetFramework` est défini sur `netcoreapp2.x`.

1. Choisissez la version SDK WebJobs 3.x des packages suivants :

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Définir la chaîne de connexion de stockage et la clé d’instrumentation Application Insights dans un *appsettings.json* fichier, à l’aide de l’infrastructure de configuration de .NET Core. Voici un exemple :

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Modifier le `Main` code de méthode pour ce faire. Voici un exemple :

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le SDK WebJobs, consultez la section [How to use the WebJobs SDK](../../app-service/webjobs-sdk-how-to.md) (Utilisation du Kit de développement logiciel (SDK) WebJobs).
