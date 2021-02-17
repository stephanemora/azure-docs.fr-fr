---
title: Gérer les notifications d’alerte d’événement d’appareil pour vos ressources Azure Stack Edge Pro | Microsoft Docs
description: Décrit comment utiliser le portail Azure pour gérer les alertes des événements d’appareil sur vos ressources Azure Stack Edge Pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 0ab7cdfb3d699a8415739565aae5d4326002bc43
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389142"
---
# <a name="manage-device-event-alert-notifications-on-azure-stack-edge-pro-resources"></a>Gérer les notifications d’alerte d’événement d’appareil sur les ressources Azure Stack Edge Pro

Cet article explique comment créer des règles d’action dans le portail Azure afin de déclencher ou supprimer des notifications d’alerte pour les événements d’appareil qui se produisent au sein d’un groupe de ressources, d’un abonnement Azure ou d’une ressource Azure Stack Edge individuelle. Cet article s’applique à tous les modèles d’Azure Stack Edge.  

## <a name="about-action-rules"></a>À propos des règles d’action

Une règle d’action peut déclencher ou supprimer des notifications d’alerte. La règle d’action est ajoutée à un *groupe d’actions* : ensemble de préférences de notification qui est utilisé pour informer les utilisateurs qui doivent agir sur les alertes déclenchées dans différents contextes pour une ressource ou un ensemble de ressources.

