---
title: Planifications de maintenance Azure (préversion) | Microsoft Docs
description: Avec les planifications de maintenance, les clients peuvent planifier les événements de maintenance prévus nécessaires que le service Azure SQL Data Warehouse utilise pour déployer de nouveaux correctifs, mises à niveau et fonctionnalités.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 09/20/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 85e8327d1cac17059b2e91b1ea21becc23002c8e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228119"
---
# <a name="using-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Utilisation des planifications de maintenance pour gérer les mises à jour et la maintenance des services

La planification de la maintenance dans Azure SQL Data Warehouse est désormais disponible en préversion. Cette nouvelle fonctionnalité intègre de manière transparente les notifications de maintenance planifiée de Service Health, le moniteur des vérifications de Resource Health et le service de planification de la maintenance d’Azure SQL Data Warehouse.

La planification de la maintenance vous permet de recevoir les nouvelles fonctionnalités, les mises à niveau et les correctifs durant la fenêtre de temps qui vous convient. Les clients sélectionnent une fenêtre de maintenance principale et secondaire comprises dans une période de 7 jours. Par exemple, samedi 22 h – dimanche 01 h (fenêtre principale) et mercredi 19 h – 22 h (fenêtre secondaire). Si nous ne pouvons pas effectuer la maintenance dans la fenêtre principale, nous réessaierons dans la fenêtre secondaire.

Pendant le déploiement, une planification de maintenance définie par le système est appliquée à toutes les instances Azure SQL Data Warehouse récemment créées. La planification peut être modifiée une fois le déploiement terminé.

Chaque fenêtre de maintenance peut durer entre 3 et 8 heures. 3 heures est la durée la plus courte qu’il vous est possible de choisir. La maintenance peut se produire à tout moment dans la fenêtre sélectionnée. En outre, vous devez vous attendre à une brève perte de connectivité pendant que le service déploie du nouveau code dans votre entrepôt de données. 

Dans la préversion de cette fonctionnalité, nous demandons aux clients de sélectionner leurs fenêtres principale et secondaire dans des plages de jours différentes.   
Toutes les opérations de maintenance doivent être effectuées dans les fenêtres de maintenance planifiées. Aucune maintenance n’aura lieu en dehors des fenêtres spécifiées sans notification préalable. Si votre entrepôt de données est suspendu pendant une maintenance planifiée, il sera mis à jour pendant l’opération de reprise.  


## <a name="alerts-and-monitoring"></a>Alertes et supervision

L’intégration transparente des notifications Service Health et du moniteur de vérifications Resource Health permet aux clients de rester informés des activités de maintenance imminentes. En tirant parti d’Azure Monitor, la nouvelle automatisation permet aux clients de choisir comment être informés des événements de maintenance imminents, et quels flux automatisés doivent être déclenchés pour gérer les temps d’arrêt et réduire l’impact sur les opérations.

Tous les événements de maintenance sont précédés d’un préavis de 24 heures. Pour réduire les temps d’arrêt des instances, vérifiez qu’aucune transaction de longue durée n’est en cours dans votre entrepôt de données avant le début de la période de maintenance choisie. Quand la maintenance démarre, toutes les sessions actives sont annulées, les transactions non validées sont restaurées et votre entrepôt de données connaît une brève perte de connectivité. Vous êtes averti dès que la maintenance est terminée dans votre entrepôt de données. 

Si vous avez reçu une notification pour vous informer d’une maintenance à venir, et si nous n’avons pas pu effectuer cette maintenance pendant la période prévue, vous recevrez une notification d’annulation. La maintenance reprendra lors de la prochaine période de maintenance planifiée.
 
Tous les événements de maintenance actifs sont affichés dans la section « Service Health - Maintenance planifiée ». Un enregistrement complet des événements passés est conservé dans l’historique Service Health. La maintenance peut être supervisée via le tableau de bord du portail des vérifications Azure Service Health pendant un événement actif.

### <a name="maintenance-schedule-availability"></a>Disponibilité de la planification de maintenance

Même si l’option de planification de la maintenance n’est pas encore disponible dans votre région, vous avez la possibilité d’afficher et de modifier à tout moment la planification de la maintenance. Quand l’option de planification de la maintenance sera disponible dans votre région, la planification identifiée deviendra immédiatement active dans votre entrepôt de données.


## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus](viewing-maintenance-schedule.md) sur l’affichage des planifications de maintenance 
- [En savoir plus](changing-maintenance-schedule.md) sur la modification des planifications de maintenance
- [En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sur la création, l’affichage et la gestion des alertes à l’aide d’Azure Monitor
- [En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sur les actions webhook pour les règles d’alerte de journal
- [En savoir plus](https://docs.microsoft.com/azure/service-health/service-health-overview) sur Azure Service Health







