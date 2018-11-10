---
title: Vue d’ensemble d’Azure Monitor pour conteneurs (préversion) | Microsoft Docs
description: Cet article décrit Azure Monitor pour les conteneurs qui surveille la solution AKS Container Service et la valeur qu’elle offre en surveillant l’intégrité de vos clusters AKS et Container Instances dans Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2018
ms.author: magoedte
ms.openlocfilehash: b90aa9e3c627708b2640086b2b812b8c7079e5bf
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50912528"
---
# <a name="azure-monitor-for-containers-preview-overview"></a>Vue d’ensemble d’Azure Monitor pour conteneurs (préversion)

Azure Monitor pour les conteneurs est une fonctionnalité conçue pour surveiller les performances des charges de travail de conteneur déployées sur des clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS). La surveillance de vos conteneurs est cruciale, particulièrement lorsque vous exécutez un cluster de production à grande échelle, avec plusieurs applications.

Azure Monitor pour les conteneurs vous permet de surveiller les performances en collectant des métriques sur le processeur et la mémoire à partir des contrôleurs, des nœuds et des conteneurs qui sont disponibles dans Kubernetes via l’API Metrics. Les journaux de conteneur sont aussi collectés.  Une fois que vous avez activé la supervision des clusters Kubernetes, ces métriques et ces journaux sont automatiquement collectés à l’aide d’une version conteneurisée de l’agent Log Analytics pour Linux, puis stockés dans votre espace de travail [Log Analytics](../log-analytics/log-analytics-queries.md). 
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Qu’est-ce qu’Azure Monitor pour les conteneurs vous apporte ?

Azure Monitor pour les conteneurs inclut plusieurs vues prédéfinies qui montrent les charges de travail de conteneur résidantes et les éléments qui affectent l’intégrité des performances du cluster Kubernetes surveillé. Vous pouvez ainsi :  

* Identifier les conteneurs AKS en cours d’exécution sur le nœud, ainsi que leur utilisation moyenne du processeur et de la mémoire. Cette information peut vous aider à identifier les goulots d’étranglement des ressources.
* Identifier l’emplacement du conteneur dans un contrôleur ou un pod. Cette information peut vous permettre de voir les performances globales du contrôleur ou du pod. 
* Voir la quantité de ressources utilisée par les charges de travail qui sont exécutées sur l’hôte et qui ne sont pas liées aux processus standard nécessaires à la prise en charge du pod
* Comprendre le comportement du cluster lorsqu’il subit des charges moyennes et très importantes. Cette information peut vous aider à identifier les besoins en capacité et à déterminer la charge maximale que le cluster peut supporter. 

## <a name="how-do-i-access-this-feature"></a>Comment accéder à cette fonctionnalité ?
Vous pouvez accéder à Azure Monitor pour les conteneurs de deux manières : à partir d’Azure Monitor ou à partir du cluster AKS sélectionné directement. À partir d’Azure Monitor, vous disposez d’une perspective globale de tous les conteneurs déployés, ceux qui sont surveillés et ceux qui ne sont pas, vous permettant ainsi de rechercher et de filtrer sur vos abonnements et groupes de ressources, puis d’explorer Azure Monitor pour les conteneurs à partir du conteneur sélectionné.  Sinon, vous pouvez accéder simplement et directement à la fonctionnalité à partir d’un conteneur AKS sélectionné dans la page AKS.  

![Vue d’ensemble des méthodes d’accès à Azure Monitor pour les conteneurs](./media/monitoring-container-insights-overview/azmon-containers-views.png)

Si vous souhaitez surveiller et gérer vos hôtes de conteneurs Docker et Windows pour afficher la configuration, un audit et l’utilisation des ressources, consultez la [solution de surveillance des conteneurs](../log-analytics/log-analytics-containers.md).

## <a name="next-steps"></a>Étapes suivantes
Pour commencer la surveillance de votre cluster AKS, consultez [Comment intégrer Azure Monitor pour les conteneurs](monitoring-container-insights-onboard.md) pour comprendre les exigences et les méthodes disponibles pour activer la surveillance.  
