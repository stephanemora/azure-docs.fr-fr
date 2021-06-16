---
title: Notifications préalables (préversion) pour les événements de maintenance planifiée
description: Recevez une notification avant la maintenance planifiée pour Azure SQL Database.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: scott-kim-sql
ms.author: scottkim
ms.reviewer: mathoma
ms.date: 03/02/2021
ms.openlocfilehash: 1b79d9f88c61ca4bc11895e26f8ffc4fd69b8a35
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110706568"
---
# <a name="advance-notifications-for-planned-maintenance-events-preview"></a>Notifications préalables pour les événements de maintenance planifiée (préversion)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Les notifications préalables (préversion) sont disponibles pour les bases de données configurées pour la [fenêtre de maintenance (préversion)](maintenance-window.md). Les notifications préalables permettent aux clients de configurer des notifications à envoyer jusqu’à 24 heures à l’avance de tout événement planifié.

Les notifications peuvent être configurées de sorte que vous puissiez recevoir des textes, des e-mails, des notifications push Azure et des messages vocaux lorsque le démarrage de la maintenance planifiée est prévu dans les 24 heures suivantes. Des notifications supplémentaires sont envoyées au début et à la fin de la maintenance.

> [!Note]
> Bien qu’il soit possible de choisir une fenêtre de maintenance pour les instances managées Azure SQL, les notifications préalables ne sont actuellement pas disponibles pour les instances managées Azure SQL.

## <a name="create-an-advance-notification"></a>Créer une notification préalable

Les notifications préalables sont disponibles pour les bases de données Azure SQL dont la fenêtre de maintenance est configurée. 

Effectuez les étapes suivantes pour activer une notification.  

1. Accédez à la page [Maintenance planifiée](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance), sélectionnez **Alertes d’intégrité**, puis **Ajouter une alerte d’intégrité de service**.

    :::image type="content" source="media/advance-notifications/health-alerts.png" alt-text="option de menu pour créer une alerte d’intégrité":::

2. Dans la section **Actions**, sélectionnez **Ajouter des groupes d’actions**. 

    :::image type="content" source="media/advance-notifications/add-action-group.png" alt-text="option de menu pour ajouter un groupe d’actions":::

3. Complétez le formulaire **Créer un groupe d’actions**, puis sélectionnez **Suivant : Notifications**.  

    :::image type="content" source="media/advance-notifications/create-action-group.png" alt-text="formulaire pour créer un groupe d’actions":::

1. Sous l’onglet **Notifications** , sélectionnez le **Type de notification**. L’option **E-mail/SMS/Push/Voix** offre la plus grande souplesse et est l’option recommandée. Sélectionnez le stylet pour configurer la notification.  

    :::image type="content" source="media/advance-notifications/notifications.png" alt-text="configurer les notifications":::



   1. Remplissez le formulaire *Ajouter ou modifier une notification* qui s’ouvre et sélectionnez **OK** : 

   2. Les actions et les étiquettes sont facultatives. Ici, vous pouvez configurer des actions supplémentaires à déclencher ou utiliser des étiquettes pour classer et organiser vos ressources Azure. 

   4. Vérifiez les détails sous l’onglet **Vérifier + créer**, puis sélectionnez **Créer**. 

7. Une fois l’option Créer sélectionnée, l’écran de configuration de la règle d’alerte s’ouvre et le groupe d’actions est sélectionné. Donnez un nom à votre nouvelle règle d’alerte, choisissez le groupe de ressources pour celle-ci, puis sélectionnez **Créer une règle d’alerte**. 

8. Cliquez à nouveau sur l’élément de menu **Alertes d’intégrité**. La liste des alertes contient maintenant votre nouvelle alerte. 


Vous êtes prêt. La prochaine fois qu’un événement de maintenance Azure SQL sera planifié, vous recevrez une notification préalable.

## <a name="receiving-notifications"></a>Réception des notifications

Le tableau suivant indique les notifications d’informations générales que vous pouvez recevoir : 

|Statut|Description|
|:---|:---|
|**Déploiement planifié**| Reçue 24 heures avant l’événement de maintenance. La maintenance est planifiée le DATE entre 17h00 et 8h00 (heure locale) pour la base de données xyz.|
|**En cours** | La maintenance de la base de données *xyz* est en cours de démarrage.| 
|**Terminé** | La maintenance de la base de données *xyz* est terminée. |

Le tableau suivant contient les notifications supplémentaires qui peuvent être envoyées pendant que la maintenance est en cours : 

|Statut|Description|
|:---|:---|
|**Étendu** | La maintenance est en cours, mais n’est pas terminée pour la base de données *xyz*. La maintenance se poursuivra à la prochaine fenêtre de maintenance.| 
|**Canceled**| La maintenance de la base de données *xyz* est annulée et sera replanifiée ultérieurement. |
|**Bloqué**|Un problème est survenu lors de la maintenance de la base de données *xyz*. Nous vous informerons quand nous reviendrons.| 
|**Repris**|Le problème a été résolu et la maintenance se poursuivra à la prochaine fenêtre de maintenance.|


## <a name="next-steps"></a>Étapes suivantes

- [Fenêtre de maintenance](maintenance-window.md)
- [FAQ sur la fenêtre de maintenance](maintenance-window-faq.yml)
- [Vue d’ensemble des alertes dans Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md)
- [Envoyer un e-mail au rôle Azure Resource Manager](../../azure-monitor/alerts/action-groups.md#email-azure-resource-manager-role)