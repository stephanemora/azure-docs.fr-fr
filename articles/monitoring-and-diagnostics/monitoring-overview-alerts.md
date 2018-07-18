---
title: Vue d’ensemble des alertes classiques dans Microsoft Azure et Azure Monitor
description: Les alertes vous permettent d’analyser des mesures de ressources, événements ou journaux Azure, puis d’envoyer des alertes quand une condition spécifiée est remplie.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 89c3975a1212aa991e42e0cce4fe5521b53bd373
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263668"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Que sont les alertes classiques dans Microsoft Azure ?

> [!NOTE]
> Cet article décrit comment créer des alertes de métriques classiques plus anciennes. Azure Monitor prend désormais en charge de [nouvelles alertes de métrique quasi en temps réel et une nouvelle expérience d’alertes](monitoring-overview-unified-alerts.md). 
>

Azure vous permet de définir des conditions sur des données et d’être informé quand les dernières données de monitoring répondent à ces conditions.


## <a name="alerts-on-azure-monitor-data"></a>Alertes pour des données Azure Monitor
Deux types d’alertes classiques sont disponibles : les alertes de métriques et les alertes de journal d’activité.

* **Alertes de métrique classiques** : ces alertes se déclenchent quand la valeur d’une métrique spécifiée dépasse le seuil défini. Ces alertes génèrent une notification lorsqu’elles sont activées (quand le seuil est atteint et que la condition d’alerte est remplie) et lorsqu’elles sont résolues (quand le seuil est de nouveau atteint et que la condition n’est plus remplie). Pour les alertes métriques plus récentes, voir ci-dessous.

* **Alertes classiques du journal d’activité** : alertes du journal de streaming qui sont déclenchées lorsqu’un événement du journal d’activité généré répond aux critères de filtre que vous avez définis. Ces alertes ne peuvent être qu’à l’état actif, puisque le moteur d’alerte ne fait qu’appliquer les critères de filtre aux nouveaux événements. Ces alertes peuvent être utilisées pour être informé lorsqu’un nouvel incident d’état du service se produit ou lorsqu’un utilisateur ou une application effectue une opération dans votre abonnement (par exemple, « Supprimer la machine virtuelle »).

Pour les données du journal de diagnostic disponibles dans Azure Monitor, nous vous suggérons de router les données dans Log Analytics (anciennement OMS) et d’utiliser une alerte de requête Log Analytics. Log Analytics utilise maintenant la [nouvelle méthode d’alerte](monitoring-overview-unified-alerts.md) 

Le diagramme suivant récapitule les sources de données d’Azure Monitor, et explique comment créer des alertes à partir de ces données.

![Alertes expliquées](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Taxonomie des alertes Azure Monitor (classiques)
Azure utilise les termes suivants pour décrire les alertes classiques et leurs fonctions :
* **Alerte** : définition de critères (une ou plusieurs règles ou conditions) qui est activée lorsque les critères sont remplis.
* **Actif** : état indiquant que les critères définis par une alerte classique sont remplis.
* **Résolu** : état indiquant que les critères définis par une alerte classique ne sont plus remplis après l’avoir été.
* **Notification** : action entreprise quand une alerte classique devient active.
* **Action** : appel émis vers le destinataire d’une notification (par exemple, en envoyant un e-mail ou en publiant une URL Webhook). Les notifications peuvent généralement déclencher plusieurs actions.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Comment recevoir une notification d’une alerte classique Azure Monitor ?
Auparavant, les alertes Azure des différents services utilisaient leurs propres méthodes de notification intégrées. 

À présent, Azure Monitor propose un regroupement de notifications réutilisable appelé *groupe d’actions*. Les groupes d’actions spécifient un ensemble de destinataires pour une notification, et dès qu’une alerte faisant référence au groupe d’actions est activée, tous les destinataires reçoivent cette notification. Vous pouvez ainsi réutiliser un groupe de destinataires (par exemple, votre liste d’ingénieurs techniques) pour plusieurs objets d’alerte. Les groupes d’actions prennent en charge les notifications via la publication d’une URL de webhook, en plus des notifications par adresse e-mail, numéro de SMS et plusieurs autres actions.  Pour plus d’informations, consultez [Groupes d’actions](monitoring-action-groups.md). 

Les alertes de journal d’activité classiques plus anciennes utilisent des groupes d’actions.

Toutefois, les anciennes alertes de métriques n’utilisent pas les groupes d’actions. Au lieu de cela, vous pouvez configurer les actions suivantes : 
* envoyer des notifications par courrier électronique à l’administrateur de service, aux coadministrateurs ou aux adresses e-mail supplémentaires que vous spécifiez ;
* appeler un webhook, qui permet de lancer des actions d’automatisation supplémentaires.

Les webhooks autorisent l’automatisation et la correction avec, par exemple :
    - Runbook Azure Automation
    - Fonction Azure
    - Application logique Azure
    - Un service tiers

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez obtenir des informations sur les règles d’alerte et leur configuration avec :

* En savoir plus sur les [métriques](monitoring-overview-metrics.md)
* Configurer les [alertes métriques classiques via le Portail Azure](insights-alerts-portal.md)
* Configurer les [alertes métriques classiques PowerShell](insights-alerts-powershell.md)
* Configurer [l’interface de ligne de commande (CLI) des alertes métriques classiques](insights-alerts-command-line-interface.md)
* Configurer les [l’API REST Azure Monitor des alertes métriques](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* En savoir plus sur les [journaux d’activité](monitoring-overview-activity-logs.md)
* Configurer les [alertes de journal d’activité par le biais du portail Azure](monitoring-activity-log-alerts.md)
* Configurer les [alertes de journal d’activité au moyen de Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Consulter le [schéma de webhook d’alerte de journal d’activité](monitoring-activity-log-alerts-webhook.md)
* En savoir plus sur les [groupes d’actions](monitoring-action-groups.md)
* Configurer des [alertes plus récentes](monitor-alerts-unified-usage.md)
