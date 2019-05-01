---
title: Créer et gérer des groupes d’actions sur le Portail Azure
description: Découvrez comment créer et gérer des groupes d’action sur le Portail Azure.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 3d06024b7fa4356d4ad0e8b52c45c2ead62ef784
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60778335"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Créer et gérer des groupes d’actions sur le Portail Azure
## <a name="overview"></a>Présentation ##
Un groupe d’actions est une collection de préférences de notification définies par le propriétaire d’un abonnement Azure. Les alertes Azure Monitor et Service Health utilisent des groupes d’actions pour avertir les utilisateurs qu’une alerte a été déclenchée. Plusieurs alertes peuvent utiliser le même groupe d’actions ou des groupes d’actions différents selon les besoins de l’utilisateur. Vous pouvez configurer jusqu'à 2 000 groupes d'actions au sein d'un abonnement.

Vous configurez une action pour avertir une personne par e-mail ou SMS, qu'ils reçoivent une confirmation indiquant qu’ils ont été ajoutés au groupe d’actions.

Cet article vous montre comment créer et gérer des groupes d’actions dans le portail Azure.

Chaque action se compose des propriétés suivantes :

* **Nom** : identificateur unique au sein du groupe d’actions.  
* **Type d’action** : L’action effectuée. Exemples : envoi d'un appel vocal, d'un SMS ou d'un e-mail ; ou déclenchement de différents types d'actions automatisées. Reportez-vous aux types présentés plus loin dans cet article. 
* **Détails** : Les détails correspondants qui varient selon le *type d’action*. 

