---
title: Surveiller les conteneurs sur Azure Service Fabric avec les journaux Azure Monitor | Microsoft Docs
description: Utilisez les journaux Azure Monitor pour surveiller les conteneurs en cours d’exécution sur des clusters Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: d03d68560502821b9c343be983d9f7b5a83ed977
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663183"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Surveiller les conteneurs avec les journaux Azure Monitor
 
Cet article décrit les étapes requises pour configurer le conteneur de journaux Azure Monitor solution de surveillance pour afficher les événements de conteneur. Pour configurer votre cluster afin de collecter les événements de conteneur, consultez ce [tutoriel pas à pas](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Configurer la solution de surveillance des conteneurs

> [!NOTE]
> Vous devez disposer d’Azure Monitor journaux configurée pour votre cluster, ainsi que déployer sur vos nœuds de l’agent Log Analytique. Si vous ne faites pas, suivez les étapes de [configuré des journaux d’Azure Monitor](service-fabric-diagnostics-oms-setup.md) et [ajouter l’agent d’Analytique de journal à un cluster](service-fabric-diagnostics-oms-agent.md) première.

1. Une fois que votre cluster est configuré avec les journaux d’Azure Monitor et de l’agent d’Analytique de journal, déployez vos conteneurs. Attendez que vos conteneurs soient déployés avant de passer à l’étape suivante.

2. Dans la Place de marché Azure, recherchez *Solution Container Monitoring*, puis cliquez sur la ressource **Solution Container Monitoring** qui doit s’afficher sous la catégorie Surveillance + Gestion.

    ![Ajout de la solution Conteneurs](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Créez la solution dans l’espace de travail qui a été créé pour le cluster. Après cette modification, l’agent se met à collecter des données Docker sur les conteneurs. Au bout de 15 minutes environ, la solution doit s’afficher avec les statistiques et journaux entrants, comme illustré ci-après.

    ![Tableau de bord Log Analytics de base](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

L’agent permet de collecter plusieurs journaux spécifiques aux conteneurs qui peuvent être interrogés dans les journaux Azure Monitor ou permettent de visualiser les indicateurs de performance. Les types de journaux collectés sont :

* ContainerInventory : affiche des informations sur les images, le nom et l’emplacement du conteneur
* ContainerImageInventory : informations sur les images déployées, ID ou tailles compris
* ContainerLog : journaux d’erreurs spécifiques, journaux de docker (stdout, etc.) et autres entrées
* ContainerServiceLog : les commandes de démon docker qui ont été exécutées
* Performances : les compteurs de performances, dont l’utilisation par le conteneur du processeur, de la mémoire, du E/S de disque, du trafic réseau et de mesures personnalisées à partir des machines hôtes



## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur [Azure Monitor enregistre la solution conteneurs](../azure-monitor/insights/containers.md).
* En savoir plus sur l’orchestration des conteneurs dans Service Fabric : [Service Fabric et conteneurs](service-fabric-containers-overview.md)
* Familiarisez-vous avec les [journal des requêtes et recherches](../log-analytics/log-analytics-log-searches.md) fonctionnalités offertes dans le cadre des journaux d’Azure Monitor
* Configurer les journaux Azure Monitor pour configurer [alerte automatisée](../log-analytics/log-analytics-alerts.md) règles afin de faciliter la détection et les diagnostics