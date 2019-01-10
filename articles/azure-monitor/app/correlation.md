---
title: Corrélation de télémétrie dans Azure Application Insights | Microsoft Docs
description: Corrélation de télémétrie dans Application Insights
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/31/2018
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: a6937b5b6b3b85dd51d80a928de02a00c361cc0e
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117603"
---
# <a name="telemetry-correlation-in-application-insights"></a>Corrélation de télémétrie dans Application Insights

Dans l’environnement des microservices, les tâches associées à chaque opération logique doivent être exécutées dans différents composants du service. Chacun de ces composants peut être surveillé séparément par [Application Insights](../../azure-monitor/app/app-insights-overview.md). Le composant application web communique avec le composant fournisseur d’authentification pour valider les informations d’identification utilisateur et avec le composant API pour obtenir des données à des fins de visualisation. Le composant API peut à son tour interroger les données des autres services et utiliser les composants fournisseur de caches et notifier le composant facturation à propos de cet appel. Application Insights prend en charge la corrélation de télémétrie distribuée. Il vous permet de détecter le composant responsable de défaillances ou d’une dégradation des performances.

Cet article décrit le modèle de données utilisé par Application Insights pour mettre en corrélation les données de télémétrie envoyées par plusieurs composants. Il couvre les protocoles et les techniques de propagation de contexte. Il couvre également l’implémentation des concepts de corrélation sur différentes plateformes et dans différents langages.

## <a name="telemetry-correlation-data-model"></a>Modèle de données de corrélation de télémétrie

Application Insights définit le [modèle de données](../../azure-monitor/app/data-model.md) pour la corrélation de télémétrie distribuée. Pour associer la télémétrie à l’opération logique, chaque élément de télémétrie comporte un champ de contexte appelé `operation_Id`. Cet identificateur est partagé par chaque élément de télémétrie dans la trace distribuée. Par conséquent, même en perdant la télémétrie d’une seule couche, vous pouvez toujours associer la télémétrie communiquée par d’autres composants.

Une opération logique distribuée se compose généralement d’un ensemble d’opérations plus petites : des requêtes traitées par l’un des composants. Ces opérations sont définies par la [télémétrie des requêtes](../../azure-monitor/app/data-model-request-telemetry.md). Chaque télémétrie de requête possède son propre `id` qui l’identifie globalement de façon unique. Et toutes les données de télémétrie (traces, exceptions, etc.) associées à cette requête doivent définir `operation_parentId` sur la valeur de la requête `id`.

Chaque opération sortante (comme un appel HTTP vers un autre composant) est représentée par la [télémétrie des dépendances](../../azure-monitor/app/data-model-dependency-telemetry.md). La télémétrie des dépendances définit également son propre `id` globalement unique. La télémétrie des requêtes, initiée par cet appel de dépendances, l’utilise en tant que `operation_parentId`.

Vous pouvez générer la vue d’une opération logique distribuée en utilisant `operation_Id`, `operation_parentId` et `request.id` avec `dependency.id`. Ces champs définissent également l’ordre de causalité des appels de télémétrie.

Dans un environnement de microservices, les traces des composants peuvent se diriger vers différents stockages. Chaque composant peut avoir sa propre clé d’instrumentation dans Application Insights. Pour obtenir la télémétrie de l’opération logique, vous devez interroger les données à partir de chaque stockage. Lorsque les stockages sont très nombreux, vous avez besoin d’aide pour savoir où chercher.

Le modèle de données Application Insights définit deux champs pour résoudre ce problème : `request.source` et `dependency.target`. Le premier champ identifie le composant qui a initié la demande de dépendance, alors que le deuxième identifie le composant qui a retourné la réponse de l’appel de dépendance.


## <a name="example"></a>Exemples

Prenons l’exemple d’une application STOCK PRICES affichant le cours actuel sur le marché d’une action en utilisant l’API externe nommée STOCKS API. Le navigateur web client ouvre la page `Stock page` de l’application STOCK PRICES en utilisant `GET /Home/Stock`. L’application interroge l’API STOCK au moyen d’un appel HTTP `GET /api/stock/value`.

Vous pouvez analyser la télémétrie obtenue en exécutant une requête :

