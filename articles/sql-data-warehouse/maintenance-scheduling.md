---
title: Calendriers de maintenance Azure
description: La planification de maintenance permet aux clients de planifier les événements de maintenance planifiée nécessaires au service Azure SQL Data Warehouse pour déployer de nouveaux correctifs, mises à niveau et fonctionnalités.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 07/16/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 91b202f8a5df841fa3d6aa1f0903999b395f8137
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686073"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Utiliser les planifications de maintenance pour gérer les mises à jour et la maintenance des services

Les planifications de maintenance sont maintenant disponibles dans toutes les régions Azure SQL Data Warehouse. La fonctionnalité de planification de maintenance intègre les notifications de maintenance planifiée de Service Health, le moniteur des vérifications d’Azure Resource Health et le service de planification de la maintenance d’Azure SQL Data Warehouse.

Vous utilisez la planification de maintenance afin de choisir la bonne fenêtre pour recevoir les nouvelles fonctionnalités, les mises à niveau et les correctifs. Vous choisissez une fenêtre de maintenance principale et une fenêtre de maintenance secondaire dans un délai de sept jours. Pour pouvoir utiliser cette fonctionnalité, vous devrez identifier une fenêtre principale et une fenêtre secondaire sur des plages temporelles distinctes.

Par exemple, vous pouvez planifier une fenêtre principale du samedi 22:00 au dimanche 01:00, et une fenêtre secondaire le mercredi de 19:00 à 22:00. Si Azure SQL Data Warehouse ne peut pas effectuer la maintenance pendant la fenêtre principale, il retente l’opération pendant la fenêtre secondaire. La maintenance du service peut se produire à la fois pendant les fenêtres principale et secondaire. Pour garantir une complétion rapide de toutes les opérations de maintenance, les niveaux d’entrepôt de données DW400(c) et inférieurs peuvent effectuer la maintenance en dehors d’une fenêtre de maintenance désignée.

Pendant le déploiement, une planification de maintenance définie par le système est appliquée à toutes les instances Azure SQL Data Warehouse récemment créées. La planification peut être modifiée une fois le déploiement terminé.

Chaque fenêtre de maintenance peut durer de trois à huit heures. La maintenance peut intervenir à tout moment pendant la fenêtre. Quand la maintenance démarre, toutes les sessions actives sont annulées et les transactions non validées sont restaurées. Attendez-vous à plusieurs brèves pertes de connectivité lorsque le service déploie du nouveau code dans votre entrepôt de données. Vous êtes averti une fois la maintenance de votre entrepôt de données terminée.

 Toutes les opérations de maintenance doivent se terminer dans les fenêtres de maintenance planifiée. Aucune maintenance ne peut avoir lieu en dehors des fenêtres de maintenance définies, sans notification préalable. Si votre entrepôt de données est suspendu pendant une maintenance planifiée, il sera mis à jour pendant l’opération de reprise. 

## <a name="alerts-and-monitoring"></a>Alertes et supervision

L’intégration des notifications Azure Service Health et du moniteur de vérifications Azure Resource Health permet aux clients d’être informés des activités de maintenance imminentes. Cette nouvelle automatisation tire parti d’Azure Monitor. Vous décidez comment vous souhaitez être informé des événements de maintenance imminente. Vous pouvez également choisir les flux automatisés qui vous aideront à gérer les temps d’arrêt et à minimiser l’impact sur les opérations.

Une notification 24 heures à l’avance doit précéder tous les événements de maintenance qui ne concernent pas les niveaux DWC400c et inférieurs. Pour réduire le temps d’arrêt des instances, vérifiez qu’aucune transaction de longue durée n’est en cours dans votre entrepôt de données avant le début de la période de maintenance choisie.

> [!NOTE]
> Dans le cas où nous sommes amenés à déployer une mise à jour critique dans le temps, les temps de notification avancés peuvent être considérablement réduits.

Si vous avez reçu une notification pour une prochaine maintenance mais que Microsoft Azure SQL Data Warehouse n’a pas pu effectuer cette maintenance pendant la période prévue, vous recevrez une notification d’annulation. La maintenance reprendra lors de la prochaine période de maintenance planifiée.

Tous les événements de maintenance actifs sont affichés dans la section **Service Health - Maintenance planifiée**. L’historique d’Azure Service Health contient un enregistrement complet des événements passés. Vous pouvez surveiller la maintenance via le tableau de bord du portail de vérification Azure Service Health pendant un événement actif.

### <a name="maintenance-schedule-availability"></a>Disponibilité de la planification de maintenance

