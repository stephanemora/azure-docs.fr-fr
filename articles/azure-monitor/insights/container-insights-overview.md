---
title: Vue d’ensemble d’Azure Monitor pour les conteneurs | Microsoft Docs
description: Cet article décrit Azure Monitor pour les conteneurs qui surveille la solution AKS Container Service et la valeur qu’elle offre en surveillant l’intégrité de vos clusters AKS et Container Instances dans Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: d6321564672097fbf901d1d33afac9f606fcb63a
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521833"
---
# <a name="azure-monitor-for-containers-overview"></a>Vue d’ensemble d’Azure Monitor pour les conteneurs

Azure Monitor pour les conteneurs est une fonctionnalité conçue pour surveiller les performances des charges de travail de conteneur déployées sur Azure Container Instances ou sur des clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS). La surveillance de vos conteneurs est cruciale, particulièrement lorsque vous exécutez un cluster de production à grande échelle, avec plusieurs applications.

Azure Monitor pour les conteneurs vous permet de surveiller les performances en collectant des métriques sur le processeur et la mémoire à partir des contrôleurs, des nœuds et des conteneurs qui sont disponibles dans Kubernetes via l’API Metrics. Les journaux d’activité de conteneur sont aussi collectés.  Une fois que vous activez l’analyse à partir des clusters Kubernetes, métriques et les journaux sont collectés automatiquement pour vous d’une version en conteneur de l’agent d’Analytique de journal pour Linux. Les métriques sont écrites dans le magasin de métriques et les données de journal sont écrites dans le magasin de journaux associé votre [Analytique de journal](../log-query/log-query-overview.md) espace de travail. 

![Azure Monitor pour l’architecture de conteneurs](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Qu’est-ce qu’Azure Monitor pour les conteneurs vous apporte ?

Azure Monitor pour les conteneurs offre une expérience de surveillance complète à l’aide de différentes fonctionnalités d’Azure Monitor vous permettant de comprendre les performances et l’intégrité de votre cluster Kubernetes et les charges de travail de conteneur. Avec Azure Monitor pour les conteneurs, vous pouvez :

* Identifier les conteneurs AKS en cours d’exécution sur le nœud, ainsi que leur utilisation moyenne du processeur et de la mémoire. Cette information peut vous aider à identifier les goulots d’étranglement des ressources.
* Identifier l’utilisation du processeur et de la mémoire des groupes de conteneurs et de leurs conteneurs hébergés dans Azure Container Instances.  
* Identifier l’emplacement du conteneur dans un contrôleur ou un pod. Cette information peut vous permettre de voir les performances globales du contrôleur ou du pod.
* Voir la quantité de ressources utilisée par les charges de travail qui sont exécutées sur l’hôte et qui ne sont pas liées aux processus standard nécessaires à la prise en charge du pod
* Comprendre le comportement du cluster lorsqu’il subit des charges moyennes et très importantes. Cette information peut vous aider à identifier les besoins en capacité et à déterminer la charge maximale que le cluster peut supporter. 
* Configurer des alertes pour vous avertir ou enregistrez-la lorsque l’utilisation du processeur et mémoire sur des nœuds ou des conteneurs dépassent les seuils de façon proactive.  

## <a name="how-do-i-access-this-feature"></a>Comment accéder à cette fonctionnalité ?
Vous pouvez accéder à Azure Monitor pour les conteneurs de deux manières : à partir d’Azure Monitor ou à partir du cluster AKS sélectionné directement. À partir d’Azure Monitor, vous avez une perspective globale de tous les conteneurs déploiement, ceux-ci étant gérés ou non gérés, vous permettant de rechercher et filtrer sur vos abonnements et les groupes de ressources et puis Explorez Azure Monitor pour les conteneurs à partir de la conteneur sélectionné.  Sinon, vous pouvez accéder de la fonctionnalité directement à partir d’un conteneur AKS sélectionné à partir de la page AKS.  

![Vue d’ensemble des méthodes d’accès à Azure Monitor pour les conteneurs](./media/container-insights-overview/azmon-containers-experience.png)

Si vous souhaitez surveiller et gérer vos hôtes de conteneurs Docker et Windows pour afficher la configuration, un audit et l’utilisation des ressources, consultez la [solution de supervision des conteneurs](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Étapes suivantes
Pour commencer la surveillance de votre cluster AKS, passez en revue [comment activer l’analyse Azure pour les conteneurs](container-insights-onboard.md) pour comprendre les exigences et les méthodes disponibles pour activer l’analyse.  
