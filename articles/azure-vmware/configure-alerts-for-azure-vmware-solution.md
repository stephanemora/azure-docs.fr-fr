---
title: Configurer des alertes et utiliser des métriques dans Azure VMware Solution
description: Découvrez comment utiliser des alertes pour recevoir des notifications. Découvrez également comment utiliser les métriques pour obtenir des informations plus approfondies sur votre cloud privé Azure VMware Solution.
ms.topic: how-to
ms.date: 07/23/2021
ms.openlocfilehash: 718838d5335ff10200fc41029a6431a96552894b
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114653573"
---
# <a name="configure-azure-alerts-in-azure-vmware-solution"></a>Configurer des alertes Azure dans Azure VMware Solution 

Dans cet article, vous allez apprendre à configurer des [groupes d’actions Azure](../azure-monitor/alerts/action-groups.md) dans des [alertes Microsoft Azure](../azure-monitor/alerts/alerts-overview.md) pour recevoir des notifications d’événements déclenchés que vous définissez. Vous en apprendrez également plus sur l’utilisation de [métriques Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md) pour obtenir des informations plus approfondies sur votre cloud privé Azure VMware Solution.

>[!NOTE]
>Les incidents qui affectent la disponibilité d’un hôte Azure VMware Solution et de sa restauration correspondante sont envoyés automatiquement à l’administrateur de compte, à l’administrateur de service (autorisation classique), au coadministrateur (autorisation classique) et aux propriétaires (rôle RBAC) des abonnements contenant des Clouds privés Azure VMware Solution.

## <a name="supported-metrics-and-activities"></a>Métriques et activités prises en charge

Les métriques suivantes sont visibles via Azure Monitor Metrics.

| **Nom du signal**                                                         | **Type de signal** | **Service de surveillance** |
|-------------------------------------------------------------------------|-----------------|---------------------|
| Capacité totale du disque du magasin de données                                           | Métrique          | Plateforme            |
| Pourcentage du disque de magasin de données utilisé                                          | Métrique          | Plateforme            |
| Pourcentage d’UC                                                          | Métrique          | Plateforme            |
| Mémoire effective moyenne                                                | Métrique          | Plateforme            |
| Surcharge moyenne de la mémoire                                                 | Métrique          | Plateforme            |
| Mémoire totale moyenne                                                    | Métrique          | Plateforme            |
| Utilisation moyenne de la mémoire                                                    | Métrique          | Plateforme            |
| Disque de magasin de données utilisé                                                     | Métrique          | Plateforme            |
| Toutes les opérations d’administration                                           | Journal d’activité    | Administratif      |
| Inscrivez le fournisseur de ressources Microsoft.AVS (Microsoft.AVS/privateClouds) | Journal d’activité    | Administratif      |
| Créez ou mettez à jour une instance PrivateCloud. (Microsoft.AVS/privateClouds)          | Journal d’activité    | Administratif      |
| Supprimez une instance PrivateCloud. (Microsoft.AVS/privateClouds)                    | Journal d’activité    | Administratif      |

## <a name="configure-an-alert-rule"></a>Configurer une règle d'alerte
1. À partir de votre cloud privé Azure VMware Solution, sélectionnez **Supervision** > **Alertes**, puis **Nouvelle règle d’alerte**.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png" alt-text="Capture d’écran montrant où configurer une règle d’alerte dans votre cloud privé Azure VMware Solution." lightbox="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png":::

   Un nouvel écran de configuration s’ouvre, dans lequel vous allez :
   - Définir l'étendue
   - Configurer une condition
   - Configurer le groupe d’actions
   - Définir les détails de la règle d’alerte
    
   :::image type="content" source="../devtest-labs/media/activity-logs/create-alert-rule-done.png" alt-text="Capture d’écran montrant la page Créer une règle d’alerte." lightbox="../devtest-labs/media/activity-logs/create-alert-rule-done.png":::

1. Sous **Étendue**, sélectionnez la ressource cible que vous souhaitez analyser. Par défaut, le cloud privé Azure VMware Solution à partir duquel vous avez ouvert le menu Alertes a été défini.

