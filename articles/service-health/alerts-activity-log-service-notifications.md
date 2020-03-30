---
title: Recevoir des alertes de journal d’activité sur les notifications de service Azure
description: Soyez informé par SMS, e-mail ou webhook en cas de service Azure.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: d318adc76959ac24f4be9946167965a83053f632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749307"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Créer des alertes de journal d’activité sur les notifications de service
## <a name="overview"></a>Vue d’ensemble

Cet article vous indique comment configurer des alertes de journal d’activité pour les notifications sur l’intégrité du service à l’aide du Portail Azure.  

Les notifications sur l’intégrité du service sont stockées dans le [journal d’activité Azure](../azure-monitor/platform/platform-logs-overview.md). Dans la mesure où le volume d’informations stockées dans le journal d’activité peut être important, il existe une interface utilisateur distincte permettant de faciliter l’affichage et la configuration d’alertes sur ces notifications. 

Vous pouvez recevoir une alerte lorsqu’Azure envoie des notifications sur l’état du service sur votre abonnement Azure. Vous pouvez configurer l’alerte en fonction des éléments suivants :

- la classe de la notification sur l’intégrité du service (problèmes de service, maintenance planifiée, avis d’intégrité) ;
- l’abonnement affecté ;
- le ou les services affectés ;
- la ou les régions affectées ;

> [!NOTE]
> Les notifications d’intégrité de service n’envoient pas d’alerte sur les événements d’intégrité des ressources.

Vous pouvez également configurer à qui l’alerte doit être envoyée :

- sélectionner un groupe d’actions existant ;
- créer un nouveau groupe d’actions (qui peut être utilisé pour les alertes futures).

Pour en savoir plus sur les groupes d’actions, consultez [Créer et gérer des groupes d’actions](../azure-monitor/platform/action-groups.md).

