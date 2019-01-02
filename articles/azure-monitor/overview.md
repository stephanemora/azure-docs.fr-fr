---
title: Vue d’ensemble d’Azure Monitor | Microsoft Docs
description: Vue d’ensemble des services et fonctionnalités Microsoft qui contribuent à une stratégie de surveillance complète de vos services et applications Azure.
author: bwren
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: bwren
ms.openlocfilehash: 160078787d28f43ef800d25c8b50fb316b38944f
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436175"
---
# <a name="azure-monitor-overview"></a>Vue d’ensemble d’Azure Monitor

Azure Monitor optimise la disponibilité et les performances de vos applications en fournissant une solution complète pour collecter, analyser et agir sur les données de télémétrie de vos environnements cloud et locaux. Il vous aide à comprendre le fonctionnement de vos applications et identifie de façon proactive les problèmes qui les affectent et les ressources dont elles dépendent.

> [!VIDEO https://www.youtube.com/embed/_hGff5bVtkM]

## <a name="overview"></a>Vue d’ensemble
Le diagramme suivant donne une vue d’ensemble d’Azure Monitor. Au centre du diagramme se trouvent les magasins de données pour les métriques et les journaux, qui sont les deux types fondamentaux d’utilisation des données par Azure Monitor. Sur la gauche figurent les [sources qui collectent des données de télémétrie à partir de différentes ressources surveillées](platform/data-sources.md) et remplissent les [magasins de données](platform/data-collection.md). Sur la droite se trouvent les différentes actions qu’Azure Monitor effectue avec les données collectées (analyse, alerte et diffusion en continu sur des systèmes externes).


![Vue d’ensemble d’Azure Monitor](media/overview/overview.png)


## <a name="monitoring-data-platform"></a>Plateforme de données de surveillance
Toutes les données collectées par Azure Monitor font partie d’un des deux types fondamentaux, [les métriques et les journaux](platform/data-collection.md). Les [métriques](platform/data-collection.md#metrics) sont des valeurs numériques décrivant un aspect d’un système à un moment précis dans le temps. Elles sont légères et capables de prendre en charge des scénarios en quasi temps réel. Les [journaux](platform/data-collection.md#logs) contiennent différents types de données organisées en enregistrements, avec différents jeux de propriétés pour chaque type. Les données de télémétrie, comme les événements et les traces, sont stockées sous forme de journaux en plus des données de performances, afin qu’elles puissent être toutes combinées pour analyse.

Pour de nombreuses ressources Azure, vous verrez les données collectées par Azure Monitor directement sur la page Vue d’ensemble correspondante sur le portail Azure. Par exemple, jetez un œil à toutes les machines virtuelles pour voir plusieurs graphiques affichant les mesures de performances. Cliquez sur un des graphiques pour ouvrir les données dans [Metrics Explorer](../azure-monitor/platform/metrics-charts.md) sur le portail Azure, ce qui vous permet de représenter les valeurs de plusieurs métriques dans un graphique au fil du temps.  Vous pouvez afficher les graphiques de manière interactive ou les épingler au tableau de bord pour les voir avec d’autres visualisations.

![Mesures](media/overview/metrics.png)

<<<<<<< HEAD Les données de journal collectées par Azure Monitor peuvent être analysées à l’aide de [requêtes](log-query/log-query-overview.md) qui permettent de récupérer, consolider et analyser les données collectées rapidement.  Vous pouvez créer et tester des requêtes à l’aide de [Log Analytics](../log-analytics/log-analytics-log-search-portals.md) dans le Portail Azure, avant d’analyser directement les données à l’aide de ces outils ou d’enregistrer les requêtes pour les utiliser pour les [visualisations](visualizations.md) ou les [règles d’alerte](../monitoring-and-diagnostics/monitoring-overview-alerts.md).
======= Les données de journal collectées par Azure Monitor sont stockées dans Log Analytics, qui inclut un [langage de requête riche](../azure-monitor/log-query/log-query-overview.md) pour rapidement récupérer, consolider et analyser les données collectées.  Vous pouvez créer et tester des requêtes à l’aide de la [page Log Analytics](../azure-monitor/log-query/portals.md) du portail Azure, avant d’analyser directement les données à l’aide de ces outils ou d’enregistrer les requêtes pour les utiliser pour les [visualisations](visualizations.md) ou les [règles d’alerte](../azure-monitor/platform/alerts-overview.md).
>>>>>>> 98b6f1655e9ada6d4b6a408ada0f1e4c9ed727d2

Azure Monitor utilise une version du [langage de requête Data Explorer](/azure/kusto/query/) qui est adapté aux requêtes simples dans les journaux, mais inclut également des fonctionnalités avancées telles que les agrégations, les jointures et les analyses intelligentes. Il existe [plusieurs leçons](log-query/get-started-queries.md) pour vous aider à apprendre le langage de requête.  Des conseils particuliers sont fournis aux utilisateurs qui connaissent déjà [SQL](log-query/sql-cheatsheet.md) et [Splunk](log-query/splunk-cheatsheet.md).

![Journaux](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Quelles sont les données collectées par Azure Monitor ?
Azure Monitor peut recueillir des données de diverses sources. Vous pouvez envisager un monitoring des données de vos applications sous forme de niveaux allant de votre application à la plateforme elle-même, en passant par le système d’exploitation et les services sur lesquels s’appuie votre application. Azure Monitor collecte des données à partir de chacun des niveaux suivants :

- **Données de surveillance de l’application** : données concernant les performances et la fonctionnalité du code que vous avez écrit, quelle que soit la plateforme.
- **Données de surveillance du système d’exploitation invité** : données concernant le système d’exploitation sur lequel votre application est exécutée. Il peut s’agir d’Azure, d’un autre cloud ou d’un système local. 
- **Données de surveillance des ressources Azure** : données sur le fonctionnement d’une ressource Azure.
- **Données de surveillance de l’abonnement Azure** : données concernant le fonctionnement et la gestion d’un abonnement Azure, mais aussi données concernant l’intégrité et le fonctionnement d’Azure. 
- **Données de surveillance du locataire Azure** : données concernant le fonctionnement des services Azure au niveau du locataire, tels qu’Azure Active Directory.

Dès que vous créez un abonnement Azure et commencez à ajouter des ressources telles que des machines virtuelles et des applications web, Azure Monitor commence la collecte de données.  Les [journaux d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) enregistrent la création et la modification des ressources. Les [métriques](../monitoring-and-diagnostics/monitoring-overview-metrics.md) vous indiquent les performances de la ressource et les ressources qu’elle consomme. 

Augmentez le champ des données que vous collectez dans le fonctionnement actuel des ressources en [activant les diagnostics](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) et en [ajoutant un agent](platform/agent-windows.md) aux ressources de calcul. Vous recueillerez ainsi les données de télémétrie pour le fonctionnement interne de la ressource et pourrez configurer différentes [sources de données](platform/agent-data-sources.md) afin de collecter les journaux et les métriques à partir du système d’exploitation invité Windows et Linux. 

[Ajoutez un package d’instrumentation à votre application](../application-insights/app-insights-azure-web-apps.md) pour autoriser Application Insights à collecter des informations détaillées sur votre application, notamment les affichages de pages, les requêtes d’application et les exceptions. Vérifiez la disponibilité de votre application en configurant un [test de disponibilité](../application-insights/app-insights-monitor-web-app-availability.md) pour simuler le trafic utilisateur.

### <a name="custom-sources"></a>Sources personnalisées
Azure Monitor peut collecter des données de journal à partir de n’importe quel client REST à l’aide de l’[API de collecte de données](../azure-monitor/platform/data-collector-api.md). Cela vous permet de créer des scénarios de supervision personnalisés et d’étendre la supervision à des ressources qui n’exposent pas de données de télémétrie via d’autres sources.



## <a name="insights"></a>Insights
La supervision des données est utile uniquement si elle permet d’augmenter votre visibilité sur le fonctionnement de votre environnement informatique. Azure Monitor inclut plusieurs fonctionnalités et outils qui fournissent des informations importantes sur vos applications et d’autres ressources dont elles dépendent. La [supervision des solutions](insights/solutions.md) et des fonctionnalités telles qu’[Application Insights](../application-insights/app-insights-overview.md) et Container Insights fournit des informations détaillées sur les différents aspects de votre application et les services Azure spécifiques. 

### <a name="application-insights"></a>Application Insights
[Application Insights](../application-insights/app-insights-overview.md) surveille la disponibilité, les performances et l’utilisation de votre application web, qu’elle soit hébergée dans le cloud ou localement. Cette solution s’appuie sur la puissante plateforme d’analyse de données dans Azure Monitor pour vous fournir des informations détaillées sur le fonctionnement de votre application et pour diagnostiquer les erreurs sans attendre qu’un utilisateur les signale. Application Insights inclut des points de connexion à divers outils de développement et s’intègre à Visual Studio pour prendre en charge vos processus DevOps.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor pour des conteneurs
Azure Monitor pour les conteneurs est une fonctionnalité conçue pour surveiller les performances des charges de travail de conteneur déployées sur des clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS). Cela vous permet de visualiser les performances en collectant des métriques sur la mémoire et le processeur à partir des contrôleurs, des nœuds et des conteneurs qui sont disponibles dans Kubernetes via l’API Metrics. Les journaux de conteneur sont aussi collectés.  Une fois que vous avez activé la supervision des clusters Kubernetes, ces métriques et ces journaux sont automatiquement collectés à l’aide d’une version conteneurisée de l’agent Log Analytics pour Linux.

![Intégrité du conteneur](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor pour les machines virtuelles
Les insights de machine virtuelle Azure Monitor surveillent vos machines virtuelles Azure en analysant les performances et l’intégrité de vos machines virtuelles Windows et Linux, y compris leurs différents processus et les dépendances interconnectées envers d’autres ressources et processus externes. Cette solution inclut la prise en charge de la supervision des performances et des dépendances des applications pour les machines virtuelles hébergées sur site ou par un autre fournisseur cloud.  


![Insights de machine virtuelle](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Solutions de supervision
Les [solutions de supervision](insights/solutions.md) dans Azure Monitor sont des jeux de logique empaquetés qui fournissent des informations détaillées pour une application ou un service spécifique. Elles incluent une logique de collecte des données de surveillance pour l’application ou le service, des [requêtes](log-query/log-query-overview.md) permettant d’analyser ces données et des [vues](../log-analytics/log-analytics-view-designer.md) pour la visualisation. [Microsoft](insights/solutions-inventory.md) et ses partenaires proposent des solutions de supervision qui assurent la supervision de divers services Azure et d’autres applications.

![Solutions de supervision](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Réagir à des situations critiques
En plus de vous permettre d’analyser les données de supervision de manière interactive, une solution de supervision efficace doit pouvoir répondre de manière proactive à des conditions critiques identifiées dans les données qu’elle collecte. Il peut par exemple s’agir d’envoyer un SMS ou un courrier à un administrateur en charge de l’examen d’un problème. Ou de lancer un processus automatisé qui essaie de corriger une condition d’erreur.


### <a name="alerts"></a>Alertes
Les [alertes dans Azure Monitor](../azure-monitor/platform/alerts-overview.md) vous avertissent de manière proactive en cas de condition critique, et sont susceptibles d’essayer de prendre des mesures correctives. Les règles d’alerte basées sur les métriques émettent des alertes en temps quasi réel à partir de valeurs numériques, alors que les règles basées sur les journaux acceptent une logique complexe sur des données issues de différentes sources.

Les règles d’alerte dans Azure Monitor utilisent des [groupes d’actions](../azure-monitor/platform/action-groups.md), qui contiennent des ensembles uniques de destinataires et d’actions qui peuvent être partagés entre plusieurs règles. Selon vos besoins, les groupes d’actions peuvent effectuer des actions telles que l’utilisation de webhooks pour que les alertes démarrent des actions externes ou pour l’intégration à vos outils ITSM.

![Alertes](media/overview/alerts.png)

### <a name="autoscale"></a>Autoscale
La mise à l’échelle automatique vous permet de disposer de la bonne quantité de ressources en cours d’exécution pour gérer la charge sur votre application. Elle vous permet de créer des règles qui utilisent les métriques collectées par Azure Monitor pour déterminer quand ajouter automatiquement des ressources pour gérer les augmentations de charge tout en réalisant des économies en supprimant les ressources inactives. Vous spécifiez un nombre minimal et maximal d’instances et la logique permettant de définir quand augmenter ou diminuer les ressources.

![Autoscale](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Affichage des données de supervision
Les [visualisations](visualizations.md), telles que les tables et les graphiques, sont des outils efficaces pour résumer les données de supervision et les proposer à différents publics. Azure Monitor dispose de ses propres fonctionnalités de visualisation des données de supervision et s’appuie sur d’autres services Azure pour leur publication auprès de différents publics.

### <a name="dashboards"></a>Tableaux de bord
Les [tableaux de bord Azure](../azure-portal/azure-portal-dashboards.md) vous permettent de combiner différents genres de données, y compris les métriques et les journaux, dans un même volet dans le [portail Azure](https://portal.azure.com). Si vous le souhaitez, vous pouvez partager le tableau de bord avec d’autres utilisateurs d’Azure. Les éléments d’Azure Monitor peuvent être ajoutés à un tableau de bord Azure en plus de la sortie de n’importe quelle requête de journal ou graphique de métriques. Par exemple, vous pouvez créer un tableau de bord qui combine des vignettes affichant un graphique de métriques, un tableau de journaux d’activité, un graphique d’utilisation provenant d’Application Insights et la sortie d’une requête de journal.

![tableau de bord](media/overview/dashboard.png)

### <a name="views"></a>Views
Les [vues](../log-analytics/log-analytics-view-designer.md) présentent de manière visuelle les données de journal dans Azure Monitor.  Chaque vue inclut une seule vignette comportant plusieurs visualisations, par exemple sous forme de barres et de graphiques en courbes, en plus des listes résumant les données critiques.  Les solutions de supervision incluent des vues qui synthétisent les données pour une application donnée. Vous pouvez créer vos propres vues pour présenter les données de n’importe quelle requête de journal. Comme les autres éléments d’Azure Monitor, les vues peuvent être ajoutées aux tableaux de bord Azure.

![Affichage](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) est un service d’analyse métier qui fournit des visualisations interactives d’une large gamme de sources de données. Il constitue un moyen efficace de mettre les données à disposition d’autres personnes dans et en dehors de votre organisation. Vous pouvez configurer Power BI pour [importer automatiquement les données de journal à partir d’Azure Monitor](../log-analytics/log-analytics-powerbi.md) afin de tirer parti de ces visualisations supplémentaires.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Intégrer et exporter des données
Vous devrez souvent intégrer Azure Monitor à d’autres systèmes et créer des solutions personnalisées qui utilisent vos données de supervision. D’autres services Azure fonctionnent avec Azure Monitor en vue de fournir cette intégration.

### <a name="event-hub"></a>Event Hub
[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs) est une plateforme de diffusion en continu et un service d’ingestion d’événements qui peut transformer et stocker les données à l’aide de n’importe quel fournisseur de services d’analyse en temps réel ou de n’importe quel adaptateur de traitement par lot/stockage. Utilisez Event Hubs pour [diffuser en continu les données de journal à partir d’Azure Monitor](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) auprès des outils SIEM (Security Information and Event Management) et de supervision.

> [!VIDEO https://www.youtube.com/embed/SPHxCgbcvSw]

### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) est un service qui vous permet d’automatiser les tâches et processus métier à l’aide de flux de travail qui s’intègrent à différents systèmes et services. Des activités permettent de lire et d’écrire les métriques et les journaux dans Azure Monitor, ce qui vous permet de créer des flux de travail capables de s’intégrer à de nombreux autres systèmes.

![Application logique](platform/media/collect-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="api"></a>API
Plusieurs API sont disponibles pour lire et écrire les métriques et les journaux vers et depuis Azure Monitor en plus d’accéder aux alertes générées. Vous pouvez également configurer et récupérer les alertes. Cela vous offre essentiellement des possibilités illimitées pour créer des solutions personnalisées qui s’intègrent à Azure Monitor.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus :

* [Métriques et journaux](platform/data-collection.md) pour les données collectées par Azure Monitor.
* [Sources de données](platform/data-sources.md) pour découvrir comment les différents composants de votre application envoient des données de télémétrie.
* [Requêtes de journal](log-query/log-query-overview.md) pour l’analyse des données collectées.
