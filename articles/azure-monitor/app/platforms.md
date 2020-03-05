---
title: 'Application Insights : langages, plateformes et intégrations | Microsoft Docs'
description: Langages, plateformes et intégrations disponibles pour Application Insights
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: d87b1d0aa74263f249db2030f2366b93b8c2b421
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669826"
---
# <a name="supported-languages"></a>Langues prises en charge

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)
* [Python (préversion)](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Plateformes et infrastructures prises en charge

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentation pour les applications déjà déployées (sans codes, basées sur un agent)
* [Machines virtuelles et groupes de machines virtuelles identiques Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET : pour les applications déjà actives](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md) incluant les rôles web et de travail
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Instrumentation par le biais du code (SDK)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python (préversion)](../../azure-monitor/app/opencensus-python.md)
* [Application Windows universelle](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Rôles de travail, services et applications de bureau Windows](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Frameworks de journalisation
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog ou System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J ou Logback](../../azure-monitor/app/java-trace-logs.md)
* [Plug-in LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Exportation et analyse de données
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>Kits de développement logiciel (SDK) non pris en charge
Nous sommes conscients qu’il existe plusieurs autres SDK pris en charge par la communauté. Toutefois, Azure Monitor assure uniquement la prise en charge lors de l’utilisation des kits de développement logiciel pris en charge figurant sur cette page. Nous évaluons constamment les opportunités de développer notre prise en charge d’autres langues. Suivez notre page d’[annonces GitHub](https://github.com/microsoft/ApplicationInsights-Announcements/issues) pour recevoir les dernières informations concernant les kits de développement (SDK). 
