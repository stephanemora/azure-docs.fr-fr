---
title: Surveiller l’exécution des fonctions Azure
description: Découvrez comment utiliser Azure Application Insights avec Azure Functions pour surveiller l’exécution des fonctions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: glenga
ms.openlocfilehash: fecf6759dd7b277dda10fa2656e6ae9407490370
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922961"
---
# <a name="monitor-azure-functions"></a>Surveiller l’exécution des fonctions Azure

[Azure Functions](functions-overview.md) est intégré à [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) pour surveiller les fonctions. Cet article vous montre comment configurer Azure Functions pour envoyer des fichiers de journaux générés par le système à Application Insights.

Nous vous recommandons d’utiliser Application Insights, car il collecte des journaux, les performances et les données d’erreur. Automatiquement, il détecte les anomalies de performances et inclut des outils d’analytique puissants pour vous aider à diagnostiquer les problèmes et à comprendre l’utilisation de vos fonctions. Il a été conçu pour vous permettre d’améliorer continuellement les performances et la convivialité. Vous pouvez même utiliser Application Insights pendant le développement de projet application de fonction locale. Pour plus d’informations, consultez [What ' s Application Insights ?](../azure-monitor/app/app-insights-overview.md).

Comme l’instrumentation Application Insights requise est intégrée à Azure Functions, il vous suffit est une clé d’instrumentation valide pour connecter votre application de fonction à une ressource Application Insights.

## <a name="application-insights-pricing-and-limits"></a>Tarification et limites d’Application Insights

Vous pouvez essayer gratuitement l’intégration d’Application Insights avec les applications de fonctions. Il existe une limite quotidienne à la quantité de données peut être traitée gratuitement. Vous risquez d’atteindre cette limite pendant le test. Azure envoie des notifications sur le portail et par e-mail lorsque la limite quotidienne est proche. Si vous manquez pas ces alertes et a atteint la limite, les nouveaux journaux ne s’affiche dans les requêtes d’Application Insights. N’oubliez pas de la limite afin d’éviter les temps de résolution des problèmes inutiles. Pour plus d’informations, consultez l’article [Gérer la tarification et le volume de données dans Application Insights](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Activer l’intégration à Application Insights

Pour qu’une application de fonction envoie des données à Application Insights, elle a besoin de connaître la clé d’instrumentation d’une ressource Application Insights. Cette clé doit se trouver dans un paramètre d’application nommé **APPINSIGHTS_INSTRUMENTATIONKEY**.

### <a name="new-function-app-in-the-portal"></a>Nouvelle application de fonction dans le portail

Lorsque vous [créer votre function app dans le portail Azure](functions-create-first-azure-function.md), intégration d’Application Insights est activée par défaut. La ressource Application Insights a le même nom que votre application de fonction, et il est créé dans la même région ou dans la région la plus proche.

Pour passer en revue la ressource Application Insights en cours de création, sélectionnez-le pour développer le **Application Insights** fenêtre. Vous pouvez modifier le **nouveau nom de ressource** ou choisissez un autre **emplacement** dans un [zone géographique Azure](https://azure.microsoft.com/global-infrastructure/geographies/) où vous souhaitez stocker vos données.

![Activer Application Insights pendant la création d’une application de fonction](media/functions-monitoring/enable-ai-new-function-app.png)

Lorsque vous choisissez **créer**, une ressource Application Insights est créée avec votre function app, qui a le `APPINSIGHTS_INSTRUMENTATIONKEY` définies dans les paramètres de l’application. Tout est prêt à l’emploi.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Ajouter à une application de fonction existante 

Lorsque vous créez une application de fonction en utilisant le [Azure CLI](functions-create-first-azure-function-azure-cli.md), [Visual Studio](functions-create-your-first-function-visual-studio.md), ou [Visual Studio Code](functions-create-first-function-vs-code.md), vous devez créer la ressource Application Insights. Vous pouvez ensuite ajouter la clé d’instrumentation à partir de cette ressource comme paramètre d’application dans votre function app.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Premières versions de fonctions utilisaient une surveillance intégrée, ce qui n’est plus recommandé. Lorsque vous activez l’intégration d’Application Insights pour une function app, vous devez également [désactiver la journalisation intégrée](#disable-built-in-logging).  

## <a name="view-telemetry-in-monitor-tab"></a>Affichage des données de télémétrie dans l’onglet Surveiller

Avec [intégration d’Application Insights](#enable-application-insights-integration), vous pouvez afficher les données de télémétrie dans le **moniteur** onglet.

1. Dans la page d’application de fonction, sélectionnez une fonction qui a exécuté au moins une fois après que Application Insights a été configuré. Puis sélectionnez le **moniteur** onglet.

   ![Sélectionner l’onglet Surveiller](media/functions-monitoring/monitor-tab.png)

1. Sélectionnez **Actualiser** périodiquement, jusqu'à ce que la liste d’appels de fonction s’affiche.

   Il peut prendre jusqu'à cinq minutes pour la liste s’affiche alors que le client de télémétrie regroupe les données pour la transmission au serveur. (Le délai ne s’applique pas à la [Live Metrics Stream](../azure-monitor/app/live-stream.md). Le service se connecte à l’hôte Functions lorsque vous chargez la page, de sorte que les journaux d’activité sont transmis directement sur la page.)

   ![Liste d’appels](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Pour afficher les journaux d’activité liés à un appel de fonction spécifique, sélectionnez le lien de la colonne **Date** correspondant à cet appel.

   ![Lien des détails d’appel](media/functions-monitoring/invocation-details-link-ai.png)

   La sortie de journalisation pour cet appel s’affiche dans une nouvelle page.

   ![Détails des appels](media/functions-monitoring/invocation-details-ai.png)

Vous pouvez voir que les deux pages ont un **exécuter dans Application Insights** lien vers la requête Application Insights Analytique qui Récupère les données.

![Exécuter dans Application Insights](media/functions-monitoring/run-in-ai.png)

La requête suivante s’affiche. Vous pouvez voir que la liste d’appel est limitée à la dernière des 30 derniers jours. La liste affiche pas plus de 20 lignes (`where timestamp > ago(30d) | take 20`). La liste de détails de l’appel est pour les 30 derniers jours sans limite.

![Liste d’appels Application Insights Analytics](media/functions-monitoring/ai-analytics-invocation-list.png)

Pour plus d’informations, consultez la section [Interroger les données de télémétrie](#query-telemetry-data) dans la suite de cet article.

## <a name="view-telemetry-in-application-insights"></a>Afficher les données de télémétrie dans Application Insights

Pour ouvrir Application Insights à partir d’une application de fonction dans le portail Azure, accédez à l’application de fonction **vue d’ensemble** page. Sous **configuré fonctionnalités**, sélectionnez **Application Insights**.

![Ouvrir Application Insights à partir de la page de présentation d’application (fonction)](media/functions-monitoring/ai-link.png)

Pour plus d’informations sur l’utilisation d’Application Insights, consultez la [documentation d’Application Insights](https://docs.microsoft.com/azure/application-insights/). Cette section présente des exemples montrant comment afficher les données dans Application Insights. Si vous êtes déjà familiarisé avec Application Insights, vous pouvez accéder directement à [les sections sur la façon de configurer et personnaliser les données de télémétrie](#configure-categories-and-log-levels).

![Onglet vue d’ensemble des Insights d’application](media/functions-monitoring/metrics-explorer.png)

Les domaines d’Application Insights suivants peuvent être utiles lorsque vous évaluez le comportement, les performances et les erreurs dans vos fonctions :

| Tab | Description |
| ---- | ----------- |
| **[échecs](../azure-monitor/app/asp-net-exceptions.md)** |  Créer des graphiques et des alertes basées sur les échecs de fonction et les exceptions du serveur. Le **Nom de l’opération** est le nom de la fonction. Échecs de dépendances ne sont pas visibles à moins que vous implémentiez des données de télémétrie personnalisées pour les dépendances. |
| **[Performances](../azure-monitor/app/performance-counters.md)** | Analyser les problèmes de performances. |
| **Serveurs** | Afficher l’utilisation des ressources et débit par serveur. Ces données peuvent être utiles pour déboguer les scénarios où les fonctions ralentissent vos ressources sous-jacentes. Les serveurs sont appelés **instances de rôle cloud**. |
| **[Métriques](../azure-monitor/app/metrics-explorer.md)** | Créer des graphiques et des alertes qui sont basées sur les mesures. Métriques incluent le nombre d’appels de fonction, les temps d’exécution et les taux de réussite. |
| **[Live Metrics Stream](../azure-monitor/app/live-stream.md)** | Afficher les données de métriques tel qu’il a créé en temps réel. |

## <a name="query-telemetry-data"></a>Interroger les données de télémétrie

[Application Insights Analytique](../azure-monitor/app/analytics.md) vous donne accès à toutes les données de télémétrie sous la forme de tables dans une base de données. Analytics fournit un langage de requête pour l’extraction, la manipulation et la visualisation des données.

![Sélectionner Analytics](media/functions-monitoring/select-analytics.png)

![Exemple Analytics](media/functions-monitoring/analytics-traces.png)

Voici un exemple de requête qui montre la distribution des demandes par nœud Worker au cours des 30 dernières minutes.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Les tables qui sont disponibles sont affichées dans le **schéma** onglet sur la gauche. Les données générées par les appels de fonction sont disponibles dans les tables suivantes :

| Table | Description |
| ----- | ----------- |
| **traces** | Journaux créés par le runtime et par code de fonction. |
| **requests** | Une demande pour chaque appel de fonction. |
| **exceptions** | Toutes les exceptions levées par le runtime. |
| **customMetrics** | Le nombre d’appels réussies et échouées, taux de réussite et la durée. |
| **customEvents** | Événements suivis par le runtime, par exemple : requêtes HTTP qui déclenchent une fonction. |
| **performanceCounters** | Informations sur les performances des serveurs que les fonctions sont en cours d’exécution. |

Les autres tables sont pour les tests de disponibilité et les données de télémétrie client et le navigateur. Vous pouvez implémenter une télémétrie personnalisée en vue d’y ajouter des données.

Dans chaque table, un champ `customDimensions` contient certaines des données spécifiques à Azure Functions.  Par exemple, la requête suivante récupère toutes les traces dont le niveau de journal est `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Le runtime fournit le `customDimensions.LogLevel` et `customDimensions.Category` champs. Vous pouvez fournir des champs supplémentaires dans les journaux que vous écrivez dans votre code de fonction. Consultez [Journalisation structurée](#structured-logging) plus loin dans cet article.

## <a name="configure-categories-and-log-levels"></a>Configurer les catégories et les niveaux de journal

Vous pouvez utiliser Application Insights sans aucune configuration personnalisée. La configuration par défaut peut engendrer de grands volumes de données. Si vous utilisez un abonnement Azure Visual Studio, vous risquez d’atteindre votre plafond de données pour Application Insights. Plus loin dans cet article, vous allez apprendre à configurer et personnaliser les données que vos fonctions envoient à Application Insights. Pour une application de fonction, la journalisation est configurée dans le [host.json] fichier.

### <a name="categories"></a>Catégories

L’enregistreur d’événements d’Azure Functions inclut une *catégorie* par journal. La catégorie indique quelle partie du code d’exécution ou de votre code de fonction a écrit le journal. 

Le runtime Functions crée des journaux avec une catégorie qui commencent par « Host ». La « function started » « function executed », et les journaux « function completed » ont la catégorie « Host.Executor ». 

Si vous écrivez des journaux dans le code de votre fonction, leur catégorie est « Fonction ».

### <a name="log-levels"></a>Niveaux de journal

L’enregistreur d’événements Azure Functions inclut également un *niveau de journal* avec chaque journal. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) est une énumération, et le code d’entier indique l’importance relative :

|LogLevel    |Code|
|------------|---|
|Trace       | 0 |
|Déboguer       | 1 |
|Information | 2 |
|Avertissement     | 3 |
|Error       | 4 |
|Critique    | 5. |
|Aucun        | 6. |

Le niveau de journal `None` est expliqué dans la section suivante. 

### <a name="log-configuration-in-hostjson"></a>Configuration des journaux dans host.json

Le fichier [host.json] configure la quantité de journalisation qu’une application de fonction envoie à Application Insights. Pour chaque catégorie, vous indiquez le niveau de journal minimal à envoyer. Il existe deux exemples : le premier exemple cible le [runtime de Functions version 2.x](functions-versions.md#version-2x) (.NET Core) et le deuxième exemple concerne la version 1.x de Common language runtime.

### <a name="version-2x"></a>Version 2.x

Le runtime v2.x utilise la [hiérarchie des filtres de journalisation .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>Version 1.x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

Cet exemple montre comment configurer les règles suivantes :

* Pour les journaux ayant la catégorie `Host.Results` ou `Function`, envoyer uniquement `Error` niveau et versions ultérieures à Application Insights. Les journaux d’activité pour les niveaux `Warning` et inférieurs sont ignorés.
* En ce qui concerne les journaux d’activité de la catégorie `Host.Aggregator`, envoyer tous les journaux d’activité à Application Insights. Le niveau du journal `Trace` est identique à ce que certains enregistreurs d’événements appellent `Verbose`, mais utilisez `Trace` dans le fichier [host.json].
* Pour tous les autres journaux d’activité, envoyer uniquement les niveaux `Information` et supérieurs à Application Insights.

La valeur de catégorie dans [host.json] contrôle la journalisation de toutes les catégories qui commencent par la même valeur. `Host` dans [host.json] journalisation pour les contrôles `Host.General`, `Host.Executor`, `Host.Results`, et ainsi de suite.

Si [host.json] inclut plusieurs catégories qui commencent par la même chaîne, les plus longues sont traitées en priorité. Supposons que vous souhaitez que tous les éléments à partir de l’exécution à l’exception `Host.Aggregator` pour vous connecter à `Error` niveau, mais que vous voulez `Host.Aggregator` pour vous connecter à la `Information` niveau :

### <a name="version-2x"></a>Version 2.x 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>Version 1.x 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Pour supprimer tous les journaux d’activité relatifs à une catégorie, vous pouvez utiliser le niveau de journal `None`. Aucun journal n’est écrites avec cette catégorie, il n’existe aucun niveau de journal au-dessus de lui.

Les sections suivantes décrivent les principales catégories de journaux d’activité générées par le runtime. 

### <a name="category-hostresults"></a>Catégorie Host.Results

Ces journaux d’activité apparaissent sous la dénomination « requests » dans Application Insights. Ils indiquent la réussite ou l’échec d’une fonction.

![Graphique de demandes](media/functions-monitoring/requests-chart.png)

Tous ces journaux sont écrits au `Information` niveau. Si vous filtrez à `Warning` ou ultérieur, vous ne pourrez pas visualiser ces données.

### <a name="category-hostaggregator"></a>Catégorie Host.Aggregator

Ces journaux d’activité fournissent des nombres et des moyennes d’appels de fonction sur une période de temps [configurable](#configure-the-aggregator). La période par défaut est 30 secondes ou 1 000 résultats, selon la première de ces éventualités. 

Les journaux d’activité sont disponibles dans la table **customMetrics** dans Application Insights. Le nombre d’exécutions, taux de réussite et la durée sont des exemples.

![Requête portant sur customMetrics](media/functions-monitoring/custom-metrics-query.png)

Tous ces journaux sont écrits au `Information` niveau. Si vous filtrez à `Warning` ou ultérieur, vous ne pourrez pas visualiser ces données.

### <a name="other-categories"></a>Autres catégories

Tous les journaux d’activité des catégories autres que celles déjà mentionnées apparaissent dans la table **traces** dans Application Insights.

![Requête portant sur traces](media/functions-monitoring/analytics-traces.png)

Tous les journaux avec les catégories qui commencent par `Host` sont écrits par le runtime Functions. Le « Function started » et les journaux « Function completed » ont la catégorie `Host.Executor`. Pour les exécutions réussies, ces journaux sont `Information` niveau. Exceptions sont journalisées au `Error` niveau. En outre, le runtime crée des journaux d’activité de niveau `Warning`, par exemple, les messages de file d’attente envoyés à la file d’attente de messages incohérents.

Journaux écrits par votre code de fonction ont la catégorie `Function` et peut être n’importe quel niveau de journal.

## <a name="configure-the-aggregator"></a>Configurer le paramètre d’agrégation

Comme indiqué dans la section précédente, le runtime agrège les données sur les exécutions de fonction sur une période de temps. La période par défaut est 30 secondes ou 1 000 exécutions, selon la première de ces éventualités. Vous pouvez configurer ce paramètre dans le fichier [host.json].  Voici un exemple :

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurer l’échantillonnage

Application Insights a un [échantillonnage](../azure-monitor/app/sampling.md) fonctionnalité qui peut vous éviter de générer trop de données télémétriques sur terminée exécutions parfois des pics de charge. Lorsque le taux d’exécutions entrants dépasse un seuil spécifié, Application Insights commence à ignorer aléatoirement certains des exécutions entrantes. Le paramètre par défaut pour nombre maximal d’exécutions par seconde est 20 (cinq dans la version 1.x). Vous pouvez configurer l’échantillonnage dans [host.json].  Voici un exemple :

### <a name="version-2x"></a>Version 2.x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>Version 1.x 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

> [!NOTE]
> [L’échantillonnage](../azure-monitor/app/sampling.md) est activé par défaut. Si vous semblez être des données manquantes, vous devrez peut-être ajuster les paramètres d’échantillonnage pour s’ajuster à votre scénario d’analyse spécifique.

## <a name="write-logs-in-c-functions"></a>Écrire des journaux d’activité dans des fonctions C#

Vous pouvez écrire des journaux d’activité dans votre code de fonction qui apparaissent sous forme de traces dans Application Insights.

### <a name="ilogger"></a>ILogger

Utilisez un paramètre [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) dans vos fonctions au lieu d’un paramètre `TraceWriter`. Journaux créés à l’aide de `TraceWriter` accédez à Application Insights, mais `ILogger` vous permet de faire [journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

L’objet `ILogger` permet d’appeler les [méthodes d’extension `Log<level>` sur ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) pour créer des journaux. Le code suivant écrit `Information` journaux ayant la catégorie « Fonction ».

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Journalisation structurée

C’est l’ordre des espaces réservés, pas leurs noms, qui détermine les paramètres utilisés dans le message du journal. Supposons que vous avez le code suivant :

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Si vous conservez la même chaîne de message et que vous inversez l’ordre des paramètres, les valeurs dans le texte du message résultant ne sont pas situées à la bonne place.

Ce mode de gestion des espaces réservés vous permet d’effectuer une journalisation structurée. Application Insights stocke les paires nom-valeur de paramètre et la chaîne de message. Ainsi, les arguments du message deviennent des champs pouvant faire l’objet de requêtes.

Si votre appel de méthode logger ressemble à l’exemple précédent, vous pouvez interroger le champ `customDimensions.prop__rowKey`. Le `prop__` préfixe est ajouté pour vous assurer qu’aucun collisions entre les champs, le runtime et les champs ajoutés votre code de fonction ajoute.

Vous pouvez également exécuter une requête portant sur la chaîne de message d’origine en référençant le champ `customDimensions.prop__{OriginalFormat}`.  

Voici un exemple de représentation JSON des données `customDimensions` :

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Journalisation des métriques personnalisées

Dans les fonctions de script C#, vous pouvez utiliser la méthode d’extension `LogMetric` sur `ILogger` pour créer des métriques personnalisées dans Application Insights. Voici un exemple d’appel de méthode :

```csharp
logger.LogMetric("TestMetric", 1234);
```

Ce code est une alternative à l’appel `TrackMetric` à l’aide de l’API Application Insights pour .NET.

## <a name="write-logs-in-javascript-functions"></a>Écrire des journaux d’activité dans les fonctions JavaScript

Dans les fonctions Node.js, utilisez `context.log` pour écrire des journaux d’activité. Journalisation structurée n’est pas activée.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Journalisation des métriques personnalisées

Lorsque vous exécutez sur [version 1.x](functions-versions.md#creating-1x-apps) du runtime Functions, fonctions de Node.js peuvent utiliser le `context.log.metric` méthode pour créer des mesures personnalisées dans Application Insights. Cette méthode n’est pas actuellement pris en charge dans la version 2.x. Voici un exemple d’appel de méthode :

```javascript
context.log.metric("TestMetric", 1234);
```

Ce code est une alternative à l’appel `trackMetric` à l’aide du SDK Node.js pour Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Connecter les données de télémétrie personnalisées C# fonctions

Vous pouvez utiliser le package NuGet [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) pour envoyer des données de télémétrie personnalisées à Application Insights. L’exemple C# suivant utilise [l’API de télémétrie personnalisée](../azure-monitor/app/api-custom-events-metrics.md). L’exemple concerne une bibliothèque de classes .NET, mais le code Application Insights est le même pour le script C#.

### <a name="version-2x"></a>Version 2.x

Le runtime version 2.x utilise les nouvelles fonctionnalités d’Application Insights pour mettre automatiquement en corrélation les données de télémétrie avec l’opération en cours. Il est inutile de définir manuellement l’opération `Id`, `ParentId`, ou `Name` champs.

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

### <a name="version-1x"></a>Version 1.x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

N’appelez pas `TrackRequest` ou `StartOperation<RequestTelemetry>` , car vous verrez les demandes en double pour un appel de fonction.  Le runtime d’Azure Functions effectue automatiquement le suivi des demandes.

Ne définissez pas `telemetryClient.Context.Operation.Id`. Ce paramètre global entraîne une corrélation incorrecte lorsque de nombreuses fonctions sont exécutées simultanément. Au lieu de cela, créez une nouvelle instance de télémétrie (`DependencyTelemetry`, `EventTelemetry`) et modifiez sa propriété `Context`. Passez dans l’instance de télémétrie correspondant à la méthode `Track` sur `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Cette méthode garantit que les données de télémétrie possèdent les détails de corrélation correctes pour l’appel de fonction en cours.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Journal des données de télémétrie personnalisées dans les fonctions JavaScript

Le [SDK Node.js pour Application Insights](https://www.npmjs.com/package/applicationinsights) est en version bêta. Voici un exemple de code qui envoie des données de télémétrie personnalisées à Application Insights :

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    context.done();
};
```

Le `tagOverrides` jeux de paramètres le `operation_Id` à l’ID d’invocation. de la fonction Ce paramètre permet de mettre en corrélation toutes les données de télémétrie générées automatiquement et personnalisées pour un appel de fonction donné.

## <a name="dependencies"></a>Les dépendances

Fonctions v2 collecte automatiquement les dépendances pour les demandes HTTP, ServiceBus et SQL.

Vous pouvez écrire du code personnalisé pour afficher les dépendances. Pour obtenir des exemples, consultez l’exemple de code dans le [ C# section de données de télémétrie personnalisées](#log-custom-telemetry-in-c-functions). L’exemple de code entraîne un *cartographie d’application* dans Application Insights, qui ressemble à l’image suivante :

![Mise en correspondance d’applications](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>Signaler des problèmes

Pour signaler un problème avec l’intégration d’Application Insights dans Azure Functions, ou pour faire une suggestion ou une demande, [créez un problème dans GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Journaux d’activité en continu

Lors du développement d’une application, il est utile de visualiser des informations de journalisation en temps quasi réel. Vous pouvez afficher un flux de fichiers journaux générés par vos fonctions dans le portail Azure ou dans une session de ligne de commande sur votre ordinateur local.

Cela équivaut à la sortie vue lorsque vous déboguez vos fonctions au cours de [développement local](functions-develop-local.md). Pour plus d’informations, consultez [Diffusion en continu des journaux d’activité](../app-service/troubleshoot-diagnostic-logs.md#streamlogs).

> [!NOTE]
> Diffusion en continu des journaux prennent en charge qu’une seule instance de l’hôte Functions. Lorsque votre fonction est mis à l’échelle à plusieurs instances, les données à partir d’autres instances ne sont pas affichées dans le flux du journal. Le [Live Metrics Stream](../azure-monitor/app/live-stream.md) dans Application Insights pris en charge plusieurs instances. Également dans quasiment en temps réel, analytique de diffusion en continu est également basés sur [données échantillonnées](#configure-sampling).

### <a name="portal"></a>Portail

Pour afficher les journaux de diffusion en continu dans le portail, sélectionnez le **fonctionnalités de la plateforme** onglet dans votre function app. Ensuite, sous **surveillance**, choisissez **diffusion de journaux**.

![Activer les journaux de diffusion en continu dans le portail](./media/functions-monitoring/enable-streaming-logs-portal.png)

Il connecte à votre application dans le journal de service de diffusion en continu et journaux d’application sont affichés dans la fenêtre. Vous pouvez basculer entre **journaux d’Application** et **journaux du serveur Web**.  

![Afficher les journaux de diffusion en continu dans le portail](./media/functions-monitoring/streaming-logs-window.png)

### <a name="azure-cli"></a>Azure CLI

Vous pouvez activer les journaux de diffusion en continu à l’aide de la [Azure CLI](/cli/azure/install-azure-cli). Utilisez les commandes suivantes pour vous connecter, choisir votre abonnement et diffuser les fichiers journaux :

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Vous pouvez activer les journaux de diffusion en continu à l’aide de [Azure PowerShell](/powershell/azure/overview). Pour PowerShell, utilisez les commandes suivantes pour ajouter votre compte Azure, choisissez votre abonnement et diffuser les fichiers journaux :

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Désactiver la journalisation intégrée

Lorsque vous activez Application Insights, désactivez la journalisation intégrée qui utilise le stockage Azure. La journalisation intégrée est utile pour tester avec des charges de travail légères, mais n’est pas prévue pour la production de charge élevée. Pour surveiller la production, nous recommandons Application Insights. Si la journalisation intégrée est utilisée en production, l’enregistrement de journal peut être incomplet en raison de la limitation sur le stockage Azure.

Pour désactiver la journalisation intégrée, supprimez le paramètre d’application `AzureWebJobsDashboard`. Pour plus d’informations sur la suppression de paramètres d’application dans le portail Azure, consultez la section **Paramètres de l’application** dans [Comment gérer une application de fonction dans le portail Azure](functions-how-to-use-azure-function-app-settings.md#settings). Avant de supprimer le paramètre d’application, assurez-vous qu’aucune des fonctions existantes dans la même application de fonction n’utilisent le paramètre pour des déclencheurs de stockage Azure ou des liaisons.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Application Insights](/azure/application-insights/)
* [Journalisation ASP.NET Core](/aspnet/core/fundamentals/logging/)

[host.json]: functions-host-json.md
