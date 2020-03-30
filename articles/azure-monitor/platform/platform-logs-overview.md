---
title: Vue d’ensemble des journaux de plateforme Azure | Microsoft Docs
description: Vue d’ensemble des journaux dans Azure Monitor qui fournissent des données riches et fréquentes sur le fonctionnement d’une ressource Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 89de6b3737c8a1e91832aba8f749078806b64e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659318"
---
# <a name="overview-of-azure-platform-logs"></a>Vue d’ensemble des journaux de plateforme Azure
Les journaux de plateforme fournissent des informations de diagnostic et d’audit détaillées pour les ressources Azure et la plateforme Azure dont elles dépendent. Elles sont générées automatiquement, même si vous devez configurer certains journaux de plateforme à transférer vers une ou plusieurs destinations à des fins de rétention. Cet article fournit une vue d’ensemble des journaux de plateforme, y compris les informations fournies et de la façon dont vous pouvez les configurer pour la collecte et l’analyse.

## <a name="types-of-platform-logs"></a>Types de journaux de plateforme
Le tableau suivant répertorie les journaux de plateforme spécifiques disponibles dans différentes couches d’Azure.

| Journal | Couche | Description |
|:---|:---|:---|
| Journaux d’activité de ressources | Ressources Azure | Fournissent des insights sur les opérations effectuées au sein d’une ressource Azure (le *plan de données*), par exemple l’obtention d’un secret à partir d’un Key Vault ou l’envoi d’une requête à une base de données. Le contenu des journaux de ressources varie en fonction du service Azure et du type de ressource.<br><br>*Les journaux de ressources étaient auparavant appelés journaux de diagnostic.*  |
| Journal d’activité | Abonnement Azure | Fournit des informations sur les opérations effectuées sur chaque ressource Azure dans l’abonnement à partir de l’extérieur (*plan de gestion*) en plus des mises à jour sur des événements Service Health. Avec le journal d’activité, répondez aux questions _quoi_, _qui_ et _quand_ concernant toutes les opérations d’écriture (PUT, POST, DELETE) effectuées sur les ressources dans votre abonnement. Vous pouvez également comprendre l’état de l’opération et d’autres propriétés pertinentes.  Il n’y qu’un seul journal d’activité par abonnement Azure. |
| Journaux Azure Active Directory | Locataire Azure |  Contiennent l’historique de l’activité de connexion et la piste d’audit des modifications apportées dans Azure Active Directory pour un locataire particulier. Consultez [Présentation des rapports Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) pour obtenir une description complète des journaux Azure Active Directory.   |

> [!NOTE]
> Le journal d’activité Azure est principalement utilisé pour les activités qui se produisent dans Azure Resource Manager. Il ne suit pas les ressources à l’aide du modèle Classic/RDFE. Certains types de ressources Classic ont un fournisseur de ressources proxy dans Azure Resource Manager (par exemple, Microsoft.ClassicCompute). Si vous interagissez avec un type de ressource Classic par le biais d’Azure Resource Manager à l’aide de ces fournisseurs de ressources proxy, les opérations s’affichent dans le journal d’activité. Si vous interagissez avec un type de ressource Classic en dehors de proxys Azure Resource Manager, vos actions sont uniquement enregistrées dans le journal des opérations. Le journal des opérations est accessible dans une section distincte du portail.

![Vue d’ensemble des journaux de plateforme](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Affichage des journaux de plateforme
Il existe différentes options d’affichage et d’analyse des différents journaux de la plateforme Azure.

- Affichez le journal d’activité dans le portail Azure et accédez aux événements à partir de PowerShell et de l’interface CLI. Consultez [Afficher et récupérer les événements du journal d’activité Azure](activity-log-view.md) pour plus d’informations. 
- Affichez les rapports de sécurité et d’activité Azure Active Directory dans le portail Azure. Consultez [Présentation des rapports Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)  pour plus d’informations.
- Les journaux de ressource sont générés automatiquement par les ressources Azure prises en charge, mais vous ne pouvez les afficher que si vous les envoyez à une [destination](#destinations). 

## <a name="destinations"></a>Destinations
Vous pouvez envoyer des journaux de plateforme à une ou plusieurs des destinations répertoriées dans le tableau suivant, en fonction de vos besoins d’analyse. Configurez des destinations pour les journaux de plateforme en [créant un paramètre de diagnostic](diagnostic-settings.md).

| Destination | Scénario | References |
|:---|:---|:---|:---|
| Espace de travail Log Analytics | Analysez les journaux avec d’autres données de surveillance et tirez parti des fonctionnalités d’Azure Monitor, telles que les requêtes et alertes de journal. | [Journal d’activité et journaux de ressource](resource-logs-collect-workspace.md)<br>[Journaux Azure Active Directory](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Stockage Azure | Archivez les journaux à des fins d’audit, d’analyse statique ou de sauvegarde. |[Journal d’activité et journaux de ressource](archive-diagnostic-logs.md)<br>[Journaux Azure Active Directory](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Event Hub | Diffusez les journaux en continu sur des systèmes de journalisation et de télémétrie tiers.  |[Journal d’activité et journaux de ressource](resource-logs-stream-event-hubs.md)<br>[Journaux Azure Active Directory](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>Étapes suivantes

* [Afficher le schéma du journal d’activité pour différentes catégories](activity-log-schema.md)
* [Afficher le schéma de journal des ressources pour différents services Azure](diagnostic-logs-schema.md)
