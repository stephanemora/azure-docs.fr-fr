---
title: Azure Application Insights - Collecte automatique de dépendance | Microsoft Docs
description: Application Insights collecte et visualise automatiquement les dépendances
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: reference
ms.date: 10/16/2018
ms.author: mbullwin
ms.openlocfilehash: b6188bbf89d9aee842d7f8e232a820b59c4d9e31
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023251"
---
# <a name="application-insights-nuget-packages"></a>Packages NuGet Application Insights

Voici la liste actuelle des versions stables des Packages NuGet pour Application Insights.

## <a name="common-packages-for-aspnet"></a>Packages courants pour ASP.NET

| Nom du package | Version stable | Description | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.8.0 | Fournit des fonctionnalités essentielles pour la transmission de tous les types de données de télémétrie Application Insights et est un package dépendant pour tous les autres packages Application Insights | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Permet l’interception d’appels de méthode | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Collecteur de dépendances Application Insights pour les applications .NET. Package dépendant pour les packages Application Insights spécifiques à la plateforme. Il fournit la collecte automatique des données de télémétrie des dépendances. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Le collecteur de compteurs de performances Application Insights vous permet d’envoyer les données collectées par les compteurs de performances à Application Insights. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.8.0 | Application Insights pour les applications web .NET | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Le package NuGet Windows Server Application Insights fournit la collecte automatique des données de télémétrie Application Insights pour les applications .NET. Ce package peut être utilisé en tant que package dépendant pour les packages Application Insights spécifiques à la plateforme ou que package autonome pour les applications .NET non couvertes par les packages spécifiques à la plateforme (comme dans le cas des rôles de travail .NET). | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Fournit un canal de télémétrie au SDK Windows Server Application Insights qui conserve les données de télémétrie dans les scénarios hors connexion. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Packages courants pour ASP.NET Core

| Nom du package | Version stable | Description | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Application Insights pour applications web ASP.NET Core. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.8.0 | Ce package fournit des fonctionnalités essentielles pour la transmission de tous les types de données de télémétrie Application Insights et est un package dépendant pour tous les autres packages Application Insights | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Collecteur de dépendances Application Insights pour les applications .NET. Package dépendant pour les packages Application Insights spécifiques à la plateforme. Il fournit la collecte automatique des données de télémétrie des dépendances. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Le collecteur de compteurs de performances Application Insights vous permet d’envoyer les données collectées par les compteurs de performances à Application Insights. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Le package NuGet Windows Server Application Insights fournit la collecte automatique des données de télémétrie Application Insights pour les applications .NET. Ce package peut être utilisé en tant que package dépendant pour les packages Application Insights spécifiques à la plateforme ou que package autonome pour les applications .NET non couvertes par les packages spécifiques à la plateforme (comme dans le cas des rôles de travail .NET). | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Fournit un canal de télémétrie au SDK Windows Server Application Insights qui conserve les données de télémétrie dans les scénarios hors connexion. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="listenerscollectorsappenders"></a>Écouteurs/collecteurs/appenders

| Nom du package | Version stable | Description | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Permet le transfert d’événements depuis DiagnosticSource vers Application Insights. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Le package EventSourceListener Application Insights permet d’envoyer des données depuis les événements EventSource vers Application Insights. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Le package EtwCollector Application Insights permet d’envoyer des données depuis le suivi d’événements pour Windows (ETW) vers Application Insights. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | Écouteur de suivi personnalisé qui vous permet d’envoyer les messages du journal de suivi à Application Insights. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Appender personnalisé qui vous permet d’envoyer les messages du journal Log4Net à Application Insights. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  Cible personnalisée qui vous permet d’envoyer les messages du journal NLog à Application Insights. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Supervise les exceptions dans votre application et collecte automatiquement des instantanés en vue d’une analyse hors connexion. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Nom du package | Version stable | Description | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Ce package fournit la décoration automatique des données de télémétrie avec le contexte Service Fabric dans lequel l’application est en cours d’exécution. N’utilisez pas ce package NuGet pour les applications Service Fabric natives. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Module Application Insights pour les applications Service Fabric. Utilisez ce package NuGet uniquement pour les applications Service Fabric natives. Pour les applications exécutées dans des conteneurs, utilisez le package Microsoft.ApplicationInsights.ServiceFabric. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Status Monitor

| Nom du package | Version stable | Description | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Active la collecte des données de runtime pour les applications x64 | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Active la collecte des données de runtime pour les applications x86. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Ces packages font partie des fonctionnalités essentielles de la supervision du runtime dans [Status Monitor](app-insights-monitor-performance-live-website-now.md). Vous n’avez pas besoin de télécharger ces packages directement ; utilisez simplement le programme d’installation de Status Monitor. Si vous souhaitez en savoir plus sur le fonctionnement de ces packages, vous pouvez commencer par consulter ce [billet de blog](http://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) rédigé par un de nos développeurs.

## <a name="additional-packages"></a>Packages supplémentaires

| Nom du package | Version stable | Description | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | Cette extension permet de superviser Application Insights sur un service Azure App Service. SDK version 2.6.1. Instructions : ajoutez les paramètres d’application « APPINSIGHTS_INSTRUMENTATIONKEY » avec votre clé d’instrumentation, puis redémarrez l’application web.| [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | Ce package contient les fichiers nécessaires pour l’injection d’Application Insights sans code. | [Télécharger le package](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Étapes suivantes

- Supervisez [ASP.NET Core](app-insights-asp-net-core.md).
- Profilez des [applications web Azure Linux](app-insights-profiler-aspnetcore-linux.md) ASP.NET Core.
- Déboguez des [instantanés](app-insights-snapshot-debugger.md) ASP.NET.