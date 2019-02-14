---
title: 'Application Insights : langages, plateformes et intégrations | Microsoft Docs'
description: Langages, plateformes et intégrations disponibles pour Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/01/2016
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 825f51742a65ead545f7c7fcb0a53d1a82a08b1a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992804"
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>Analyse développeur : langages, plateformes et intégrations
Ces éléments sont des implémentations [Application Insights](../../azure-monitor/app/app-insights-overview.md) dont nous avons entendu parler, y compris par des tiers.

## <a name="languages---officially-supported-by-application-insights-team"></a>Langages : officiellement pris en charge par l’équipe Application Insights
* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Pages web JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="languages---community-supported"></a>Langages : pris en charge par la communauté
* [F#](https://safe-stack.github.io/docs/template-azure-ai/)
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Tout autre élément](#projects)

## <a name="platforms-and-frameworks"></a>Plates-formes et infrastructures
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET : pour les applications déjà actives](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center)
* [Angular](https://github.com/MarkPieszak/angular-application-insights)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* 
  [Services cloud Azure](../../azure-monitor/app/cloudservices.md)&#151;incluant les rôles web et de travail
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)
* [Docker](../../azure-monitor/app/docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Ionic](https://github.com/SoftwarePioniere/ionic-application-insights)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (App Center)
* [J2EE](../../azure-monitor/app/java-get-started.md)
* [J2EE : pour les applications déjà actives](../../azure-monitor/app/java-live.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [SAFE Stack](https://safe-stack.github.io/docs/template-azure-ai/)
* [Spring](https://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [Application Windows universelle](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Rôles de travail, services et applications de bureau Windows](../../azure-monitor/app/windows-desktop.md)
* [Tout autre élément](#projects)

## <a name="logging-frameworks"></a>Frameworks de journalisation
* [Log4Net, NLog ou System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J ou Logback](../../azure-monitor/app/java-trace-logs.md)
* [Journalisation sémantique (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) - s’intègre au [bloc applicatif de journalisation sémantique](https://msdn.microsoft.com/library/dn440729.aspx)
* [Tests de charge basés sur le cloud](https://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [Plug-in LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)
* [Logrus](https://github.com/jjcollinge/logrus-appinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="content-management-systems"></a>Systèmes de gestion de contenu
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](../../azure-monitor/app/sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>Exportation et analyse de données
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md )

## <a name="projects"></a> Génération de votre propre kit de développement logiciel (SDK)
S’il n’existe pas encore de kit de développement logiciel (SDK) pour votre langage ou plateforme, peut-être souhaitez-vous créer un ? Examinons le code des kits de développement logiciel (SDK) existants répertoriés dans le [projet Kit de développement logiciel (SDK) Application Insights sur GitHub](https://github.com/Microsoft/AppInsights-Home).
