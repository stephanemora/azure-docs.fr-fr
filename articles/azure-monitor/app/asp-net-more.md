---
title: Tirer parti d’Azure Application Insights | Microsoft Docs
description: Après avoir pris en main Application Insights, voici un résumé des fonctionnalités que vous pouvez explorer.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/03/2017
ms.author: mbullwin
ms.openlocfilehash: 7e62efb0b64b0b882b36126af4a1f9d520d92475
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65230636"
---
# <a name="more-telemetry-from-application-insights"></a>Plus de télémétrie dans Application Insights
Une fois que vous avez [ajouté Application Insights à votre code ASP.NET](../../azure-monitor/app/asp-net.md), vous pouvez encore suivre quelques étapes supplémentaires pour obtenir davantage de données de télémétrie. 

| Action | Ce que vous obtenez|
|---|---|
|(serveurs IIS) [Installer Status Monitor](https://go.microsoft.com/fwlink/?LinkId=506648) sur chaque ordinateur serveur.<br/>(Applications web Azure) Dans le panneau de configuration Azure de l’application web, ouvrez le panneau Application Insights.| [**Compteurs de performance**](../../azure-monitor/app/performance-counters.md)<br/>[**Exceptions**](asp-net-exceptions.md) - arborescences détaillées des appels de procédure<br/>[**Dépendances**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Ajouter l’extrait de code JavaScript à vos pages web](../../azure-monitor/app/javascript.md)|[Performances des pages](../../azure-monitor/app/usage-overview.md), exceptions du navigateur, performances AJAX. Télémétrie côté client personnalisée.|
|[Créer des tests web de disponibilité](../../azure-monitor/app/monitor-web-app-availability.md)|Recevoir des alertes si votre site est indisponible|
|[Vérifier que buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) est généré par MSBuild|[Créer des annotations dans les graphiques de mesures](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Écrire des mesures et des événements personnalisés](../../azure-monitor/app/api-custom-events-metrics.md)|Compter des mesures et des événements commerciaux, suivre l’utilisation détaillée et plus encore.|
|[Profiler votre site en ligne](https://aka.ms/AIProfilerPreview)|Minutage de fonctions précis à partir de votre application web en ligne|