1. Sous **Condition**, sélectionnez **Ajouter une condition** et, dans la fenêtre qui s’ouvre, sélectionnez le signal que vous souhaitez créer pour la règle d’alerte. 

   Dans notre exemple, nous avons sélectionné **Pourcentage de magasin de banques de donnée utilisé**, ce qui est pertinent du point de vue du [Contrat SLA Azure VMware Solution](https://aka.ms/avs/sla). 

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/configure-signal-logic-options.png" alt-text="Capture d’écran montrant la fenêtre Configurer la logique du signal avec des signaux à créer pour la règle d’alerte."::: 

1. Définissez la logique qui déclenchera l’alerte, puis sélectionnez **Terminé**. 

   Dans notre exemple, seul le **Seuil** et la **Fréquence d’évaluation** ont été ajustés. 
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/define-alert-logic-threshold.png" alt-text="Capture d’écran montrant le seuil, l’opérateur, le type d’agrégation et la granularité, la valeur de seuil et la fréquence d’évaluation pour la logique d’alerte de signal."::: 

1. Sous **Actions**, sélectionnez **Ajouter des groupes d’actions**. Le groupe d’actions définit *comment* la notification est reçue et *qui* la reçoit.   Vous pouvez recevoir des notifications par e-mail, SMS, [notification Push Azure Mobile App](https://azure.microsoft.com/features/azure-portal/mobile-app/) ou message vocal.

1. Sélectionnez un groupe d’actions existant ou sélectionnez **Créer un groupe d’actions** pour en créer un.
 
1. Dans la fenêtre qui s’ouvre, sous l’onglet **Fonctions de base**, attribuez un nom et un nom d’affichage au groupe d’actions.

1. Sélectionnez l’onglet **Notifications**, puis un **Type de notification** et un **Name**. Sélectionnez ensuite **OK**.

   Notre exemple est basé sur une notification par e-mail.

   :::image type="content" source="../azure-monitor/alerts/media/action-groups/action-group-2-notifications.png" alt-text="Capture d’écran montrant les paramètres d’e-mail, de SMS, d’envoi push et de message vocal pour l’alerte." lightbox="../azure-monitor/alerts/media/action-groups/action-group-2-notifications.png":::    

1. (Facultatif) Configurez les **Actions** si vous souhaitez prendre des mesures proactives et recevoir des notifications sur l’événement. Sélectionnez un **Type d’action** disponible, puis cliquez sur **Vérifier + créer**. 

   - **Runbooks Automation :** pour automatiser les tâches basées sur les alertes

   - **Azure Functions :** pour l’exécution de code serverless pilotée par les événements

   - **ITSM :** pour s’intégrer à un fournisseur de services comme ServiceNow pour créer un ticket

   - **Application logique :** pour une orchestration de flux de travail plus complexe

   - **Webhooks :** pour déclencher un processus dans un autre service


1. Sous les **Détails de la règle d’alerte**, indiquez un nom, une description, un groupe de ressources pour stocker la règle d’alerte, la gravité. Sélectionnez ensuite **Créer une règle d’alerte**.
   
   La règle d’alerte est visible et peut être gérée à partir du Portail Azure.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png" alt-text="Capture d’écran montrant la nouvelle règle d’alerte dans la fenêtre Règles." lightbox="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png":::      

   Dès qu’une métrique atteint le seuil défini dans une règle d’alerte, le menu **Alertes** est mis à jour et rendu visible.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png" alt-text="Capture d’écran montrant l’alerte après avoir atteint le seuil défini dans la règle d’alerte." lightbox="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png":::     

   En fonction du groupe d’actions configuré, vous recevrez une notification via le support configuré. Dans notre exemple, nous avons configuré sur e-mail.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-notification.png" alt-text="Capture d’écran d’une alerte Azure Monitor avec la chaîne d’erreur et l’événement de date et d’heure déclenché."::: 

## <a name="work-with-metrics"></a>Utiliser des métriques

1. À partir de votre cloud privé Azure VMware Solution, sélectionnez **Supervision** > **Métriques**. Sélectionnez ensuite la métrique de votre choix dans la liste déroulante.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png" alt-text="Capture d’écran montrant la fenêtre Métriques et un focus sur la liste déroulante Métrique." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png":::   

1. Vous pouvez modifier les paramètres du diagramme, tels que l'**Intervalle de temps** ou la **Granularité temporelle**. 

   Les autres options sont :
   - **Explorer les journaux** et interroger les données de l’espace de travail Log Analytics associé
   - **Épingler ce diagramme** à un tableau de bord Azure pour plus de commodité.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png" alt-text="Capture d’écran montrant les options d’intervalle de temps et de granularité temporelle pour la métrique." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png":::  
 
 
## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré une règle d’alerte pour votre cloud privé Azure VMware Solution, vous souhaiterez peut-être en savoir plus sur les éléments suivants :
- [Mesures Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md)
- [Alertes Azure Monitor](../azure-monitor/alerts/alerts-overview.md)
- [Groupes d’actions Azure](../azure-monitor/alerts/action-groups.md)

Vous pouvez également continuer avec l’un des autres guides pratiques [Azure VMware Solution](index.yml).