```
(requests | union dependencies | union pageViews) 
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Dans la vue du résultat, notez que tous les éléments de télémétrie partagent la racine `operation_Id`. Quand l’appel ajax est établi à partir de la page, le nouvel ID unique `qJSXU` est affecté à la télémétrie des dépendances et l’ID de pageView est utilisé en tant que `operation_ParentId`. À son tour, la requête du serveur utilise l’ID d’ajax en tant que `operation_ParentId`, etc.

| itemType   | Nom                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Maintenant lorsque l’appel `GET /api/stock/value` est établi vers un service externe, vous souhaitez connaître l’identité de ce serveur. Vous pouvez définir le champ `dependency.target` de manière appropriée. Quand le service externe ne prend pas en charge la supervision, `target` est défini sur le nom d’hôte du service comme `stock-prices-api.com`. Toutefois, si ce service s’identifie en retournant un en-tête HTTP prédéfini, `target` contient l’identité du service qui permet à Application Insights de générer la trace distribuée en interrogeant la télémétrie à partir de ce service. 

## <a name="correlation-headers"></a>En-têtes de corrélation

Nous travaillons sur une proposition RFC pour le [protocole HTTP de corrélation](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Cette proposition définit deux en-têtes :

- `Request-Id` transporte le GUID de l’appel
- `Correlation-Context` transporte la collection de paires nom/valeur des propriétés de trace distribuée

La norme définit également deux schémas de génération de `Request-Id` : plat et hiérarchique. Le schéma plat inclut une clé `Id` connue définie pour la collection `Correlation-Context`.

Application Insights définit [l’extension](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) pour le protocole HTTP de corrélation. Il utilise les paires nom/valeur `Request-Context` pour propager la collection de propriétés utilisée par l’appelant ou l’appelé. Le Kit de développement logiciel (SDK) Application Insights utilise cet en-tête pour définir les champs `dependency.target` et `request.source`.

### <a name="w3c-distributed-tracing"></a>Traçage distribué W3C

Nous migrons vers le [format de traçage distribué W3C](https://w3c.github.io/trace-context/). Il définit :
- `traceparent` : exécute un ID d’opération et un identificateur de l’appel uniques au niveau global
- `tracestate` : exécute un contexte spécifique de système de traçage.

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-classic-apps"></a>Activer la prise en charge du traçage distribué W3C pour les applications ASP.NET Classic

Cette fonctionnalité est disponible dans les packages Microsoft.ApplicationInsights.Web et Microsoft.ApplicationInsights.DependencyCollector, en commençant par la version 2.8.0-beta1.
Elle est **désactivée** par défaut. Pour l’activer, changez `ApplicationInsights.config` :

* sous `RequestTrackingTelemetryModule`, ajoutez l’élément `EnableW3CHeadersExtraction` avec la valeur définie sur `true`
* sous `DependencyTrackingTelemetryModule`, ajoutez l’élément `EnableW3CHeadersInjection` avec la valeur définie sur `true`

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Activer la prise en charge du traçage distribué W3C pour les applications ASP.NET Core

Cette fonctionnalité est disponible dans Microsoft.ApplicationInsights.AspNetCore, version 2.5.0-beta1, et Microsoft.ApplicationInsights.DependencyCollector, version 2.8.0-beta1.
Elle est **désactivée** par défaut. Pour l’activer, définissez `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` sur `true` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

## <a name="open-tracing-and-application-insights"></a>Open Tracing et Application Insights

La [spécification du modèle de données Open Tracing](https://opentracing.io/) et les modèles de données Application Insights correspondent de la façon suivante :

| Application Insights                  | Open Tracing                                      |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` avec `span.kind = server`                  |
| `Dependency`                          | `Span` avec `span.kind = client`                  |
| `Id` de `Request` et `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` de type `ChildOf` (étendue parent)   |

Pour plus d’informations sur le modèle de données Application Insights, consultez [Modèle de données](../../azure-monitor/app/data-model.md). 

