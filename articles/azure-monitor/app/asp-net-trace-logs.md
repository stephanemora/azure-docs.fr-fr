---
title: Exploration des journaux d’activité .NET dans Application Insights
description: Rechercher dans les journaux générés par la Trace, NLog ou Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mbullwin
ms.openlocfilehash: 74cb1b3ec4e0570aa4316e6f45e99719f36815d1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150715"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Exploration des journaux d’activité .NET/.NET Core dans Application Insights

Envoyer des journaux de suivi de diagnostic pour votre application ASP.NET/ASP.NET Core ILogger, NLog, log4Net ou System.Diagnostics.Trace pour [Azure Application Insights][start]. Vous pouvez ensuite Explorer et les rechercher. Ces journaux est fusionnés avec les autres fichiers journaux à partir de votre application, afin de pouvoir identifier les traces qui sont associés à chaque demande de l’utilisateur et de les mettre en corrélation avec d’autres événements et les rapports d’exception.

> [!NOTE]
> Avez-vous besoin du module de capture de journaux ? Il est un adaptateur très utile pour les enregistreurs d’événements par des tiers. Mais si vous n’utilisez pas déjà NLog, log4Net ou System.Diagnostics.Trace, vous pouvez appeler [ **Application Insights TrackTrace()** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) directement.
>
>
## <a name="install-logging-on-your-app"></a>Installation de la journalisation sur votre application
Installez votre infrastructure de journalisation choisie dans votre projet, dont le résultat dans une entrée dans le fichier app.config ou web.config.