Pour plus d’informations sur les règles d’action, consultez [Configuration d’une règle d’action](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#configuring-an-action-rule). Pour plus d’informations sur les groupes d’actions, consultez [Créer et gérer des groupes d’actions dans le Portail Azure](/azure/azure-monitor/platform/action-groups).

> [!NOTE]
> La fonctionnalité des règles d’action est en préversion. Certains écrans et certaines étapes peuvent changer à mesure que le processus est affiné.


## <a name="create-an-action-rule"></a>Créer une règle d’action

Procédez comme suit dans le portail Azure afin de créer une règle d’action pour votre appareil Azure Stack Edge.

> [!NOTE]
> Ces étapes permettent de créer une règle d’action qui envoie des notifications à un groupe d’actions. Pour plus d’informations sur la création d’une règle d’action permettant de supprimer des notifications, consultez [Configuration d’une règle d’action](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#configuring-an-action-rule).

1. Accédez à l’appareil Azure Stack Edge dans le portail Azure, puis accédez à **Supervision > Alertes**. Sélectionnez **Gérer les actions**.

   ![Supervision des alertes, vue Gérer les actions](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-01.png)

2. Sélectionnez **Règles d’action (préversion)** , puis sélectionnez **+ Nouvelle règle d’action**.

   ![Gérer les actions, option Règles d’action](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-02.png)

3. Dans l’écran **Créer une règle d’action**, utilisez l’option **Étendue** pour sélectionner un abonnement Azure, un groupe de ressources ou une ressource cible. La règle d’action agira sur toutes les alertes générées au sein de cette étendue.

   1. Choisissez **Sélectionner** par **Étendue**.

      ![Sélectionner une étendue pour une nouvelle règle d’action](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-01.png)

   2. Sélectionnez l’**Abonnement** pour la règle d’action, puis filtrez éventuellement par un type de **Ressource**. Pour filtrer en fonction des ressources Azure Stack Edge, sélectionnez **Appareils Data Box Edge (dataBoxEdge)** .

      La zone **Ressources** liste les ressources disponibles en fonction de vos sélections.
  
      ![Ressources disponibles dans l’écran Sélectionner une étendue](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-02.png)

   3. Cochez la case en regard de chaque ressource à laquelle vous souhaitez appliquer la règle. Vous pouvez sélectionner l’abonnement, des groupes de ressources ou des ressources individuelles. Ensuite, sélectionnez **Terminé**.

      ![Exemples de paramètres pour l’étendue d’une règle d’action](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-03.png)

      L’écran **Créer une règle d’action** affiche l’étendue sélectionnée.

      ![Étendue terminée pour une règle d’action Azure Stack Edge](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-04.png)

4. Utilisez les options de **Filtre** pour limiter l’application de la règle à un sous-ensemble d’alertes au sein de l’étendue sélectionnée.

   1. Sélectionnez **Ajouter** pour ouvrir le volet **Ajouter des filtres**.

      ![Option permettant d’ajouter des filtres à une règle d’action](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-01.png)

   2. Sous **Filtres**, ajoutez chaque filtre que vous souhaitez appliquer. Pour chaque filtre, sélectionnez le type de filtre, l’**Opérateur** et la **Valeur**.
   
      Pour obtenir la liste des options de filtre, consultez [Critères de filtre](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#filter-criteria).

      Les exemples de filtres ci-dessous s’appliquent à toutes les alertes de niveaux de gravité 2, 3 et 4 que le service Monitor déclenche pour les ressources Azure Stack Edge.

      Quand vous avez terminé d’ajouter des filtres, sélectionnez **Terminé**.
   
      ![Exemple de filtre pour une règle d’action](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-02.png)

5. Dans l’écran **Créer une règle d’action**, sélectionnez **Groupe d’actions** pour créer une règle qui envoie des notifications. Ensuite, choisissez **Sélectionner** par **Actions**.

   ![Option Groupe d’actions permettant de créer une règle d’action qui envoie des notifications](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-01.png)

   > [!NOTE]
   > Pour créer une règle qui supprime des notifications, vous devez choisir **Suppression**. Pour plus d’informations, consultez [Configuration d’une règle d’action](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#configuring-an-action-rule).

6. Sélectionnez le groupe d’actions que vous souhaitez utiliser avec cette règle d’action. Choisissez ensuite **Sélectionner**. Votre nouvelle règle d’action sera ajoutée aux préférences de notification du groupe d’actions sélectionné.

   Si vous devez créer un groupe d’actions, sélectionnez **+ Créer un groupe d’actions** et suivez les étapes décrites dans [Créer un groupe d’actions à l’aide du portail Azure](/azure/azure-monitor/platform/action-groups#create-an-action-group-by-using-the-azure-portal).

   ![Sélectionnez un groupe d’actions à utiliser avec la règle, puis choisissez Sélectionner.](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-02.png)

7. Donnez un **Nom** et une **Description** à la nouvelle règle d’action, puis assignez-la à un groupe de ressources.

9. La nouvelle règle est activée par défaut. Si vous ne souhaitez pas commencer à utiliser la règle immédiatement, sélectionnez **Non** pour **Activer la règle lors de sa création**.

10. Quand vous avez terminé de définir vos paramètres, sélectionnez **Créer**.

    ![Paramètres terminés pour une règle d’action qui envoie des notifications d’alerte](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-completed-settings.png)

    L’écran **Règles d’action (préversion)** s’ouvre, mais il est possible que votre nouvelle règle d’action n’apparaisse pas immédiatement. Le focus est **Tous** les groupes de ressources.

11. Pour voir votre nouvelle règle d’action, sélectionnez le groupe de ressources auquel elle est associée.

    ![Écran Règles d’action avec affichage de la nouvelle règle](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-displayed.png)


## <a name="view-notifications"></a>Voir les notifications

Des notifications sont émises quand un nouvel événement déclenche une alerte pour une ressource qui se trouve dans l’étendue d’une règle d’action.

Le groupe d’actions pour une règle définit qui reçoit une notification et le type de notification envoyé : e-mail, SMS ou les deux.

La réception de notifications après le déclenchement d’une alerte peut prendre quelques minutes.

La notification par e-mail ressemble à celle-ci.

![Exemple de notification par e-mail pour une règle d’action](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/sample-action-rule-email-notification.png)


## <a name="next-steps"></a>Étapes suivantes

<!-- - See [Create and manage action groups in the Azure portal](/azure/azure-monitor/platform/action-groups) for guidance on creating a new action group.
- See [Configure an action rule](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#configuring-an-action-rule) for more info about creating action rules that send or suppress alert notifications. -2 bullets referenced above. Making room for local tasks in "Next Steps." --> 
- Pour plus d’informations sur l’examen des événements d’appareil, de l’état du matériel et des graphiques de métriques, consultez [Superviser votre appareil Azure Stack Edge Pro](azure-stack-edge-monitor.md). 
- Pour plus d’informations sur l’optimisation d’Azure Monitor pour les appareils Azure Stack Edge Pro GPU, consultez [Utilisation d’Azure Monitor](azure-stack-edge-gpu-enable-azure-monitor.md).
- Pour plus d’informations sur la gestion des alertes individuelles, consultez [Créer, afficher et gérer des alertes de métriques à l’aide d’Azure Monitor](/azure/azure-monitor/platform/alerts-metric).
