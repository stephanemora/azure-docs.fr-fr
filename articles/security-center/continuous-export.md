---
title: Exporter les alertes et recommandations d’Azure Security Center vers les SIEM | Microsoft Docs
description: Cet article explique comment configurer l’exportation continue des alertes et recommandations de sécurité vers les SIEM
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: cf8fdd8d91c035d374277c4752fb761c0c4e72c7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905633"
---
# <a name="export-security-alerts-and-recommendations"></a>Exporter les alertes et recommandations de sécurité

Azure Security Center génère des alertes et recommandations de sécurité détaillées. Vous pouvez les afficher dans le portail ou au moyen d’outils de programmation. Si nécessaire, vous pouvez également exporter ces informations ou les envoyer à d’autres outils de supervision dans votre environnement. 

Cet article décrit l’ensemble des outils qui sont à votre disposition pour exporter les alertes et les recommandations manuellement, ou automatiquement de manière continue.

À l’aide de ces outils, vous pouvez :

* Exporter en continu vers des espaces de travail Log Analytics
* Exporter en continu vers des Azure Event Hubs (pour les intégrations à des solutions SIEM tierces)
* Exporter vers un fichier CSV (opération unique)



## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale (GA)|
|Prix :|Gratuit|
|Rôles et autorisations obligatoires :|**Rôle d’administrateur de sécurité** sur le groupe de ressources (ou **Propriétaire**)<br>Doit également disposer d’autorisations en écriture sur la ressource cible|
|Clouds :|![Oui](./media/icons/yes-icon.png) Clouds commerciaux<br>![Oui](./media/icons/yes-icon.png) Gouvernement des États-Unis<br>![Oui](./media/icons/yes-icon.png) China Gov (vers Event Hub), autres zones Gov|
|||



## <a name="set-up-a-continuous-export"></a>Configurer une exportation continue

Les étapes ci-dessous sont nécessaires si vous configurez une exportation continue vers un espace de travail Log Analytics ou vers Azure Event Hubs.

1. Dans la barre latérale de Security Center, sélectionnez **Tarification et paramètres**.

1. Sélectionnez l’abonnement pour lequel vous souhaitez configurer l’exportation de données.
    
