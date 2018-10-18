---
title: Planifications de maintenance Azure (préversion) | Microsoft Docs
description: Avec les planifications de maintenance, les clients peuvent planifier les événements de maintenance prévus nécessaires que le service Azure SQL Data Warehouse utilise pour déployer de nouveaux correctifs, mises à niveau et fonctionnalités.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/07/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a6eedc0bac7aab69a9138f4f63d0d9d802e74dfc
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228106"
---
# <a name="change-a-maintenance-schedule"></a>Changer une planification de maintenance 

## <a name="portal"></a>Portail
Il est possible de mettre à jour ou changer une planification de maintenance à tout moment. Toutefois, si l’instance sélectionnée est actuellement dans un cycle de maintenance actif, les nouveaux paramètres sont enregistrés, mais ils seront appliqués uniquement pendant la prochaine période de maintenance définie. [Explorez](https://docs.microsoft.com/azure/service-health/resource-health-overview) la supervision de votre entrepôt de données pendant un événement de maintenance actif. 

Dans la préversion, vous devrez sélectionner deux fenêtres de maintenance dans une période de sept jours. Chaque fenêtre de maintenance peut durer entre trois et huit heures, trois heures étant la durée la plus courte qu’il vous est possible de choisir. La maintenance peut avoir lieu à tout moment dans une fenêtre de maintenance définie. En dehors des fenêtres de temps définies, elle ne peut pas avoir lieu sans notification préalable. La maintenance entraîne également une brève perte de connectivité, le temps que le service déploie le nouveau code dans votre entrepôt de données. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Définir les fenêtres principale et secondaire

Les fenêtres principale et secondaire doivent être définies dans des plages de jours distinctes (plage Mardi – Jeudi pour la fenêtre principale et plage Samedi – Dimanche pour la fenêtre secondaire)

Effectuez les étapes suivantes pour changer la planification de maintenance qui a été appliquée à votre entrepôt de données dans le portail.
1.  Connectez-vous au [Portail Azure](https://portal.azure.com/).
2.  Sélectionnez l’entrepôt de données à mettre à jour. La page s’ouvre dans le panneau Vue d’ensemble. 
3.  Pour accéder à la page des paramètres de la planification de maintenance, cliquez sur le lien récapitulatif Planification de maintenance (préversion) dans le panneau Vue d’ensemble ou utilisez l’option Planification de maintenance dans le menu Ressource à gauche.  

    ![Options du panneau Vue d’ensemble](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Vous pouvez définir la plage de jours par défaut pour votre fenêtre de maintenance principale à l’aide des cases d’option en haut de la page. Cette sélection détermine si la fenêtre principale a lieu un jour de semaine ou pendant le week-end. Les valeurs des listes déroulantes en dessous sont mises à jour en fonction de votre sélection. Dans la préversion, il est possible que certaines régions ne prennent pas en charge l’ensemble complet des options de jour disponibles. Ces valeurs seront mises à jour dans les prochains mois.

   ![Panneau Paramètres de maintenance](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Choisissez vos fenêtres de maintenance principale et secondaire par défaut en utilisant les listes déroulantes Jour, Heure de début et Fenêtre de temps sous chaque fenêtre. Le résumé de la planification en bas du panneau est mis à jour avec les valeurs sélectionnées dans les listes déroulantes.

#### <a name="dropdown-options"></a>Options des listes déroulantes
- Jour : jour par défaut où doit avoir lieu la maintenance pendant la fenêtre sélectionnée.
- Heure de début : heure par défaut à laquelle doit commencer la fenêtre de maintenance.
- Fenêtre de temps : durée par défaut de la fenêtre de temps.

  Après avoir sélectionné vos fenêtres de maintenance par défaut, cliquez sur Enregistrer. Un message de confirmation s’affiche indiquant que votre nouvelle planification n’est pas active. Si vous enregistrez une planification dans une région qui ne prend pas encore en charge la planification de maintenance, le message suivant s’affiche. Vos paramètres sont enregistrés et seront appliqués dès que la fonctionnalité sera disponible dans la région que vous avez sélectionnée.    

    ![Inactif dans le toast région](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sur les actions webhook pour les règles d’alerte de journal
- [En savoir plus](https://docs.microsoft.com/azure/service-health/service-health-overview) sur Azure Service Health


