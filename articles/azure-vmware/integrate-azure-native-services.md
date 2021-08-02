---
title: Intégrer et déployer des services Azure natifs
description: Découvrez comment intégrer et déployer les outils natifs Microsoft Azure pour surveiller et gérer vos charges de travail Azure VMware Solution.
ms.topic: how-to
ms.date: 06/14/2021
ms.openlocfilehash: e7af5617ee62f451f57daf806f70c7c433e40612
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111758118"
---
# <a name="integrate-and-deploy-azure-native-services"></a>Intégrer et déployer des services Azure natifs

Les services natifs Microsoft Azure vous permettent de surveiller, gérer et protéger vos machines virtuelles dans un environnement hybride (Azure, Azure VMware Solution et en local). Pour plus d’informations, consultez [Fonctionnalités prises en charge pour les machines virtuelles](../security-center/security-center-services.md).

Les services natifs Azure que vous pouvez intégrer à Azure VMware Solution sont les suivants :

- **Espace de travail Log Analytics :** chaque espace de travail possède son propre référentiel de données et sa propre configuration pour le stockage des données de journal. Les solutions et sources de données sont configurées pour stocker leurs données dans un espace de travail spécifique. Déployez facilement l’agent Log Analytics à l’aide des serveurs Azure Arc prenant en charge l’extension de machine virtuelle pour les machines virtuelles nouvelles et existantes. 
- **Azure Security Center :** un système d’administration de la sécurité de l’infrastructure unifié qui renforce la sécurité des centres de données et fournit une protection avancée contre les menaces pour vos charges de travail hybrides dans le cloud ou en local. 
- **Azure Sentinel** est une solution SIEM (Informations de sécurité et gestion d’événements) native cloud. Elle fournit une analyse de la sécurité, une détection des alertes et une réponse automatisée aux menaces dans un environnement. Azure Sentinel est basé sur un espace de travail Log Analytics.
- **Azure Arc :** étend la gestion d’Azure à toute infrastructure, notamment Azure VMware Solution, les infrastructures locales ou autres plateformes cloud. 
- **Azure Update Management :** gère les mises à jour du système d’exploitation de vos ordinateurs Windows et Linux dans un environnement hybride.
- **Azure Monitor** : solution complète pour la collecte, l’analyse et l’exploitation de la télémétrie à partir de vos environnements cloud et locaux. Il ne nécessite aucun déploiement. 

Dans cet article, vous allez intégrer les services natifs Azure dans votre cloud privé Azure VMware Solution. Vous apprendrez également à utiliser les outils permettant de gérer vos machines virtuelles tout au long de leur cycle de vie. 


## <a name="enable-azure-update-management"></a>Activer Azure Update Management

Le service [Azure Update Management](../automation/update-management/overview.md) dans Azure Automation gère les mises à jour du système d’exploitation de vos ordinateurs Windows et Linux dans un environnement hybride. Il surveille la conformité des mises à jour correctives, et transfère les alertes d’écart de mise à jour corrective à Azure Monitor à des fins de correction. La solution Azure Update Management doit se connecter à votre espace de travail Log Analytics pour utiliser les données stockées afin d’évaluer l’état des mises à jour sur vos machines virtuelles.

1. [Créez un compte Azure Automation](../automation/automation-create-standalone-account.md). 

   >[!TIP]
   >Vous pouvez [utiliser un modèle Azure Resource Manager (ARM) afin de créer un compte Automation](../automation/quickstart-create-automation-account-template.md). L’utilisation d’un modèle ARM comprend moins d’étapes que les autres méthodes de déploiement.

1. [Activez Update Management à partir d’un compte Automation](../automation/update-management/enable-from-automation-account.md).  Ainsi, vous pouvez lier votre espace de travail Log Analytics à votre compte Automation. Cela active également des machines virtuelles Azure et non-Azure dans Update Management.

   - Si vous disposez d’un espace de travail, sélectionnez **Gestion des mises à jour**. Sélectionnez ensuite l’espace de travail Log Analytics et le compte Automation, puis cliquez sur **Activer**. Le configuration prend jusqu’à 15 minutes.

   - Si vous souhaitez créer un nouvel espace de travail Log Analytics, consultez [Créer un espace de travail Log Analytics dans le Portail Azure](../azure-monitor/logs/quick-create-workspace.md). Vous pouvez également créer un espace de travail avec [CLI](../azure-monitor/logs/quick-create-workspace-cli.md), [PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md) ou un [modèle Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md).
 
1. Une fois Update Management activé, vous pouvez [déployer des mises à jour sur des machines virtuelles et examiner les résultats](../automation/update-management/deploy-updates.md). 


## <a name="enable-azure-security-center"></a>Activation d’Azure Security Center

Azure Security Center offre une protection avancée contre les menaces sur vos machines virtuelles Azure VMware Solution et locales. Il évalue la vulnérabilité des machines virtuelles Azure VMware Solution et déclenche une alerte si nécessaire. Ces alertes de sécurité peuvent être transférées à Azure Monitor à des fins de résolution. 

Azure Security Center offre de nombreuses fonctionnalités, notamment :
- Monitoring d’intégrité de fichier
- Détection des attaques sans fichier
- Évaluation de correctif du système d’exploitation 
- Évaluation des erreurs de configuration de la sécurité
- Évaluation de la protection des points de terminaison

