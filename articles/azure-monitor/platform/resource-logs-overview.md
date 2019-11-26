---
title: Vue d’ensemble des journaux de ressource Azure | Microsoft Docs
description: Découvrez les services pris en charge et le schéma d’événement pour les journaux de ressource Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/22/2019
ms.openlocfilehash: b953f9b5e5fd8c853746caad3047986786bd1317
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989619"
---
# <a name="azure-resource-logs-overview"></a>Vue d’ensemble des journaux de ressource Azure
Les journaux de ressource Azure sont des [journaux de plateforme](platform-logs-overview.md) émis par des ressources Azure qui décrivent leur fonctionnement interne. Tous les journaux de ressource partagent un schéma commun de niveau supérieur, avec la flexibilité pour chaque service d’émettre des propriétés uniques pour ses propres événements.

> [!NOTE]
> Les journaux de ressource étaient auparavant appelés journaux de diagnostic.

## <a name="collecting-resource-logs"></a>Collecte des journaux de ressource
Les journaux de ressource sont générés automatiquement par les ressources Azure prises en charge, mais ils ne sont collectés que si vous les configurez à l’aide d’un [paramètre de diagnostic](diagnostic-settings.md). Créez un paramètre de diagnostic pour chaque ressource Azure pour transférer les journaux aux destinations suivantes :

| Destination | Scénario |
|:---|:---|:---|
| [Espace de travail Log Analytics](resource-logs-collect-workspace.md) | Analysez les journaux avec d’autres données de surveillance et tirez parti des fonctionnalités d’Azure Monitor telles que les requêtes de journal et les alertes de journal. |
| [Azure Storage](resource-logs-collect-storage.md) | Archivez les journaux pour audit ou sauvegarde. |
| [Hub d’événements](resource-logs-stream-event-hubs.md) | Diffusez les journaux en continu sur des systèmes de journalisation et de télémétrie tiers.  |

## <a name="compute-resources"></a>Ressources de calcul
Les journaux de ressource sont différents des journaux au niveau du système d’exploitation invité dans les ressources de calcul Azure. Les ressources de calcul nécessitent qu’un agent collecte les journaux et les métriques à partir de leur système d’exploitation invité, notamment les données telles que les journaux d’événements, les journaux syslog et les compteurs de performances. Utilisez l’[Extension Diagnostic](agents-overview.md#azure-diagnostic-extension) pour router les données de journaux des machines virtuelles Azure et pour que l’[agent Log Analytics](agents-overview.md#log-analytics-agent) collecte les journaux et métriques des machines virtuelles dans Azure, dans d’autres clouds et localement dans un espace de travail Log Analytics. Pour plus de détails, consultez [Sources des données de surveillance pour Azure Monitor](data-sources.md).

## <a name="resource-logs-schema"></a>Schéma des journaux de ressource
Pour plus d’informations sur le schéma et les catégories des journaux de ressources, voir [Schéma des journaux de ressources](diagnostic-logs-schema.md). 

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez les autres journaux de plateforme Azure](platform-logs-overview.md) que vous pouvez utiliser pour surveiller Azure.