Pour plus d’informations sur le mode de configuration des alertes de notification sur l’intégrité du service à l’aide de modèles Azure Resource Manager, consultez [Modèles Resource Manager](../azure-monitor/platform/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Regarder une vidéo expliquant comment configurer votre première alerte Azure Service Health

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Alerte et nouveau groupe d’actions à l’aide du portail Azure
1. Dans le [portail](https://portal.azure.com), sélectionnez **Intégrité du service**.

    ![Service Intégrité du service](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. Dans la section **Alertes**, sélectionnez **Alertes d’intégrité**.

    ![Onglet Alertes d’intégrité](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Sélectionnez **Créer une alerte d’intégrité du service** et renseignez les champs.

    ![Commande Créer une alerte d’intégrité du service](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Sélectionnez l’**abonnement**, les **services** et les **régions** pour lesquels vous voulez être alerté.

    ![La boîte de dialogue « Ajouter une alerte activité journal »](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

    > [!NOTE]
    > Cet abonnement est utilisé pour enregistrer l’alerte de journal d’activité. La ressource d’alerte est déployée pour cet abonnement et surveille les événements du journal d’activité.

1. Choisissez les **types d’événements** pour lesquels vous voulez être alerté : *problème de service*, *maintenance planifiée* et *avis d’intégrité* 

1. Définissez les détails de l’alerte en entrant le **nom de la règle d’alerte** et une **description**.

1. Sélectionnez le **groupe de ressources** dans lequel vous souhaitez enregistrer l’alerte.

1. Créez un groupe d’action en sélectionnant **Nouveau groupe d'actions**. Entrez un nom dans la zone **Nom du groupe d’actions** et entrez un nom dans la zone **Nom court**. Le nom court est référencé dans les notifications envoyées lorsque cette alerte se déclenche.

    ![Créer un nouveau groupe d’action](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Définissez ensuite une liste de destinataires en indiquant les éléments suivants :

    a. **Nom** : entrez le nom, l’alias ou l’identificateur du destinataire.

    b. **Type d’action** : sélectionnez SMS, message électronique, Webhook, application Azure et plus encore.

    c. **Détails** : selon le type d’action choisi, indiquez un numéro de téléphone, une adresse e-mail ou une URI Webhook etc.

1. Sélectionnez **OK** pour créer le groupe d’actions, puis **Créer une règle d’alerte** pour terminer votre alerte.

Dans quelques minutes, l’alerte est active et commence à se déclencher en fonction des conditions que vous avez spécifiées lors de la création.

Découvrez comment [configurer des notifications de Webhook pour les systèmes de gestion de problème existants](service-health-alert-webhook-guide.md). Pour plus d’informations sur le schéma de webhook sur les alertes de journal d’activité, consultez [Webhooks pour les alertes du journal d’activité Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

>[!NOTE]
>Le groupe d’actions défini dans cette procédure est réutilisable en tant que groupe d’actions existant, pour la définition de toutes les futures alertes.
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Alerte avec groupe d’actions existant à l’aide du portail Azure

1. Suivez les étapes 1 à 6 dans la section précédente pour créer votre notification d’intégrité du service. 

1. Sous **Définir un groupe d’actions**, cliquez sur le bouton **Sélectionner un groupe d’actions**. Sélectionnez le groupe d’actions approprié.

1. Sélectionnez **Ajouter** pour ajouter le groupe d’actions, puis **Créer une règle d’alerte** pour terminer votre alerte.

Dans quelques minutes, l’alerte est active et commence à se déclencher en fonction des conditions que vous avez spécifiées lors de la création.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Alerte et nouveau groupe d’actions à l’aide des modèles Azure Resource Manager

L’exemple suivant crée un groupe d’actions avec une cible de messagerie et active toutes les notifications d’intégrité de service pour l’abonnement cible.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "actionGroups_name": {
            "defaultValue": "SubHealth",
            "type": "String"
        },
        "activityLogAlerts_name": {
            "defaultValue": "ServiceHealthActivityLogAlert",
            "type": "String"
        },
        "emailAddress":{
            "type":"string"
        }
    },
    "variables": {
        "alertScope":"[concat('/','subscriptions','/',subscription().subscriptionId)]"
    },
    "resources": [
        {
            "comments": "Action Group",
            "type": "microsoft.insights/actionGroups",
            "name": "[parameters('actionGroups_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "groupShortName": "[parameters('actionGroups_name')]",
                "enabled": true,
                "emailReceivers": [
                    {
                        "name": "[parameters('actionGroups_name')]",
                        "emailAddress": "[parameters('emailAddress')]"
                    }
                ],
                "smsReceivers": [],
                "webhookReceivers": []
            },
            "dependsOn": []
        },
        {
            "comments": "Service Health Activity Log Alert",
            "type": "microsoft.insights/activityLogAlerts",
            "name": "[parameters('activityLogAlerts_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "scopes": [
                    "[variables('alertScope')]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ServiceHealth"
                        },
                        {
                            "field": "properties.incidentType",
                            "equals": "Incident"
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
                            "webhookProperties": {}
                        }
                    ]
                },
                "enabled": true,
                "description": ""
            },
            "dependsOn": [
                "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
            ]
        }
    ]
}
```

## <a name="manage-your-alerts"></a>Gérez vos alertes

Une fois l’alerte créée, elle apparaît dans la section **Alertes** du panneau **Moniteur**. Sélectionnez l’alerte que vous souhaitez gérer pour :

* la modifier
* la supprimer
* la désactiver ou l’activer si vous voulez arrêter temporairement ou reprendre la réception de notifications pour l’alerte.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez les [meilleures pratiques de configuration d’alertes Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Découvrez comment [configurer des notifications Push mobiles pour Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Découvrez comment [configurer des notifications de Webhook pour les systèmes de gestion de problème existants](service-health-alert-webhook-guide.md).
- En savoir plus sur les [notifications sur l’intégrité du service](service-notifications.md).
- En savoir plus sur la [limitation du débit des notifications](../azure-monitor/platform/alerts-rate-limiting.md).
- Consultez le [schéma webhook des alertes de journal d’activité](../azure-monitor/platform/activity-log-alerts-webhook.md).
- Obtenir une [vue d’ensemble des alertes du journal d’activité](../azure-monitor/platform/alerts-overview.md) et découvrir comment recevoir des alertes.
- En savoir plus sur les [groupes d’actions](../azure-monitor/platform/action-groups.md).
