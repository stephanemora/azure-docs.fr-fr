---
title: Créer et gérer des groupes d’actions sur le Portail Azure
description: Découvrez comment créer et gérer des groupes d’action sur le Portail Azure.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 38cccf17980f5a6a2cf162cdecdc6aad40d4f38e
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432555"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Créer et gérer des groupes d’actions sur le Portail Azure
## <a name="overview"></a>Vue d’ensemble ##
Un groupe d’actions est une collection de préférences de notification définies par le propriétaire d’un abonnement Azure. Les alertes Azure Monitor et Service Health utilisent des groupes d’actions pour avertir les utilisateurs qu’une alerte a été déclenchée. Plusieurs alertes peuvent utiliser le même groupe d’actions ou des groupes d’actions différents selon les besoins de l’utilisateur.

Quand une action est configurée pour notifier une personne par courrier électronique ou SMS, cette personne reçoit une confirmation indiquant qu’elle a été ajoutée au groupe d’actions.

Cet article vous montre comment créer et gérer des groupes d’actions dans le portail Azure.

Chaque action se compose des propriétés suivantes :

* **Nom** : identificateur unique au sein du groupe d’actions.  
* **Type d’action** : l’action à effectuer. Exemples : envoi d'un appel vocal, d'un SMS ou d'un e-mail ; ou déclenchement de différents types d'actions automatisées. Reportez-vous aux types présentés plus loin dans cet article. 
* **Détails** : détails correspondants qui varient selon le *type d’action*. 

