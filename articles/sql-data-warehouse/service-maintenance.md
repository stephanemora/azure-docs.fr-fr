---
title: Maintenance planifiée - Azure SQL Data Warehouse | Microsoft Docs
description: Apprenez à anticiper les événements de maintenance planifiée pour votre SQL Data Warehouse.
services: sql-data-warehouse
author: antvgski
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/19/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a273ad4c256267865d3af324f0ef755a6cb75c5c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
ms.locfileid: "31799890"
---
# <a name="planning-for-maintenance-on-your-azure-sql-data-warehouse"></a>Planification de la maintenance de votre entrepôt Azure SQL Data Warehouse

Apprenez à anticiper les événements de maintenance planifiée sur votre entrepôt Azure SQL Data Warehouse.

## <a name="what-is-a-planned-maintenance-event"></a>Qu’est-ce qu’un événement de maintenance planifiée ?
Un événement de maintenance planifiée correspond au moment où votre entrepôt de données doit être mis hors connexion pour une opération de maintenance. Ces événements permettent d’appliquer des mises à niveau de service, des nouvelles fonctionnalités ou des correctifs. 

## <a name="frequency"></a>Fréquence
En moyenne, au moins un événement de maintenance planifiée se produit par mois. 

## <a name="notifications-and-downtime"></a>Notification et temps d’arrêt
Vous recevez une notification avant chaque événement de maintenance planifiée. L’événement de maintenance annule toutes les requêtes en cours et met votre entrepôt de données hors connexion. Le temps d’arrêt prévu pour chaque entrepôt de données est d’environ 30 minutes. Normalement, vous recevez une notification lorsque la maintenance est terminée. 

## <a name="setting-up-alerts"></a>Configuration d’alertes

Nous vous recommandons d’utiliser [Azure Monitor](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) pour configurer les alertes dans le journal de maintenance planifiée. Ces alertes peuvent vous aider à planifier la maintenance requise afin de minimiser l’impact sur votre entreprise. 

Pour configurer les notifications, utilisez ces [instructions sur les alertes de journal](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la surveillance, consultez [Surveiller votre charge de travail à l’aide de vues de gestion dynamique](sql-data-warehouse-manage-monitor.md).
