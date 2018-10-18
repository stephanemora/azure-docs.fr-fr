---
title: Planifications de maintenance Azure (préversion) | Microsoft Docs
description: Grâce aux planifications de maintenance, les clients peuvent planifier les événements de maintenance prévus nécessaires que le service Azure SQL Data Warehouse utilise pour déployer de nouveaux correctifs, mises à niveau et fonctionnalités.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/06/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 6fbf914c8035344d33e8d2739fb9d92d5757c3d1
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228092"
---
# <a name="viewing-a-maintenance-schedule"></a>Afficher une planification de maintenance 

## <a name="portal"></a>Portail

Par défaut, pour toutes les nouvelles instances Azure SQL Data Warehouse, deux fenêtres de maintenance (principale et secondaire) de huit heures sont appliquées durant le déploiement. Cette durée peut être changée aussitôt après le déploiement. Aucune maintenance ne peut avoir lieu en dehors des fenêtres de maintenance définies sans notification préalable.
Effectuez les étapes suivantes pour afficher la planification de maintenance qui a été appliquée à votre entrepôt de données dans le portail.

Effectuez les étapes suivantes pour afficher la planification de maintenance qui a été appliquée à votre entrepôt de données dans le portail.
1.  Connectez-vous au [Portail Azure](https://portal.azure.com/).
2.  Sélectionnez l’entrepôt de données à afficher. 
3.  L’entrepôt Azure SQL Data Warehouse sélectionné s’ouvre dans le panneau Vue d’ensemble. La planification de maintenance appliquée à l’entrepôt de données sélectionné est affichée sous « Planification de la maintenance (préversion) ».

![Panneau Vue d’ensemble](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sur la création, l’affichage et la gestion des alertes à l’aide d’Azure Monitor.
[En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sur les actions webhook pour les règles d’alerte de journal.
[En savoir plus](https://docs.microsoft.com/azure/service-health/service-health-overview) sur Azure Service Health.


