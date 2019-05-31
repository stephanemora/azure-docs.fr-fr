---
title: Vue d’ensemble des Insights dans Azure Monitor | Microsoft Docs
description: Insights fournissent une expérience de surveillance personnalisée dans Azure Monitor pour les services et applications spécifiques. Cet article fournit une brève description de chacun des informations qui sont actuellement disponibles.
services: azure-monitor
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: bwren
ms.openlocfilehash: 8cb39a174c570b7019e872d731f49252a9505406
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247228"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Vue d’ensemble des Insights dans Azure Monitor
Insights fournissent une expérience de surveillance personnalisée pour les services et applications spécifiques. Ils stockent des données dans le [plateforme de données Azure Monitor](../platform/data-platform.md) et tirer parti d’autres fonctionnalités d’Azure Monitor pour l’analyse et la génération d’alertes, mais peut collecter des données supplémentaires et fournir une expérience utilisateur unique dans le portail Azure. Accéder aux insights à partir de la **Insights** section du menu Azure Monitor dans le portail Azure.

Les sections suivantes fournissent une brève description des informations qui sont actuellement disponibles dans Azure Monitor. Consultez la documentation détaillée pour plus d’informations sur chacun.

## <a name="application-insights"></a>Application Insights
Application Insights est un service extensible de gestion des performances des applications (APM) destiné aux développeurs web sur de multiples plateformes. Utilisez-le pour analyser votre application web en direct. Il fonctionne pour les applications sur un large éventail de plateformes, notamment .NET, Node.js et Java EE, hébergé en local, hybride ou n’importe quel cloud public. Il s’intègre à votre processus DevOps et offre des points de connexion à une variété d’outils de développement.

Consultez [What ' s Application Insights ?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor pour les conteneurs
Azure Monitor pour les conteneurs supervise les performances des charges de travail de conteneur déployées sur Azure Container Instances ou sur des clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS). La surveillance de vos conteneurs est cruciale, particulièrement lorsque vous exécutez un cluster de production à grande échelle, avec plusieurs applications.

Consultez [Azure Monitor pour une vue d’ensemble de conteneurs](../insights/container-insights-overview.md).

![Azure Monitor pour les conteneurs](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure Monitor pour les groupes de ressources (version préliminaire)
Azure Monitor pour les groupes de ressources permet de trier et diagnostiquer les problèmes que rencontrent de vos ressources individuelles, tout en offrant le contexte de l’intégrité et les performances du groupe de ressources dans sa globalité.

Consultez [surveiller des groupes de ressources avec Azure Monitor (version préliminaire)](../insights/resource-group-insights.md).

![Azure Monitor pour les groupes de ressources](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor pour les machines virtuelles (version préliminaire)
Azure Monitor pour machines virtuelles surveille les machines virtuelles et groupes de machines virtuelles identiques à grande échelle. Il analyse les performances et l’intégrité des machines virtuelles Windows et Linux, et supervise leurs processus et dépendances vis-à-vis d’autres ressources et des processus externes.

Consultez [What ' s Azure Monitor pour les machines virtuelles ?](vminsights-overview.md)

![Azure Monitor pour machines virtuelles](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur la [plateforme de données Azure Monitor](../platform/data-platform.md) exploité par insights.
* En savoir plus sur les différentes [sources de données utilisées par Azure Monitor](../platform/data-sources.md) et les différents types de données collectées par chacun des analyses.