Pour des définitions des concepts Open Tracing, consultez les pages [specification](https://github.com/opentracing/specification/blob/master/specification.md) et [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) relatives à Open Tracing.

## <a name="telemetry-correlation-in-net"></a>Corrélation de télémétrie dans .NET

Au fil du temps, .NET a défini plusieurs façons de mettre en corrélation des journaux de diagnostics et de télémétrie. `System.Diagnostics.CorrelationManager` permet de suivre [LogicalOperationStack et ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). `System.Diagnostics.Tracing.EventSource` et Windows ETW définissent la méthode [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx). `ILogger` utilise les [étendues de journaux](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). WCF et Http lient la propagation de contexte « en cours ».

Toutefois, ces méthodes n’ont pas activé le traçage distribué automatique. `DiagnosticsSource` est une méthode permettant de prendre en charge la corrélation automatique entre ordinateurs. Les bibliothèques .NET prennent en charge Diagnostics Source et permettent la propagation automatique entre ordinateurs du contexte de corrélation via le protocole http de transport.

Le [guide d’utilisation des classes Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) de Diagnostics Source explique les principes de base du suivi des classes Activity. 

ASP.NET Core 2.0 prend en charge l’extraction des en-têtes Http et le démarrage de la nouvelle classe Activity. 

`System.Net.HttpClient` à partir de la version `4.1.0` prend en charge l’injection automatique des en-têtes Http de corrélation et le suivi de l’appel Http en tant que classe Activity.

Il existe un nouveau module Http [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) pour ASP.NET Classic. Ce module implémente la corrélation de télémétrie à l’aide de DiagnosticsSource. Il démarre l’activité en fonction des en-têtes de requête entrante. Il met également en corrélation la télémétrie à différentes étapes du traitement de la requête. Même dans les cas où chaque étape du traitement IIS s’exécute sur différents threads de gestion.

Le Kit de développement logiciel (SDK) à partir de la version `2.4.0-beta1` utilise DiagnosticsSource et Activity pour collecter la télémétrie et l’associer à l’activité en cours. 

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Corrélation des données de télémétrie dans le Kit de développement logiciel (SDK) Java
Le [Kit de développement logiciel (SDK) Java Application Insights](../../azure-monitor/app/java-get-started.md) prend en charge la corrélation automatique des données de télémétrie à partir de la version `2.0.0`. Il renseigne automatiquement `operation_id` pour toutes les données de télémétrie (traces, exceptions, événements personnalisés, etc.) émises dans l’étendue d’une demande. Il s’occupe aussi de propager les en-têtes de corrélation (comme décrit ci-dessus) pour les appels entre les services via le protocole HTTP si [l’agent du Kit de développement logiciel (SDK) Java](../../azure-monitor/app/java-agent.md) est configuré. Remarque : seuls les appels effectués via le client HTTP Apache sont pris en charge pour la fonctionnalité de corrélation. Si vous utilisez un modèle Rest Spring ou Feign, ils peuvent être utilisés avec le client HTTP Apache en arrière-plan.

La propagation automatique de contexte entre les technologies de messagerie (par exemple, Kafka, RabbitMQ, Azure Service Bus) n’est pas prise en charge. Il est toutefois possible de coder manuellement ces scénarios à l’aide d’API `trackDependency` et `trackRequest`, où une dépendance de données de télémétrie représente un message étant ajouté à la file d’attente par un producteur, et où la demande représente un message en cours de traitement par un consommateur. Dans ce cas, `operation_id` et `operation_parentId` doivent être propagés dans les propriétés du message.

<a name="java-role-name"></a>
## <a name="role-name"></a>Nom du rôle

Dans certains cas, vous souhaiterez peut-être personnaliser la façon dont les noms de composant sont affichés dans la [cartographie d’application](../../azure-monitor/app/app-map.md). Pour ce faire, vous pouvez définir manuellement l’élément `cloud_RoleName` en effectuant l’une des opérations suivantes :

Si vous utilisez Spring Boot avec le starter SpringBoot Application Insights, le seul changement nécessaire consiste à définir votre nom personnalisé pour l’application dans le fichier application.properties.

`spring.application.name=<name-of-app>`

Le starter SpringBoot assigne automatiquement cloudRoleName à la valeur que vous entrez pour la propriété spring.application.name.

Si vous utilisez `WebRequestTrackingFilter`, `WebAppNameContextInitializer` définit automatiquement le nom de l’application. Ajoutez ce qui suit à votre fichier de configuration (ApplicationInsights.xml) :
```XML
<ContextInitializers>
  <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
</ContextInitializers>
```

Par le biais de la classe du contexte cloud :

```Java
telemetryClient.getContext().getCloud().setRole("My Component Name");
```

## <a name="next-steps"></a>Étapes suivantes

- [Écrire des données de télémétrie personnalisées](../../azure-monitor/app/api-custom-events-metrics.md)
- [Découvrez plus en détail](../../azure-monitor/app/app-map.md#set-cloudrolename) la définition de cloud_RoleName pour d’autres kits SDK.
- Intégrez tous les composants de votre microservice sur Application Insights. Consultez les [plateformes prises en charge](../../azure-monitor/app/platforms.md).
- Pour connaître les types et les modèles de données Application Insights, consultez [Modèle de données](../../azure-monitor/app/data-model.md).
- Découvrez comment [étendre et filtrer la télémétrie](../../azure-monitor/app/api-filtering-sampling.md).
- [Informations de référence sur la configuration d’Application Insights](configuration-with-applicationinsights-config.md)

