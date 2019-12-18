---
title: Vue d’ensemble des journaux de plateforme Azure | Microsoft Docs
description: Vue d’ensemble des journaux dans Azure Monitor qui fournissent des données riches et fréquentes sur le fonctionnement d’une ressource Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 480c028f11de9a7c44168b217ad3553d721d01e1
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894561"
---
# <a name="overview-of-azure-platform-logs"></a>Vue d’ensemble des journaux de plateforme Azure
Les journaux de plateforme fournissent des informations de diagnostic et d’audit détaillées pour les ressources Azure et la plateforme Azure dont elles dépendent. Elles sont générées automatiquement, même si vous devez configurer certains journaux de plateforme à transférer vers une ou plusieurs destinations à des fins de rétention. Cet article fournit une vue d’ensemble des journaux de plateforme, y compris les informations fournies et de la façon dont vous pouvez les configurer pour la collecte et l’analyse.

## <a name="types-of-platform-logs"></a>Types de journaux de plateforme
Le tableau suivant répertorie les journaux de plateforme spécifiques disponibles dans différentes couches d’Azure.

| Couche | Journaux d’activité | Description |
|:---|:---|:---|
| Ressources Azure | [Journaux de ressources](resource-logs-overview.md) | Fournissent des insights sur les opérations effectuées au sein d’une ressource Azure (le *plan de données*), par exemple l’obtention d’un secret à partir d’un Key Vault ou l’envoi d’une requête à une base de données. Le contenu des journaux de ressources varie en fonction du service Azure et du type de ressource.<br>*Les journaux de ressources étaient auparavant appelés journaux de diagnostic.*  |
| Abonnement Azure | [Journal d’activité](activity-logs-overview.md) | Fournit des informations sur les opérations effectuées sur chaque ressource Azure dans l’abonnement à partir de l’extérieur (*plan de gestion*) en plus des mises à jour sur des événements Service Health. Il n’y qu’un seul journal d’activité par abonnement Azure.   |
| Locataire Azure | [Journaux Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)  | Contiennent l’historique de l’activité de connexion et la piste d’audit des modifications apportées dans Azure Active Directory pour un locataire particulier.   |


![Vue d’ensemble des journaux de plateforme](media/platform-logs-overview/logs-overview.png)

## <a name="viewing-platform-logs"></a>Affichage des journaux de plateforme
Vous pouvez afficher le [Journal d’activité](activity-log-view.md) et les [journaux Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) dans le portail Azure. Pour afficher les journaux de ressources, vous devez les envoyer à une [destination](#destinations).


## <a name="destinations"></a>Destinations
Vous pouvez envoyer des journaux de plateforme à une ou plusieurs des destinations répertoriées dans le tableau suivant, en fonction de vos besoins d’analyse. 

| Destination | Scénario | Références |
|:---|:---|:---|:---|
| Espace de travail Log Analytics | Analysez les journaux avec d’autres données de surveillance et tirez parti des fonctionnalités d’Azure Monitor, telles que les requêtes et alertes de journal. | [Journaux de ressources](resource-logs-collect-storage.md)<br>[Journal d’activité](activity-log-collect.md)<br>[Journaux Azure Active Directory](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Stockage Azure | Archivez les journaux à des fins d’audit, d’analyse statique ou de sauvegarde. |[Journaux de ressources](archive-diagnostic-logs.md)<br>[Journal d’activité](activity-log-export.md)<br>[Journaux Azure Active Directory](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Event Hub | Diffusez les journaux en continu sur des systèmes de journalisation et de télémétrie tiers.  |[Journaux de ressources](resource-logs-stream-event-hubs.md)<br>[Journal d’activité](activity-log-export.md)<br>[Journaux Azure Active Directory](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |


## <a name="diagnostic-settings-and-log-profiles"></a>Paramètres de diagnostic et profils de journal
Configurez des destinations pour les journaux de ressources et les journaux Azure Active Directory en [créant un paramètre de diagnostic](diagnostic-settings.md). Configurez des destinations pour le journal d’activité en [créant un profil de journal](activity-log-export.md) ou en le [connectant à un espace de travail log Analytics](activity-log-collect.md).

Le paramètre de diagnostic et le profil de journal définissent les éléments suivants :

- Une ou plusieurs destinations auxquelles envoyer les journaux et les métriques sélectionnés.
- Les catégories de journal et les métriques de la ressource sont envoyées aux destinations.
- Si vous sélectionnez un compte de stockage en tant que destination, durée de rétention de chaque catégorie de journal.



## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur le journal d’activité](activity-logs-overview.md)
* [En savoir plus sur les journaux de ressources](resource-logs-overview.md)
* [Afficher le schéma de journal des ressources pour différents services Azure](diagnostic-logs-schema.md)
