---
title: Créer et gérer des groupes d’action sur le Portail Azure | Microsoft Docs
description: Découvrez comment créer et gérer des groupes d’action sur le Portail Azure.
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: dukek
ms.openlocfilehash: a1f163acea4e1965ab90b32e23e502b13f8908be
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Créer et gérer des groupes d’actions sur le Portail Azure
## <a name="overview"></a>Vue d'ensemble ##
Cet article vous montre comment créer et gérer des groupes d’actions dans le portail Azure.

Vous pouvez configurer une liste d’actions avec des groupes d’actions. Ces groupes peuvent être réutilisés par chaque alerte que vous définissez, afin que les mêmes actions soient entreprises chaque fois qu’une alerte se déclenche.

Chaque action se compose des propriétés suivantes :

* **Nom** : identificateur unique au sein du groupe d’actions.  
* **Type d’action** : envoyer un appel vocal ou un SMS, envoyer un courrier électronique, appeler un webhook, envoyer des données à un outil ITSM, appeler une application logique, envoyer une notification Push vers l’application Azure ou exécuter un runbook Automation.
* **Détails** : numéro de téléphone, adresse e-mail ou URI de Webhook ou informations de connexion ITSM correspondants.

Pour plus d’informations sur l’utilisation de modèles Azure Resource Manager pour configurer des groupes d’actions, consultez la page [Modèles Resource Manager de groupes d’actions](monitoring-create-action-group-with-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Créer un groupe d’actions à l’aide du Portail Azure ##
1. Sur le [Portail](https://portal.azure.com), sélectionnez **Moniteur**. Le panneau **Moniteur** consolide tous vos paramètres et données de monitorage sur un même affichage.

    ![Le service « Moniteur »](./media/monitoring-action-groups/home-monitor.png)
2. Dans la section **Paramètres**, sélectionnez **Groupes d’actions**.

    ![L’onglet « Groupes d’actions »](./media/monitoring-action-groups/action-groups-blade.png)
3. Sélectionnez **Ajouter un groupe d’actions** et renseignez les champs.

    ![La commande « Ajouter un groupe d’actions »](./media/monitoring-action-groups/add-action-group.png)
4. Entrez un nom dans la zone **Nom du groupe d’actions** et un autre dans la zone **Nom court**. Le nom court est utilisé à la place du nom complet du groupe d’actions lorsque les notifications sont envoyées à l’aide de ce groupe.

      ![La boîte de dialogue « Ajouter un groupe d’actions »](./media/monitoring-action-groups/action-group-define.png)

5. La zone **Abonnement** est automatiquement renseignée avec votre abonnement actuel. Cet abonnement est celui dans lequel est enregistré le groupe d’actions.

6. Sélectionnez le **Groupe de ressources** dans lequel le groupe d’actions est enregistré.

7. Définissez une liste d’actions en indiquant les éléments suivants pour chaque action :

    a. **Nom** : entrez un identificateur unique pour cette action.

    b. **Type d’action** : sélectionnez E-mail, SMS, Push, Voix, application logique, Webhook, ITSM ou Runbook Automation.

    c. **Détails** : selon le type d’action, saisissez un numéro de téléphone, une adresse e-mail, un URI de webhook, une application Azure, une connexion ITSM ou un runbook Automation. Pour ITSM Action, spécifiez l’**élément de travail** et les autres champs nécessaires à votre outil ITSM.

8. Sélectionnez **OK** pour créer le groupe d’actions.

## <a name="action-specific-information"></a>Informations spécifiques sur l’action
<dl>
<dt>Envoi de l’application Azure</dt>
<dd>Vous pouvez avoir jusqu’à 10 actions d’application Azure dans un groupe d’actions.</dd>
<dd>À ce stade, l’action d’application Azure prend uniquement en charge des alertes ServiceHealth. Tout autre moment d’alerte sera ignoré. Consultez [Configuration d’alertes lorsqu’une notification d’intégrité de service est publiée](monitoring-activity-log-alerts-on-service-notifications.md).</dd>

<dt>E-mail</dt>
<dd>Vous pouvez avoir jusqu’à 50 actions d’e-mail dans un groupe d’actions</dd>
<dd>Consultez l’article [Informations de limitation du débit](./monitoring-alerts-rate-limiting.md)</dd>

<dt>ITSM</dt>
<dd>Vous pouvez avoir jusqu’à 10 actions ITSM dans un groupe d’actions</dd>
<dd>ITSM Action requiert une connexion ITSM. Découvrez comment créer une [connexion ITSM](../log-analytics/log-analytics-itsmc-overview.md).</dd>

<dt>Application logique</dt>
<dd>Vous pouvez avoir jusqu’à 10 actions d’application logique dans un groupe d’actions</dd>

<dt>Runbook</dt>
<dd>Vous pouvez avoir jusqu’à 10 actions de runbook dans un groupe d’actions</dd>

<dt>SMS</dt>
<dd>Vous pouvez avoir jusqu’à 10 actions de SMS dans un groupe d’actions</dd>
<dd>Consultez l’article [Informations de limitation du débit](./monitoring-alerts-rate-limiting.md)</dd>
<dd>Consultez l’article [Comportement des alertes SMS](monitoring-sms-alert-behavior.md)</dd>

<dt>Voice</dt>
<dd>Vous pouvez avoir jusqu’à 10 actions de Voice dans un groupe d’actions</dd>
<dd>Consultez l’article [Informations de limitation du débit](./monitoring-alerts-rate-limiting.md)</dd>

<dt>Webhook</dt>
<dd>Vous pouvez avoir jusqu’à 10 actions de Webhook dans un groupe d’actions
<dd>Logique de nouvelle tentative : l’appel de webhook sera retenté 2 fois au maximum lorsque les codes d’état HTTP suivants sont renvoyés (408, 429, 503, 504) ou quand le point de terminaison HTTP ne répond pas. La première nouvelle tentative se produit après 10 secondes. La deuxième et la dernière nouvelles tentatives se produisent après 100 secondes.</dd>
</dl>

## <a name="manage-your-action-groups"></a>Gérer des groupes d’actions ##
Après sa création, un groupe d’actions apparaît dans la section **Groupes d’actions** du panneau **Moniteur**. Sélectionnez le groupe d’actions que vous souhaitez gérer pour :

* Ajouter, modifier ou supprimer des actions.
* Supprimer le groupe d’actions.

## <a name="next-steps"></a>Étapes suivantes ##
* En savoir plus sur le [comportement des alertes SMS](monitoring-sms-alert-behavior.md).  
* [Comprendre le schéma Webhook des alertes du journal d’activité](monitoring-activity-log-alerts-webhook.md).  
* En savoir plus sur [ITSM Connector](../log-analytics/log-analytics-itsmc-overview.md)
* En savoir plus sur la [limitation de la fréquence](monitoring-alerts-rate-limiting.md) des alertes.
* Obtenir une [vue d’ensemble des alertes du journal d’activité](monitoring-overview-alerts.md) et découvrir comment recevoir des alertes.  
* Découvrir comment [configurer des alertes lorsqu’une notification d’intégrité de service est publiée](monitoring-activity-log-alerts-on-service-notifications.md).
