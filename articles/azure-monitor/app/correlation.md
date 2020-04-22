---
title: Corrélation de télémétrie dans Azure Application Insights | Microsoft Docs
description: Corrélation de télémétrie dans Application Insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 2e862410e2bf12e09e1a6388bbb6f7105b5b2edf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405263"
---
# <a name="telemetry-correlation-in-application-insights"></a>Corrélation de télémétrie dans Application Insights

Dans l’environnement des microservices, les tâches associées à chaque opération logique doivent être exécutées dans différents composants du service. Vous pouvez superviser séparément chacun de ces composants en utilisant [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights prend en charge la corrélation de télémétrie distribuée, qui vous permet de détecter le composant responsable des défaillances ou de la dégradation des performances.

Cet article décrit le modèle de données utilisé par Application Insights pour mettre en corrélation les données de télémétrie envoyées par plusieurs composants. Il couvre les protocoles et les techniques de propagation de contexte. Il traite également de l’implémentation des tactiques de corrélation sur différentes plateformes et dans différents langages.

## <a name="data-model-for-telemetry-correlation"></a>Modèle de données pour la corrélation de télémétrie

Application Insights définit le [modèle de données](../../azure-monitor/app/data-model.md) pour la corrélation de télémétrie distribuée. Pour associer la télémétrie à une opération logique, chaque élément de télémétrie comporte un champ de contexte appelé `operation_Id`. Cet identificateur est partagé par chaque élément de télémétrie dans la trace distribuée. Ainsi, même si vous perdez la télémétrie d’une seule couche, vous pouvez toujours associer la télémétrie communiquée par d’autres composants.

Une opération logique distribuée se compose généralement d’un ensemble d’opérations plus petites qui sont des requêtes traitées par un des composants. Ces opérations sont définies par la [télémétrie des requêtes](../../azure-monitor/app/data-model-request-telemetry.md). Chaque élément de télémétrie d’une requête a son propre `id`, qui l’identifie de façon univoque et globale. Par ailleurs, tous les éléments de télémétrie (comme les traces et les exceptions) associés à la requête doivent définir le `operation_parentId` sur la valeur de l’`id` de la requête.

Chaque opération sortante, telle qu’un appel HTTP vers un autre composant, est représentée par la [télémétrie des dépendances](../../azure-monitor/app/data-model-dependency-telemetry.md). La télémétrie des dépendances définit également ses propres `id` qui sont globalement uniques. La télémétrie des requêtes, lancée par cet appel de dépendances, utilise cet `id` comme `operation_parentId`.

Vous pouvez générer une vue de l’opération logique distribuée en utilisant `operation_Id`, `operation_parentId` et `request.id` avec `dependency.id`. Ces champs définissent également l’ordre de causalité des appels de télémétrie.

Dans un environnement de microservices, les traces des composants peuvent se diriger vers différents éléments de stockage. Chaque composant peut avoir sa propre clé d’instrumentation dans Application Insights. Pour obtenir la télémétrie pour l’opération logique, Application Insights interroge les données de chaque élément de stockage. Quand le nombre d’éléments de stockage est important, vous avez besoin d’une indication quant à l’endroit à regarder ensuite. Le modèle de données Application Insights définit deux champs pour résoudre ce problème : `request.source` et `dependency.target`. Le premier champ identifie le composant qui a lancé la demande de dépendance. Le deuxième champ identifie le composant qui a retourné la réponse de l’appel de dépendance.

## <a name="example"></a>Exemple

Intéressons-nous à un exemple. Une application appelée Stock Prices affiche le cours actuel sur le marché d’une action en utilisant une API externe nommée Stock. L’application Stock Prices a une page nommée Stock que le navigateur web client ouvre en utilisant `GET /Home/Stock`. L’application interroge l’API Stock en utilisant l’appel HTTP `GET /api/stock/value`.

Vous pouvez analyser la télémétrie obtenue en exécutant une requête :

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Dans les résultats, notez que tous les éléments de télémétrie partagent la racine `operation_Id`. Quand un appel Ajax est effectué à partir de la page, un nouvel ID unique (`qJSXU`) est affecté à la télémétrie des dépendances, et l’ID de la pageView est utilisé en tant que `operation_ParentId`. La requête de serveur utilise ensuite l’ID Ajax en tant que `operation_ParentId`.

| itemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Quand l’appel `GET /api/stock/value` est effectué vers un service externe, vous devez connaître l’identité de ce serveur afin de pouvoir définir le champ `dependency.target` de façon appropriée. Quand le service externe ne prend pas en charge la supervision, `target` prend comme valeur le nom d’hôte du service (par exemple `stock-prices-api.com`). Cependant, si le service s’identifie lui-même en retournant un en-tête HTTP prédéfini, `target` contient l’identité du service qui permet à Application Insights de générer une trace distribuée en interrogeant la télémétrie provenant de ce service.

## <a name="correlation-headers"></a>En-têtes de corrélation

Application Insights effectue la transition vers [W3C Trace-Context](https://w3c.github.io/trace-context/), qui définit :

- `traceparent`: représente l’ID d’opération globalement unique et l’identificateur unique de l’appel.
- `tracestate`: Représente le contexte de traçage spécifique au système.

La dernière version du SDK Application Insights prend en charge le protocole Trace-Context, mais il peut être nécessaire d’y adhérer. (La compatibilité descendante avec le protocole de corrélation précédent pris en charge par le SDK Application Insights sera conservée.)

Le [protocole HTTP de corrélation, également appelé Request-Id](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) est déprécié. Ce protocole définit deux en-têtes :

- `Request-Id`: représente le GUID de l’appel.
- `Correlation-Context`: représente la collection de paires nom-valeur des propriétés de trace distribuée.

Application Insights définit également l’[extension](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) pour le protocole HTTP de corrélation. Il utilise des paires nom-valeur `Request-Context` pour propager la collection de propriétés utilisée par l’appelant ou l’appelé. Le SDK Application Insights utilise cet en-tête pour définir les champs `dependency.target` et `request.source`.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Activer la prise en charge du traçage distribué W3C pour les applications ASP.NET classiques
 
  > [!NOTE]
  >  À compter de `Microsoft.ApplicationInsights.Web` et de `Microsoft.ApplicationInsights.DependencyCollector`, aucune configuration n’est nécessaire.

La prise en charge de Trace-Context du W3C est implémentée de façon à assurer une compatibilité descendante. La corrélation devrait fonctionner avec les applications instrumentées avec les versions précédentes du SDK (sans prise en charge du protocole W3C).

Si vous voulez continuer à utiliser le protocole `Request-Id` hérité, vous pouvez désactiver Trace-Context en utilisant cette configuration :

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Si vous exécutez une version antérieure du SDK, nous vous recommandons de la mettre à jour ou d’appliquer la configuration suivante pour activer Trace-Context.
Cette fonctionnalité est disponible dans les packages `Microsoft.ApplicationInsights.Web` et `Microsoft.ApplicationInsights.DependencyCollector` à compter de la version 2.8.0-beta1.
Elle est désactivée par défaut. Pour l’activer, apportez les modifications suivantes à `ApplicationInsights.config` :

- Sous `RequestTrackingTelemetryModule`, ajoutez l’élément `EnableW3CHeadersExtraction` et définissez sa valeur sur `true`.
- Sous `DependencyTrackingTelemetryModule`, ajoutez l’élément `EnableW3CHeadersInjection` et définissez sa valeur sur `true`.
- Ajoutez `W3COperationCorrelationTelemetryInitializer` sous `TelemetryInitializers`. Il se présente de façon similaire à cet exemple :

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Activer la prise en charge du traçage distribué W3C pour les applications ASP.NET Core

 > [!NOTE]
  > À compter de `Microsoft.ApplicationInsights.AspNetCore` version 2.8.0, aucune configuration n’est nécessaire.
 
La prise en charge de Trace-Context du W3C est implémentée de façon à assurer une compatibilité descendante. La corrélation devrait fonctionner avec les applications instrumentées avec les versions précédentes du SDK (sans prise en charge du protocole W3C).

Si vous voulez continuer à utiliser le protocole `Request-Id` hérité, vous pouvez désactiver Trace-Context en utilisant cette configuration :

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Si vous exécutez une version antérieure du SDK, nous vous recommandons de la mettre à jour ou d’appliquer la configuration suivante pour activer Trace-Context.

Cette fonctionnalité est dans `Microsoft.ApplicationInsights.AspNetCore` version 2.5.0-beta1 et dans `Microsoft.ApplicationInsights.DependencyCollector` version 2.8.0-beta1.
Elle est désactivée par défaut. Pour l’activer, affectez la valeur `true` à `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Activer la prise en charge du traçage distribué W3C pour les applications Java

#### <a name="java-30-agent"></a>Agent Java 3.0

  L’agent Java 3.0 prend en charge W3C prêt à l'emploi et aucune configuration supplémentaire n’est requise. 

#### <a name="java-sdk"></a>Kit de développement logiciel (SDK) Java
- **Configuration d’entrée**

  - Pour les applications Java EE, ajoutez ce qui suit à la balise `<TelemetryModules>` dans le fichier ApplicationInsights.xml :

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - Pour les applications Spring Boot, ajoutez ces propriétés :

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Configuration de sortie**

  Ajoutez le code suivant au fichier AI-Agent.xml :

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Le mode de compatibilité descendante est activé par défaut et le paramètre `enableW3CBackCompat` est facultatif. Utilisez-le uniquement quand vous souhaitez désactiver la compatibilité descendante.
  >
  > Dans l’idéal, vous la désactiveriez quand tous vos services auraient été mis à jour vers une version plus récente des SDK prenant en charge le protocole W3C. Nous vous recommandons vivement de déplacer ces SDK plus récents dès que possible.

> [!IMPORTANT]
> Vérifiez que les configurations entrante et sortante sont rigoureusement identiques.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Activer la prise en charge du suivi distribué W3C pour les applications web

Cette fonctionnalité se trouve dans `Microsoft.ApplicationInsights.JavaScript`. Elle est désactivée par défaut. Pour l’activer, utilisez la configuration `distributedTracingMode`. AI_AND_W3C est fourni à des fins de compatibilité descendante avec tous les services instrumentés par Application Insights hérités.

- **Configurationnpm (à ignorer si vous utilisez la configuration Snippet)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Configuration Snippet (à ignorer si vous utilisez la configuration npm)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...other configuration options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing et Application Insights

La [spécification du modèle de données OpenTracing](https://opentracing.io/) et les modèles de données Application Insights correspondent de la façon suivante :

| Application Insights                   | OpenTracing                                        |
|------------------------------------    |-------------------------------------------------    |
| `Request`, `PageView`                  | `Span` avec `span.kind = server`                    |
| `Dependency`                           | `Span` avec `span.kind = client`                    |
| `Id` de `Request` et `Dependency`     | `SpanId`                                            |
| `Operation_Id`                         | `TraceId`                                           |
| `Operation_ParentId`                   | `Reference` de type `ChildOf` (étendue parent)     |

Pour plus d’informations, consultez le [Modèle de données de télémétrie d’Application Insights](../../azure-monitor/app/data-model.md).

Pour obtenir des définitions des concepts d’OpenTracing, consultez la [spécification](https://github.com/opentracing/specification/blob/master/specification.md) et les [conventions sémantiques](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) d’OpenTracing.

## <a name="telemetry-correlation-in-opencensus-python"></a>Corrélation de télémétrie dans OpenCensus Python

OpenCensus Python suit les spécifications du modèle de données d’`OpenTracing` décrites ci-dessus. Il prend aussi en charge [Trace-Context de W3C](https://w3c.github.io/trace-context/) sans nécessiter de configuration.

### <a name="incoming-request-correlation"></a>Corrélation de requêtes entrantes

OpenCensus Python met en corrélation les en-têtes Trace-Context W3C des requêtes entrantes avec les intervalles générés à partir des requêtes elles-mêmes. OpenCensus effectue cette opération automatiquement en s’intégrant à ces frameworks d’application web populaires : Flask, Django et Pyramid. Vous devez seulement renseigner les en-têtes Trace-Context de W3C avec le [format correct](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), puis les envoyer avec la requête. Voici un exemple d’application Flask qui illustre ceci :

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Ce code exécute un exemple d’application Flask sur votre machine locale, en écoutant le port `8080`. Pour corréler le contexte de trace, vous envoyez une requête au point de terminaison. Dans cet exemple, vous pouvez utiliser une commande `curl` :
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
En examinant le [format de l’en-tête Trace-Context](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), vous pouvez déduire les informations suivantes :

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Si vous examinez l’entrée de la requête envoyée à Azure Monitor, vous pouvez voir des champs renseignés avec les informations d’en-tête de trace. Vous trouverez ces données sous Journaux (Analytics) dans la ressource Azure Monitor Application Insights.

![Télémétrie des requêtes dans les journaux (Analytics)](./media/opencensus-python/0011-correlation.png)

Le champ `id` est au format `<trace-id>.<span-id>`, où `trace-id` provient de l’en-tête de trace passé dans la requête, et où `span-id` est un tableau de 8 octets généré pour cette étendue.

Le champ `operation_ParentId` est au format `<trace-id>.<parent-id>`, où `trace-id` et `parent-id` sont extraits de l’en-tête de trace passé dans la requête.

### <a name="log-correlation"></a>Corrélation des journaux

OpenCensus Python vous permet de mettre en corrélation des journaux en ajoutant un ID de trace, un ID d’étendue et un indicateur d’échantillonnage aux enregistrements de journal. Vous ajoutez ces attributs en installant l’[intégration de la journalisation](https://pypi.org/project/opencensus-ext-logging/) d’OpenCensus. Les attributs suivants vont être ajoutés aux objets `LogRecord` de Python : `traceId`, `spanId` et `traceSampled`. Notez que ceci prend effet uniquement pour les journaliseurs créés après l’intégration.

Voici un exemple d’application qui illustre cela :

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Quand ce code s’exécute, voici ce qui s’affiche dans la console :
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Notez qu’un `spanId` est présent pour le message du journal qui se trouve dans l’étendue. Il s’agit du même `spanId` que celui qui appartient à l’étendue nommée `hello`.

Vous pouvez exporter les données du journal avec `AzureLogHandler`. Pour plus d’informations, consultez [cet article](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs).

## <a name="telemetry-correlation-in-net"></a>Corrélation de télémétrie dans .NET

Au fil du temps, .NET a défini plusieurs façons de mettre en corrélation les journaux de télémétrie et de diagnostic :

- `System.Diagnostics.CorrelationManager` permet d’effectuer le suivi de [LogicalOperationStack et ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx).
- `System.Diagnostics.Tracing.EventSource` et le suivi d’événements pour Windows (ETW) définissent la méthode [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx).
- `ILogger` utilise les [étendues de journaux](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes).
- WCF (Windows Communication Foundation) et HTTP relient la propagation de contexte « actuelle ».

Toutefois, ces méthodes ne permettaient pas la prise en charge du traçage distribué automatique. `DiagnosticSource` prend en charge la corrélation automatique entre machines. Les bibliothèques .NET prennent en charge `DiagnosticSource` et autorisent la propagation automatique entre machines du contexte de corrélation via le transport, comme HTTP.

Le [Guide des activités utilisateur](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) dans `DiagnosticSource` explique les principes de base du suivi des activités.

ASP.NET Core 2.0 prend en charge l’extraction des en-têtes HTTP et le démarrage de nouvelles activités.

`System.Net.Http.HttpClient`, à compter de la version 4.1.0, prend en charge l’injection automatique des en-têtes HTTP de corrélation et le suivi des appels HTTP en tant qu’activités.

Il existe un nouveau module HTTP, [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), pour ASP.NET Classic. Ce module implémente la corrélation de télémétrie à l’aide de `DiagnosticSource`. Il démarre une activité en fonction des en-têtes de requête entrante. Il met également en corrélation les données de télémétrie des différentes phases de traitement des requêtes, même quand chaque phase du traitement IIS (Internet Information Services) s’exécute sur un thread managé différent.

Le SDK Application Insights, à compter de la version 2.4.0-beta1, utilise `DiagnosticSource` et `Activity` pour collecter la télémétrie et l’associer à l’activité en cours.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Corrélation de télémétrie dans Java

L’[agent Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) ainsi que le [Kit de développement logiciel (SDK) Java](../../azure-monitor/app/java-get-started.md) version 2.0.0 ou ultérieure prennent en charge la corrélation automatique de télémétrie. Il renseigne automatiquement `operation_id` pour toutes les données de télémétrie (comme les traces, les exceptions et les événements personnalisés) émises dans l’étendue d’une requête. Il propage aussi les en-têtes de corrélation (décrits plus haut) pour les appels entre les services via le protocole HTTP si l’[agent du SDK Java](../../azure-monitor/app/java-agent.md) est configuré.

> [!NOTE]
> L’agent Java Application Insights collecte automatiquement les demandes et les dépendances pour JMS, Kafka, net/webflux et bien plus. Pour le Kit de développement logiciel (SDK) Java, seuls les appels effectués via Apache HttpClient sont pris en charge pour la fonctionnalité de corrélation. La propagation automatique de contexte entre les technologies de messagerie (comme Kafka, RabbitMQ et Azure Service Bus) n’est pas prise en charge dans le Kit de développement logiciel (SDK). 

> [!NOTE]
> Pour collecter des données de télémétrie personnalisées, vous devez instrumenter l’application à l'aide du kit de développement logiciel (SDK) Java 2.6. 

### <a name="role-names"></a>Nom des rôles

Vous pouvez personnaliser la façon dont les noms des composants sont affichés dans la [cartographie d’application](../../azure-monitor/app/app-map.md). Pour cela, vous pouvez définir manuellement `cloud_RoleName` en effectuant une des opérations suivantes :

- Pour l'agent Java 3.0 Application Insights, définissez le nom du rôle cloud comme suit :

    ```json
    {
      "instrumentationSettings": {
        "preview": {
          "roleName": "my cloud role name"
        }
      }
    }
    ```
    Vous pouvez également définir le nom de rôle cloud à l'aide de la variable d’environnement `APPLICATIONINSIGHTS_ROLE_NAME`.

- Avec Application Insights Java SDK 2.5.0 et ultérieur, vous pouvez spécifier `cloud_RoleName` en ajoutant `<RoleName>` à votre fichier ApplicationInsights.xml :

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Si vous utilisez Spring Boot avec Application Insights Spring Boot Starter, vous devez seulement définir votre nom personnalisé pour l’application dans le fichier application.properties :

  `spring.application.name=<name-of-app>`

  Spring Boot Starter attribue automatiquement `cloudRoleName` à la valeur que vous entrez pour la propriété `spring.application.name`.

## <a name="next-steps"></a>Étapes suivantes

- Écrivez des [données de télémétrie personnalisées](../../azure-monitor/app/api-custom-events-metrics.md).
- Pour des scénarios de corrélation avancés dans ASP.NET Core et ASP.NET, consultez [Effectuer le suivi des opérations personnalisées](custom-operations-tracking.md).
- Découvrez plus en détail la [définition de cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) pour d’autres kits SDK.
- Intégrez tous les composants de votre microservice sur Application Insights. Consultez les [plateformes prises en charge](../../azure-monitor/app/platforms.md).
- Pour connaître les types Application Insights, consultez [Modèle de données](../../azure-monitor/app/data-model.md).
- Découvrez comment [étendre et filtrer la télémétrie](../../azure-monitor/app/api-filtering-sampling.md).
- Consultez les [informations de référence sur la configuration d’Application Insights](configuration-with-applicationinsights-config.md).
