---
title: Exporter les alertes et recommandations d’Azure Security Center vers les SIEM | Microsoft Docs
description: Cet article explique comment configurer l’exportation continue des alertes et recommandations de sécurité vers les SIEM
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: e12fc5d92cfc850e1d049bc11286c0c863e718b0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459189"
---
# <a name="export-security-alerts-and-recommendations-preview"></a>Exporter les alertes et recommandations de sécurité (préversion)

Azure Security Center génère des alertes et recommandations de sécurité détaillées. Vous pouvez les afficher dans le portail ou au moyen d’outils de programmation. Si nécessaire, vous pouvez également exporter ces informations ou les envoyer à d’autres outils de supervision dans votre environnement. 

Cet article décrit l’ensemble des outils (en préversion) qui sont à votre disposition pour exporter les alertes et les recommandations manuellement, ou automatiquement de manière continue.

À l’aide de ces outils, vous pouvez :

* Générer des rapports détaillés au format CSV
* Exporter les données vers des espaces de travail Log Analytics
* Exporter les données vers Azure Event Hubs (en vue de l’intégration à des SIEM tiers)

## <a name="setting-up-a-continuous-export"></a>Configuration d’une exportation continue

1. Dans la barre latérale de Security Center, cliquez sur **Tarification et paramètres**.

1. Sélectionnez l’abonnement pour lequel vous souhaitez configurer l’exportation de données.
    
