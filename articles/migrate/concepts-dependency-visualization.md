---
title: Analyse des dépendances dans l’outil d’évaluation de serveur Azure Migrate
description: Décrit comment utiliser l’analyse des dépendances pour l’évaluation à l’aide de l’outil d’évaluation de serveur Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: f0b956620895ae2264b53916015d440f5e586eb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024759"
---
# <a name="dependency-analysis"></a>Analyse des dépendances

Cet article décrit l’analyse des dépendances dans l’outil Azure Migrate : évaluation de serveur.

## <a name="overview"></a>Vue d’ensemble

L’analyse des dépendances vous permet d’identifier les dépendances entre les machines locales que vous souhaitez évaluer et faire migrer vers Azure. 

- Dans Azure Migrate : évaluation de serveur, vous rassemblez des machines dans un groupe, puis vous évaluez le groupe. L’analyse des dépendances vous permet de regrouper les machines de manière plus précise, avec une grande fiabilité d’évaluation.
- L’analyse des dépendances vous permet d’identifier les machines qui doivent être migrées ensemble. Vous pouvez déterminer si les machines sont en cours d’utilisation, ou si elles peuvent être désactivées au lieu d’être migrées.
- L’analyse des dépendances permet de s’assurer que rien n’est laissé de côté et d’éviter les pannes inopinées durant la migration.
- L’analyse est particulièrement utile quand vous ne savez pas avec certitude si les machines font partie d’un déploiement d’application que vous souhaitez faire migrer vers Azure.
- [Passez en revue les questions courantes sur l’analyse des dépendances](common-questions-discovery-assessment.md#what-is-dependency-visualization).

Deux options permettent de déployer l’analyse des dépendances.

- **Basée sur un agent** : l’analyse des dépendances basée sur les agents nécessite l’installation d’agents sur chaque machine locale à analyser.
- **Sans agent** : avec l’analyse sans agent, vous n’avez pas besoin d’installer d’agents sur les machines qui doivent faire l’objet d’une vérification croisée. Cette option est en préversion et est disponible uniquement pour les machines virtuelles VMware.

> [!NOTE]
> L’analyse des dépendances basée sur les agents n’est pas disponible dans Azure Government. Vous pouvez utiliser l’analyse des dépendances sans agent.

## <a name="agentless-analysis"></a>Analyse sans agent

L’analyse des dépendances sans agent fonctionne en capturant les données de connexion TCP des machines pour lesquelles elle est activée. Aucun agent n’est installé sur les machines à analyser.

### <a name="collected-data"></a>Données collectées

Après le démarrage de la découverte des dépendances, l’appliance interroge les données des machines toutes les cinq minutes pour les rassembler. Ces données sont collectées à partir de machines virtuelles invitées par le biais de vCenter Server, à l’aide d’API vSphere. Les données collectées sont traitées sur l’appliance Azure Migrate pour déduire les informations d’identité et sont envoyées à Azure Migrate toutes les six heures.

L’interrogation rassemble ces données à partir des machines : 
- Nom des processus ayant des connexions actives
- Nom de l’application qui exécute des processus qui ont des connexions actives
- Port de destination sur les connexions actives

## <a name="agent-based-analysis"></a>Analyse basée sur les agents

Pour l’analyse basée sur les agents, l’outil d’évaluation de serveur utilise la [solution Service Map](../azure-monitor/insights/service-map.md) dans Azure Monitor pour activer la visualisation et l’analyse des dépendances. L’agent [Microsoft Monitoring Agent/Log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent) et l’[agent de dépendances](../azure-monitor/platform/agents-overview.md#dependency-agent) doivent être installés sur chaque machine que vous souhaitez analyser.

### <a name="collected-data"></a>Données collectées

Pour l’analyse basée sur les agents, les données suivantes sont collectées :

- Nom du serveur de l’ordinateur source, processus, nom de l’application.
- Nom du serveur de l’ordinateur de destination, processus, nom de l’application et port.
- Le nombre de connexions, la latence et les informations de transfert de données sont collectés et mis à la disposition des requêtes Log Analytics. 


## <a name="compare-agentless-and-agent-based"></a>Comparaison entre la visualisation sans agent et la visualisation basée sur les agents

Les différences entre la visualisation sans agent et la visualisation basée sur les agents sont résumées dans le tableau.

**Prérequis** | **Sans agent** | **Basé sur un agent**
--- | --- | ---
Support | Cette option est en préversion et est disponible uniquement pour les machines virtuelles VMware. [Vérifiez](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) quels sont les systèmes d’exploitation pris en charge. | En disponibilité générale.
Agent | Inutile d’installer des agents sur les machines qui doivent faire l’objet d’une vérification croisée. | Agents à installer sur chaque machine locale à analyser : [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) et l’[Agent de dépendances](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Log Analytics | Non requis. | Azure Migrate utilise la solution [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) dans les [journaux Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) pour l’analyse des dépendances. 
Fonctionnement | Capture les données de connexion TCP sur les ordinateurs activés pour la visualisation des dépendances. Après la découverte, il collecte des données toutes les cinq minutes. | Les agents Service Map installés sur un ordinateur collectent des données sur les processus TCP et sur les connexions entrantes/sortantes pour chaque processus.
Données | Nom du serveur de l’ordinateur source, processus, nom de l’application.<br/><br/> Nom du serveur de l’ordinateur de destination, processus, nom de l’application et port. | Nom du serveur de l’ordinateur source, processus, nom de l’application.<br/><br/> Nom du serveur de l’ordinateur de destination, processus, nom de l’application et port.<br/><br/> Le nombre de connexions, la latence et les informations de transfert de données sont collectés et mis à la disposition des requêtes Log Analytics. 
Visualisation | La carte des dépendances d’un serveur unique peut être affichée sur une durée allant d’une heure à 30 jours. | Carte des dépendances d’un serveur unique.<br/><br/> La carte peut être affichée pendant une heure uniquement.<br/><br/> Carte des dépendances d’un groupe de serveurs.<br/><br/> Ajoutez et supprimez des serveurs dans un groupe à partir de la vue cartographique.
Exportation de données | Ne peut pas être téléchargée sous forme de tableau. | Les données peuvent être interrogées avec Log Analytics.



## <a name="next-steps"></a>Étapes suivantes
- Passez en revue les exigences concernant la configuration de l’analyse basée sur les agents pour les [machines virtuelles VMware](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements), les [serveurs physiques](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements) et les [machines virtuelles Hyper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- [Passez en revue](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) les exigences relatives à l’analyse sans agent des machines virtuelles VMware.
- [Configurez](how-to-create-group-machine-dependencies.md) la visualisation des dépendances basée sur les agents.
- [Essayez](how-to-create-group-machine-dependencies-agentless.md) la visualisation des dépendances sans agent pour les machines virtuelles VMware.
- Passer en revue les [questions courantes](common-questions-discovery-assessment.md#what-is-dependency-visualization) sur la visualisation des dépendances


