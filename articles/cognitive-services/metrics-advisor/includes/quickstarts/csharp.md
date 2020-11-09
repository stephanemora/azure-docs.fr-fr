---
title: Démarrage rapide de Metrics Advisor C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 10/14/2020
ms.author: mbullwin
ms.openlocfilehash: 8e9b2c69344a19c027f72983a02834aee2e14ccf
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186928"
---
[Documentation de référence](https://aka.ms/azsdk/net/docs/ref/metricsadvisor) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src) | [Package (NuGet)](https://www.nuget.org/packages/Azure.AI.MetricsAdvisor) | [Exemples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* Version actuelle de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Une fois que vous disposez de votre abonnement Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Créer une ressource Metrics Advisor"  target="_blank">créez une ressource Metrics Advisor<span class="docon docon-navigate-external x-hidden-focus"></span></a> dans le portail Azure pour déployer votre instance Metrics Advisor.  
* Votre propre base de données SQL avec des données de séries chronologiques.
   
> [!TIP]
> * Vous trouverez des exemples Metrics Advisor .NET sur [GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md).
> * 10 à 30 minutes peuvent être nécessaires pour que votre ressource Metrics Advisor déploie une instance de service que vous puissiez utiliser. Cliquez sur **Accéder à la ressource** une fois le déploiement réussi. Après le déploiement, vous pouvez commencer à utiliser votre instance Metrics Advisor avec le portail web et l’API REST. 
> * Vous trouverez l’URL de l’API REST dans le portail Azure, dans la section **Vue d’ensemble** de votre ressource. Voici le résultat :
>    * `https://<instance-name>.cognitiveservices.azure.com/`
   
## <a name="setting-up"></a>Configuration

### <a name="install-the-client-library"></a>Installer la bibliothèque de client 

Une fois que vous avez créé un projet, installez la bibliothèque de client en cliquant avec le bouton droit sur la solution de projet dans l’ **Explorateur de solutions** et en sélectionnant **Gérer les packages NuGet**. Dans le gestionnaire de package qui s’ouvre, sélectionnez **Parcourir** , cochez **Inclure la préversion** et recherchez `Azure.AI.MetricsAdvisor`. Sélectionnez la version `1.0.0-beta.1`, puis **Installer**. 

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `metrics-advisor-quickstart`. Cette commande crée un projet C# simple nommé « Hello World » avec un seul fichier source : *program.cs*. 

```console
dotnet new console -n metrics-advisor-quickstart
```

Déplacez vos répertoires vers le dossier d’application nouvellement créé. Vous pouvez générer l’application avec :

```console
dotnet build
```

La sortie de génération ne doit contenir aucun avertissement ni erreur. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installer la bibliothèque de client 

Si vous utilisez un IDE autre que Visual Studio, vous pouvez installer la bibliothèque de client Metrics Advisor pour .NET avec la commande suivante :

```console
dotnet add package Azure.AI.MetricsAdvisor --version 1.0.0-beta.1
```

> [!TIP]
> Vous voulez voir l’intégralité du fichier de code de démarrage rapide à la fois ? Vous le trouverez sur [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.MetricsAdvisor_1.0.0-beta.1/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples), qui contient les exemples de code utilisés dans ce guide de démarrage rapide.

À partir du répertoire du projet, ouvrez le fichier *program.cs* et ajoutez ce qui suit en utilisant les directives `using` suivantes :

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Azure.AI.MetricsAdvisor.Administration;
using Azure.AI.MetricsAdvisor.Models;
```

Dans la méthode `Main()` de l’application, ajoutez des appels pour les méthodes utilisées dans ce guide de démarrage rapide. Vous les créerez ultérieurement.

```csharp
static void Main(string[] args){
    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>Modèle objet

Les classes suivantes gèrent certaines des fonctionnalités principales du kit SDK Metrics Advisor C#.

|Name|Description|
|---|---|
| [MetricsAdvisorClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs) | **Utilisé pour**  : <br> – Lister les incidents <br> – Lister les causes premières des incidents <br> – Récupérer les données de série chronologique d’origine et les données de série chronologique enrichies par le service <br> – Lister les alertes <br> – Ajouter des commentaires pour ajuster votre modèle |
| [MetricsAdvisorAdministrationClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs)| **Vous permet d’effectuer les tâches suivantes :** <br> – Gérer les flux de données <br> – Paramétrer les configurations de détection des anomalies <br> – Paramétrer les configurations d’alertes d’anomalies <br> – Gérer les hooks  |
| [DataFeed](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeed/DataFeed.cs)| **Ce que Metrics Advisor ingère de votre source de données. Un `DataFeed` contient des lignes de :** <br> – Horodatages <br> – Zéro ou plusieurs dimensions <br> – Une ou plusieurs mesures  |
| [Mesure](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeedMetric.cs)| Un objet `Metric` est une mesure quantifiable utilisée pour surveiller et évaluer l’état d’un processus d’entreprise spécifique. Il peut s’agir d’une combinaison de plusieurs valeurs de séries chronologiques divisées en dimensions. Par exemple, une métrique d’intégrité du Web peut contenir des dimensions pour le nombre d’utilisateurs et le marché en-US. |

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client Metrics Advisor pour .NET :

* [Authentifier le client](#authenticate-the-client)
* [Ajouter un flux de données](#add-a-data-feed)
* [Vérifier l’état d’ingestion](#check-the-ingestion-status)
* [Configurer la détection des anomalies](#configure-anomaly-detection)
* [Créer un hook](#create-a-hook)
* [Créer une configuration d’alerte](#create-an-alert-configuration)
* [Interroger l’alerte](#query-the-alert)

## <a name="authenticate-the-client"></a>Authentifier le client

Dans la classe `Program` de l’application, créez des variables pour les clés et le point de terminaison de votre ressource.

> [!IMPORTANT]
> Accédez au portail Azure. Si la ressource Metrics Advisor que vous avez créée dans la section **Prérequis** a été déployée, cliquez sur le bouton **Accéder à la ressource** sous **Étapes suivantes**. Vos clés d’abonnement et le point de terminaison se trouvent dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. <br><br>Pour récupérer votre clé API, vous devez accéder à [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net). Sélectionnez les éléments appropriés : **Annuaire** , **Abonnements** et **Espace de travail** pour votre ressource, puis choisissez **Bien démarrer**. Vous pourrez ensuite récupérer vos clés API à partir de [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key).   
>
> N’oubliez pas de supprimer la clé de votre code une fois que vous avez terminé, et ne la postez jamais publiquement. Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Pour plus d’informations, consultez l’article sur la [sécurité](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) de Cognitive Services.

Une fois que vous avez l’abonnement et les clés d’API, créez un MetricsAdvisorKeyCredential. Avec le point de terminaison et les informations d’identification de la clé, vous pouvez créer un [`MetricsAdvisorClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs) :

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var client = new MetricsAdvisorClient(new Uri(endpoint), credential);
```

Vous pouvez également créer un [`MetricsAdvisorAdministrationClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs) pour effectuer des opérations d’administration :

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var adminClient = new MetricsAdvisorAdministrationClient(new Uri(endpoint), credential);
```

## <a name="add-a-data-feed"></a>Ajouter un flux de données

Metrics Advisor prend en charge plusieurs types de sources de données. Dans cet exemple, nous allons montrer comment créer un `DataFeed` qui extrait des données d’un serveur SQL Server. Remplacez `connection_String` par votre propre chaîne de connexion au serveur SQL Server, puis remplacez `query` par une requête qui retourne vos données à un horodatage unique. Vous devrez également ajuster les valeurs `metric` et `dimension` en fonction de vos données personnalisées.


```csharp
string sqlServerConnectionString = "<connection_String>";
string sqlServerQuery = "<query>";

var dataFeedName = "Sample data feed";
var dataFeedSource = new MySqlDataFeedSource(sqlServerConnectionString, sqlServerQuery);
var dataFeedGranularity = new DataFeedGranularity(DataFeedGranularityType.Daily);

var dataFeedMetrics = new List<DataFeedMetric>()
{
    new DataFeedMetric("cost"),
    new DataFeedMetric("revenue")
};
var dataFeedDimensions = new List<MetricDimension>()
{
    new MetricDimension("category"),
    new MetricDimension("city")
};
var dataFeedSchema = new DataFeedSchema(dataFeedMetrics)
{
    DimensionColumns = dataFeedDimensions
};

var ingestionStartTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var dataFeedIngestionSettings = new DataFeedIngestionSettings(ingestionStartTime);

Response<DataFeed> response = await adminClient.CreateDataFeedAsync(dataFeedName, dataFeedSource,
    dataFeedGranularity, dataFeedSchema, dataFeedIngestionSettings);

DataFeed dataFeed = response.Value;

Console.WriteLine($"Data feed ID: {dataFeed.Id}");

// Only the ID of the data feed is known at this point. You can perform another service
// call to get more information, such as status, created time, the list of administrators,
// or the metric IDs.

response = await adminClient.GetDataFeedAsync(dataFeed.Id);

dataFeed = response.Value;

Console.WriteLine($"Data feed status: {dataFeed.Status.Value}");
Console.WriteLine($"Data feed created time: {dataFeed.CreatedTime.Value}");

Console.WriteLine($"Data feed administrators:");
foreach (string admin in dataFeed.Options.Administrators)
{
    Console.WriteLine($" - {admin}");
}

Console.WriteLine($"Metric IDs:");
foreach (DataFeedMetric metric in dataFeed.Schema.MetricColumns)
{
    Console.WriteLine($" - {metric.MetricName}: {metric.MetricId}");
}
```

## <a name="check-the-ingestion-status"></a>Vérifier l’état d’ingestion

Vérifiez l’état d’ingestion d’un `DataFeed` créé précédemment :

```csharp
string dataFeedId = "<dataFeedId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.Parse("2020-09-09T00:00:00Z");
var options = new GetDataFeedIngestionStatusesOptions(startTime, endTime);

Console.WriteLine("Ingestion statuses:");
Console.WriteLine();

int statusCount = 0;

await foreach (DataFeedIngestionStatus ingestionStatus in adminClient.GetDataFeedIngestionStatusesAsync(dataFeedId, options))
{
    Console.WriteLine($"Timestamp: {ingestionStatus.Timestamp}");
    Console.WriteLine($"Status: {ingestionStatus.Status.Value}");
    Console.WriteLine($"Service message: {ingestionStatus.Message}");
    Console.WriteLine();

    // Print at most 10 statuses.
    if (++statusCount >= 10)
    {
        break;
    }
}
```

## <a name="configure-anomaly-detection"></a>Configurer la détection des anomalies 

Créez une configuration de détection des anomalies pour indiquer au service les points de données qui doivent être considérés comme des anomalies.

```csharp
string metricId = "<metricId>";
string configurationName = "Sample anomaly detection configuration";

var hardThresholdSuppressCondition = new SuppressCondition(1, 100);
var hardThresholdCondition = new HardThresholdCondition(AnomalyDetectorDirection.Down, hardThresholdSuppressCondition)
{
    LowerBound = 5.0
};

var smartDetectionSuppressCondition = new SuppressCondition(4, 50);
var smartDetectionCondition = new SmartDetectionCondition(10.0, AnomalyDetectorDirection.Up, smartDetectionSuppressCondition);

var detectionCondition = new MetricWholeSeriesDetectionCondition()
{
    HardThresholdCondition = hardThresholdCondition,
    SmartDetectionCondition = smartDetectionCondition,
    CrossConditionsOperator = DetectionConditionsOperator.Or
};

var detectionConfiguration = new AnomalyDetectionConfiguration(metricId, configurationName, detectionCondition);

Response<AnomalyDetectionConfiguration> response = await adminClient.CreateMetricAnomalyDetectionConfigurationAsync(detectionConfiguration);

detectionConfiguration = response.Value;

Console.WriteLine($"Anomaly detection configuration ID: {detectionConfiguration.Id}");
```

### <a name="create-a-hook"></a>Créer un hook

Metrics Advisor prend en charge les classes `EmailHook` et `WebHook` pour s’abonner aux notifications d’alertes. Dans cet exemple, nous allons montrer comment créer un `EmailHook`. Vous devez transmettre le hook à une configuration d’alerte d’anomalie pour commencer à obtenir des notifications. Pour plus d’informations, consultez l’exemple de [création d’une configuration d’alerte d’anomalie](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor#create-an-anomaly-alert-configuration).

```csharp
string hookName = "Sample hook";
var emailsToAlert = new List<string>()
{
    "email1@sample.com",
    "email2@sample.com"
};

var emailHook = new EmailNotificationHook(hookName, emailsToAlert);

Response<NotificationHook> response = await adminClient.CreateHookAsync(emailHook);

NotificationHook hook = response.Value;

Console.WriteLine($"Hook ID: {hook.Id}");
```

##  <a name="create-an-alert-configuration"></a>Créer une configuration d’alerte

Créez un `AnomalyAlertConfiguration` pour indiquer au service quelles anomalies doivent déclencher des alertes.

```csharp
string hookId = "<hookId>";
string anomalyDetectionConfigurationId = "<anomalyDetectionConfigurationId>";

string configurationName = "Sample anomaly alert configuration";
var idsOfHooksToAlert = new List<string>() { hookId };

var scope = MetricAnomalyAlertScope.GetScopeForWholeSeries();
var metricAlertConfigurations = new List<MetricAnomalyAlertConfiguration>()
{
    new MetricAnomalyAlertConfiguration(anomalyDetectionConfigurationId, scope)
};

AnomalyAlertConfiguration alertConfiguration = new AnomalyAlertConfiguration(configurationName, idsOfHooksToAlert, metricAlertConfigurations);

Response<AnomalyAlertConfiguration> response = await adminClient.CreateAnomalyAlertConfigurationAsync(alertConfiguration);

alertConfiguration = response.Value;

Console.WriteLine($"Alert configuration ID: {alertConfiguration.Id}");
```

### <a name="query-the-alert"></a>Interroger l’alerte

Examinez les alertes créées par une configuration d’alerte d’anomalie donnée et listez les anomalies qui ont déclenché ces alertes.

```csharp
string anomalyAlertConfigurationId = "<anomalyAlertConfigurationId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.UtcNow;
var options = new GetAlertsOptions(startTime, endTime, TimeMode.AnomalyTime);

int alertCount = 0;

await foreach (AnomalyAlert alert in client.GetAlertsAsync(anomalyAlertConfigurationId, options))
{
    Console.WriteLine($"Alert at timestamp: {alert.Timestamp}");
    Console.WriteLine($"Id: {alert.Id}");
    Console.WriteLine($"Anomalies that triggered this alert:");

    await foreach (DataAnomaly anomaly in client.GetAnomaliesForAlertAsync(anomalyAlertConfigurationId, alert.Id))
    {
        Console.WriteLine("  Anomaly:");
        Console.WriteLine($"    Status: {anomaly.Status.Value}");
        Console.WriteLine($"    Severity: {anomaly.Severity}");
        Console.WriteLine($"    Series key:");

        foreach (KeyValuePair<string, string> keyValuePair in anomaly.SeriesKey.AsDictionary())
        {
            Console.WriteLine($"      Dimension '{keyValuePair.Key}': {keyValuePair.Value}");
        }

        Console.WriteLine();
    }

    // Print at most 3 alerts.
    if (++alertCount >= 3)
    {
        break;
    }
}
```

### <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application à partir de votre répertoire d’application avec la commande `dotnet run`.

```dotnet
dotnet run
```