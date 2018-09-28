---
title: Gérer les instances d’alerte
description: Gestion des instances d’alerte dans Azure
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 614e65ca791559f4649963ee82940c3f96beddb3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948449"
---
# <a name="manage-alert-instances"></a>Gérer les instances d’alerte
Avec l’[expérience des alertes unifiées](https://aka.ms/azure-alerts-overview) dans Azure Monitor, vous pouvez maintenant voir vos différents types d’alertes dans Azure, sur plusieurs abonnements, dans un volet unique. Cet article vous explique comment afficher vos instances d’alerte et comment aller plus loin sur le portail pour rechercher des instances d’alerte spécifiques à dépanner.

1. Il existe trois façons d’accéder à la page des alertes

   + Dans le [portail](https://portal.azure.com/), sélectionnez **Surveiller** et choisissez **Alertes** dans la section Surveiller.  
    ![Surveillance](./media/monitoring-alerts-managing-alerts-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + Vous pouvez accéder aux alertes à partir d’une **ressource** spécifique. Une fois qu’une ressource est ouverte, parcourez sa table des matières pour accéder à la section Surveiller, puis choisissez **Alertes**, la page d’accueil étant préfiltrée sur les alertes correspondant à cette ressource en particulier.
   
    ![Surveillance](./media/monitoring-alerts-managing-alerts-instances/alert-resource.JPG)
    
   + Vous pouvez accéder aux alertes à partir d’un **groupe de ressources** spécifique. Une fois qu’un groupe de ressources est ouvert, parcourez sa table des matières pour accéder à la section Surveiller, puis choisissez **Alertes**, la page d’accueil étant préfiltrée sur les alertes correspondant à ce groupe de ressources en particulier.    
   
    ![Surveillance](./media/monitoring-alerts-managing-alerts-instances/alert-rg.JPG)

1.  Vous accéderez à la page **Résumé des alertes**, qui vous donne une vue d’ensemble de toutes vos instances d’alerte dans Azure. Vous pouvez modifier la vue Résumé en sélectionnant **plusieurs abonnements** (au maximum 5) ou en filtrant sur des **groupes de ressources**, des **ressources** spécifiques ou des **intervalles de temps**. Cliquez sur Nombre total d’alertes ou sur l’une des bandes de gravité pour accéder à la liste de vos alertes.     
    ![Résumé des alertes](./media/monitoring-alerts-managing-alerts-instances/alerts-summary.jpg)
 
1.  Vous accédez à la page **Toutes les alertes**, où toutes les instances d’alerte dans Azure sont répertoriées. Si vous accédez au portail à partir d’une notification d’alerte, vous pouvez utiliser les filtres disponibles pour vous concentrer sur cette instance d’alerte spécifique. (**Remarque** : si vous avez accédé à la page en cliquant sur une des bandes de gravité, la liste sera préfiltrée sur ce niveau de gravité lorsque vous y accéderez.) Outre les filtres disponibles dans la page précédente, vous pouvez désormais également filtrer sur la base du service de surveillance (par exemple, la plateforme pour les mesures), de la condition de surveillance (déclenchée ou résolue), de la gravité, de l’état de l’alerte (nouvelle/reconnue/fermée) ou de l’ID de groupe intelligent.

    ![Toutes les alertes](./media/monitoring-alerts-managing-alerts-instances/all-alerts.jpg)

    > [!NOTE]
    >  Si vous avez accédé à la page en cliquant sur une des bandes de gravité, la liste sera préfiltrée sur ce niveau de gravité lorsque vous y accéderez.
 
1.  Si vous cliquez sur n’importe quelle instance d’alerte, la page **Détails de l’alerte** s’ouvre, vous permettant de rechercher des informations approfondies sur cette instance d’alerte spécifique.   
![Détails de l’alerte](./media/monitoring-alerts-managing-alerts-instances/alert-details.jpg)  
