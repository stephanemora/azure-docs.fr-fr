---
title: Plateformes prises en charge par Azure Security Center | Microsoft Docs
description: Ce document fournit une liste des plateformes prises en charge par Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 3c8bf69b745f5dba8c08556908df4d4ae5b5769f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521914"
---
# <a name="supported-platforms"></a>Plateformes prises en charge 

Cette page affiche les plateformes et les environnements pris en charge par Azure Security Center.

## <a name="combinations-of-environments"></a>Combinaisons d’environnements <a name="vm-server"></a>

Azure Security Center prend en charge des machines virtuelles et des serveurs sur différents types d’environnements hybrides :

* Azure uniquement
* Azure et en local
* Azure et autres clouds
* Azure, autres clouds et en local

Dans le cas d’un environnement Azure activé sur un abonnement Azure, Azure Security Center détecte automatiquement les ressources IaaS déployées dans l’abonnement.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Security Center dépend de l’[agent Log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent). Vérifiez que vos machines exécutent l’un des systèmes d’exploitation pris en charge pour cet agent, comme décrit dans les pages suivantes :

* [Agent Log Analytics pour les systèmes d’exploitation pris en charge par Windows](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)
* [Agent Log Analytics pour les systèmes d’exploitation pris en charge par Linux](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

Vérifiez également que votre agent Log Analytics est [correctement configuré pour envoyer des données à Security Center](security-center-enable-data-collection.md#manual-agent).

> [!TIP]
> Pour en savoir plus sur des fonctionnalités Security Center spécifiques disponibles sur Windows et Linux, consultez [Couverture des fonctionnalités pour les machines](security-center-services.md).

## <a name="managed-virtual-machine-services"></a>Services de machine virtuelle managés <a name="virtual-machine"></a>

Des machines virtuelles sont également créées dans un abonnement client dans le cadre de certains services gérés par Azure, comme Azure Kubernetes (AKS) et Azure Databricks, entre autres. Security Center détecte également ces machines virtuelles, et l’agent Log Analytics peut être installé et configuré si un système d’exploitation pris en charge est disponible.

## <a name="cloud-services"></a>Services cloud <a name="cloud-services"></a>

Les machines virtuelles s’exécutant dans un service cloud sont également prises en charge. Seuls les rôles de travail et web des services cloud en cours d’exécution dans des emplacements de production sont surveillés. Pour en savoir plus sur les services cloud, consultez [Vue d’ensemble d’Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).

La protection des machines virtuelles résidant dans Azure Stack est également prise en charge. Pour plus d’informations sur l’intégration de Security Center avec Azure Stack, consultez [Intégrer vos machines virtuelles Azure Stack à Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Security Center collecte des données avec l’agent Log Analytics](security-center-enable-data-collection.md).
- Découvrez comment [Security Center gère et protège les données](security-center-data-security.md).
- [Découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception](security-center-planning-and-operations-guide.md).