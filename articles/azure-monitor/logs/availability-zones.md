---
title: Zones de disponibilité dans Azure Monitor
description: Zones de disponibilité dans Azure Monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/18/2021
ms.custom: references_regions
ms.openlocfilehash: c01e83fbbf117c5491e9e12ac24aa0d5d2fa8b67
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122527962"
---
# <a name="availability-zones-in-azure-monitor"></a>Zones de disponibilité dans Azure Monitor

Les [zones de disponibilité Azure](../../availability-zones/az-overview.md) protègent vos applications et données contre les pannes des centres de données et peuvent offrir une résilience pour les fonctionnalités Azure Monitor telles qu’Application Insights et toute autre fonctionnalité qui repose sur un espace de travail Log Analytics. Quand un espace de travail est lié à une zone de disponibilité, il reste actif et opérationnel même si un centre de données spécifique fonctionne mal ou est complètement hors service, en s’appuyant sur la disponibilité des autres zones de la région. Vous n’avez pas besoin de faire quoi que ce soit pour basculer vers une autre zone, ni même de savoir qu’un incident a lieu. 


## <a name="regions"></a>Régions
Consultez [Régions et zones de disponibilité dans Azure](https://azure.microsoft.com/global-infrastructure/geographies/#geographies) pour les régions Azure qui ont des zones de disponibilité. Actuellement, Azure Monitor prend en charge les régions suivantes. 

- USA Est 2
- USA Ouest 2

## <a name="dedicated-clusters"></a>Clusters dédiés
La prise en charge d’Azure Monitor pour les zones de disponibilité nécessite un espace de travail Log Analytics lié à un [cluster dédié Azure Monitor](logs-dedicated-clusters.md). Les clusters dédiés sont une option de déploiement qui permet de bénéficier de fonctionnalités avancées pour les journaux Azure Monitor, y compris des zones de disponibilité.

Les clusters dédiés ne peuvent pas tous utiliser les zones de disponibilité. Les clusters dédiés créés après la mi-octobre 2020 peuvent être configurés pour prendre en charge les zones de disponibilité lors de leur création. Les nouveaux clusters créés après cette date sont activés par défaut pour les zones de disponibilité dans les régions où Azure Monitor les prend en charge.


> [!NOTE]
> Les ressources Application Insights peuvent utiliser des zones de disponibilité uniquement si elles sont basées sur un espace de travail et que ce dernier utilise un cluster dédié. Les ressources Application Insights classiques ne peuvent pas utiliser les zones de disponibilité.


## <a name="determine-current-cluster-for-your-workspace"></a>Déterminer le cluster actuel pour votre espace de travail
Pour déterminer l’état actuel de lien d’espace de travail pour votre espace de travail, utilisez l’[interface CLI, PowerShell ou REST](logs-dedicated-clusters.md#check-workspace-link-status) pour récupérer les [détails du cluster](logs-dedicated-clusters.md#check-cluster-provisioning-status). Si le cluster utilise une zone de disponibilité, il aura une propriété appelée `isAvailabilityZonesEnabled` avec la valeur `true`. Une fois qu’un cluster est créé, cette propriété ne peut pas être modifiée.

## <a name="create-dedicated-cluster-with-availability-zone"></a>Créer un cluster dédié avec une zone de disponibilité
Déplacez votre espace de travail vers une zone de disponibilité en [créant un nouveau cluster dédié](logs-dedicated-clusters.md#create-a-dedicated-cluster) dans une région qui prend en charge les zones de disponibilité. Le cluster est automatiquement activé pour les zones de disponibilité. Ensuite, [liez votre espace de travail au nouveau cluster](logs-dedicated-clusters.md#link-a-workspace-to-a-cluster).

> [!IMPORTANT]
> La zone de disponibilité est définie sur le cluster au moment de sa création et ne peut pas être modifiée.

La transition vers un nouveau cluster peut être un processus graduel. Ne supprimez pas le cluster précédent tant que des données n’ont pas été vidées. Par exemple, si la conservation de votre espace de travail est définie sur 60 jours, vous souhaiterez peut-être garder votre ancien cluster en cours d’exécution pendant cette période avant de le supprimer.

Toutes les requêtes portant sur votre espace de travail interrogent les deux clusters, comme il se doit pour vous fournir un seul jeu de résultats unifié. Cela signifie que toutes les fonctionnalités Azure Monitor qui reposent sur l’espace de travail, telles que les classeurs et les tableaux de bord, continueront à obtenir le jeu de résultats unifié et complet, basé sur les données des deux clusters.

## <a name="billing"></a>Facturation
Il existe un [coût d’utilisation pour un cluster dédié](logs-dedicated-clusters.md#create-a-dedicated-cluster). Il nécessite une réservation de capacité quotidienne de 1 000 Go. Cette capacité sera réduite le 4 août 2021 à 500 Go. 

Si vous disposez déjà d’un cluster dédié et que vous choisissez de le conserver pour accéder à ses données, les deux clusters dédiés vous seront facturés. À partir du 4 août 2021, la réservation de capacité minimale requise pour les clusters dédiés sera réduite à 500 Go/jour et nous vous recommandons d’appliquer ce minimum à votre ancien cluster pour réduire les frais.

Le nouveau cluster n’est pas facturé au cours de sa première journée pour éviter une double facturation lors de la configuration. Seules les données ingérées avant la fin de la migration sont encore facturées le jour de la migration. 


## <a name="next-steps"></a>Étapes suivantes

- Consultez [Utilisation de requêtes dans Azure Monitor Log Analytics](queries.md) pour voir comment les utilisateurs interagissent avec les packs de requêtes dans Log Analytics.

