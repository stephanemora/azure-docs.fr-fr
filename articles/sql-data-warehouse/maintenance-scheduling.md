---
title: Planifications de maintenance Azure (préversion) | Microsoft Docs
description: La planification de maintenance permet aux clients de planifier les événements de maintenance planifiée nécessaires au service Azure SQL Data Warehouse pour déployer de nouveaux correctifs, mises à niveau et fonctionnalités.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 11/27/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: d626fd9b083b9ca2c55c286a1dd806620a639434
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52498030"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Utiliser les planifications de maintenance pour gérer les mises à jour et la maintenance des services

Les planifications de maintenance sont maintenant disponibles dans toutes les régions Azure SQL Data Warehouse. Cette fonctionnalité intègre les notifications de maintenance planifiée de Service Health, le moniteur des vérifications d’Azure Resource Health et le service de planification de la maintenance d’Azure SQL Data Warehouse.

Vous utilisez la planification de maintenance afin de choisir la bonne fenêtre pour recevoir les nouvelles fonctionnalités, les mises à niveau et les correctifs. Vous choisissez une fenêtre de maintenance principale et une fenêtre de maintenance secondaire dans un délai de sept jours. Par exemple, une fenêtre principale le samedi 22:00 à dimanche 01:00 et une fenêtre secondaire le mercredi de 19:00 à 22:00. Si Azure SQL Data Warehouse ne peut pas effectuer la maintenance pendant la fenêtre principale, il retente l’opération pendant la fenêtre secondaire.

Pendant le déploiement, une planification de maintenance définie par le système est appliquée à toutes les instances Azure SQL Data Warehouse récemment créées. La planification peut être modifiée une fois le déploiement terminé.

Chaque fenêtre de maintenance peut durer de trois à huit heures. La maintenance peut intervenir à tout moment pendant la fenêtre. Attendez-vous à une brève perte de connectivité lorsque le service déploie du nouveau code dans votre entrepôt de données. 

Pour pouvoir utiliser cette fonctionnalité, vous devrez identifier une fenêtre principale et une fenêtre secondaire sur des plages temporelles distinctes. Toutes les opérations de maintenance doivent se terminer dans les fenêtres de maintenance planifiée. Aucune maintenance ne peut avoir lieu en dehors des fenêtres de maintenance définies, sans notification préalable. Si votre entrepôt de données est suspendu pendant une maintenance planifiée, il sera mis à jour pendant l’opération de reprise.  


## <a name="alerts-and-monitoring"></a>Alertes et supervision

L’intégration des notifications Azure Service Health et du moniteur de vérifications Azure Resource Health permet aux clients d’être informés des activités de maintenance imminentes. Cette nouvelle automatisation tire parti d’Azure Monitor. Vous décidez comment vous souhaitez être informé des événements de maintenance imminente. Vous décidez également des flux automatisés qui peuvent vous aider à gérer les temps d’arrêt et à minimiser l’impact sur vos opérations.

Un préavis est envoyé 24 heures avant chaque événement de maintenance. Pour réduire le temps d’arrêt des instances, vérifiez qu’aucune transaction de longue durée n’est en cours dans votre entrepôt de données avant le début de la période de maintenance choisie. Au début de la maintenance, toutes les sessions actives sont annulées. Les transactions non validées sont restaurées et votre entrepôt de données connaît une brève perte de connectivité. Vous êtes averti dès que la maintenance est terminée dans votre entrepôt de données. 

Si vous avez reçu une notification pour une prochaine maintenance mais que Microsoft Azure SQL Data Warehouse n’a pas pu effectuer cette maintenance pendant la période prévue, vous recevrez une notification d’annulation. La maintenance reprendra lors de la prochaine période de maintenance planifiée.
 
Tous les événements de maintenance actifs sont affichés dans la section **Service Health - Maintenance planifiée**. L’historique d’Azure Service Health contient un enregistrement complet des événements passés. Vous pouvez surveiller la maintenance via le tableau de bord du portail de vérification Azure Service Health pendant un événement actif.

### <a name="maintenance-schedule-availability"></a>Disponibilité de la planification de maintenance

Même si la planification de la maintenance n’est pas encore disponible dans votre région, vous pouvez l’afficher et la modifier à tout moment. Quand la planification de la maintenance sera disponible dans votre région, la planification identifiée deviendra immédiatement active dans votre entrepôt de données.


## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus](viewing-maintenance-schedule.md) sur l’affichage d’une planification de maintenance. 
- [En savoir plus](changing-maintenance-schedule.md) sur la modification d’une planification de maintenance.
- [En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sur la création, l’affichage et la gestion des alertes à l’aide d’Azure Monitor.
- [En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sur les actions webhook pour les règles d’alerte de journal.
- [En savoir plus](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups) sur la création et la gestion de groupes d’actions.
- [En savoir plus](https://docs.microsoft.com/azure/service-health/service-health-overview) sur Azure Service Health.







