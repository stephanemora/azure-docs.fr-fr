---
title: Utiliser le Portail Azure pour créer des alertes classiques pour les services Azure | Microsoft Docs
description: Déclenchez des e-mails ou des notifications, ou appelez des URL de sites web (webhooks) ou déclenchez une automatisation lorsque les conditions spécifiées sont remplies.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/23/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: d0e5512eb3f963898ded6d7155f8b75cfb6ef911
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287427"
---
# <a name="use-the-azure-portal-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>Utiliser le portail Azure pour créer des alertes de métriques classiques dans Azure Monitor pour les services Azure 

> [!div class="op_single_selector"]
> * [Portail](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [INTERFACE DE LIGNE DE COMMANDE](insights-alerts-command-line-interface.md)
>

> [!NOTE]
> Cet article décrit comment créer des alertes de métriques classiques plus anciennes. Désormais, Azure Monitor prend en charge des [alertes de métriques plus récentes](monitoring-near-real-time-metric-alerts.md). 


Cet article vous montre comment configurer des alertes de métriques classiques Azure avec le portail Azure. 

Vous pouvez recevoir une alerte en fonction des métriques pour vos services Azure, ou vous pouvez recevoir des alertes pour les événements qui se produisent dans Azure.

* **Valeurs de mesure** : l’alerte se déclenche quand la valeur d’une mesure spécifiée dépasse un seuil que vous attribuez dans un des deux sens. C’est-à-dire que le déclenchement se fait à la fois quand la condition est remplie et une fois que la condition n’est plus remplie.    

* **Événements du journal d’activité** : une alerte peut se déclencher sur *chaque* événement ou lorsqu’un événement particulier se produit. Découvrez plus en détail les [alertes du journal d’activité](monitoring-activity-log-alerts.md).

Vous pouvez configurer une alerte de métrique classique pour effectuer les opérations suivantes lors de son déclenchement :

* Envoyer des notifications par e-mail à l’administrateur du service et aux coadministrateurs.
* Envoyer un e-mail à d’autres adresses e-mail que vous spécifiez.
* Appeler un webhook.
* Démarrer l’exécution d’un runbook Azure (uniquement à partir du Portail Azure).

Vous pouvez configurer et obtenir des informations sur les règles d’alerte de métrique classique à partir des emplacements suivants : 

* [Portail Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [interface de ligne de commande Azure](insights-alerts-command-line-interface.md)
* [API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Créer une règle d’alerte sur une métrique avec le Portail Azure
1. Dans le [portail](https://portal.azure.com/), localisez la ressource que vous souhaitez surveiller, puis sélectionnez-la.

2. Sélectionnez **Alertes (classiques)** dans la section **SURVEILLANCE**. Le texte et l’icône peuvent varier légèrement pour les différentes ressources. Si vous ne trouvez pas **Alertes (classiques)** à cet endroit, vous les trouverez peut-être dans **Alertes** ou **Règles d’alerte**.

    ![Surveillance](./media/insights-alerts-portal/AlertRulesButton.png)

3. Sélectionnez la commande **Ajouter une alerte de métrique (classique)** puis renseignez les champs.

    ![Ajouter une alerte](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **Nommez** votre règle d’alerte. Choisissez ensuite une **Description** qui apparait également dans les adresses électroniques de notification.

5. Sélectionnez la **métrique** que vous souhaitez surveiller. Choisissez ensuite une **Condition** et une valeur de **seuil** pour la métrique. Choisissez également la **Période** de temps pendant laquelle la règle de métrique doit être satisfaite pour que l’alerte se déclenche. Par exemple, si vous utilisez la période « Au cours des 5 dernières minutes » et que votre alerte recherche une utilisation de l’UC supérieure à 80 %, l’alerte se déclenche quand l’utilisation de l’UC dépasse 80 % depuis cinq minutes. Après le premier déclenchement, elle se déclenche à nouveau lorsque l’UC reste au-dessous de 80 % pendant cinq minutes. La mesure de la métrique de l’UC est effectuée chaque minute.

6. Sélectionnez **Propriétaires de messagerie...** si vous souhaitez que les administrateurs et les coadministrateurs reçoivent des notifications par e-mail lorsque l’alerte se déclenche.

7. Si vous souhaitez envoyer des notifications à d’autres adresses e-mail lorsque l’alerte se déclenche, ajoutez-les dans le champ **Adresse(s) de messagerie d’administrateur(s) supplémentaire(s)**. Séparez plusieurs messages électroniques par des points-virgules, dans le format suivant : *email@contoso.com; email2@contoso.com*

8. Insérez un URI valide dans le champ **Webhook** si vous souhaitez qu’il soit appelé lorsque l’alerte se déclenche.

9. Si vous utilisez Azure Automation, vous pouvez sélectionner un Runbook à exécuter lorsque l’alerte se déclenche.

10. Sélectionnez **OK** pour créer l’alerte.   

Après quelques minutes, l’alerte est active et se déclenche comme décrit précédemment.

## <a name="manage-your-alerts"></a>Gérez vos alertes
Une fois que vous avez créé une alerte, vous pouvez la sélectionner et exécuter l’une des tâches suivantes :

* Afficher un graphique indiquant le seuil de la métrique et les valeurs réelles du jour précédent.
* La modifier ou la supprimer.
* La **Désactiver** ou l’**Activer** si vous voulez arrêter temporairement ou reprendre l’envoi de notifications pour cette alerte.

## <a name="next-steps"></a>Étapes suivantes
* [Consultez une vue d’ensemble de la surveillance Azure](monitoring-overview.md), notamment les types d’informations que vous pouvez collecter et surveiller.
* En savoir plus sur les [nouvelles alertes de métriques](monitoring-near-real-time-metric-alerts.md).
* Découvrez plus en détail la [configuration des webhooks dans les alertes](insights-webhooks-alerts.md).
* Découvrez plus d’informations sur la [configuration des alertes sur les événements de journal d’activité](monitoring-activity-log-alerts.md).
* Découvrez plus en détails les [runbooks Azure Automation](../automation/automation-starting-a-runbook.md).
* Consultez une [vue d’ensemble des journaux de diagnostic](monitoring-overview-of-diagnostic-logs.md) et collecter des métriques détaillées à fréquence élevée sur votre service.
* Consultez une [vue d’ensemble de la collecte des métriques](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
