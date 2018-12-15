---
title: Surveiller les conteneurs dans Azure Service Fabric avec Log Analytics | Microsoft Docs
description: Utilisez Log Analytics pour surveiller les conteneurs qui sont exécutés dans les clusters Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: d5fd55ec93ce07e30e4c6f123f9be8492581053c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52972250"
---
# <a name="monitor-containers-with-log-analytics"></a>Surveiller les conteneurs avec Log Analytics
 
Cet article traite des étapes nécessaires pour configurer la solution de supervision de conteneurs Azure Log Analytics, qui permet de visualiser les événements de conteneur. Pour configurer votre cluster afin de collecter les événements de conteneur, consultez ce [tutoriel pas à pas](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Configurer la solution de surveillance des conteneurs

> [!NOTE]
> Vous devez avoir configuré Log Analytics pour votre cluster et déployé l’agent Log Analytics sur vos nœuds. Si ce n’est pas le cas, commencez par suivre les procédures des articles indiquant comment [configurer Log Analytics](service-fabric-diagnostics-oms-setup.md) et [ajouter l’agent Log Analytics à un cluster](service-fabric-diagnostics-oms-agent.md).

1. Une fois votre cluster configuré avec Log Analytics et l’agent Log Analytics, déployez vos conteneurs. Attendez que vos conteneurs soient déployés avant de passer à l’étape suivante.

2. Dans la Place de marché Azure, recherchez *Solution Container Monitoring*, puis cliquez sur la ressource **Solution Container Monitoring** qui doit s’afficher sous la catégorie Surveillance + Gestion.

    ![Ajout de la solution Conteneurs](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Créez la solution dans l’espace de travail qui a été créé pour le cluster. Après cette modification, l’agent se met à collecter des données Docker sur les conteneurs. Au bout de 15 minutes environ, la solution doit s’afficher avec les statistiques et journaux entrants, comme illustré ci-après.

    ![Tableau de bord Log Analytics de base](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

L’agent permet de collecter plusieurs journaux propres aux conteneurs qui peuvent être interrogés dans Log Analytics ou utilisés pour visualiser les indicateurs de performance. Les types de journaux collectés sont :

* ContainerInventory : affiche des informations sur les images, le nom et l’emplacement du conteneur
* ContainerImageInventory : informations sur les images déployées, ID ou tailles compris
* ContainerLog : journaux d’erreurs spécifiques, journaux de docker (stdout, etc.) et autres entrées
* ContainerServiceLog : les commandes de démon docker qui ont été exécutées
* Performances : les compteurs de performances, dont l’utilisation par le conteneur du processeur, de la mémoire, du E/S de disque, du trafic réseau et de mesures personnalisées à partir des machines hôtes



## <a name="next-steps"></a>Étapes suivantes
* Apprenez-en davantage sur la [solution Container Monitoring de Log Analytics](../azure-monitor/insights/containers.md).
* En savoir plus sur l’orchestration des conteneurs dans Service Fabric : [Service Fabric et conteneurs](service-fabric-containers-overview.md)
* Familiarisez-vous avec les fonctionnalités de [requêtes et recherches dans les journaux](../log-analytics/log-analytics-log-searches.md) offertes dans le cadre de Log Analytics
* Configurez Log Analytics pour paramétrer des règles d’[alerte automatisée](../log-analytics/log-analytics-alerts.md) afin de faciliter la détection et les diagnostics