Pour plus d’informations sur l’utilisation de modèles Azure Resource Manager pour configurer des groupes d’actions, consultez la page [Modèles Resource Manager de groupes d’actions](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Créer un groupe d’actions à l’aide du Portail Azure ##
1. Sur le [Portail](https://portal.azure.com), sélectionnez **Moniteur**. Le panneau **Moniteur** consolide tous vos paramètres et données de monitorage sur un même affichage.

    ![Le service « Moniteur »](./media/action-groups/home-monitor.png)
1. Sélectionnez **Alertes**, puis **Gérer les groupes d’actions**.

    ![Bouton Gérer les groupes d’actions](./media/action-groups/manage-action-groups.png)
1. Sélectionnez **Ajouter un groupe d’actions** et renseignez les champs.

    ![La commande « Ajouter un groupe d’actions »](./media/action-groups/add-action-group.png)
1. Entrez un nom dans la zone **Nom du groupe d’actions** et un autre dans la zone **Nom court**. Le nom court est utilisé à la place du nom complet du groupe d’actions lorsque les notifications sont envoyées à l’aide de ce groupe.

      ![La boîte de dialogue « Ajouter un groupe d’actions »](./media/action-groups/action-group-define.png)

1. La zone **Abonnement** est automatiquement renseignée avec votre abonnement actuel. Cet abonnement est celui dans lequel est enregistré le groupe d’actions.

1. Sélectionnez le **Groupe de ressources** dans lequel le groupe d’actions est enregistré.

1. Définissez une liste d’actions en indiquant les éléments suivants pour chaque action :

    a. **Nom** : entrez un identificateur unique pour cette action.

    b. **Type d’action** : sélectionnez E-mail, SMS, Push, Voix, application logique, Webhook, ITSM ou Runbook Automation.

    c. **Détails** : selon le type d’action, saisissez un numéro de téléphone, une adresse e-mail, un URI de webhook, une application Azure, une connexion ITSM ou un runbook Automation. Pour ITSM Action, spécifiez l’**élément de travail** et les autres champs nécessaires à votre outil ITSM.

1. Sélectionnez **OK** pour créer le groupe d’actions.

## <a name="manage-your-action-groups"></a>Gérer des groupes d’actions ##
Après sa création, un groupe d’actions apparaît dans la section **Groupes d’actions** du panneau **Moniteur**. Sélectionnez le groupe d’actions que vous souhaitez gérer pour :

* Ajouter, modifier ou supprimer des actions.
* Supprimer le groupe d’actions.

## <a name="action-specific-information"></a>Informations spécifiques sur l’action
**Envoi (Push) d'application Azure** : vous pouvez avoir jusqu'à 10 actions d'application Azure par groupe d'actions. À ce stade, l’action d’application Azure prend uniquement en charge des alertes ServiceHealth. Tout autre moment d’alerte sera ignoré. Consultez [Configuration d’alertes lorsqu’une notification d’intégrité de service est publiée](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

**E-mail** : les e-mails seront envoyés à partir des adresses e-mail suivantes. Vérifiez que le filtrage e-mail est correctement configuré
   - azure-noreply@microsoft.com
   - azureemail-noreply@microsoft.com
   - alerts-noreply@mail.windowsazure.com

Vous pouvez avoir jusqu’à 1 000 actions d’e-mail par groupe d’actions. Consultez l’article [Informations de limitation du débit](./../../azure-monitor/platform/alerts-rate-limiting.md)

**ITSM** : vous pouvez avoir jusqu’à 10 actions ITSM par groupe d’actions. ITSM Action requiert une connexion ITSM. Découvrez comment créer une [connexion ITSM](../../azure-monitor/platform/itsmc-overview.md).

**Application logique** : vous pouvez avoir jusqu’à 10 actions d’application logique par groupe d’actions.

**Function App** : les touches de fonction de Function Apps, qui sont configurées comme actions, sont lues par le biais de l’API Functions, ce qui nécessite actuellement l’utilisation d’applications de fonction v2 pour définir le paramètre d’application AzureWebJobsSecretStorageType sur « files » (fichiers). Consultez [Modifications apportées à la gestion de clés dans les fonctions V2]( https://aka.ms/funcsecrets) pour plus d’informations.

**Runbook** : vous pouvez avoir jusqu’à 10 actions Runbook par groupe d’actions. Pour connaître les limites des charges utiles Runbook, consultez [Limites du service d’abonnement Azure](../../azure-subscription-service-limits.md).

**SMS** : vous pouvez avoir jusqu’à 10 actions SMS par groupe d’actions. Consultez l'article [Informations sur la limitation du taux](./../../azure-monitor/platform/alerts-rate-limiting.md). Consultez l'article [Comportement des alertes SMS](../../azure-monitor/platform/alerts-sms-behavior.md).

**Voix** : vous pouvez avoir jusqu’à 10 actions Voice par groupe d’actions.</dd>
Consultez l’article [Informations de limitation du débit](./../../azure-monitor/platform/alerts-rate-limiting.md)</dd>

**Webhook** : vous pouvez avoir jusqu’à 10 actions Webhook par groupe d’actions. Logique de nouvelle tentative : le délai d’expiration d’une réponse est de 10 secondes. L’appel de webhook sera retenté 2 fois au maximum lorsque les codes d’état HTTP suivants sont retournés : 408, 429, 503, 504 ou que le point de terminaison HTTP ne répond pas. La première nouvelle tentative se produit après 10 secondes. La deuxième et la dernière nouvelles tentatives se produisent après 100 secondes.

Plage d’adresses IP sources
    - 13.106.57.181
    - 13.106.54.3
    - 13.106.54.19
    - 13.106.38.142
    - 13.106.38.148
    - 13.106.57.196

Pour recevoir des mises à jour sur les modifications apportées à ces adresses IP, nous vous recommandons de configurer une [alerte Service Health](./../../azure-monitor/platform/service-notifications.md) qui surveille les notifications d’information relatives au service Groupes d’actions.


## <a name="next-steps"></a>Étapes suivantes ##
* En savoir plus sur le [comportement des alertes SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* [Comprendre le schéma Webhook des alertes du journal d’activité](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* En savoir plus sur [ITSM Connector](../../azure-monitor/platform/itsmc-overview.md)
* En savoir plus sur la [limitation de la fréquence](../../azure-monitor/platform/alerts-rate-limiting.md) des alertes.
* Obtenir une [vue d’ensemble des alertes du journal d’activité](../../azure-monitor/platform/alerts-overview.md) et découvrir comment recevoir des alertes.  
* Découvrir comment [configurer des alertes lorsqu’une notification d’intégrité de service est publiée](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

