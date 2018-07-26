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
ms.openlocfilehash: a0abcbdaa7e998413efb717be6e0addc5607ec5c
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114009"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Que sont les alertes classiques dans Microsoft Azure ?

> [!NOTE]
> Cet article décrit comment créer des alertes de métrique classiques plus anciennes. Azure Monitor prend désormais en charge de [nouvelles alertes de métrique quasi en temps réel et une nouvelle expérience d’alertes](monitoring-overview-unified-alerts.md). 
>

L’utilisation d’alertes vous permet de configurer des conditions sur des données, et d’être informé quand les conditions correspondent aux dernières données de surveillance.


## <a name="alerts-on-azure-monitor-data"></a>Alertes pour des données Azure Monitor
Deux types d’alertes classiques sont disponibles : les alertes de métrique et les alertes de journal d’activité.

* **Alertes de métrique classiques** : ces alertes se déclenchent quand la valeur d’une métrique spécifiée dépasse le seuil que vous définissez. L’alerte génère une notification quand elle est « activée » (quand le seuil est atteint et que la condition d’alerte est remplie). Elle génère également une notification quand elle est « résolue » (quand le seuil n’est plus atteint et que la condition n’est plus remplie). 

* **Alertes de journal d’activité classiques** : cette alerte du journal de diffusion en continu se déclenche quand un événement du journal d’activité généré répond aux critères de filtre que vous avez définis. Ces alertes ne peuvent être qu’à l’état « activé », car le moteur d’alerte ne fait qu’appliquer les critères de filtre aux nouveaux événements. Ces alertes peuvent vous informer quand un nouvel incident d’état du service se produit ou quand un utilisateur ou une application effectue une opération dans votre abonnement (par exemple, « Supprimer la machine virtuelle »).

Pour recevoir les données du journal de diagnostic disponibles via Azure Monitor, acheminez-les vers Log Analytics (anciennement Operations Management Suite) et utilisez une alerte de requête Log Analytics. Log Analytics utilise désormais la [nouvelle méthode d’alerte](monitoring-overview-unified-alerts.md). 

Le diagramme suivant récapitule les sources de données d’Azure Monitor, et suggère comment alerter à partir de ces données.

![Alertes expliquées](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Taxonomie des alertes Azure Monitor (classiques)
Azure utilise les termes suivants pour décrire les alertes classiques et leurs fonctions :
* **Alerte** : définition de critères (une ou plusieurs règles ou conditions) qui est activée lorsque les critères sont remplis.
* **Active** : état qui se produit lorsque les critères définis par une alerte classique sont remplis.
* **Résolue** : état qui se produit lorsque les critères définis par une alerte classique ne sont plus remplis après l’avoir été.
* **Notification** : action effectuée quand une alerte classique devient active.
* **Action** : appel spécifique adressé au destinataire d’une notification (par exemple, par e-mail ou via une publication sur une URL de webhook). Les notifications peuvent généralement déclencher plusieurs actions.

## <a name="how-do-i-receive-notifications-from-an-azure-monitor-classic-alert"></a>Comment recevoir une notification d’une alerte classique Azure Monitor ?
Auparavant, les alertes Azure des différents services utilisaient leurs propres méthodes de notification intégrées. 

À présent, Azure Monitor propose un regroupement de notifications réutilisable appelé *groupe d’actions*. Les groupes d’actions spécifient un ensemble de destinataires pour une notification. Quand une alerte est activée, qui référence le groupe d’actions, tous les destinataires reçoivent la notification. Cette fonctionnalité vous permet de réutiliser un groupe de destinataires (par exemple, votre liste d’ingénieurs techniques) pour plusieurs objets d’alerte. Les groupes d’actions prennent en charge la notification via différentes méthodes. Ces méthodes peuvent être une publication sur une URL de webhook, l’envoi d’e-mails ou de SMS, et plusieurs autres actions. Pour plus d’informations, voir [Créer et gérer des groupes d’actions dans le portail Azure](monitoring-action-groups.md). 

Les alertes de journal d’activité classiques plus anciennes utilisent des groupes d’actions.

En revanche, les alertes de métrique plus anciennes n’utilisent pas de groupes d’actions. Au lieu de cela, vous pouvez configurer les actions suivantes : 
* envoyer des notifications par courrier électronique à l’administrateur de service, aux coadministrateurs ou à des adresses e-mail supplémentaires que vous spécifiez ;
* appeler un webhook, qui permet de lancer des actions d’automatisation supplémentaires.

Webhook permet l’automatisation et la correction, par exemple, à l’aide des services suivants :
- Runbook Azure Automation
- Azure Functions
- Application logique Azure
- Service tiers

## <a name="next-steps"></a>Étapes suivantes
Accédez à des informations sur les règles d’alerte et la manière de les configurer en utilisant la documentation suivante :

* En savoir plus sur les [métriques](monitoring-overview-metrics.md).
* Configurer [des alertes de métrique classiques à l’aide du portail Azure](insights-alerts-portal.md)
* Configurer [des alertes de métrique classiques à l’aide de PowerShell](insights-alerts-powershell.md)
* Configurer [des alertes de métrique classiques à l’aide d’Azure CLI](insights-alerts-command-line-interface.md)
* Configurer [des alertes de métrique classiques à l’aide de l’API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* En savoir plus sur les [Journaux d’activité](monitoring-overview-activity-logs.md)
* Configurer [des alertes de journal d’activité à l’aide du portail Azure](monitoring-activity-log-alerts.md)
* Configurer [des alertes de journal d’activité à l’aide du Gestionnaire des ressources](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Consulter le [schéma de webhook d’alerte de journal d’activité](monitoring-activity-log-alerts-webhook.md)
* En savoir plus sur les [groupes d’actions](monitoring-action-groups.md)
* Configurer [des alertes plus récentes](monitor-alerts-unified-usage.md)