1. Dans la barre latérale de la page de paramètres de cet abonnement, sélectionnez **Exportation continue**.

    [![Options d’exportation dans Azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) Vous voyez ici toutes les options d’exportation. Il y a un onglet distinct pour chaque cible d’exportation disponible. 

1. Sélectionnez le type de données que vous souhaitez exporter, puis choisissez les filtres à appliquer sur chaque type (par exemple, exporter uniquement les alertes d’un niveau de gravité élevé).

1. Éventuellement, si votre sélection inclut l’une de ces quatre recommandations, vous pouvez inclure les résultats de l’évaluation des vulnérabilités :

    - Les résultats de l’évaluation des vulnérabilités sur vos bases de données SQL doivent être corrigés
    - Les résultats de l’évaluation des vulnérabilités de vos serveurs SQL sur des machines doivent être corrigés (préversion)
    - Les vulnérabilités dans les images Azure Container Registry doivent être corrigées (avec Qualys)
    - Les vulnérabilités de vos machines virtuelles doivent être corrigées

    Pour inclure les résultats avec ces recommandations, activez l’option **Intégrer les découvertes de sécurité**.

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Intégrer les découvertes de sécurité dans la configuration de l’exportation continue" :::


1. À partir de la zone « Cible d’exportation », choisissez l’emplacement où enregistrer les données. Les données peuvent être enregistrées à un emplacement cible dans un abonnement différent (par exemple, sur une instance Event Hub centrale ou un espace de travail Log Analytics central).

1. Sélectionnez **Enregistrer**.


## <a name="set-up-continuous-export-via-the-rest-api"></a>Configurer une exportation continue via l’API REST

La fonctionnalité d’exportation continue peut être configurée et gérée via l’[API Automations](https://docs.microsoft.com/rest/api/securitycenter/automations) d’Azure Security Center. Utilisez cette API pour créer ou mettre à jour des automatisations pour l’exportation vers l’une des destinations possibles suivantes :

- Azure Event Hub
- Espace de travail Log Analytics
- Azure Logic Apps 

L’API fournit des fonctionnalités supplémentaires qui ne sont pas disponibles dans le portail Azure, par exemple :

* **Volume supérieur** : l’API vous permet de créer plusieurs configurations d’exportation sur un seul abonnement. La page **Exportation continue** dans l’interface utilisateur du portail du service Security Center ne prend en charge qu’une seule configuration d’exportation par abonnement.

* **Fonctionnalités supplémentaires** : l’API offre des paramètres supplémentaires qui n’apparaissent pas dans l’interface utilisateur. Par exemple, vous pouvez ajouter des balises à votre ressource d’automatisation, ainsi que définir votre exportation sur la base d’un ensemble plus vaste de propriétés d’alerte et de recommandation que celles proposées dans la page **Exportation continue** de l’interface utilisateur du portail du service Security Center.

* **Étendue plus ciblée** : l’API fournit un niveau plus granulaire pour l’étendue de vos configurations d’exportation. Lorsque vous définissez une exportation avec l’API, vous pouvez le faire au niveau du groupe de ressources. Si vous utilisez la page **Exportation continue** dans l’interface utilisateur du portail du service Security Center, vous devez la définir au niveau de l’abonnement.

    > [!TIP]
    > Si vous avez défini plusieurs configurations d’exportation à l’aide de l’API, ou si vous avez utilisé des paramètres uniquement d’API, ces fonctionnalités supplémentaires n’apparaissent pas dans l’interface utilisateur du service Security Center. Au lieu de cela, une bannière s’affiche, qui vous informe que d’autres configurations existent.

Pour plus d’informations sur l’API Automations, consultez la [documentation de l’API REST](https://docs.microsoft.com/rest/api/securitycenter/automations).



## <a name="configure-siem-integration-via-azure-event-hubs"></a>Configurer une intégration SIEM via Azure Event Hubs

Azure Event Hubs est une excellente solution pour la consommation par programmation de toutes les données de streaming. Pour les alertes et recommandations Azure Security Center, il s’agit de la méthode recommandée pour une intégration à une solution SIEM tierce.

> [!NOTE]
> Dans la plupart des cas, utiliser Azure Event Hubs est la meilleure méthode de streaming des données de supervision vers des outils externes. [Cet article](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) fournit une brève description de la façon dont vous pouvez envoyer en streaming des données de supervision à partir de différentes sources vers un hub d’événements, ainsi que des liens vers des instructions détaillées.

> [!NOTE]
> Si vous avez précédemment exporté des alertes Security Center vers un SIEM à partir du journal d’activité Azure, vous pouvez utiliser la procédure ci-dessous à la place de cette méthode.

Pour afficher les schémas d’événements des types de données exportés, visitez les [schémas d’événements Event Hub](https://aka.ms/ASCAutomationSchemas).


### <a name="to-integrate-with-a-siem"></a>Pour intégrer les données à un SIEM 

Après avoir configuré l’exportation continue des données Security Center que vous avez choisies vers Azure Event Hubs, vous pouvez configurer le connecteur approprié pour votre solution SIEM :

* **Azure Sentinel** : utilisez le [connecteur de données](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) des alertes Azure Security Center natives mis à disposition.
* **Splunk** : utilisez le [module complémentaire Azure Monitor pour Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** : utilisez une [source de journal configurée manuellement](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** : utilisez [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Par ailleurs, si vous voulez que les données exportées en continu à partir de votre hub d’événements configuré soient automatiquement déplacées vers Azure Data Explorer, suivez les instructions détaillées dans [Ingérer des données Event Hub dans Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Exportation continue vers un espace de travail Log Analytics

Si vous voulez analyser des données Azure Security Center dans un espace de travail Log Analytics ou utiliser des alertes Azure avec Security Center, configurez l’exportation continue vers votre espace de travail Log Analytics.

Pour exporter vers un espace de travail Log Analytics, les solutions Log Analytics de Security Center doivent être activées sur votre espace de travail. Si vous utilisez le portail Azure, la solution de niveau Gratuit de Security Center est automatiquement activée quand vous activez l’exportation continue. Toutefois, si vous configurez vos paramètres d’exportation continus par programme, vous devez activer ou désactiver manuellement Azure Defender sur la page **Tarification et paramètres**.

### <a name="log-analytics-tables-and-schemas"></a>Tables et schémas Log Analytics

Les alertes et les recommandations de sécurité sont stockées dans les tables *SecurityAlert* et *SecurityRecommendations* respectivement. Le nom de la solution Log Analytics contenant ces tables varie selon que vous avez activé Azure Defender ou non : Security ('Security and Audit') ou SecurityCenterFree.

![Table *SecurityAlert* dans Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Pour afficher les schémas d’événements des types de données exportés, visitez les [schémas de table Log Analytics](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Voir les alertes de sécurité exportées et les recommandations les concernant dans Azure Monitor

Dans certains cas, vous pouvez choisir de voir les alertes de sécurité exportées et/ou les recommandations dans [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

Azure Monitor fournit une expérience d’alerte unifiée pour diverses alertes Azure, dont le Journal de diagnostic, les Alertes de métriques et les alertes personnalisées basées sur des requêtes d’espace de travail Log Analytics.

Pour voir les alertes et les recommandations à partir de Security Center dans Azure Monitor, configurez une règle d’alerte en fonction des requêtes Log Analytics (Alerte de journal) :

1. Dans la page **Alertes** d’Azure Monitor, sélectionnez **Nouvelle règle d’alerte**.

    ![Page Alertes d’Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Dans la page Créer une règle, configurez votre nouvelle règle (de la même façon que vous configurez une [règle d’alerte de journal dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)) :

    * Pour **Ressource**, sélectionnez l’espace de travail Log Analytics vers lequel vous avez exporté des alertes de sécurité et des recommandations.

    * Pour **Condition**, sélectionnez **Recherche personnalisée dans les journaux**. Dans la page qui s’affiche, configurez la requête, la période de recherche arrière et la période de fréquence. Dans la requête de recherche, vous pouvez taper *SecurityAlert* ou *SecurityRecommendation* pour interroger les types de données vers lequel Security Center exporte en continu quand vous activez la fonctionnalité d’exportation continue vers Log Analytics. 
    
    * Vous pouvez éventuellement configurer le [Groupe d’actions](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) que vous souhaitez déclencher. Les groupes d’actions peuvent déclencher l’envoi d’e-mails, des tickets ITSM, des webhooks, et plus encore.
    ![Règle d’alerte Azure Monitor](./media/continuous-export/azure-monitor-alert-rule.png)

Vous voyez maintenant de nouvelles alertes ou recommandations Azure Security Center (en fonction de votre configuration) dans les alertes Azure Monitor, avec un déclenchement automatique d’un groupe d’actions (le cas échéant).

## <a name="manual-one-time-export-of-security-alerts"></a>Exportation ponctuelle et manuelle des alertes de sécurité

Pour télécharger un rapport CSV pour les alertes ou les recommandations, ouvrez la page **Alertes de sécurité** ou **Recommandations**, puis sélectionnez le bouton **Télécharger le rapport CSV**.

[![Télécharger les données d’alertes dans un fichier CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Ces rapports contiennent les alertes et les recommandations générées pour les ressources incluses dans les abonnements actuellement sélectionnés.



## <a name="faq---continuous-export"></a>FAQ – Exportation continue

### <a name="what-are-the-costs-involved-in-exporting-data"></a>Quels sont les coûts liés à l’exportation des données ?

L’activation d’une exportation continue n’entraîne aucun coût. Des coûts peuvent résulter de l’ingestion et de la rétention de données dans votre espace de travail Log Analytics, en fonction de la configuration que vous définissez ici. 

Pour en savoir plus, consultez la [tarification de l’espace de travail Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Pour en savoir plus, consultez la [tarification d’Azure Event Hub](https://azure.microsoft.com/pricing/details/event-hubs/).


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à configurer des exportations continues de vos alertes et recommandations. Vous avez également appris à télécharger vos données d’alertes dans un fichier CSV. 

Pour des informations connexes, consultez la documentation suivante : 

- [Documentation Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Documentation Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Documentation Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- [Automatisation de flux de travail et schémas de types de données d’exportation continue](https://aka.ms/ASCAutomationSchemas)
