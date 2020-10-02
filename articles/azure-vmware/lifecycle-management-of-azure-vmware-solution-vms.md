---
title: Gestion du cycle de vie des machines virtuelles du service Azure VMware Solution
description: Découvrez comment gérer tous les aspects du cycle de vie des machines virtuelles de votre service Azure VMware Solution avec les outils natifs de Microsoft Azure.
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: 20948ec088d11468b5750ca89979050965246b58
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663123"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Gestion du cycle de vie des machines virtuelles du service Azure VMware Solution

Les outils natifs de Microsoft Azure vous permettent de surveiller et gérer vos machines virtuelles dans l’environnement Azure. Ils vous permettent également de surveiller et gérer vos machines virtuelles sur le service Azure VMware Solution et vos machines virtuelles locales. Cette vue d’ensemble examine les offres Azure d’architecture de surveillance intégrée et la façon dont vous pouvez utiliser ses outils natifs pour gérer les machines virtuelles de votre service Azure VMware Solution tout au long de leur cycle de vie.

## <a name="benefits"></a>Avantages

- Vous pouvez utiliser les services natifs d’Azure pour gérer vos machines virtuelles dans un environnement hybride (Azure, Azure VMware Solution et local).
- Surveillance et visibilité intégrées de vos machines virtuelles Azure, de Azure VMware Solution et locales.
- Avec Azure Update Management dans Azure Automation, vous pouvez gérer les mises à jour de système d’exploitation pour vos machines Windows et Linux. 
- Azure Security Center offre une protection avancée contre les menaces, à savoir :
    - Monitoring d’intégrité de fichier
    - Alertes de sécurité sans fichier
    - Évaluation de correctif du système d’exploitation
    - Évaluation des erreurs de configuration de la sécurité
    - Évaluation de la protection des points de terminaison 
- Déployez facilement l’agent Microsoft Monitoring Agent (MMA) à l’aide d’Azure ARC pour de nouvelles machines virtuelles. 
- Votre espace de travail Log Analytics dans Azure Monitor active la collecte des journaux et la collecte des compteurs de performances à l’aide de l’agent MMA ou d’extensions. Collectez les données et les journaux sur un point unique, et présentez-les à différents services natifs Azure. 
- Les avantages supplémentaires d’Azure Monitor sont les suivants : 
    - Surveillance transparente 
    - Meilleure visibilité de l’infrastructure 
    - Notifications instantanées 
    - Résolution automatique 
    - Rentabilité 

## <a name="integrated-azure-monitoring-architecture"></a>Architecture de surveillance Azure intégrée

Le diagramme suivant illustre l’architecture de surveillance intégrée pour les machines virtuelles du service Azure VMware Solution.

![Architecture de surveillance Azure intégrée](media/lifecycle-mgmt-avs-vms/integrated-azure-monitoring-architecture.png)

## <a name="integrating-and-deploying-azure-native-services"></a>Intégration et déploiement des services natifs d’Azure

**Azure ARC** étend la gestion d’Azure à toute infrastructure, notamment service Azure VMware Solution, infrastructure locale ou autre plateforme cloud. Vous pouvez déployer Azure ARC en installant un cluster Azure Kubernetes Service (AKS) dans l’environnement Azure VMware Solution. Pour plus d’informations, consultez [Connecter un cluster Kubernetes activé par Azure Arc](../azure-arc/kubernetes/connect-cluster.md).

Vous pouvez surveiller les machines virtuelles du service Azure VMware Solution via l’agent MMA (également appelé agent Log Analytics ou agent Linux OMS). L’agent MMA peut être installé automatiquement lorsque les machines virtuelles sont approvisionnées via des flux de travail de cycle de vie de machine virtuelle ARC. Il peut également être installé lors du déploiement de machines virtuelles à partir d’un modèle dans vCenter, là encore, avec des machines virtuelles approvisionnées via des flux de travail ARC. Toutes les machines virtuelles du service Azure VMware Solution approvisionnées peuvent alors avoir l’agent MMA installé et envoyer des journaux à l’espace de travail Azure Log Analytics. Pour plus d’informations, consultez [Présentation de l’agent Log Analytics](../azure-monitor/platform/log-analytics-agent.md).

L’**espace de travail Log Analytics** active la collecte des journaux et la collecte des compteurs de performances à l’aide de l’agent MMA ou d’extensions. Pour créer votre espace de travail Log Analytics, consultez [Créer un espace de travail Log Analytics dans le portail Azure](../azure-monitor/learn/quick-create-workspace.md).
- Pour ajouter des machines virtuelles Windows à votre espace de travail log Analytics, consultez [Installer l’agent Log Analytics sur des ordinateurs Windows](../azure-monitor/platform/agent-windows.md).
- Pour ajouter des machines virtuelles Linux à votre espace de travail log Analytics, consultez [Installer l’agent Log Analytics sur des ordinateurs Linux](../azure-monitor/platform/agent-linux.md).