1. Dans la barre latérale de la page de paramètres de cet abonnement, sélectionnez **Exportation continue (préversion)** .

    [![Options d’exportation dans Azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) Vous voyez ici toutes les options d’exportation. Il y a un onglet distinct pour chaque cible d’exportation disponible. 

1. Sélectionnez le type de données que vous souhaitez exporter et choisissez les filtres à appliquer sur chaque type (par exemple, exporter uniquement les alertes avec un niveau de gravité élevé).

1. À partir de la zone « Cible d’exportation », choisissez l’emplacement où enregistrer les données. Les données peuvent être enregistrées à un emplacement cible dans un abonnement différent (par exemple, sur une instance Event Hub centrale ou un espace de travail Log Analytics central).

1. Cliquez sur **Enregistrer**.

## <a name="continuous-export-through-azure-event-hubs"></a>Exportation continue avec Azure Event Hubs  

> [!NOTE]
> Dans la plupart des cas, utiliser Azure Event Hubs est la meilleure méthode de streaming des données de supervision vers des outils externes. [Cet article](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) fournit une brève description de la façon dont vous pouvez envoyer en streaming des données de supervision à partir de différentes sources vers un hub d’événements, ainsi que des liens vers des instructions détaillées.

> [!NOTE]
> Si vous avez précédemment exporté des alertes Security Center vers un SIEM à partir du journal d’activité Azure, vous pouvez utiliser la procédure ci-dessous à la place de cette méthode.

Pour afficher les schémas d’événements des types de données exportés, visitez les [schémas d’événements Event Hub](https://aka.ms/ASCAutomationSchemas).

### <a name="to-integrate-with-a-siem"></a>Pour intégrer les données à un SIEM 

Après avoir configuré l’exportation continue des données de Security Center que vous avez choisies vers Azure Event Hubs, vous pouvez configurer le connecteur approprié sur votre SIEM en suivant les instructions ci-dessous.

Suivez les instructions propres à votre SIEM fournies dans [cette page](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/?cdn=disable) et utilisez le connecteur approprié :

* **Splunk** : utilisez le [module complémentaire Azure Monitor pour Splunk](https://splunkbase.splunk.com/app/3534/)
* **IBM QRadar** : utilisez une [source de journal configurée manuellement](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_logsource_microsoft_azure_event_hubs.html)
* **ArcSight** : utilisez [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Avec **Azure Sentinel**, utilisez le [connecteur de données](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) des alertes natives d’Azure Security Center mis à disposition.

Par ailleurs, si vous voulez que les données exportées en continu à partir de votre hub d’événements configuré soient automatiquement déplacées vers Azure Data Explorer, suivez les instructions détaillées dans [Ingérer des données Event Hub dans Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).


## <a name="continuous-export-to-a-log-analytics-workspace"></a>Exportation continue vers un espace de travail Log Analytics

Pour exporter les données vers un espace de travail Log Analytics, assurez-vous que les solutions Log Analytics avec le niveau tarifaire Gratuit ou Standard de Security Center sont activées sur votre espace de travail. Si vous utilisez le portail Azure, la solution avec le niveau Gratuit de Security Center est automatiquement activée lorsque vous activez l’exportation continue. En revanche, si vous configurez vos paramètres d’exportation continue programmatiquement, vous devez sélectionner manuellement le niveau tarifaire Gratuit ou Standard pour l’espace de travail requis sous **Tarification et paramètres**.  

### <a name="log-analytics-tables-and-schemas"></a>Tables et schémas Log Analytics

Les alertes et les recommandations de sécurité sont stockées dans les tables *SecurityAlert* et *SecurityRecommendations* respectivement. Le nom de la solution Log Analytics contenant ces tables varie selon que vous êtes sur le niveau Gratuit ou Standard (voir les [tarifs](security-center-pricing.md)) : Security('Security and Audit') ou SecurityCenterFree.

![Table *SecurityAlert* dans Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Pour afficher les schémas d’événements des types de données exportés, visitez les [schémas de table Log Analytics](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Voir les alertes de sécurité exportées et les recommandations les concernant dans Azure Monitor

Dans certains cas, vous pouvez choisir de voir les alertes de sécurité exportées et/ou les recommandations dans [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

Azure Monitor fournit une expérience d’alerte unifiée pour diverses alertes Azure, dont le Journal de diagnostic, les Alertes de métriques et les alertes personnalisées basées sur des requêtes d’espace de travail Log Analytics.

Pour voir les alertes et les recommandations à partir de Security Center dans Azure Monitor, configurez une règle d’alerte en fonction des requêtes Log Analytics (Alerte de journal) :

1. Dans la page **Alertes** d’Azure Monitor, cliquez sur **Nouvelle règle d’alerte**.

    ![Page Alertes d’Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Dans la page Créer une règle, configurez votre nouvelle règle (de la même façon que vous configurez une [règle d’alerte de journal dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)) :

    * Pour **Ressource**, sélectionnez l’espace de travail Log Analytics vers lequel vous avez exporté des alertes de sécurité et des recommandations.

    * Pour **Condition**, sélectionnez **Recherche personnalisée dans les journaux**. Dans la page qui s’affiche, configurez la requête, la période de recherche arrière et la période de fréquence. Dans la requête de recherche, vous pouvez taper *SecurityAlert* ou *SecurityRecommendation* pour interroger les types de données vers lequel Security Center exporte en continu quand vous activez la fonctionnalité d’exportation continue vers Log Analytics. 
    
    * Vous pouvez éventuellement configurer le [Groupe d’actions](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) que vous souhaitez déclencher. Les groupes d’actions peuvent déclencher l’envoi d’e-mails, des tickets ITSM, des webhooks, et plus encore.
    ![Règle d’alerte Azure Monitor](./media/continuous-export/azure-monitor-alert-rule.png)

Vous voyez maintenant de nouvelles alertes ou recommandations Azure Security Center (en fonction de votre configuration) dans les alertes Azure Monitor, avec un déclenchement automatique d’un groupe d’actions (le cas échéant).

## <a name="manual-one-time-export-of-security-alerts"></a>Exportation ponctuelle et manuelle des alertes de sécurité

Pour télécharger un rapport CSV des alertes ou des recommandations, ouvrez la page **Alertes de sécurité** ou **Recommandations**, puis cliquez sur le bouton **Télécharger le rapport au format CSV (préversion)** .

[![Télécharger les données d’alertes dans un fichier CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Ces rapports contiennent les alertes et les recommandations générées pour les ressources incluses dans les abonnements actuellement sélectionnés.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à configurer des exportations continues de vos alertes et recommandations. Vous avez également appris à télécharger vos données d’alertes dans un fichier CSV. 

Pour des informations connexes, consultez la documentation suivante : 

- [Documentation Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Documentation Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Documentation Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- [Automatisation de flux de travail et schémas de types de données d’exportation continue](https://aka.ms/ASCAutomationSchemas)
