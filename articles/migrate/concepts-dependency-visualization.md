---
title: Analyse des dépendances dans l’outil de découverte et d’évaluation d’Azure Migrate
description: Décrit comment utiliser l’analyse des dépendances pour évaluation à l’aide de l’outil de découverte et d’évaluation d’Azure Migrate.
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: 184c8099c0e86d8f8744948137b344c732bbf7b8
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778370"
---
# <a name="dependency-analysis"></a>Analyse des dépendances

Cet article décrit l’analyse des dépendances dans l’outil de découverte et d’évaluation d’Azure Migrate.

L’analyse des dépendances identifie les dépendances entre serveurs locaux découverts. Il offre les avantages suivants :

- Vous pouvez rassembler des serveurs dans des groupes à des fins d’évaluation de manière plus précise avec davantage de confiance.
- Vous pouvez identifier les serveurs qui doivent être migrés ensemble. Cela est particulièrement utile quand vous ne savez pas avec certitude quels serveurs font partie d’un déploiement d’application que vous souhaitez faire migrer vers Azure.
- Vous pouvez déterminer si les serveurs sont en cours d’utilisation, ou les serveurs que vous pouvez être désactiver au lieu de les migrer.
- L’analyse des dépendances permet de s’assurer que rien n’est laissé de côté et d’éviter ainsi les pannes inopinées après la migration.
- [Passez en revue les questions courantes sur l’analyse des dépendances](common-questions-discovery-assessment.md#what-is-dependency-visualization).

## <a name="analysis-types"></a>Types d’analyse

Deux options permettent de déployer l’analyse des dépendances.

**Option** | **Détails** | **Cloud public** | **Azure Government**
----  |---- | ----
**Sans agent** | Interroge les données des serveurs sur VMware à l’aide d’API vSphere.<br/><br/> Vous n’avez pas besoin d’installer des agents sur les serveurs.<br/><br/> Cette option est actuellement en préversion uniquement pour les serveurs sur VMware. | Pris en charge. | Pris en charge.
**Analyse basée sur les agents** | Utilise la [solution Service Map](../azure-monitor/vm/service-map.md) dans Azure Monitor pour activer la visualisation et l’analyse des dépendances.<br/><br/> Vous devez installer des agents sur chaque serveur local à analyser. | Prise en charge | Non pris en charge.

## <a name="agentless-analysis"></a>Analyse sans agent

L’analyse des dépendances sans agent fonctionne en capturant les données de connexion TCP des serveurs pour lesquels elle est activée. Aucun agent n’est installé sur les serveurs. Les connexions avec les mêmes serveur et processus source, ainsi que le serveur de destination, le processus et le port sont regroupés de façon logique dans une dépendance. Vous pouvez visualiser les données de dépendance capturées dans une vue cartographique ou les exporter au format CSV. Aucun agent n’est installé sur les serveurs à analyser.

### <a name="dependency-data"></a>Données de dépendance

Une fois la découverte des données de dépendance lancée, l’interrogation commence :

- L’appliance Azure Migrate interroge les données de connexion TCP des serveurs toutes les cinq minutes pour les collecter.
- Les données sont collectées à partir de serveurs invités via vCenter Server à l’aide d’API vSphere.
- L’interrogation rassemble ces données :

    - Nom des processus ayant des connexions actives
    - Nom de l’application qui exécute des processus qui ont des connexions actives
    - Port de destination sur les connexions actives

- Les données collectées sont traitées sur l’appliance Azure Migrate pour déduire les informations d’identité et sont envoyées à Azure Migrate toutes les six heures


## <a name="agent-based-analysis"></a>Analyse basée sur les agents

Pour l’analyse basée sur un agent, l’outil de découverte et d’évaluation d’Azure Migrate utilise la solution [Service Map](../azure-monitor/vm/service-map.md) dans Azure Monitor. Vous installez l’agent [Microsoft Monitoring Agent/Log Analytics](../azure-monitor/agents/agents-overview.md#log-analytics-agent) et l’[agent Dependency](../azure-monitor/agents/agents-overview.md#dependency-agent) sur chaque serveur que vous souhaitez analyser.

### <a name="dependency-data"></a>Données de dépendance

L’analyse basée sur les agents fournit les données suivantes :

- Nom du serveur source, processus, nom de l’application.
- Nom du serveur de destination, processus, nom de l’application et port.
- Le nombre de connexions, la latence et les informations de transfert de données sont collectés et mis à la disposition des requêtes Log Analytics.

## <a name="compare-agentless-and-agent-based"></a>Comparaison entre la visualisation sans agent et la visualisation basée sur les agents

Les différences entre la visualisation sans agent et la visualisation basée sur les agents sont résumées dans le tableau.

**Prérequis** | **Sans agent** | **Basé sur un agent**
--- | --- | ---
**Support** | En préversion pour les serveurs sur VMware uniquement. [Vérifiez](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) quels sont les systèmes d’exploitation pris en charge. | En disponibilité générale.
**Agent** | Aucun agent nécessaire sur les serveurs à analyser. | Agents requis sur chaque serveur local à analyser.
**Log Analytics** | Non requis. | Azure Migrate utilise la solution [Service Map](../azure-monitor/vm/service-map.md) dans les [journaux Azure Monitor](../azure-monitor/logs/log-query-overview.md) pour l’analyse des dépendances.<br/><br/> Vous associez un espace de travail Log Analytics avec un projet. L’espace de travail doit résider dans les régions USA Est, Asie Sud-Est ou Europe Ouest. L’espace de travail doit se trouver au sein d’une région dans laquelle [Service Map est pris en charge](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).
**Processus** | Capture les données de connexion TCP. Après la découverte, il collecte des données toutes les cinq minutes. | Les agents Service Map installés sur un serveur collectent des données sur les processus TCP et sur les connexions entrantes/sortantes pour chaque processus.
**Données** | Nom du serveur source, processus, nom de l’application.<br/><br/> Nom du serveur de destination, processus, nom de l’application et port. | Nom du serveur source, processus, nom de l’application.<br/><br/> Nom du serveur de destination, processus, nom de l’application et port.<br/><br/> Le nombre de connexions, la latence et les informations de transfert de données sont collectés et mis à la disposition des requêtes Log Analytics. 
**Visualisation** | La carte des dépendances d’un serveur unique peut être affichée sur une durée allant d’une heure à 30 jours. | Carte des dépendances d’un serveur unique.<br/><br/> Carte des dépendances d’un groupe de serveurs.<br/><br/>  La carte peut être affichée pendant une heure uniquement.<br/><br/> Ajoutez et supprimez des serveurs dans un groupe à partir de la vue cartographique.
Exportation de données | Les données des 30 derniers jours peuvent être téléchargées dans un format CSV. | Les données peuvent être interrogées avec Log Analytics.



## <a name="next-steps"></a>Étapes suivantes

- [Configurez](how-to-create-group-machine-dependencies.md) la visualisation des dépendances basée sur les agents.
- [Essayez](how-to-create-group-machine-dependencies-agentless.md) une visualisation des dépendances sans agent pour les serveurs sur VMware.
- Passer en revue les [questions courantes](common-questions-discovery-assessment.md#what-is-dependency-visualization) sur la visualisation des dépendances
