---
title: Configurer l’analyse pour Azure Functions
description: Découvrez comment connecter votre application de fonction à Application Insights à des fins de surveillance et de configuration de la collecte de données.
ms.date: 8/31/2020
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.openlocfilehash: 3afe10184ba2b3f0eba02111b98b31e86b26e075
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006478"
---
# <a name="how-to-configure-monitoring-for-azure-functions"></a>Comment configurer l’analyse pour Azure Functions

Azure Functions s’intègre à Application Insights pour vous permettre de mieux analyser vos applications de fonction. Application Insights, fonctionnalité d’Azure Monitor, est un service extensible de gestion des performances des applications (APM) qui collecte les données générées par votre application de fonction, notamment les informations que votre application écrit dans les journaux. L’intégration d’Application Insights est généralement activée lors de la création de votre application de fonction. Si votre application n’a pas de clé d’instrumentation définie, vous devez d’abord [activer l’intégration d’Application Insights](#enable-application-insights-integration). 

Vous pouvez utiliser Application Insights sans en personnaliser la configuration. La configuration par défaut risque d’engendrer d'importants volumes de données. Si vous utilisez un abonnement Azure Visual Studio, vous risquez d’atteindre votre plafond de données pour Application Insights. Pour en savoir plus sur les coûts liés à Application Insights, consultez [Gérer l’utilisation et les coûts d’Application Insights](../azure-monitor/app/pricing.md). Pour plus d’informations, consultez [Solutions avec un volume élevé de télémétrie](#solutions-with-high-volume-of-telemetry).

Plus loin dans cet article, vous découvrirez comment configurer et personnaliser les données que vos fonctions envoient à Application Insights. La journalisation d'une application de fonction est configurée dans le fichier [host.json]. 

> [!NOTE]
> Vous pouvez utiliser des paramètres d’application spécialement configurés pour représenter des paramètres spécifiques dans un fichier host.json pour un environnement spécifique. Cela vous permet de modifier efficacement les paramètres host.json sans avoir à republier le fichier host.json dans votre projet. Pour plus d’informations, consultez [Substituer les valeurs de host.json](functions-host-json.md#override-hostjson-values).

## <a name="configure-categories"></a>Configurer des catégories

L’enregistreur d’événements d’Azure Functions inclut une *catégorie* par journal. La catégorie indique quelle partie du code d’exécution ou de votre code de fonction a écrit le journal. Les catégories diffèrent entre la version 1.x et les versions ultérieures. Le tableau suivant décrit les principales catégories de journaux d’activité générées par le runtime. 

# <a name="v2x"></a>[v2.x+](#tab/v2)

| Category | Table de charge de travail | Description |
| ----- | ----- | ----- |
| **`Function.<YOUR_FUNCTION_NAME>`** | **dependencies**| Les données de dépendance sont collectées automatiquement pour certains services. Pour les exécutions ayant réussi, ces journaux d’activité sont de niveau `Information`. Pour plus d'informations, consultez [Dépendances](functions-monitoring.md#dependencies). Les exceptions sont journalisées au niveau `Error`. Le runtime crée également des journaux de niveau `Warning`, par exemple lorsque des messages de la file d’attente sont envoyés à la file d’attente de [messages incohérents](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>`** | **customMetrics**<br/>**customEvents** | Les SDK C# et JavaScript vous permettent de collecter des métriques personnalisées et de journaliser des événements personnalisés. Pour plus d’informations, consultez [Données de télémétrie personnalisées](functions-monitoring.md#custom-telemetry-data).|
| **`Function.<YOUR_FUNCTION_NAME>`** | **traces**| Inclut les journaux des fonctions démarrées et terminées pour des exécutions de fonctions spécifiques. Pour les exécutions ayant réussi, ces journaux d’activité sont de niveau `Information`. Les exceptions sont journalisées au niveau `Error`. Le runtime crée également des journaux de niveau `Warning`, par exemple lorsque des messages de la file d’attente sont envoyés à la file d’attente de [messages incohérents](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>.User`** | **traces**| Les journaux générés par l’utilisateur, qui peuvent être de n’importe quel niveau de journalisation. Pour en savoir plus sur l’écriture dans les journaux à partir de vos fonctions, consultez la page sur les [écritures dans des journaux](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Ces journaux d’activité générés par le runtime fournissent des nombres et des moyennes d’appels de fonction sur une période de temps [configurable](#configure-the-aggregator). La période par défaut est 30 secondes ou 1 000 résultats, selon la première de ces éventualités. Le nombre d’exécutions, le taux de réussite et la durée en sont des exemples. Tous ces journaux sont écrits au niveau `Information`. Si vous filtrez sur le niveau `Warning` ou supérieur, vous ne pouvez pas visualiser ces données. |
| **`Host.Results`** | **requests** | Ces journaux générés par le runtime indiquent la réussite ou l’échec d’une fonction. Tous ces journaux sont écrits au niveau `Information`. Si vous filtrez sur le niveau `Warning` ou supérieur, vous ne pouvez pas visualiser ces données. |
| **`Microsoft`** | **traces** | Catégorie de journal complète qui reflète un composant du runtime .NET appelé par l’hôte.  |
| **`Worker`** | **traces** | Journaux générés par le processus de traitement des langages pour les langages autres que .NET. Les journaux de processus de traitement des langages peuvent également être journalisés dans une catégorie `Microsoft.*`, telle que `Microsoft.Azure.WebJobs.Script.Workers.Rpc.RpcFunctionInvocationDispatcher`. Ces journaux sont écrits au niveau `Information`.|

> [!NOTE]
> Pour les fonctions de la bibliothèque de classes .NET, ces catégories partent du principe que vous utilisez `ILogger` et non `ILogger<T>`. Pour plus d’informations, consultez la [documentation relative à Functions ILogger](functions-dotnet-class-library.md#ilogger). 

# <a name="v1x"></a>[v1.x](#tab/v1)

| Category | Table de charge de travail | Description |
| ----- | ----- | ----- |
| **`Function`** | **traces**| Les journaux générés par l’utilisateur, qui peuvent être de n’importe quel niveau de journalisation. Pour en savoir plus sur l’écriture dans les journaux à partir de vos fonctions, consultez la page sur les [écritures dans des journaux](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Ces journaux d’activité générés par le runtime fournissent des nombres et des moyennes d’appels de fonction sur une période de temps [configurable](#configure-the-aggregator). La période par défaut est 30 secondes ou 1 000 résultats, selon la première de ces éventualités. Le nombre d’exécutions, le taux de réussite et la durée en sont des exemples. Tous ces journaux sont écrits au niveau `Information`. Si vous filtrez sur le niveau `Warning` ou supérieur, vous ne pouvez pas visualiser ces données. |
| **`Host.Executor`** | **traces** | Inclut les journaux des **fonctions démarrées** et **terminées** pour des exécutions de fonctions spécifiques. Pour les exécutions ayant réussi, ces journaux d’activité sont de niveau `Information`. Les exceptions sont journalisées au niveau `Error`. Le runtime crée également des journaux de niveau `Warning`, par exemple lorsque des messages de la file d’attente sont envoyés à la file d’attente de [messages incohérents](functions-bindings-storage-queue-trigger.md#poison-messages).  |
| **`Host.Results`** | **requests** | Ces journaux générés par le runtime indiquent la réussite ou l’échec d’une fonction. Tous ces journaux sont écrits au niveau `Information`. Si vous filtrez sur le niveau `Warning` ou supérieur, vous ne pouvez pas visualiser ces données. |

---

La colonne **Table** indique dans quelle table Application Insights le journal est écrit. 

## <a name="configure-log-levels"></a>Configurer des niveaux de journaux

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Pour chaque catégorie, vous indiquez le niveau de journal minimal à envoyer. Les paramètres host.json varient en fonction de la [version du runtime Functions](functions-versions.md). 

L’exemple ci-dessous définit la journalisation en fonction des règles suivantes :

+ Pour les journaux de `Host.Results` ou de `Function`, seuls les événements sont journalisés dans `Error` ou à un niveau supérieur. 
+ Pour les journaux de `Host.Aggregator`, journalisez toutes les métriques générées (`Trace`).
+ Pour tous les autres journaux, notamment les journaux des utilisateurs, journalisez uniquement les événements de niveau `Information` et supérieur.

# <a name="v2x"></a>[v2.x+](#tab/v2)

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

# <a name="v1x"></a>[v1.x](#tab/v1) 

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

---

Si [host.json] inclut plusieurs journaux qui commencent par la même chaîne, les journaux les plus définis sont traités en priorité. Prenons l’exemple suivant qui journalise tout dans le runtime, à l’exception de `Host.Aggregator`, au niveau de `Error` :

# <a name="v2x"></a>[v2.x+](#tab/v2)

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

# <a name="v1x"></a>[v1.x](#tab/v1) 

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

---

Vous pouvez utiliser un paramètre de niveau de journalisation `None` pour empêcher l’écriture de journaux pour une catégorie. 

> [!CAUTION]
> Azure Functions s’intègre à Application Insights en stockant des événements de télémétrie dans des tables Application Insights. Définir le niveau de journalisation d’une catégorie sur une valeur autre que `Information` empêche la télémétrie de circuler vers ces tables. Par conséquent, vous ne pourrez pas voir les données associées dans Application Insights ou sous l’onglet Superviser de la fonction.
>
> Dans les exemples ci-dessus :
> * Si la catégorie `Host.Results` est définie sur le niveau de journalisation `Error`, elle collecte uniquement les événements de télémétrie d’exécution de l’hôte dans la table `requests` pour les exécutions de fonctions ayant échoué, ce qui empêche d’afficher les détails des exécutions de l’hôte ayant réussi à la fois dans Application Insights et sous l’onglet Superviser de la fonction.
> * Si la catégorie `Function` est définie sur le niveau de journalisation `Error`, elle cesse de collecter les données de télémétrie de fonction relatives à `dependencies`, à `customMetrics` et à `customEvents` pour toutes les fonctions, ce qui empêche de voir ces données dans Application Insights. Elle collecte uniquement les `traces` journalisées avec le niveau `Error`. 
>
> Dans les deux cas, vous continuerez à collecter les données d’erreurs et d’exceptions dans Application Insights et sous l’onglet Superviser de la fonction. Pour plus d’informations, consultez [Solutions avec un volume élevé de télémétrie](#solutions-with-high-volume-of-telemetry).


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

Application Insights présente une fonctionnalité [d’échantillonnage](../azure-monitor/app/sampling.md) qui peut vous éviter de produire une quantité excessive de données de télémétrie portant sur les exécutions terminées aux heures de forte activité. Lorsque le taux d'exécutions entrantes dépasse un seuil spécifié, Application Insights commence à ignorer de manière aléatoire certaines exécutions entrantes. Par défaut, le nombre maximal d’exécutions par seconde est fixé à 20 (cinq dans la version 1.x). Vous pouvez configurer l’échantillonnage dans [host.json](./functions-host-json.md#applicationinsights).  Voici un exemple :

# <a name="v2x"></a>[v2.x+](#tab/v2)

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request;Exception"
      }
    }
  }
}
```


Vous pouvez exclure certains types de données de télémétrie de l’échantillonnage. Dans cet exemple, les données de type `Request` et `Exception` sont exclues de l’échantillonnage. Cela garantit la journalisation de *toutes* les exécutions de fonction (requêtes) et exceptions, tandis que d’autres types de données de télémétrie restent soumis à l’échantillonnage. 

# <a name="v1x"></a>[v1.x](#tab/v1)  

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
---

Pour plus d’informations, consultez [Échantillonnage dans Application Insights](../azure-monitor/app/sampling.md).

## <a name="configure-scale-controller-logs"></a>Configurer les journaux de contrôleur d’échelle

_Cette fonctionnalité est en préversion._ 

Vous pouvez faire en sorte que le [contrôleur d’échelle Azure Functions](./event-driven-scaling.md#runtime-scaling) émette des journaux vers Application Insights ou vers le stockage Blob pour mieux comprendre les décisions prises par ce contrôleur pour votre application de fonction.

Pour activer cette fonctionnalité, vous ajoutez un paramètre d’application nommé `SCALE_CONTROLLER_LOGGING_ENABLED` à vos paramètres d’application de fonction. La valeur de ce paramètre doit être au format `<DESTINATION>:<VERBOSITY>`, en fonction des éléments suivants :

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

Par exemple, la commande Azure CLI suivante active la journalisation détaillée à partir du contrôleur de mise à l’échelle pour Application Insights :

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

Dans cet exemple, remplacez `<FUNCTION_APP_NAME>` et `<RESOURCE_GROUP_NAME>` par le nom de votre application de fonction et le nom du groupe de ressources, respectivement. 

La commande Azure CLI suivante désactive la journalisation en définissant le niveau de détail sur `None` :

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

Vous pouvez également désactiver la journalisation en supprimant le paramètre `SCALE_CONTROLLER_LOGGING_ENABLED` à l’aide de la commande Azure CLI suivante :

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

Une fois la journalisation du contrôleur d’échelle activée, vous pouvez [interroger vos journaux de contrôleur d’échelle](analyze-telemetry-data.md#query-scale-controller-logs). 

## <a name="enable-application-insights-integration"></a>Activer l’intégration à Application Insights

Pour qu’une application de fonction envoie des données à Application Insights, elle a besoin de connaître la clé d’instrumentation d’une ressource Application Insights. Cette clé doit se trouver dans un paramètre d’application nommé **APPINSIGHTS_INSTRUMENTATIONKEY**.

Lorsque vous créez votre application de fonction dans le [portail Azure](./functions-get-started.md), à partir de la ligne de commande avec [Azure Functions Core Tools](./create-first-function-cli-csharp.md), ou à l’aide de [Visual Studio Code](./create-first-function-vs-code-csharp.md), l’intégration d’Application Insights est activée par défaut. La ressource Application Insights, qui porte le même nom que votre application de fonction est créée dans la même région ou dans la région la plus proche.

### <a name="new-function-app-in-the-portal"></a>Nouvelle application de fonction dans le portail

Pour examiner la ressource Application Insights en cours de création, sélectionnez-la de manière à développer la fenêtre **Application Insights**. Vous pouvez modifier le **Nouveau nom de ressource** ou choisir un autre **Emplacement** dans une [Zone géographique Azure](https://azure.microsoft.com/global-infrastructure/geographies/) où vous souhaitez stocker vos données.

![Activer Application Insights pendant la création d’une application de fonction](media/functions-monitoring/enable-ai-new-function-app.png)

Lorsque vous sélectionnez **Créer**, une ressource Application Insights est créée avec votre application de fonction, et `APPINSIGHTS_INSTRUMENTATIONKEY` est défini dans les paramètres de l’application. Tout est prêt.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Ajouter à une application de fonction existante 

Si une ressource Application Insights n’a pas été créée avec votre application de fonction, procédez comme suit pour créer la ressource. Vous pouvez ensuite ajouter la clé d’instrumentation de cette ressource en tant que [paramètre d’application](functions-how-to-use-azure-function-app-settings.md#settings) dans votre application de fonction.

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez **Application de fonction**, puis choisissez votre application de fonction. 

1. Sélectionnez la bannière **Application Insights n'est pas configuré** en haut de la fenêtre. Si vous ne voyez pas cette bannière, cela signifie que votre application a probablement déjà Application Insights activé.

    :::image type="content" source="media/configure-monitoring/enable-application-insights.png" alt-text="Activer Application Insights à partir du portail":::

1. Développez **Changer votre ressource** et créez une ressource Application Insights en utilisant les paramètres spécifiés dans le tableau suivant.  

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nouveau nom de la ressource** | Nom d’application unique | Il est plus facile d’utiliser le même nom que celui de votre application de fonction, qui doit être unique dans votre abonnement. | 
    | **Lieu** | Europe Ouest | Si possible, utilisez la même [région](https://azure.microsoft.com/regions/) que celle de votre application de fonction ou une région proche. |

    :::image type="content" source="media/configure-monitoring/ai-general.png" alt-text="Création d’une ressource Application Insights":::

1. Sélectionnez **Appliquer**. 

   La ressource Application Insights est créée dans le même groupe de ressources et le même abonnement que votre application de fonction. Une fois la ressource créée, fermez la fenêtre Application Insights.

1. Dans votre application de fonction, sélectionnez **Configuration** sous **Paramètres**, puis sélectionnez **Paramètres d'application**. Si vous voyez un paramètre nommé `APPINSIGHTS_INSTRUMENTATIONKEY`, cela signifie que l’intégration d’Application Insights est activée pour votre application de fonction s’exécutant dans Azure. Si, pour une raison quelconque, ce paramètre n’existe pas, ajoutez-le en utilisant votre clé d’instrumentation Application Insights comme valeur.

> [!NOTE]
> Les premières versions de Functions utilisaient la surveillance intégrée, ce qui n’est plus recommandé. Lorsque vous activez l’intégration d’Application Insights pour un telle application de fonction, vous devez également [désactiver la journalisation intégrée](#disable-built-in-logging).  

## <a name="disable-built-in-logging"></a>Désactiver la journalisation intégrée

Lorsque vous activez Application Insights, désactivez la journalisation intégrée qui utilise le Stockage Azure. La journalisation intégrée permet de tester des charges de travail légères, mais n'a pas vocation à être utilisée en production avec des charges importantes. Nous recommandons Application Insights à des fins de surveillance de la production. Si la journalisation intégrée est utilisée en production, l’enregistrement de journal peut être incomplet en raison d’une limitation du Stockage Azure.

Pour désactiver la journalisation intégrée, supprimez le paramètre d’application `AzureWebJobsDashboard`. Pour plus d’informations sur la suppression de paramètres d’application dans le portail Azure, consultez la section **Paramètres de l’application** dans [Comment gérer une application de fonction dans le portail Azure](functions-how-to-use-azure-function-app-settings.md#settings). Avant de supprimer le paramètre d’application, assurez-vous qu’aucune fonction existante dans la même application de fonction ne l’utilise pour les déclencheurs ou les liaisons du Stockage Azure.

## <a name="solutions-with-high-volume-of-telemetry"></a>Solutions avec un volume élevé de télémétrie 

Vos applications de fonction peuvent constituer une partie essentielle des solutions qui, par nature, provoquent de gros volumes de télémétrie (solutions IoT, solutions basées sur les événements, systèmes financiers à charge élevée, systèmes d’intégration...). Dans ce cas, vous devez envisager une configuration supplémentaire pour réduire les coûts tout en maintenant l’observabilité.

En fonction de la façon dont la télémétrie générée va être consommée, des tableaux de bord en temps réel, des alertes, des diagnostics détaillés, etc., vous devez définir une stratégie pour réduire le volume de données généré. Cette stratégie vous permet de superviser, d’utiliser et de diagnostiquer correctement vos applications de fonction en production. Vous pouvez envisager les options suivantes :

* **Utiliser l’échantillonnage** : comme indiqué [précédemment](#configure-sampling), cela permet de réduire considérablement le volume d’événements de télémétrie ingérés tout en conservant une analyse statistiquement correcte. Il peut arriver que, même en utilisant l’échantillonnage, vous obteniez quand même un volume élevé de télémétrie. Examinez les options que vous propose l’[échantillonnage adaptatif](../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-applications). Par exemple, définissez `maxTelemetryItemsPerSecond` sur une valeur qui équilibre le volume généré avec vos besoins de supervision. Gardez à l’esprit que l’échantillonnage de télémétrie est appliqué par hôte exécutant votre application de fonction. 

* **Niveau de journalisation par défaut** : utilisez `Warning` ou `Error` comme valeur par défaut pour toutes les catégories de télémétrie. Vous pouvez maintenant choisir les [catégories](#configure-categories) que vous voulez définir sur `Information` afin de pouvoir superviser et diagnostiquer correctement vos fonctions.

* **Réglez la télémétrie de vos fonctions** : si le niveau de journalisation par défaut est défini sur `Error` ou `Warning`, aucune information détaillée de chaque fonction n’est collectée (dépendances, métriques personnalisées, événements personnalisés et traces). Pour les fonctions qui sont essentielles pour la supervision de la production, définissez une entrée explicite pour la catégorie `Function.<YOUR_FUNCTION_NAME>` et affectez-lui la valeur `Information`, afin de pouvoir collecter des informations détaillées. À ce stade, pour éviter de collecter des [journaux générés par l’utilisateur](functions-monitoring.md#writing-to-logs) au niveau `Information`, définissez la catégorie `Function.<YOUR_FUNCTION_NAME>.User` sur le niveau de journalisation `Error` ou `Warning`.

* **Catégorie Host.Aggregator** : comme décrit dans la section [Configurer des catégories](#configure-categories), cette catégorie fournit des informations agrégées sur les appels de fonction. Les informations de cette catégorie sont recueillies dans la table `customMetrics` d’Application Insights, et elles s’affichent sous l’onglet Vue d’ensemble de la fonction dans le portail Azure. En fonction de la façon dont vous configurez l’agrégateur, pensez qu’il y aura un retard, déterminé par la valeur `flushTimeout`, dans la télémétrie collectée. Si vous définissez cette catégorie sur une autre valeur différente de `Information`, vous arrêtez la collecte des données dans la table `customMetrics` et les métriques ne s’affichent pas sous l’onglet Vue d’ensemble de la fonction.

  La capture d’écran suivante montre les données de télémétrie Host.Aggregator affichées sous l’onglet Vue d’ensemble de la fonction. :::image type="content" source="media/configure-monitoring/host-aggregator-function-overview.png" alt-text="Capture d’écran de la télémétrie Host.Aggregator affichée sous l’onglet Vue d’ensemble de la fonction." lightbox="media/configure-monitoring/host-aggregator-function-overview-big.png":::

  La capture d’écran suivante montre les données de télémétrie Host.Aggregator dans la table customMetrics d’Application Insights.
  :::image type="content" source="media/configure-monitoring/host-aggregator-custom-metrics.png" alt-text="Capture d’écran de la télémétrie Host.Aggregator dans la table customMetrics d’Application Insights." lightbox="media/configure-monitoring/host-aggregator-custom-metrics-big.png":::

* **Catégorie Host.Results** : comme décrit dans [Configurer des catégories](#configure-categories), cette catégorie fournit les journaux générés par le runtime qui indiquent la réussite ou l’échec d’un appel de fonction. Les informations de cette catégorie sont recueillies dans la table `requests` d’Application Insights, et elles s’affichent sous l’onglet Superviser de la fonction et dans différents tableaux de bord Application Insights (Performances, Échecs...). Si vous définissez cette catégorie sur une autre valeur différente de `Information`, vous collectez uniquement la télémétrie générée au niveau de journalisation défini (ou supérieur). Par exemple, si vous la définissez sur `error`, seules les données concernant les exécutions ayant échoué sont suivies. 

  La capture d’écran suivante montre les données de télémétrie Host.Results affichées sous l’onglet Superviser de la fonction. :::image type="content" source="media/configure-monitoring/host-results-function-monitor.png" alt-text="Capture d’écran de la télémétrie Host.Results sous l’onglet Superviser de la fonction." lightbox="media/configure-monitoring/host-results-function-monitor-big.png":::

  La capture d’écran suivante montre les données de télémétrie Host.Results affichées dans le tableau de bord Performances d’Application Insights.
  :::image type="content" source="media/configure-monitoring/host-results-application-insights.png" alt-text="Capture d’écran de la télémétrie Host.Results dans le tableau de bord Performances d’Application Insights." lightbox="media/configure-monitoring/host-results-application-insights-big.png":::

* **Comparaison de Host.Aggregator et de Host.Results** : les deux catégories fournissent de bons insights sur les exécutions de fonction. Si nécessaire, vous pouvez supprimer les informations détaillées de l’une de ces catégories, afin de pouvoir utiliser l’autre pour la supervision et les alertes.
Voici un exemple :
# <a name="v2x"></a>[v2.x+](#tab/v2)

``` json
{
  "version": "2.0",  
  "logging": {
    "logLevel": {
      "default": "Warning",
      "Function": "Error",
      "Host.Aggregator": "Error",
      "Host.Results": "Information", 
      "Function.Function1": "Information",
      "Function.Function1.User": "Error"
    },
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond": 1,
        "excludedTypes": "Exception"
      }
    }
  }
} 
```
# <a name="v1x"></a>[v1.x](#tab/v1) 
```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Warning",
      "categoryLevels": {
        "Function": "Error",
        "Host.Aggregator": "Error",
        "Host.Results": "Information",
        "Host.Executor": "Warning"
      }
    }
  },
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

Avec cette configuration, vous disposez des éléments suivants :

* La valeur par défaut pour toutes les catégories de fonctions et de télémétrie est définie sur `Warning` (dont les catégories Microsoft et Worker). Par conséquent, par défaut, l’ensemble des erreurs et avertissements générés aussi bien par le runtime et la journalisation personnalisée sont collectés. 

* Le niveau de journalisation de la catégorie `Function` est défini sur `Error`. Par conséquent, par défaut, pour toutes les fonctions, seuls les journaux des exceptions et les journaux des erreurs sont collectés (les dépendances, les métriques générées par l’utilisateur et les événements générés par l’utilisateur sont ignorés).

* Pour la catégorie `Host.Aggregator`, étant donné qu’elle est définie sur le niveau de journalisation `Error`, aucune information agrégée à partir des appels de fonction n’est collectée dans la table `customMetrics` d’Application Insights, et aucune information sur les nombres d’exécutions (nombre total, réussies, ayant échoué...) ne s’affiche dans le tableau de bord Vue d’ensemble de la fonction.

* Pour la catégorie `Host.Results`, toutes les informations d’exécution de l’hôte sont collectées dans la table `requests` d’Application Insights. Tous les résultats des appels s’affichent dans le tableau de bord Superviser de la fonction et dans les tableaux de bord Application Insights.

* Pour la fonction appelée `Function1`, nous avons défini le niveau de journalisation sur `Information` afin que, pour cette fonction concrète, toute la télémétrie soit collectée (dépendance, métriques personnalisées, événements personnalisés). Pour la même fonction, la catégorie `Function1.User` (traces générées par l’utilisateur) est définie sur `Error`, de sorte que seule la journalisation personnalisée des erreurs est collectée. Notez que la configuration par fonction n’est pas prise en charge dans la version 1.x. 

* L’échantillonnage est configuré pour envoyer un élément de télémétrie par seconde et par type, en excluant les exceptions. Cet échantillonnage se produit pour chaque hôte de serveur exécutant notre application de fonction. Par conséquent, si nous avons quatre instances, cette configuration émet quatre éléments de télémétrie par seconde et par type et toutes les exceptions qui peuvent se produire. Notez que les nombres de métriques comme le taux de demandes et le taux d’exceptions sont ajustés pour compenser le taux d’échantillonnage, afin qu’ils affichent des valeurs approximativement correctes dans Metrics Explorer.  

> [!TIP]
> Faites des essais avec des configurations différentes pour vérifier que vous respectez vos exigences concernant la journalisation, la supervision et les alertes. Vérifiez que vous disposez de diagnostics détaillés en cas d’erreurs inattendues ou de dysfonctionnements.

### <a name="overriding-monitoring-configuration-at-runtime"></a>Remplacement de la configuration de la supervision au moment de l’exécution
Enfin, il peut arriver que vous deviez modifier rapidement le comportement de journalisation d’une certaine catégorie en production et que vous ne vouliez pas effectuer un déploiement complet uniquement pour un changement apporté au fichier `host.json`. Dans ce cas, vous pouvez remplacer les [valeurs host.json](functions-host-json.md#override-hostjson-values).


Pour configurer ces valeurs au niveau des paramètres de l’application (et éviter le redéploiement uniquement sur les changements apportés à host.json), vous devez remplacer des valeurs `host.json` spécifiques en créant une valeur équivalente comme paramètre d’application. Quand le runtime trouve un paramètre d’application au format `AzureFunctionsJobHost__path__to__setting`, il remplace le paramètre `host.json` équivalent situé sur `path.to.setting` dans le fichier code JSON. Quand il est exprimé sous la forme d’un paramètre d’application, le point (`.`) utilisé pour indiquer la hiérarchie JSON est remplacé par un trait de soulignement double (`__`). Par exemple, vous pouvez utiliser les paramètres d’application ci-dessous pour configurer des niveaux de journalisation de fonctions individuels comme indiqué dans `host.json` ci-dessus.


| Chemin de host.json | Paramètre d’application |
|----------------|-------------|
| logging.logLevel.default  | AzureFunctionsJobHost__logging__logLevel__default  |
| logging.logLeve.Host.Aggregator | AzureFunctionsJobHost__logging__logLevel__Host__Aggregator |
| logging.logLevel.Function | AzureFunctionsJobHost__logging__logLevel__Function |
| logging.logLevel.Function.Function1 | AzureFunctionsJobHost__logging__logLevel__Function1 |
| logging.logLevel.Function.Function1.User | AzureFunctionsJobHost__logging__logLevel__Function1.User |


Vous pouvez remplacer les paramètres directement dans le panneau Configuration de l’application de fonction du portail Azure ou à l’aide d’un script Azure CLI ou PowerShell.

# <a name="az-cli"></a>[az cli](#tab/v2)
```azurecli-interactive
az functionapp config appsettings set --name MyFunctionApp --resource-group MyResourceGroup --settings "AzureFunctionsJobHost__logging__logLevel__Host__Aggregator=Information"
```
# <a name="powershell"></a>[PowerShell](#tab/v1) 
```powershell
Update-AzFunctionAppSetting -Name MyAppName -ResourceGroupName MyResourceGroupName -AppSetting @{"AzureFunctionsJobHost__logging__logLevel__Host__Aggregator" = "Information"}
```
---

> [!NOTE]
> Si vous remplacez `host.json` en changeant les paramètres de l’application, votre application de fonction redémarre.
 
## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la supervision, consultez :

+ [Superviser Azure Functions](functions-monitoring.md)
+ [Analyser les données de télémétrie Azure Functions dans Application Insights](analyze-telemetry-data.md)
+ [Application Insights](/azure/application-insights/)


[host.json]: functions-host-json.md
