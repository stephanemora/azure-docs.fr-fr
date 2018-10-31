---
title: Planifications de maintenance Azure (préversion) | Microsoft Docs
description: La planification de maintenance permet aux clients de planifier les événements de maintenance planifiée nécessaires au service Azure SQL Data Warehouse pour déployer de nouveaux correctifs, mises à niveau et fonctionnalités.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/07/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 428b9970471c9365812639e251810c571698a574
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49425953"
---
# <a name="change-a-maintenance-schedule"></a>Changer une planification de maintenance 

## <a name="portal"></a>Portail
Il est possible de mettre à jour ou de modifier une planification de maintenance à tout moment. Si l’instance sélectionnée se trouve dans un cycle de maintenance actif, les paramètres sont enregistrés. Ils deviennent actifs au cours de la prochaine période de maintenance identifiée. [Explorez](https://docs.microsoft.com/azure/service-health/resource-health-overview) la supervision de votre entrepôt de données pendant un événement de maintenance actif. 

Même si les planifications de maintenance Azure sont en préversion, vous sélectionnez deux fenêtres de maintenance pendant une période de sept jours. Chaque fenêtre de maintenance peut durer de trois à huit heures. L’opération de maintenance peut se produire à tout moment dans une fenêtre de maintenance, mais n’a pas lieu en dehors des fenêtres de temps sans notification préalable. Une brève perte de connectivité a lieu lorsque le service déploie du nouveau code dans votre entrepôt de données. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Identification des fenêtres principale et secondaire

Les fenêtres principale et secondaire doivent avoir des plages de jour distinctes. Par exemple, une fenêtre principale allant du mardi au jeudi, et une fenêtre secondaire allant du samedi au dimanche.

Pour modifier la planification de maintenance de votre entrepôt de données, procédez comme suit :
1.  Connectez-vous au [Portail Azure](https://portal.azure.com/).
2.  Sélectionnez l’entrepôt de données à mettre à jour. La page s’ouvre dans le panneau Vue d’ensemble. 
3.  Ouvrez la page de paramètres de planification de maintenance en sélectionnant le lien **Maintenance Schedule (preview) summary** (Résumé de la planification de maintenance (préversion)) du panneau Vue d’ensemble. Ou sélectionnez l’option **Planification de la maintenance** dans le menu de ressource à gauche.  

    ![Options du panneau Vue d’ensemble](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Identifiez la plage de jours par défaut pour votre fenêtre de maintenance principale à l’aide des options en haut de la page. Cette sélection détermine si la fenêtre principale a lieu un jour de semaine ou pendant le week-end. Votre sélection met à jour les valeurs des listes déroulantes. Dans la préversion, il est possible que certaines régions ne prennent pas en charge l’ensemble complet des options **Jour** disponibles.

   ![Panneau Paramètres de maintenance](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Choisissez vos fenêtres de maintenance principale et secondaire par défaut à l’aide des zones de liste déroulante :
   - **Jour** : jour par défaut où doit avoir lieu la maintenance pendant la fenêtre sélectionnée.
   - **Heure de début** : heure par défaut à laquelle doit commencer la fenêtre de maintenance.
   - **Fenêtre de temps** : durée par défaut de la fenêtre de temps.

   La zone **Résumé de la planification** en bas du panneau est mise à jour avec les valeurs que vous avez sélectionnées. 
  
6. Sélectionnez **Enregistrer**. Un message s’affiche pour confirmer que votre nouvelle planification est maintenant active. 

   Si vous enregistrez une planification dans une région qui ne prend pas en charge la planification de maintenance, le message suivant s’affiche. Vos paramètres sont enregistrés et deviennent actifs dès que la fonctionnalité est disponible dans la région que vous avez sélectionnée.    

   ![Message concernant la disponibilité dans la région](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sur les actions webhook pour les règles d’alerte de journal.
- [En savoir plus](https://docs.microsoft.com/azure/service-health/service-health-overview) sur Azure Service Health.