Pour plus d’informations sur l’utilisation de modèles Azure Resource Manager pour configurer des groupes d’actions, consultez la page [Modèles Resource Manager de groupes d’actions](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Créer un groupe d’actions à l’aide du Portail Azure ##
1. Dans le [Portail](https://portal.azure.com), sélectionnez **Moniteur**. Le **moniteur** volet consolide toutes vos paramètres de surveillance et données dans une vue.

    ![Le service « Moniteur »](./media/action-groups/home-monitor.png)
1. Sélectionnez **Alertes**, puis **Gérer les groupes d’actions**.

    ![Bouton Gérer les groupes d’actions](./media/action-groups/manage-action-groups.png)
1. Sélectionnez **Ajouter un groupe d’actions** et renseignez les champs.

    ![La commande « Ajouter un groupe d’actions »](./media/action-groups/add-action-group.png)
1. Entrez un nom dans la zone **Nom du groupe d’actions** et un autre dans la zone **Nom court**. Le nom court est utilisé à la place du nom complet du groupe d’actions lorsque les notifications sont envoyées à l’aide de ce groupe.

      ![La boîte de dialogue « Ajouter un groupe d’actions »](./media/action-groups/action-group-define.png)

1. La zone **Abonnement** est automatiquement renseignée avec votre abonnement actuel. Cet abonnement est celui dans lequel est enregistré le groupe d’actions.

1. Sélectionnez le **Groupe de ressources** dans lequel le groupe d’actions est enregistré.

1. Définir une liste d’actions. Fournir les informations suivantes pour chaque action :

    a. **Nom** : entrez un identificateur unique pour cette action.

    b. **Type d’action** : sélectionnez E-mail, SMS, Push, Voix, application logique, Webhook, ITSM ou Runbook Automation.

    c. **Détails** : selon le type d’action, saisissez un numéro de téléphone, une adresse e-mail, un URI de webhook, une application Azure, une connexion ITSM ou un runbook Automation. Pour ITSM Action, spécifiez l’**élément de travail** et les autres champs nécessaires à votre outil ITSM.

1. Sélectionnez **OK** pour créer le groupe d’actions.

## <a name="manage-your-action-groups"></a>Gérer des groupes d’actions ##
Après avoir créé un groupe d’actions, il est visible dans le **groupes d’actions** section de la **moniteur** volet. Sélectionnez le groupe d’actions que vous souhaitez gérer pour :

* Ajouter, modifier ou supprimer des actions.
* Supprimer le groupe d’actions.

## <a name="action-specific-information"></a>Informations spécifiques sur l’action
> [!NOTE]
> Consultez [limites de Service d’abonnement pour la surveillance](https://docs.microsoft.com/azure/azure-subscription-service-limits#monitor-limits) pour connaître les limites numériques sur chacun des éléments ci-dessous.  

**Application Azure Push** -vous pouvez avoir un nombre limité d’actions d’application Azure dans un groupe d’actions. À ce stade, l’action d’application Azure prend uniquement en charge les alertes ServiceHealth. N’importe quel autre type d’alerte sera ignoré. Consultez [Configuration d’alertes lorsqu’une notification d’intégrité de service est publiée](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

**E-mail** : les e-mails seront envoyés à partir des adresses e-mail suivantes. Vérifiez que le filtrage e-mail est correctement configuré
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Peut avoir un nombre limité d’actions de messagerie dans un groupe d’actions. Consultez l’article [Informations de limitation du débit](./../../azure-monitor/platform/alerts-rate-limiting.md)

**ITSM** -avoir un nombre limité nombre limité d’actions ITSM dans un groupe d’actions. ITSM Action requiert une connexion ITSM. Découvrez comment créer une [connexion ITSM](../../azure-monitor/platform/itsmc-overview.md).

**Application logique** -avoir un nombre limité d’actions d’application logique dans un groupe d’actions.

**Function App** -clés de la fonction pour les applications de fonction configurés en tant que les actions sont lues via l’API de fonctions, qui exige actuellement que les applications de fonction de v2 pour configurer le paramètre « AzureWebJobsSecretStorageType » d’application pour « fichiers ». Pour plus d’informations, consultez [modifications apportées à la gestion de clés dans les fonctions V2]( https://aka.ms/funcsecrets).

**Runbook** -avoir un nombre limité d’actions de Runbook dans un groupe d’actions. Reportez-vous à la [limites de service d’abonnement Azure](../../azure-subscription-service-limits.md) pour connaître les limites sur les charges utiles de Runbook.

**SMS** -avoir un nombre limité d’actions de SMS dans un groupe d’actions. Consultez également le [informations de limitation du débit](./../../azure-monitor/platform/alerts-rate-limiting.md) et [comportement des alertes SMS](../../azure-monitor/platform/alerts-sms-behavior.md) pour plus d’informations. 

**Voix** -avoir un nombre limité d’actions de Voice dans un groupe d’actions. Consultez le [informations de limitation du débit](./../../azure-monitor/platform/alerts-rate-limiting.md) article.

**Webhook** -avoir un nombre limité d’actions de Webhook dans un groupe d’actions. Webhooks sont retentées utilisant les règles suivantes. L’appel de webhook est retentée un maximum de 2 fois lorsque les codes d’état HTTP suivants sont retournés : 408, 429, 503, 504 ou que le point de terminaison HTTP ne répond pas. La première nouvelle tentative se produit après 10 secondes. La deuxième nouvelle tentative se produit après 100 secondes. Après deux échecs, aucun groupe d’actions n’appellera le point de terminaison pendant 30 minutes. 

Plage d’adresses IP sources
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Pour recevoir des mises à jour sur les modifications apportées à ces adresses IP, nous vous recommandons configurer une [Service alerte d’intégrité, qui surveille pour les notifications d’information sur le service de groupes d’actions.


## <a name="next-steps"></a>Étapes suivantes ##

* En savoir plus sur le [comportement des alertes SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* [Comprendre le schéma Webhook des alertes du journal d’activité](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* En savoir plus sur [ITSM Connector](../../azure-monitor/platform/itsmc-overview.md)
* En savoir plus sur la [limitation de la fréquence](../../azure-monitor/platform/alerts-rate-limiting.md) des alertes.
* Obtenir une [vue d’ensemble des alertes du journal d’activité](../../azure-monitor/platform/alerts-overview.md) et découvrir comment recevoir des alertes.  
* Découvrir comment [configurer des alertes lorsqu’une notification d’intégrité de service est publiée](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

