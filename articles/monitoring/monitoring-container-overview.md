---
title: Vue d’ensemble de la surveillance des conteneurs Azure | Microsoft Docs
description: Cet article fournit une vue d’ensemble des différentes méthodes disponibles dans Azure pour surveiller les conteneurs dans Azure et évaluer rapidement l’intégrité et la disponibilité d’un cluster.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: db85f85011154dcc7adfa9d569e9015a9c5c33ca
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055043"
---
# <a name="overview-of-monitoring-containers-in-azure"></a>Vue d’ensemble de la surveillance des conteneurs dans Azure
Avec Azure, vous pouvez surveiller et gérer efficacement vos charges de travail déployées dans des conteneurs Azure qui exécutent Kubernetes ou Docker. Il est important de comprendre le fonctionnement des conteneurs avec plusieurs applications de microservices afin de fournir un service fiable à grande échelle et de prendre en charge votre plan de surveillance. Cet article fournit une vue d’ensemble des fonctionnalités de gestion et de supervision disponibles dans Azure pour vous aider à comprendre leur rôle et à les utiliser selon vos besoins.

Grâce à [Azure Monitor pour les conteneurs](monitoring-container-insights-overview.md), vous pouvez visualiser les performances et l’intégrité de votre infrastructure de conteneurs Linux d’un simple coup d’œil et analyser rapidement les problèmes. Les données de télémétrie sont stockées dans un espace de travail Log Analytics et intégrées dans le portail Azure, où vous pouvez explorer, filtrer et segmenter les données agrégées à l’aide de tableaux de bord afin de surveiller la charge, les performances et l’intégrité.  

Pour les conteneurs en cours d’exécution en dehors du service Azure Kubernetes hébergé, la [solution de conteneurs Windows et Docker](../log-analytics/log-analytics-containers.md) de Log Analytics vous permet de consulter et de gérer vos hôtes de conteneurs Windows et Docker. À partir de votre espace de travail Log Analytics, vous pouvez afficher les détails de l’inventaire, les performances et les événements provenant des nœuds et des conteneurs dans l’environnement. Vous pouvez afficher des informations d’audit détaillées précisant les commandes utilisées avec des conteneurs, et vous pouvez corriger les conteneurs en affichant et en recherchant des journaux centralisés, sans avoir à accéder à distance aux hôtes Docker ou Windows.

Pour bénéficier d’une surveillance globale ou de bout en bout de l’application, chaque dépendance, qu’il s’agisse d’une ressource Azure ou locale, doit être analysée avec Azure Monitor ou Log Analytics.  La couche application doit être incluse afin d’ajouter une couche supplémentaire de détection d’intégrité, tant au niveau de la plateforme que de l’application à l’aide d’Application Insights. Au niveau de la plateforme, il existe des Kits de développement logiciel (SDK) Application Insights pour [Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes), [Docker](https://hub.docker.com/r/microsoft/applicationinsights/) et [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights). Pour les applications de microservices, il existe une prise en charge [Java](../application-insights/app-insights-java-get-started.md), [Node.js](../application-insights/app-insights-nodejs-quick-start.md), [.Net](../application-insights/app-insights-asp-net.md), [.Net Core](../application-insights/app-insights-asp-net-core.md), ainsi que diverses autres [langues/infrastructures](../application-insights/app-insights-platforms.md). 

Dans le cas contraire, les problèmes passeront inaperçus et pourraient avoir un impact sur la disponibilité de l’application et les objectifs de niveau de service ne seront pas atteints.  