```XML
    <configuration>
      <system.diagnostics>
    <trace autoflush="true" indentsize="0">
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
   </configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Configuration d’Application Insights pour la collecte des journaux d’activité
[Ajouter Application Insights à votre projet](../../azure-monitor/app/asp-net.md) si vous n’avez pas fait. Une option permettant d’inclure le collecteur de journaux doit s’afficher.

Ou cliquez sur votre projet dans l’Explorateur de solutions pour **configurer Application Insights**. Sélectionnez le **configurer la collecte de trace** option.

> [!NOTE]
> Aucune option collecteur du menu ou le journal de Application Insights ? Consultez la [Résolution des problèmes](#troubleshooting).

## <a name="manual-installation"></a>Installation manuelle
Utilisez cette méthode si votre type de projet n’est pas pris en charge par le programme d’installation Application Insights (par exemple, un projet de bureau Windows).

1. Si vous prévoyez d'utiliser log4Net ou NLog, installez-le dans votre projet.
2. Dans l’Explorateur de solutions, cliquez sur votre projet, puis sélectionnez **gérer les Packages NuGet**.
3. Recherchez « Application Insights ».
4. Sélectionnez l’un des packages suivants :

   - Pour ILogger : [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Pour NLog : [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Pour Log4Net : [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Pour System.Diagnostics : [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

Le package NuGet installe les assemblys nécessaires et modifie le fichier web.config ou app.config si applicable.

## <a name="ilogger"></a>ILogger

Pour obtenir des exemples d’utilisation de l’implémentation de l’Application Insights ILogger avec les applications de console et ASP.NET Core, consultez [ApplicationInsightsLoggerProvider pour .NET Core ILogger journaux](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Insertion d'appels de journaux de diagnostic
Si vous utilisez System.Diagnostics.Trace, un appel standard serait :

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Si vous préférez log4net ou NLog, utilisez :

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Utiliser des événements EventSource
Vous pouvez configurer les événements [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) à envoyer à Application Insights en tant que traces. D’abord, installez le package NuGet `Microsoft.ApplicationInsights.EventSourceListener`. Ensuite, modifiez la section `TelemetryModules` du fichier [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Pour chaque source, vous pouvez définir les paramètres suivants :
 * **Nom** Spécifie le nom de l’événement EventSource à collecter.
 * **Niveau** Spécifie le niveau de journalisation à collecter : *Critique*, *erreur*, *d’information*, *toujours journaliser*, *Verbose*, ou *avertissement*.
 * **Mots clés** (facultatif) spécifiez la valeur entière de combinaisons de mots clés à utiliser.

## <a name="use-diagnosticsource-events"></a>Utiliser des événements DiagnosticSource
Vous pouvez configurer les événements [System.Diagnostics.Tracing.EventSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) de façon à les envoyer à Application Insights en tant que traces. Commencez par installer le package NuGet [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener). Puis modifiez la section « TelemetryModules » de la [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) fichier.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Pour chaque DiagnosticSource à tracer, ajoutez une entrée avec la **nom** attribut défini sur le nom de votre DiagnosticSource.

## <a name="use-etw-events"></a>Utiliser des événements ETW
Vous pouvez configurer les événements de suivi d’événements pour Windows (ETW) à envoyer à Application Insights en tant que traces. D’abord, installez le package NuGet `Microsoft.ApplicationInsights.EtwCollector`. Puis modifiez la section « TelemetryModules » de la [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) fichier.

> [!NOTE] 
> Événements ETW peuvent être collectées uniquement si le processus qui héberge le SDK s’exécute sous une identité qui est membre des utilisateurs du journal de performances ou des administrateurs.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Pour chaque source, vous pouvez définir les paramètres suivants :
 * **ProviderName** est le nom du fournisseur ETW à collecter.
 * **ProviderGuid** Spécifie le GUID du fournisseur ETW à collecter. Il peut être utilisé à la place de `ProviderName`.
 * **Niveau** définit le niveau de journalisation à collecter. Il peut être *critique*, *erreur*, *information*, *toujours journaliser*, *Verbose*, ou *Avertissement*.
 * **Mots clés** (facultatif) Définissez la valeur entière de combinaisons de mots clés à utiliser.

## <a name="use-the-trace-api-directly"></a>Utiliser l’API de suivi directement
Vous pouvez appeler directement l’API de suivi d’Application Insights. Les adaptateurs de journalisation utilisent cette API.

Par exemple : 

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

l’un des avantages de TrackTrace est que vous pouvez insérer des données relativement longues dans le message. Par exemple, vous pourriez y encoder des données POST.

Vous pouvez également ajouter un niveau de gravité à votre message. Et, comme les autres données de télémétrie, vous pouvez ajouter des valeurs de propriété pour aider à filtrer ou rechercher différents jeux de traces. Par exemple : 

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Cela vous permettrait de filtrer facilement dans [recherche] [ diagnostic] tous les messages d’un niveau de gravité particulier qui se rapportent à une base de données.

## <a name="explore-your-logs"></a>Exploration de vos journaux d’activité
Exécutez votre application en mode débogage ou déployez-la en direct.

Dans le volet de vue d’ensemble de votre application dans [le portail Application Insights][portal], sélectionnez [recherche][diagnostic].

Vous pouvez par exemple :

* Filtrer sur les traces de journal ou les éléments avec des propriétés spécifiques.
* Inspecter un élément spécifique en détail
* Rechercher d’autres données de journal système qui est lié à la même demande utilisateur (a la même OperationId).
* Enregistrez la configuration d’une page en tant que favori.

> [!NOTE]
>Si votre application envoie beaucoup de données et que vous utilisez le SDK Application Insights pour ASP.NET version 2.0.0-beta3 ou version ultérieure, le *l’échantillonnage ADAPTATIF* fonctionnalité peut fonctionner et transmettre uniquement une partie de vos données de télémétrie. [En savoir plus sur l'échantillonnage.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Résolution de problèmes
### <a name="how-do-i-do-this-for-java"></a>Comment faire pour Java ?
Utilisez les [adaptateurs de journaux Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Le menu contextuel du projet ne contient aucune option Application Insights
* Assurez-vous que les outils Application Insights est installé sur l’ordinateur de développement. Visual Studio **outils** > **Extensions et mises à jour**, recherchez **outils Application Insights**. Si ce n’est pas sur le **installé** onglet, ouvrez le **Online** onglet et l’installer.
* Cela peut être un type de projet outils Application Insights ne prend pas en charge. Utilisez [l’installation manuelle](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Il n’existe aucune option d’adaptateur dans l’outil de configuration
* Installez tout d’abord le framework de journalisation.
* Si vous utilisez System.Diagnostics.Trace, assurez-vous qu’il [configuré dans *web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Assurez-vous que vous disposez de la dernière version d’Application Insights. Dans Visual Studio, accédez à **outils** > **Extensions et mises à jour**, puis ouvrez le **mises à jour** onglet. Si **Developer Analytics Tools** est là, sélectionnez-le pour mettre à jour.

### <a name="emptykey"></a>J’obtiens le message d’erreur « la clé d’Instrumentation ne peut pas être vide »
Probablement, vous avez installé le package Nuget de journalisation sans installer Application Insights. Dans l’Explorateur de solutions, cliquez sur *ApplicationInsights.config*, puis sélectionnez **mise à jour Application Insights**. Vous allez invité à vous connecter à Azure et créer une ressource Application Insights ou réutiliser un existant. Qui doit corriger le problème.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Je peux voir les traces, mais pas les autres événements dans recherche de diagnostic
Il peut prendre un certain temps pour tous les événements et les demandes dans le pipeline.

### <a name="limits"></a>Quelle est la quantité de données conservée ?
Plusieurs facteurs affectent la quantité de données qui sont conservées. Pour plus d’informations, consultez le [limites](../../azure-monitor/app/api-custom-events-metrics.md#limits) section de la page de métriques d’événement client.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Je ne vois pas certaines entrées de journal que je m’attendais
Si votre application envoie des gros volumes de données et que vous utilisez le SDK Application Insights pour ASP.NET version 2.0.0-beta3 ou version ultérieure, la fonctionnalité d’échantillonnage ADAPTATIF peut fonctionner et envoyer seulement une partie de vos données de télémétrie. [En savoir plus sur l'échantillonnage.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Étapes suivantes

* [Diagnostiquer les défaillances et les exceptions dans ASP.NET][exceptions]
* [En savoir plus sur la recherche][diagnostic]
* [Configuration des tests de disponibilité et de réactivité][availability]
* [Résolution des problèmes][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md