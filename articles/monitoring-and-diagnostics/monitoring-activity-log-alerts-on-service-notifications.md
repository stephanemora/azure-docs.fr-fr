---
title: Recevoir des alertes de journal d’activité sur les notifications de service Azure
description: Soyez informé par SMS, e-mail ou webhook en cas de service Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 01dc3a3c6489b694af26c78ae3b4756f3e8f00b7
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263114"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Créer des alertes de journal d’activité sur les notifications de service
## <a name="overview"></a>Vue d'ensemble
Cet article vous indique comment configurer des alertes de journal d’activité pour les notifications sur l’intégrité du service à l’aide du Portail Azure.  

Vous pouvez recevoir une alerte lorsqu’Azure envoie des notifications sur l’état du service sur votre abonnement Azure. Vous pouvez configurer l’alerte en fonction des éléments suivants :

- la classe de la notification sur l’intégrité du service (problèmes de service, maintenance planifiée, avis d’intégrité) ;
- l’abonnement affecté ;
- le ou les services affectés ;
- la ou les régions affectées ;

Vous pouvez également configurer à qui l’alerte doit être envoyée :

- sélectionner un groupe d’actions existant ;
- créer un nouveau groupe d’actions (qui peut être utilisé pour les alertes futures).

Pour en savoir plus sur les groupes d’actions, consultez [Créer et gérer des groupes d’actions](monitoring-action-groups.md).

Pour plus d’informations sur le mode de configuration des alertes de notification sur l’intégrité du service à l’aide de modèles Azure Resource Manager, consultez [Modèles Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Créer une alerte sur une notification sur l’intégrité du service pour un nouveau groupe d’actions à l’aide du portail Azure
1. Dans le [portail](https://portal.azure.com), sélectionnez **Intégrité du service**.

    ![Service Intégrité du service](./media/monitoring-activity-log-alerts-on-service-notifications/home-servicehealth.png)

2. Dans la section **Alertes**, sélectionnez **Alertes d’intégrité**.

    ![Onglet Alertes d’intégrité](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades-sh.png)

3. Sélectionnez **Créer une alerte d’intégrité du service** et renseignez les champs.

    ![Commande Créer une alerte d’intégrité du service](./media/monitoring-activity-log-alerts-on-service-notifications/service-health-alert.png)

4. Sélectionnez l’**abonnement**, les **services** et les **régions** pour lesquels vous voulez être alerté.

    ![La boîte de dialogue « Ajouter une alerte activité journal »](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> Cet abonnement est utilisé pour enregistrer l’alerte de journal d’activité. La ressource d’alerte est déployée pour cet abonnement et surveille les événements du journal d’activité.

5. Choisissez les **types d’événements** pour lesquels vous voulez être alerté : *problème de service*, *maintenance planifiée* et *avis d’intégrité* 

6. Définissez les détails de l’alerte en entrant le **nom de la règle d’alerte** et une **description**.

7. Sélectionnez le **groupe de ressources** dans lequel vous souhaitez enregistrer l’alerte.

8. Créez un groupe d’action en sélectionnant **Nouveau groupe d'actions**. Entrez un nom dans la zone **Nom du groupe d’actions** et entrez un nom dans la zone **Nom court**. Le nom court est référencé dans les notifications envoyées lorsque cette alerte se déclenche.

    ![Créer un nouveau groupe d’action](./media/monitoring-activity-log-alerts-on-service-notifications/action-group-creation.png)

9. Définissez ensuite une liste de destinataires en indiquant les éléments suivants :

    a. **Nom** : entrez le nom, l’alias ou l’identificateur du destinataire.

    b. **Type d’action** : sélectionnez SMS, message électronique, Webhook, application Azure et plus encore.

    c. **Détails** : selon le type d’action choisi, indiquez un numéro de téléphone, une adresse e-mail ou une URI Webhook etc.

10. Sélectionnez **OK** pour créer le groupe d’actions, puis **Créer une règle d’alerte** pour terminer votre alerte.

Dans quelques minutes, l’alerte est active et commence à se déclencher en fonction des conditions que vous avez spécifiées lors de la création.

Découvrez comment [configurer des notifications de Webhook pour les systèmes de gestion de problème existants](../service-health/service-health-alert-webhook-guide.md). Pour plus d’informations sur le schéma de webhook sur les alertes de journal d’activité, consultez [Webhooks pour les alertes du journal d’activité Azure](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Le groupe d’actions défini dans cette procédure est réutilisable en tant que groupe d’actions existant, pour la définition de toutes les futures alertes.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Créer une alerte sur une notification sur l’intégrité du service pour un groupe d’actions existant à l’aide du portail Azure

1. Suivez les étapes 1 à 7 dans la section précédente pour créer votre notification d’intégrité du service. 

2. Sous **Définir un groupe d’actions**, cliquez sur le bouton **Sélectionner un groupe d’actions**. Sélectionnez le groupe d’actions approprié.

3. Sélectionnez **Ajouter** pour ajouter le groupe d’actions, puis **Créer une règle d’alerte** pour terminer votre alerte.

Dans quelques minutes, l’alerte est active et commence à se déclencher en fonction des conditions que vous avez spécifiées lors de la création.

## <a name="manage-your-alerts"></a>Gérez vos alertes

Une fois l’alerte créée, elle apparaît dans la section **Alertes** du panneau **Moniteur**. Sélectionnez l’alerte que vous souhaitez gérer pour :

* la modifier
* la supprimer
* la désactiver ou l’activer si vous voulez arrêter temporairement ou reprendre la réception de notifications pour l’alerte.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [configurer des notifications de Webhook pour les systèmes de gestion de problème existants](../service-health/service-health-alert-webhook-guide.md).
- En savoir plus sur les [notifications sur l’intégrité du service](monitoring-service-notifications.md).
- En savoir plus sur la [limitation du débit des notifications](monitoring-alerts-rate-limiting.md).
- Consultez le [schéma webhook des alertes de journal d’activité](monitoring-activity-log-alerts-webhook.md).
- Obtenez une [vue d’ensemble des alertes du journal d’activité](monitoring-overview-alerts.md) et découvrez comment recevoir des alertes. 
- En savoir plus sur les [groupes d’actions](monitoring-action-groups.md).
