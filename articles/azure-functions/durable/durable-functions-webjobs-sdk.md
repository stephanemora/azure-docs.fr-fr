---
title: Guide d’exécution de Fonctions durables en tant que WebJobs - Azure
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
ms.openlocfilehash: 427c3601d6a4ca65407a98d54b0206cde9af4235
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52638394"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Guide d’exécution de Fonctions durables en tant que WebJobs

[Azure Functions](../functions-overview.md) et l’extension [Fonctions durables](durable-functions-overview.md) reposent sur le [SDK WebJobs](../../app-service/web-sites-create-web-jobs.md). L’élément `JobHost` dans le SDK WebJobs correspond au runtime dans Azure Functions. Si vous devez contrôler le comportement `JobHost` en fonction de paramètres impossibles dans Azure Functions, vous pouvez développer et exécuter Fonctions durables en vous appuyant sur le SDK WebJobs. Vous pouvez ensuite exécuter votre extension Fonctions durables dans un Azure WebJob ou dans un emplacement où est exécutée une application de console.

L’exemple de chaînage Fonctions durables est disponible dans les versions de SDK WebJobs : téléchargez ou clonez le [référentiel Fonctions durables](https://github.com/azure/azure-functions-durable-extension/), puis accédez au dossier *samples\\webjobssdk\\chaining*.

## <a name="prerequisites"></a>Prérequis

Cet article suppose que vous connaissez les principes de base du SDK WebJobs, du développement de bibliothèque de classes C# pour Azure Functions et de l’extension Fonctions durables. Si vous souhaitez consulter une présentation de ces thématiques, accédez aux ressources suivantes :

* [Prise en main du Kit de développement logiciel (SDK) WebJobs](../../app-service/webjobs-sdk-get-started.md)
* [Créer votre première fonction à l’aide de Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Fonctions durables](durable-functions-sequence.md)

Pour accomplir les étapes décrites dans cet article :

* [Installez Visual Studio 2017 version 15.6 ou ultérieure](https://docs.microsoft.com/visualstudio/install/) avec la charge de travail de **développement Azure**.

  Si vous possédez déjà Visual Studio, mais que vous n’avez pas cette charge de travail, ajoutez-la en sélectionnant **Outils > Get Tools and Features** (Obtenir des outils et des fonctionnalités). 

  (Vous pouvez utilisez en lieu et place [Visual Studio Code](https://code.visualstudio.com/), mais certaines des instructions sont spécifiques à Visual Studio.)

* Installez et exécutez [l’émulateur de stockage Azure](../../storage/common/storage-use-emulator.md) version 5.2 ou plus. Sinon, vous pouvez mettre à jour le fichier *App.config* avec une chaîne de connexion Stockage Azure.

## <a name="webjobs-sdk-versions"></a>Versions du SDK WebJobs

Cet article vous explique comment développer un projet SDK WebJobs 2.x (équivalent à Azure Functions version 1.x). Pour plus d’informations sur la version 3.x, consultez [SDK WebJobs 3.x](#webjobs-sdk-3x) plus bas dans cet article. 

## <a name="create-console-app"></a>Créer une application console

Un projet SDK WebJobs est simplement un projet d’application console avec les packages NuGet appropriés installés.

Dans Visual Studio, dans la boîte de dialogue **Nouveau projet**, sélectionnez **Bureau classique Windows > Application console (.NET Framework)**. Dans le fichier de projet, l’élément `TargetFrameworkVersion` doit être défini sur `v4.6.1`.

Visual Studio propose également un modèle de projet WebJob, que vous pouvez utiliser en sélectionnant **Cloud > Azure WebJob (.NET Framework)**. Ce modèle installe de nombreux packages, qui ne vous seront peut-être pas tous utiles.

## <a name="install-nuget-packages"></a>Installer les packages NuGet

Vous avez besoin des packages NuGet pour le SDK WebJobs, les liaisons essentielles, l’infrastructure de journalisation et l’extension Tâche durable. Voici les commandes de la **console du Gestionnaire de package** pour ces packages, avec les derniers numéros de version stables à la date de rédaction de cet article :

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Vous avez également besoin de fournisseurs de journalisation. Les commandes suivantes installent le fournisseur Application Insights et l’élément `ConfigurationManager`. L’élément `ConfigurationManager` vous permet de récupérer la clé d’instrumentation Application Insights des paramètres d’application.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

La commande suivante installe le fournisseur de console :

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Code JobHost

Pour utiliser l’extension Fonctions durables, appelez `UseDurableTask` sur l’objet `JobHostConfiguration` dans votre méthode `Main` :

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Pour obtenir la liste des propriétés que vous pouvez définir dans l’objet `DurableTaskExtension`, consultez le fichier [host.json](../functions-host-json.md#durabletask).

La méthode `Main` sert également à définir les fournisseurs de journalisation. L’exemple suivant configure les fournisseurs de la console et Application Insights.

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

Le code écrit pour les fonctions du SDK WebJobs est légèrement différent de celui écrit pour le service Azure Functions.

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

Dans un projet de SDK WebJobs, vous pouvez appeler des méthodes sur l’objet du client d’orchestration au lieu d’envoyer des requêtes HTTP. Les méthodes suivantes correspondent aux trois tâches pouvant être effectuées avec l’API de gestion HTTP :

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

La fonction du client d’orchestration de l’exemple de projet démarre la fonction d’orchestration, puis entre dans une boucle qui appelle `GetStatusAsync` toutes les 2 secondes :

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

Cette section fournit une vue d’ensemble de l’exécution de l’[exemple de projet](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Pour obtenir des instructions détaillées sur la méthode d’exécution en local du projet SDK WebJobs et son déploiement sur une tâche web Azure, consultez la section [Prise en main du kit de développement logiciel (SDK) WebJobs](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Exécution locale

1. Assurez-vous que l’émulateur de stockage est en cours d’exécution (consultez les [Prérequis](#prerequisites)).

1. Si vous souhaitez afficher les fichiers journaux dans Application Insights lorsque vous exécutez en local :

  a. Créez une ressource Application Insights, de type**Général**.

  b. Enregistrez la clé d’instrumentation dans le fichier *App.config*.

1. Exécutez le projet.

### <a name="run-in-azure"></a>Exécuter dans Azure

1. Créez une application web et un compte de stockage.

1. Dans l’application web, enregistrez la chaîne de connexion de stockage dans un paramètre d’application nommé AzureWebJobsStorage.

1. Créez une ressource Application Insights, de type**Général**.

1. Enregistrez la clé d’instrumentation dans un paramètre d’application nommé APPINSIGHTS_INSTRUMENTATIONKEY.

1. Déployer le projet en tant qu’objet WebJob.

## <a name="webjobs-sdk-3x"></a>SDK WebJobs 3.x

Le principal changement introduit par la version 3.x est l’utilisation de .NET Core au lieu de .NET Framework. Pour créer un projet 3.x, les instructions demeurent identiques, avec les exceptions suivantes :

1. Créez une application console .NET Core. Dans Visual Studio, dans la boîte de dialogue **Nouveau Projet**, sélectionnez **.NET Core > App. de console (.NET Core)**. Le fichier de projet spécifie que l’élément `TargetFramework` est défini sur `netcoreapp2.0`.

1. Choisissez la version préliminaire 3.x des packages suivants :

  * `Microsoft.Azure.WebJobs.Extensions`
  * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Modifier le code de méthode `Main` afin d’obtenir la chaîne de connexion de stockage et la clé d’instrumentation Application Insights à partir d’un fichier *appsettings.json*, à l’aide de l’infrastructure de configuration .NET Core.  Voici un exemple :

   ```cs
   static void Main(string[] args)
   {
       var builder = new ConfigurationBuilder()
           .SetBasePath(Directory.GetCurrentDirectory())
           .AddJsonFile("appsettings.json");

       var appSettingsConfig = builder.Build();

       using (var loggerFactory = new LoggerFactory())
       {
           var config = new JobHostConfiguration();

           config.DashboardConnectionString = "";
           config.StorageConnectionString = 
               appSettingsConfig.GetConnectionString("AzureWebJobsStorage");
           var instrumentationKey =
               appSettingsConfig["APPINSIGHTS_INSTRUMENTATIONKEY"];

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

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le SDK WebJobs, consultez la section [How to use the WebJobs SDK](../../app-service/webjobs-sdk-how-to.md) (Utilisation du Kit de développement logiciel (SDK) WebJobs).

