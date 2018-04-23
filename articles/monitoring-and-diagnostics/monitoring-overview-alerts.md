---
title: Vue d’ensemble des alertes classiques dans Microsoft Azure et Azure Monitor | Microsoft Docs
description: Les alertes vous permettent d’analyser des mesures de ressources, événements ou journaux Azure, puis d’envoyer des alertes quand une condition spécifiée est remplie.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: robb
ms.openlocfilehash: 06ba05f71cf1f696033099c04448526a0421ad42
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Que sont les alertes classiques dans Microsoft Azure ?

> [!NOTE]
> Cet article décrit comment créer des alertes de métriques classiques plus anciennes. Azure Monitor prend désormais en charge les [alertes de métriques en temps quasi réel plus récentes](monitoring-overview-unified-alerts.md).
>

Cet article décrit les différentes sources d’alertes dans Microsoft Azure, les objectifs et avantages de ces alertes, ainsi que leur utilisation. Il s’applique particulièrement aux alertes classiques Azure Monitor. Dans Azure, les alertes constituent une méthode de surveillance. Elles permettent de définir des conditions à propos de données et d’être informé lorsque les dernières données de surveillance répondent à ces conditions.


## <a name="taxonomy-of-azure-monitor-classic-alerts"></a>Taxonomie des alertes classiques Azure Monitor
Azure utilise les termes suivants pour décrire les alertes classiques et leurs fonctions :
* **Alerte** : définition de critères (une ou plusieurs règles ou conditions) qui est activée lorsque les critères sont remplis.
* **Actif** : état indiquant que les critères définis par une alerte classique sont remplis.
* **Résolu** : état indiquant que les critères définis par une alerte classique ne sont plus remplis après l’avoir été.
* **Notification** : action entreprise lorsqu’une alerte classique devient active.
* **Action** : appel émis vers le destinataire d’une notification (par exemple, en envoyant un e-mail ou en publiant une URL Webhook). Les notifications peuvent généralement déclencher plusieurs actions.


## <a name="alerts-on-azure-monitor-data"></a>Alertes pour des données Azure Monitor
Il existe trois types d’alertes de données disponibles dans Azure Monitor : les alertes métriques, les alertes métriques quasiment en temps réel et les alertes du journal d’activité.

* **Alertes de métrique classiques** : ces alertes se déclenchent quand la valeur d’une métrique spécifiée dépasse le seuil défini. Ces alertes génèrent une notification lorsqu’elles sont activées (quand le seuil est atteint et que la condition d’alerte est remplie) et lorsqu’elles sont résolues (quand le seuil est de nouveau atteint et que la condition n’est plus remplie). Il s’agit des alertes métriques les plus anciennes. Pour les alertes métriques plus récentes, voir ci-dessous.

* **Alertes métriques en temps quasi réel** (expérience d’alertes plus récentes) : il s’agit de la nouvelle génération d’alertes métriques avec des fonctionnalités améliorées par rapport aux alertes métriques précédentes. Ces alertes peuvent être exécutées à une fréquence de 1 minute. Elles prennent également en charge l’analyse de plusieurs métriques (actuellement deux).  Ces alertes génèrent une notification lorsqu’elles sont activées (quand les seuils de toutes les métriques sont atteints en même temps et que la condition d’alerte est remplie) et lorsqu’elles sont résolues (quand le seuil d’au moins une métrique est de nouveau atteint et que la condition n’est plus remplie).

* **Alertes classiques du journal d’activité** : alertes du journal de streaming qui sont déclenchées lorsqu’un événement du journal d’activité généré répond aux critères de filtre que vous avez définis. Ces alertes ne peuvent être qu’à l’état actif, puisque le moteur d’alerte ne fait qu’appliquer les critères de filtre aux nouveaux événements. Ces alertes peuvent être utilisées pour être informé lorsqu’un nouvel incident d’état du service se produit ou lorsqu’un utilisateur ou une application effectue une opération dans votre abonnement (par exemple, « Supprimer la machine virtuelle »).

Pour les données du journal de diagnostic disponible dans Azure Monitor, nous vous suggérons de router les données dans Log Analytics et d’utiliser une alerte Log Analytics. Le diagramme suivant récapitule les sources de données d’Azure Monitor, et explique comment créer des alertes à partir de ces données.

![Alertes expliquées](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-classic-alert"></a>Comment faire pour recevoir une notification concernant une alerte classique Azure Monitor ?
Auparavant, les alertes Azure des différents services utilisaient leurs propres méthodes de notification intégrées. À présent, Azure Monitor propose un regroupement de notifications réutilisable appelé « groupe d’actions ». Les groupes d’actions spécifient un ensemble de destinataires pour une notification (autant d’adresses e-mail, de numéros de téléphone (pour SMS) ou d’URL Webhook que souhaité), et dès qu’une alerte faisant référence au groupe d’actions est activée, tous les destinataires reçoivent la notification. Vous pouvez ainsi réutiliser un groupe de destinataires (par exemple, votre liste d’ingénieurs techniques) pour plusieurs objets d’alerte. Pour le moment, seules les alertes du journal d’activité utilisent les groupes d’actions. Toutefois, il est prévu que d’autres types d’alertes Azure utilisent ces groupes d’actions.

Les groupes d’actions prennent en charge les notifications par publication d’URL Webhook, en plus des notifications par adresse e-mail et numéro de téléphone. Vous pouvez ainsi utiliser l’automatisation et la correction avec, par exemple :
    - Runbook Azure Automation
    - Fonction Azure
    - Application logique Azure
    - Un service tiers

Les alertes métriques quasiment en temps réel et les alertes de journal d’activité utilisent des groupes d’actions.

Les anciennes alertes métriques disponibles sous Alertes (classique) n’utilisent pas les groupes d’actions. Dans une alerte de métrique, vous pouvez configurer des notifications pour :
* envoyer des notifications par courrier électronique à l’administrateur de service, aux coadministrateurs ou aux adresses e-mail supplémentaires que vous spécifiez ;
* appeler un webhook, qui permet de lancer des actions d’automatisation supplémentaires.

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
* En savoir plus sur les [dernières alertes métriques](monitoring-near-real-time-metric-alerts.md)
* Apprenez-en plus sur les [notifications de service](monitoring-service-notifications.md)
* En savoir plus sur les [groupes d’actions](monitoring-action-groups.md)
* Configurer [Alertes](monitor-alerts-unified-usage.md)
