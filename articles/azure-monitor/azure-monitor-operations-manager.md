---
title: Azure Monitor pour les clients Operations Manager actuels
description: Aide aux utilisateurs actuels d’Operations Manager pour effectuer la transition de la supervision de certaines charges de travail vers Azure Monitor dans le cadre d’une transition vers le cloud.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2021
ms.openlocfilehash: 877251ba7e0c1f3c33cab37e20d609479b69520c
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251826"
---
# <a name="azure-monitor-for-existing-operations-manager-customers"></a>Azure Monitor pour les clients Operations Manager actuels
Cet article fournit des conseils aux clients qui utilisent actuellement [Operations Manager](https://docs.microsoft.com/system-center/scom/welcome) et qui planifient une transition vers [Azure Monitor](overview.md) dans le cadre d’un migration d’applications métier et d’autres ressources sur Azure. Il part du principe que votre objectif ultime est une transition complète vers le cloud, en remplaçant autant de fonctionnalités d’Operations Manager que possible par Azure Monitor, sans compromettre vos exigences opérationnelles commerciales et informatiques. 

Les recommandations spécifiques faites dans cet article changeront à mesure qu’Azure Monitor et Operations Manager ajouteront des fonctionnalités. La stratégie fondamentale restera cependant cohérente.

> [!IMPORTANT]
> L’implémentation de plusieurs fonctionnalités d’Azure Monitor décrites ici a un coût. Vous devez donc évaluer leur valeur avant de les déployer dans l’ensemble de votre environnement.

## <a name="prerequisites"></a>Prérequis
Cet article part du principe que vous utilisez déjà [Operations Manager](https://docs.microsoft.com/system-center/scom) et que vous avez au moins une compréhension de base d’[Azure Monitor](overview.md). Pour une comparaison complète entre les deux, consultez [Guide de supervision du cloud : Vue d’ensemble des plateformes de supervision](/azure/cloud-adoption-framework/manage/monitor/platform-overview). Cet article détaille les différences de fonctionnalités spécifiques entre les deux plateformes pour vous aider à comprendre certaines des recommandations présentées ici. 


## <a name="general-strategy"></a>Stratégie générale
Il n’existe aucun outil de migration permettant de convertir des ressources Operations Manager en ressources Azure Monitor, car les plateformes sont fondamentalement différentes. Votre migration constituera plutôt une [implémentation standard d’Azure Monitor](deploy.md) pendant que vous continuerez à utiliser Operations Manager. Au fur et à mesure que vous personnalisez Azure Monitor pour répondre à vos besoins en matière d’applications et de composants et que la plateforme gagne en fonctionnalités, vous pouvez commencer à mettre hors service différents agents et packs d’administration dans Operations Manager.

La stratégie générale recommandée dans cet article est la même que celle du [guide de supervision du cloud](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/), qui recommande une stratégie de [supervision d’un cloud hybride](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring) qui vous permet d’effectuer une transition progressive vers le cloud. Même si certaines fonctionnalités peuvent se chevaucher, cette stratégie vous permet de conserver vos processus d’entreprise existants tout en vous familiarisant avec la nouvelle plateforme. Ne vous éloignez des fonctionnalités d’Operations Manager que si vous pouvez les remplacer par celles d’Azure Monitor. L’utilisation de plusieurs outils de supervision ajoute certes de la complexité, mais elle vous permet de profiter de la capacité d’Azure Monitor à superviser les charges de travail cloud de nouvelle génération tout en conservant la capacité d’Operations Manager à superviser les logiciels serveur et les composants d’infrastructure qui peuvent être locaux ou se trouver dans d’autres clouds. 


## <a name="components-to-monitor"></a>Composants à superviser
Cela permet de catégoriser les différents types de charges de travail que vous devez superviser afin de déterminer une stratégie de supervision distincte pour chacun d’entre eux. Le [guide de supervision du cloud : Formuler une stratégie de supervision](/azure/cloud-adoption-framework/strategy/monitoring-strategy#high-level-modeling) fournit une décomposition détaillée des différentes couches de votre environnement qui doivent être surveillées à mesure que vous passez des applications d’entreprise héritées aux applications modernes dans le cloud.

Avant le cloud, vous utilisiez Operations Manager pour surveiller toutes les couches. Lorsque vous commencez votre transition avec l’infrastructure as a service, vous continuez à utiliser Operations Manager pour vos machines virtuelles, mais commencez à utiliser Azure Monitor pour vos ressources cloud. Au fur et à mesure que vous migrez vers des applications modernes utilisant un modèle PaaS, vous pouvez vous concentrer davantage sur Azure Monitor et commencer à mettre hors service les fonctionnalités d’Operations Manager.


![Modèles cloud](https://docs.microsoft.com/azure/cloud-adoption-framework/strategy/media/monitoring-strategy/cloud-models.png)

Ces couches peuvent être simplifiées selon les catégories suivantes, qui sont décrites plus en détail dans le reste de cet article. Bien que chaque charge de travail de supervision dans votre environnement puisse ne pas correspondre parfaitement à l’une de ces catégories, chacune d’entre elles doit être suffisamment proche d’une catégorie particulière pour que les recommandations générales s’appliquent.

**Applications métier.** Applications qui fournissent des fonctionnalités spécifiques à votre entreprise. Elles peuvent être internes ou externes et sont souvent développées en interne à l’aide de code personnalisé. Vos applications héritées sont généralement hébergées sur des machines virtuelles ou physiques sous Windows ou Linux, tandis que vos nouvelles applications seront basées sur des services d’application dans Azure, comme Azure Web Apps et Azure Functions.

**Services Azure.** Ressources dans Azure qui prennent en charge vos applications métier ayant migré sur le cloud. Cela comprend des services tels que Stockage Azure, Azure SQL et Azure IoT. Cela inclut également les machines virtuelles Azure, car elles sont supervisées comme d’autres services Azure, mais les applications et les logiciels exécutés sur le système d’exploitation invité de ces machines virtuelles nécessitent une surveillance plus poussée au-delà de l’hôte.

**Logiciels serveur.** Logiciels s’exécutant sur des machines virtuelles et physiques qui prennent en charge vos applications métier ou des applications empaquetées qui fournissent des fonctionnalités générales à votre entreprise. Les exemples incluent notamment Internet Information Server (IIS), SQL Server, Exchange et SharePoint. Cela comprend également le système d’exploitation Windows ou Linux sur vos machines virtuelles et physiques.

**Infrastructure locale.** Composants spécifiques à votre environnement local qui nécessitent une supervision. Il s’agit notamment des ressources telles que les serveurs physiques, le stockage et les composants réseau. Ce sont les composants qui sont virtualisés lorsque vous migrez sur le cloud.

## <a name="sample-walkthrough"></a>Exemple de procédure pas à pas
Voici une description hypothétique d’une migration d’Operations Manager vers Azure Monitor. Elle ne prétend pas représenter toute la complexité d’une migration réelle, mais elle en présente au moins les étapes de base et la séquence. Les sections ci-dessous décrivent chacune de ces étapes plus en détail.

Avant de déplacer des composants vers Azure, votre environnement est basé sur des machines virtuelles et physiques situées localement ou chez un fournisseur de services gérés. Il s’appuie sur Operations Manager pour surveiller les applications métier, les logiciels serveur et d’autres composants d’infrastructure de votre environnement, tels que les serveurs physiques et les réseaux. Vous utilisez des packs d’administration standard pour les logiciels serveur tels qu’IIS, SQL Server et divers logiciels de fournisseurs, et vous réglez ces packs d’administration en fonction de vos besoins spécifiques. Vous créez des packs d’administration personnalisés pour vos applications métier et d’autres composants qui ne peuvent pas être supervisés avec les packs d’administration existants et vous configurez Operations Manager pour qu’il prenne en charge vos processus métier.

Votre migration vers Azure commence avec l’IaaS, en déplaçant les machines virtuelles prenant en charge les applications métier vers Azure. Les exigences de supervision de ces applications et des logiciels serveur dont elles dépendent ne changent pas, et vous continuez à utiliser Operations Manager sur ces serveurs avec vos packs d’administration existants. 

La plateforme Azure Monitor est activée pour vos services Azure dès que vous créez un abonnement Azure. Elle collecte automatiquement les métriques de la plateforme et le journal d’activité, et vous configurez les journaux de ressources à collecter pour pouvoir analyser de manière interactive toutes les données de télémétrie disponibles à l’aide de requêtes de journal. Vous activez la fonctionnalité Azure Monitor pour machines virtuelles sur vos machines virtuelles afin d’analyser les données de supervision de l’ensemble de votre environnement et de découvrir les relations entre les ordinateurs et les processus. Vous étendez votre utilisation d’Azure Monitor à vos machines physiques et virtuelles locales en y activant des serveurs Azure Arc. 

Vous activez Application Insights pour chacune de vos applications métier. La fonctionnalité identifie les différents composants de chaque application, commence à collecter les données d’utilisation et de performances et identifie toute erreur qui se produit dans le code. Vous créez des tests de disponibilité pour tester de manière proactive vos applications externes et vous alerter de tout problème de performances ou de disponibilité. Bien qu’Application Insights vous offre des fonctionnalités puissantes qui ne sont pas disponibles dans Operations Manager, vous continuez à vous appuyer sur les packs d’administration personnalisés que vous avez développés pour vos applications métier, car ils incluent des scénarios de supervision que ne sont pas encore couverts par Azure Monitor. 

À mesure que vous vous familiarisez avec Azure Monitor, vous commencez à créer des règles d’alerte capables de remplacer certaines fonctionnalités des packs d’administration et à faire évoluer vos processus métier pour utiliser la nouvelle plateforme de supervision. Cela vous permet de commencer à supprimer des machines et des packs d’administration du groupe d’administration d’Operations Manager. Vous continuez à utiliser des packs d’administration pour les logiciels serveur critiques et l’infrastructure locale, mais vous continuez à surveiller les nouvelles fonctionnalités d’Azure Monitor qui vous permettront de mettre hors service des fonctionnalités supplémentaires.

## <a name="monitor-azure-services"></a>Surveiller les services Azure
Les services Azure nécessitent en fait qu’Azure Monitor collecte des données de télémétrie, et la plateforme est activée dès que vous créez un abonnement Azure. Le [journal d’activité](platform/activity-log.md) est automatiquement collecté pour l’abonnement et les [métriques de la plateforme](platform/data-platform-metrics.md) sont collectées automatiquement à partir des ressources Azure que vous créez. Vous pouvez immédiatement commencer à utiliser [Metrics Explorer](platform/metrics-getting-started.md), qui est similaire aux affichages des performances dans la console Opérateur, mais fournit une analyse interactive et des [agrégations de données avancées](platform/metrics-charts.md). [Créez une alerte de métrique](platform/alerts-metric.md) pour être averti lorsqu’une valeur dépasse un seuil ou [ajoutez un graphique à un tableau de bord Azure](platform/metrics-charts.md#pinning-to-dashboards) pour plus de visibilité.

[![Metrics Explorer](media/azure-monitor-operations-manager/metrics-explorer.png)](media/azure-monitor-operations-manager/metrics-explorer.png#lightbox)

[Créez un paramètre de diagnostic](platform/diagnostic-settings.md) pour chaque ressource Azure afin d’envoyer des métriques et des [journaux de ressources](platform/resource-logs.md), qui fournissent des détails sur le fonctionnement interne de chaque ressource, à un espace de travail Log Analytics. Cela vous donne toutes les données de télémétrie disponibles pour vos ressources et vous permet d’utiliser [Log Analytics](log-query/log-analytics-overview.md) pour analyser de manière interactive les données de journal et de performances à l’aide d’un langage de requête avancé qui n’a pas d’équivalent dans Operations Manager. Vous pouvez également créer des [alertes de requête de journal](platform/alerts-log-query.md), qui peuvent utiliser une logique complexe pour déterminer des conditions d’alerte et mettre en corrélation des données entre plusieurs ressources.

[![Analytique des journaux](media/azure-monitor-operations-manager/log-analytics.png)](media/azure-monitor-operations-manager/log-analytics.png#lightbox)

Les [insights](monitor-reference.md) dans Azure Monitor sont similaires aux packs d’administration en ce sens qu’ils fournissent une supervision unique pour un service Azure particulier. Les insights sont actuellement disponibles pour plusieurs services, notamment la mise en réseau, le stockage et les conteneurs, et d’autres sont en cours d’ajout.

[![Exemple d’insight](media/azure-monitor-operations-manager/insight.png)](media/azure-monitor-operations-manager/insight.png#lightbox)


Les insights sont basés sur des [classeurs](platform/workbooks-overview.md) dans Azure Monitor, qui combinent des métriques et des requêtes de journal en rapports interactifs enrichis. Créez vos propres classeurs pour combiner des données provenant de plusieurs services, comme vous le feriez pour créer des vues et des rapports personnalisés dans la console Opérateur.

### <a name="azure-management-pack"></a>Pack d’administration Azure
Le [pack d’administration Azure](https://www.microsoft.com/download/details.aspx?id=50013) permet à Operations Manager de découvrir des ressources Azure et de surveiller leur intégrité en fonction d’un ensemble spécifique de scénarios de supervision. Ce pack d’administration vous oblige à effectuer une configuration supplémentaire pour chaque ressource d’Azure, mais il peut être utile de fournir une certaine visibilité de vos ressources Azure dans la console Opérateur jusqu’à ce que vous fassiez évoluer vos processus métier pour vous concentrer sur Azure Monitor.

[![Pack d’administration Azure](media/azure-monitor-operations-manager/operations-console.png)](media/azure-monitor-operations-manager/operations-console.png#lightbox)

 Vous pouvez choisir d’utiliser le pack d’administration Azure si vous souhaitez avoir une visibilité sur certaines ressources Azure dans la console Opérateur et intégrer des alertes de base à vos processus existants. Il utilise en fait les données collectées par Azure Monitor. Vous devriez cependant vous tourner vers Azure Monitor pour une supervision complète et à long terme de vos ressources Azure. 


## <a name="monitor-server-software-and-local-infrastructure"></a>Surveiller les logiciels serveur et l’infrastructure locale
Lorsque vous déplacez des machines vers le cloud, les exigences de supervision de leurs logiciels ne changent pas. Vous n’avez plus besoin de surveiller leurs composants physiques, puisqu’ils sont virtualisés, mais le système d’exploitation invité et ses charges de travail ont les mêmes exigences, quel que soit leur environnement.

[Azure Monitor pour machines virtuelles](insights/vminsights-overview.md) est la principale fonctionnalité d’Azure Monitor permettant de superviser les machines virtuelles, leur système d’exploitation invité et leurs charges de travail. Tout comme Operations Manager, Azure Monitor pour machines virtuelles utilise un agent pour collecter les données du système d’exploitation invité des machines virtuelles. Il s’agit des mêmes données de performances et d’événements qui sont généralement utilisées par les packs d’administration pour l’analyse et la génération d’alertes. Toutefois, il n’existe pas de règles préexistantes pour identifier et signaler les problèmes liés aux applications métier et aux logiciels serveur s’exécutant sur ces ordinateurs. Vous devez créer vos propres règles d’alerte pour être informé de façon proactive des problèmes détectés.

[![Performances d’Azure Monitor pour machines virtuelles](media/azure-monitor-operations-manager/vm-insights-performance.png)](media/azure-monitor-operations-manager/vm-insights-performance.png#lightbox)

Azure Monitor ne mesure pas non plus l’intégrité de différents services et applications qui s’exécutent sur une machine virtuelle. Les alertes de métrique peuvent être résolues automatiquement lorsqu’une valeur passe en dessous d’un seuil, mais Azure Monitor n’a pas la capacité de définir des critères d’intégrité pour les applications et les services exécutés sur l’ordinateur, ni celle de fournir un cumul d’intégrité pour regrouper l’intégrité des composants connexes.

> [!NOTE]
> Une nouvelle [fonctionnalité d’intégrité des invités pour Azure Monitor pour machines virtuelles](insights/vminsights-health-overview.md) est désormais disponible en préversion publique et génère des alertes en fonction de l’état d’intégrité d’un ensemble de mesures de performances. Elle est initialement limitée à un ensemble spécifique de compteurs de performances liés au système d’exploitation invité et non aux applications ou autres charges de travail qui s’exécutent sur la machine virtuelle.
> 
> [![Intégrité des invités Azure Monitor pour machines virtuelles](media/azure-monitor-operations-manager/vm-insights-guest-health.png)](media/azure-monitor-operations-manager/vm-insights-guest-health.png#lightbox)

La supervision des logiciels sur vos ordinateurs dans un environnement hybride utilise généralement une combinaison d’Azure Monitor pour machines virtuelles et d’Operations Manager, en fonction des besoins de chaque ordinateur et de votre maturité à développer des processus opérationnels autour d’Azure Monitor. L’agent de gestion Microsoft (appelé agent Log Analytics dans Azure Monitor) est utilisé par les deux plateformes afin qu’un seul ordinateur puisse être supervisé simultanément par les deux.

> [!NOTE]
> À l’avenir, Azure Monitor pour machines virtuelles passera à l’[agent Azure Monitor](platform/azure-monitor-agent-overview.md), qui est actuellement en préversion publique. Il sera compatible avec Microsoft Monitoring Agent, de sorte que la même machine virtuelle pourra toujours être surveillée par les deux plateformes.

Continuez à utiliser Operations Manager pour bénéficier des fonctionnalités qui ne peuvent pas encore être fournies par Azure Monitor. Cela comprend les packs d’administration pour les logiciels serveur critiques comme IIS, SQL Server ou Exchange. Vous pouvez également faire développer des packs d’administration personnalisés pour l’infrastructure locale qui n’est pas accessible avec Azure Monitor. Continuez également à utiliser Operations Manager s’il est étroitement intégré à vos processus opérationnels jusqu’à ce que vous puissiez passer à la modernisation de vos opérations de service là où Azure Monitor et d’autres services Azure peuvent les augmenter ou les remplacer. 

Utilisez Azure Monitor pour machines virtuelles afin d’améliorer votre supervision actuelle, même si la fonctionnalité ne remplace pas immédiatement Operations Manager. Voici quelques exemples de fonctionnalités propres à Azure Monitor :

- Détecter et surveiller les relations entre les machines virtuelles et leurs dépendances externes.
- Afficher les données de performances agrégées entre plusieurs machines virtuelles dans des graphiques et des classeurs interactifs.
- Utiliser des [requêtes de journal](log-query/log-query-overview.md) pour analyser de manière interactive la télémétrie de vos machines virtuelles avec les données de vos autres ressources Azure.
- Créer des [règles d’alerte de journal](platform/alerts-log-query.md) basées sur une logique complexe sur plusieurs machines virtuelles.

[![Carte Azure Monitor pour machines virtuelles](media/azure-monitor-operations-manager/vm-insights-map.png)](media/azure-monitor-operations-manager/vm-insights-map.png#lightbox)

En plus des machines virtuelles Azure, Azure Monitor pour machines virtuelles peut superviser des machines locales et dans d’autres clouds à l’aide de [serveurs Azure Arc](../azure-arc/servers/overview.md). Les serveurs avec Arc vous permettent de gérer vos machines Windows et Linux hébergées en dehors d’Azure, sur votre réseau d’entreprise ou un autre fournisseur de cloud correspondant bien à la façon dont vous gérez les machines virtuelles Azure natives.



## <a name="monitor-business-applications"></a>Surveiller les applications métier
En général, vous avez besoin de packs d’administration personnalisés pour superviser vos applications métier avec Operations Manager, en tirant parti des agents installés sur chaque machine virtuelle. Application Insights d’Azure Monitor surveille les applications web, qu’elles soient dans Azure, dans d’autres clouds ou locales, de sorte que la fonctionnalité peut être utilisée pour toutes vos applications, qu’elles aient ou non été migrées vers Azure.

Si votre surveillance d’une application métier se limite aux fonctionnalités fournies par le [modèle de performances d’application .NET]() dans Operations Manager, vous pouvez très probablement migrer vers Application Insights sans aucune perte de fonctionnalité. En fait, Application Insights inclura un grand nombre de fonctionnalités supplémentaires, dont les suivantes :

- Détecter et surveiller automatiquement les composants de l’application.
- Collecter des données détaillées sur l’utilisation et les performances des applications, telles que le temps de réponse, les taux d’échec et les taux de requête.
- Collecter des données de navigation telles que les pages vues et les performances de chargement.
- Détecter les exceptions et explorer l’arborescence des appels de procédure et les requêtes connexes.
- Effectuer une analyse avancée à l’aide de fonctionnalités telles que le [suivi distribué](app/distributed-tracing.md) et la [détection intelligente](app/proactive-diagnostics.md).
- Utiliser [Metrics Explorer](platform/metrics-getting-started.md) pour analyser de manière interactive les données de performances.
- Utiliser des [requêtes de journal](log-query/log-query-overview.md) pour analyser de manière interactive la télémétrie collectée avec les données collectées pour les services Azure et Azure Monitor pour machines virtuelles.

[![Application Insights](media/azure-monitor-operations-manager/application-insights.png)](media/azure-monitor-operations-manager/application-insights.png#lightbox)

Il existe cependant certains cas de figure où vous devrez continuer à utiliser Operations Manager en plus d’Application Insights jusqu’à ce que vous soyez en mesure d’obtenir la fonctionnalité requise. Voici quelques exemples de cas où vous pourriez avoir besoin de continuer à utiliser Operations Manager :

-  Les [tests de disponibilité](app/monitor-web-app-availability.md), qui vous permettent de surveiller et de signaler la disponibilité et la réactivité de vos applications, nécessitent des requêtes entrantes provenant des adresses IP d’agents de test web. Si votre stratégie ne permet pas ce type d’accès, vous devrez peut-être continuer à utiliser les [moniteurs de disponibilité des applications web](/system-center/scom/web-application-availability-monitoring-template) dans Operations Manager.
- Dans Operations Manager, vous pouvez définir n’importe quel intervalle d’interrogation pour les tests de disponibilité, avec de nombreux clients vérifiant toutes les 60 à 120 secondes. Application Insights a un intervalle d’interrogation minimal de cinq minutes, ce qui peut être trop long pour certains clients.
- La collecte des événements générés par les applications et l’exécution de scripts sur l’agent local constituent une part importante de la supervision dans Operations Manager. Comme ces options ne sont pas des options standard dans Application Insights, vous pouvez exiger un travail personnalisé pour répondre aux besoins de votre entreprise. Cela peut inclure des règles d’alerte personnalisées utilisant des données d’événements stockées dans un espace de travail Log Analytics et des scripts lancés dans une machine virtuelle invitée à l’aide d’un [Runbook Worker hybride](../automation/automation-hybrid-runbook-worker.md).
- Selon le langage dans lequel votre application est écrite, vous pouvez être limité dans l’[instrumentation que vous pouvez utiliser avec Application Insights](app/platforms.md).

En suivant la stratégie de base décrite dans les autres sections de ce guide, continuez à utiliser Operations Manager pour vos applications métier, mais tirez parti des fonctionnalités supplémentaires fournies par Application Insights. À mesure que vous pouvez remplacer les fonctionnalités critiques par Azure Monitor, commencez à mettre hors service vos packs d’administration personnalisés.


## <a name="next-steps"></a>Étapes suivantes

- Consultez le [guide de supervision du cloud](/azure/cloud-adoption-framework/manage/monitor/) pour obtenir une comparaison détaillée d’Azure Monitor et d’Operations Manager, ainsi que plus d’informations sur la conception et l’implémentation d’un environnement de surveillance hybride.
- En savoir plus sur la [surveillance des ressources Azure dans Azure Monitor](insights/monitor-azure-resource.md).
- En savoir plus sur la [surveillance des machines virtuelles Azure dans Azure Monitor](insights/monitor-vm-azure.md).
- En savoir plus sur [Azure Monitor pour machines virtuelles](insights/vminsights-overview.md).
- En savoir plus sur [Application Insights](app/app-insights-overview.md).
