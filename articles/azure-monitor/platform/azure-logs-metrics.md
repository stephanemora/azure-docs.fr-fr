---
title: Journaux et métriques dans Azure | Microsoft Docs
description: Vue d’ensemble des journaux de diagnostic dans Azure qui fournissent des données riches et fréquentes sur le fonctionnement d’une ressource Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 3fe220506e074f881a16c1805d25fb4b39927488
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262976"
---
# <a name="logs-and-metrics-in-azure"></a>Journaux et métriques dans Azure
Il existe différents [journaux](data-platform-logs.md) et [métriques](data-platform-metrics.md)

Les applications et services d’analyse dans Azure peuvent être séparés et stockées dans la [plateforme de données Azure](data-platform.md). 

Les journaux et métriques peuvent être répartis en deux catégories

- Journaux et métriques de la plateforme qui sont générés automatiquement sans aucune configuration requise autre que 



| Couche | Journaux de plateforme | Métriques de plateforme | Journaux d’activité personnalisés | Métriques personnalisées |
|:---|:---|:---|:---|:---|
| Application  | | | | |
| SE invité     | Heartbeat |  | Extension Diagnostics<br>Agent Log Analytics | Extension Diagnostics |
| Ressource     | [Journaux de ressources](resource-logs-overview.md)<br>(spécifiques à chaque service) | [Métriques de ressources](metrics-supported.md)<br>(spécifiques à chaque service) | | [Métriques personnalisées](metrics-custom-overview.md) |
| Subscription | [Journal d’activité](activity-logs-overview.md) | | | |
| Locataire       | 

## <a name="next-steps"></a>Étapes suivantes

* [Diffuser en continu les journaux de diagnostic des ressources vers **Event Hubs**](resource-logs-stream-event-hubs.md)
* [Modifier les paramètres de diagnostic des ressources via l’API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/)
* [Analyser les journaux d’activité du stockage Azure avec Azure Monitor](collect-azure-metrics-logs.md)