>[!NOTE]
>Azure Security Center est un outil préconfiguré qui ne nécessite aucun déploiement, mais vous devez l’activer dans le Portail Azure. 

Pour activer Azure Security Center, consultez [Intégrer Azure Security Center avec Azure VMware Solution](azure-security-integration.md).

## <a name="onboard-vms-to-azure-arc-enabled-servers"></a>Intégrer des machines virtuelles à des serveurs Azure Arc

Azure Arc étend la gestion d’Azure à toutes les infrastructures, notamment Azure VMware Solution et les infrastructures locales.  Les [serveurs avec Azure Arc](../azure-arc/servers/overview.md) vous permettent de gérer vos machines virtuelles et vos serveurs physiques Windows et Linux hébergés *en dehors* d’Azure sur votre réseau d’entreprise ou sur un autre fournisseur de cloud.

Pour plus d’informations sur l’activation de serveurs Azure Arc pour plusieurs machines virtuelles Windows ou Linux, consultez [Connecter des machines hybrides à Azure à grande échelle](../azure-arc/servers/onboard-service-principal.md).

## <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>Intégrer des clusters Kubernetes hybrides avec les Kubernetes Arc

[Kubernetes avec Azure Arc](../azure-arc/kubernetes/overview.md) vous permet de joindre un cluster Kubernetes hébergé dans votre environnement Azure VMware Solution. 

Pour plus d’informations, consultez [Créer un principal de service d’intégration compatible avec Azure Arc](../azure-arc/kubernetes/create-onboarding-service-principal.md).

## <a name="deploy-the-log-analytics-agent"></a>Déployer l’agent Log Analytics

Vous pouvez surveiller les machines virtuelles Azure VMware Solution via l’agent Log Analytics. Les machines connectées à des espaces de travail Log Analytics utilisent l’[agent Log Analytics](../azure-monitor/agents/log-analytics-agent.md) pour collecter des données sur les modifications apportées aux logiciels installés, aux services Microsoft, au registre et aux fichiers Windows, ainsi qu’aux démons Linux sur les serveurs analysés. Lorsque des données sont disponibles, l’agent les envoie aux journaux Azure Monitor pour traitement. Les journaux Azure Monitor appliquent une logique aux données reçues, les enregistrent et les rendent disponibles pour analyse.

Déployez l’agent Log Analytics à l’aide des [serveurs Azure Arc prenant en charge l’extension de machine virtuelle](../azure-arc/servers/manage-vm-extensions.md).

## <a name="enable-azure-monitor"></a>Activer Azure Monitor

Le service [Azure Monitor](../azure-monitor/overview.md) est une solution complète pour la collecte, l’analyse et l’exploitation de la télémétrie de vos environnements cloud et locaux. Les avantages supplémentaires d’Azure Monitor sont les suivants : 

   - Surveillance transparente 

   - Meilleure visibilité de l’infrastructure 

   - Notifications instantanées 

   - Résolution automatique 

   - Rentabilité 

Vous pouvez collecter des données à partir de différentes sources à des fins de surveillance et d’analyse. Pour plus d’informations, consultez [Sources des données de surveillance pour Azure Monitor](../azure-monitor/agents/data-sources.md).  Vous pouvez également collecter différents types de données à des fins d’analyse, de visualisation et de génération d’alertes. Pour plus d’informations, consultez [Plateforme de données Azure Monitor](../azure-monitor/data-platform.md). 

Vous pouvez surveiller les performances du système d’exploitation invité, ainsi que découvrir et mapper des dépendances d’application pour des machines virtuelles Azure VMware Solution ou locales. Vous pouvez également créer des règles d’alerte afin d’identifier des problèmes dans votre environnement, tels que l’utilisation intensive de ressources, des patchs manquants, un espace disque faible et des pulsations de vos machines virtuelles. Vous pouvez également définir une réponse automatisée aux événements détectés en envoyant une alerte aux outils ITSM (gestion des services informatiques). Une notification de détection d’alerte peut également être envoyée par e-mail.


1. [Concevoir votre déploiement de journaux d’activité Azure Monitor](../azure-monitor/logs/design-logs-deployment.md)

1. [Vue d’ensemble de l’activation d’Azure Monitor pour machines virtuelles](../azure-monitor/vm/vminsights-enable-overview.md)

1. [Configurer l’espace de travail Log Analytics pour Azure Monitor pour machines virtuelles](../azure-monitor/vm/vminsights-configure-workspace.md).

1. Créez des règles d’alerte afin d’identifier les problèmes dans votre environnement :
   - [Créer, afficher et gérer des alertes de mesure à l’aide d’Azure Monitor](../azure-monitor/alerts/alerts-metric.md).
   - [Créer, afficher et gérer des alertes de journal à l’aide d’Azure Monitor](../azure-monitor/alerts/alerts-log.md).
   - [Règles d’action](../azure-monitor/alerts/alerts-action-rules.md) pour définir des actions et notifications automatisées.
   - [Connecter Azure aux outils de gestion des services informatiques (ITSM) à l’aide du connecteur de gestion des services informatiques](../azure-monitor/alerts/itsmc-overview.md).