Le service **Azure Update Management** dans Azure Automation gère les mises à jour du système d’exploitation de vos ordinateurs Windows et Linux dans un environnement hybride. Il surveille la conformité des mises à jour correctives, et transfère les alertes d’écart de mise à jour corrective à Azure Monitor à des fins de correction. La solution Azure Update Management doit se connecter à votre espace de travail Log Analytics pour utiliser les données stockées afin d’évaluer l’état des mises à jour sur vos machines virtuelles.
- Pour ajouter Log Analytics à la solution Azure Update Management, vous devez commencer par [créer un compte Azure Automation](../automation/automation-create-standalone-account.md).
- Pour lier votre espace de travail Log Analytics à votre compte Automation, consultez [Espace de travail Log Analytics et compte Automation](../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).
- Pour activer la solution Azure Update Management pour vos machines virtuelles, consultez [Activer Update Management à partir d’un compte Automation](../automation/update-management/update-mgmt-enable-automation-account.md).
- Une fois que vous avez ajouté des machines virtuelles à Azure Update Management, vous pouvez [déployer des mises à jour sur des machines virtuelles et examiner les résultats](../automation/update-management/update-mgmt-deploy-updates.md). 

Le service **Azure Security Center** offre une protection avancée contre les menaces pesant sur vos charges de travail hybrides dans le cloud et localement. Il évalue la vulnérabilité des machines virtuelles du service Azure VMware Solution, et déclenche des alertes en fonction des besoins. Ces alertes de sécurité peuvent être transférées à Azure Monitor à des fins de résolution.
- Le service Azure Security Center ne nécessite pas de déploiement. Pour plus d’informations, consultez la liste des [Fonctionnalités prises en charge pour les machines virtuelles](../security-center/security-center-services.md).
- Pour ajouter des machines virtuelles du service Azure VMware Solution et des machines virtuelles non Azure au service Azure Security Center, consultez [Intégrer des ordinateurs Windows à Azure Security Center](../security-center/quick-onboard-windows-computer.md) et [Intégrer des ordinateurs Linux à Azure Security Center](../security-center/quick-onboard-linux-computer.md).
- Après l’ajout de machines virtuelles, le service Azure Security Center analyse l’état de sécurité des ressources pour identifier des vulnérabilités potentielles. Il affiche également des recommandations sous l’onglet Vue d’ensemble. Pour plus d’informations, consultez les [recommandations de sécurité dans Azure Security Center](../security-center/security-center-recommendations.md).
- Vous pouvez définir des stratégies de sécurité dans Azure Security Center. Pour plus d’informations sur la configuration de vos stratégies de sécurité, consultez [Utilisation de stratégies de sécurité](../security-center/tutorial-security-policy.md).

Le service **Azure Monitor** est une solution complète pour la collecte, l’analyse et l’exploitation de la télémétrie de vos environnements cloud et locaux. Il ne nécessite aucun déploiement.
- Le service Azure Monitor vous permet de collecter des données à partir de différentes sources à des fins de surveillance et d’analyse. Pour plus d’informations, consultez [Sources des données de surveillance pour Azure Monitor](../azure-monitor/platform/data-sources.md).
- Vous pouvez également collecter différents types de données à des fins d’analyse, de visualisation et de génération d’alertes. Pour plus d’informations, consultez [Plateforme de données Azure Monitor](../azure-monitor/platform/data-platform.md).
- Pour configurer Azure Monitor avec votre espace de travail Log Analytics, consultez [Configurer l’espace de travail Log Analytics pour Azure Monitor pour machines virtuelles](../azure-monitor/insights/vminsights-configure-workspace.md).
- Vous pouvez créer des règles d’alerte afin d’identifier des problèmes dans votre environnement, tels que l’utilisation intensive de ressources, des correctifs manquants, un espace disque faible et des pulsations de vos machines virtuelles. Vous pouvez également définir une réponse automatisée aux événements détectés en envoyant une alerte aux outils de gestion des services informatiques (ITSM). Une notification de détection d’alerte peut également être envoyée par e-mail. Pour créer des règles de ce type, consultez :
    - [Créer, afficher et gérer des alertes de mesure à l’aide d’Azure Monitor](../azure-monitor/platform/alerts-metric.md).
    - [Créer, afficher et gérer des alertes de journal à l’aide d’Azure Monitor](../azure-monitor/platform/alerts-log.md).
    - [Règles d’action](../azure-monitor/platform/alerts-action-rules.md) pour définir des actions et notifications automatisées.
    - [Connecter Azure aux outils de gestion des services informatiques (ITSM) à l’aide du connecteur de gestion des services informatiques](../azure-monitor/platform/itsmc-overview.md).

Le **plateforme en tant que service (Platform as a Service, PaaS)** Azure est un environnement de développement et de déploiement dans le cloud, offrant des ressources qui vous permettent de livrer des applications basées sur le cloud. Par exemple, vous pouvez intégrer Azure SQL Database avec les machines virtuelles de votre service Azure VMware Solution. Vous pouvez ensuite corréler les alertes SQL avec les alertes de machine virtuelle du service Azure VMware Solution. Supposons, par exemple, que le pan SQL Database de votre application se trouve dans la PaaS Azure et que la couche application web de la même application est hébergée sur les machines virtuelles de votre service Azure VMware Solution. Les alertes de base de données peuvent alors être corrélées avec les alertes d’application web. La résolution des problèmes est simplifiée grâce à une visibilité unique et intégrée d’Azure, du service Azure VMware Solution et des machines virtuelles locales.
