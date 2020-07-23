---
title: Tirer parti d’Azure Application Insights | Microsoft Docs
description: Après avoir pris en main Application Insights, voici un résumé des fonctionnalités que vous pouvez explorer.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 6d30833840b62412ae03c761baacf54c939c9d33
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540024"
---
# <a name="more-telemetry-from-application-insights"></a>Plus de télémétrie dans Application Insights
Une fois que vous avez [ajouté Application Insights à votre code ASP.NET](../../azure-monitor/app/asp-net.md), vous pouvez encore suivre quelques étapes supplémentaires pour obtenir davantage de données de télémétrie. 

| Action | Ce que vous obtenez|
|---|---|
|(serveurs IIS) [Installer Status Monitor](https://go.microsoft.com/fwlink/?LinkId=506648) sur chaque ordinateur serveur.<br/>(Applications web Azure) Dans le panneau de configuration Azure de l’application web, ouvrez le panneau Application Insights.| [**Compteurs de performance**](../../azure-monitor/app/performance-counters.md)<br/>[**Exceptions**](asp-net-exceptions.md) - arborescences détaillées des appels de procédure<br/>[**Dépendances**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Ajouter l’extrait de code JavaScript à vos pages web](../../azure-monitor/app/javascript.md)|[Performances des pages](../../azure-monitor/app/usage-overview.md), exceptions du navigateur, performances AJAX. Télémétrie côté client personnalisée.|
|[Créer des tests web de disponibilité](../../azure-monitor/app/monitor-web-app-availability.md)|Recevoir des alertes si votre site est indisponible|
|[Vérifier que buildinfo.config](/visualstudio/debugger/diagnose-problems-after-deployment?view=vs-2015) est généré par MSBuild|[Créer des annotations dans les graphiques de mesures](./annotations.md)
|[Écrire des mesures et des événements personnalisés](../../azure-monitor/app/api-custom-events-metrics.md)|Compter des mesures et des événements commerciaux, suivre l’utilisation détaillée et plus encore.|
|[Profiler votre site en ligne](https://aka.ms/AIProfilerPreview)|Minutage de fonctions précis à partir de votre application web en ligne|