Même si la planification de la maintenance n’est pas encore disponible dans votre région, vous pouvez l’afficher et la modifier à tout moment. Quand la planification de la maintenance sera disponible dans votre région, la planification identifiée deviendra immédiatement active dans votre entrepôt de données.

## <a name="view-a-maintenance-schedule"></a>Afficher une planification de maintenance 

### <a name="portal"></a>Portail

Par défaut, toutes les nouvelles instances Azure SQL Data Warehouse ont deux fenêtres de maintenance (une principale et une secondaire) de huit heures, appliquées durant le déploiement. Comme indiqué ci-dessus, vous pouvez modifier les fenêtres dès que le déploiement est terminé. Aucune maintenance ne peut avoir lieu en dehors des fenêtres de maintenance définies, sans notification préalable.

Pour afficher la planification de maintenance qui a été appliquée à votre entrepôt de données, procédez comme suit :

1.  Connectez-vous au [Portail Azure](https://portal.azure.com/).
2.  Sélectionnez l’entrepôt de données à afficher. 
3.  L’entrepôt de données sélectionné s’ouvre dans le panneau de vue d’ensemble. La planification de maintenance appliquée à l’entrepôt de données s’affiche sous **Planification de la maintenance**.

![Panneau Vue d’ensemble](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Changer une planification de maintenance 

### <a name="portal"></a>Portail
Il est possible de mettre à jour ou de modifier une planification de maintenance à tout moment. Si l’instance sélectionnée se trouve dans un cycle de maintenance actif, les paramètres sont enregistrés. Ils deviennent actifs au cours de la prochaine période de maintenance identifiée. [Explorez](https://docs.microsoft.com/azure/service-health/resource-health-overview) la supervision de votre entrepôt de données pendant un événement de maintenance actif. 

### <a name="identifying-the-primary-and-secondary-windows"></a>Identification des fenêtres principale et secondaire

Les fenêtres principale et secondaire doivent avoir des plages de jour distinctes. Par exemple, une fenêtre principale allant du mardi au jeudi, et une fenêtre secondaire allant du samedi au dimanche.

Pour modifier la planification de maintenance de votre entrepôt de données, procédez comme suit :
1.  Connectez-vous au [Portail Azure](https://portal.azure.com/).
2.  Sélectionnez l’entrepôt de données à mettre à jour. La page s’ouvre dans le panneau Vue d’ensemble. 
3.  Ouvrez la page de paramètres de planification de maintenance en sélectionnant le lien **Maintenance Schedule (preview) summary** (Résumé de la planification de maintenance (préversion)) du panneau Vue d’ensemble. Ou sélectionnez l’option **Planification de la maintenance** dans le menu de ressource à gauche.  

    ![Options du panneau Vue d’ensemble](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Identifiez la plage de jours par défaut pour votre fenêtre de maintenance principale à l’aide des options en haut de la page. Cette sélection détermine si la fenêtre principale a lieu un jour de semaine ou pendant le week-end. Votre sélection met à jour les valeurs des listes déroulantes. Dans la préversion, il est possible que certaines régions ne prennent pas en charge l’ensemble complet des options **Jour** disponibles.

   ![Panneau Paramètres de maintenance](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Choisissez vos fenêtres de maintenance principale et secondaire par défaut à l’aide des zones de liste déroulante :
   - **Jour** : Jour par défaut où doit avoir lieu la maintenance pendant la fenêtre sélectionnée.
   - **Heure de début** : Heure par défaut à laquelle doit commencer la fenêtre de maintenance.
   - **Fenêtre de temps** : Durée par défaut de la fenêtre de temps.

   La zone **Résumé de la planification** en bas du panneau est mise à jour avec les valeurs que vous avez sélectionnées. 
  
6. Sélectionnez **Enregistrer**. Un message s’affiche pour confirmer que votre nouvelle planification est maintenant active. 

   Si vous enregistrez une planification dans une région qui ne prend pas en charge la planification de maintenance, le message suivant s’affiche. Vos paramètres sont enregistrés et deviennent actifs dès que la fonctionnalité est disponible dans la région que vous avez sélectionnée.    

   ![Message concernant la disponibilité dans la région](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sur la création, l’affichage et la gestion des alertes à l’aide d’Azure Monitor.
- [En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sur les actions webhook pour les règles d’alerte de journal.
- [En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) sur la création et la gestion de groupes d’actions.
- [En savoir plus](https://docs.microsoft.com/azure/service-health/service-health-overview) sur Azure Service Health.