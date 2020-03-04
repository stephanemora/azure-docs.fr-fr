---
title: Visualisation des dépendances dans Azure Migrate
description: Fournit une vue d’ensemble des calculs d’évaluation dans le service d’évaluation de serveur de Azure Migrate
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: f24656d02e19f422ff26e6b06d1631a9128dff43
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587105"
---
# <a name="dependency-visualization"></a>Visualisation de dépendance

Cet article décrit la visualisation des dépendances dans Azure Migrate : évaluation de serveur.

## <a name="what-is-dependency-visualization"></a>Qu’est-ce que la visualisation des dépendances ?

La visualisation des dépendances vous permet d’identifier les dépendances entre les machines locales que vous souhaitez évaluer et faire migrer vers Azure. 

- Dans Azure Migrate : évaluation de serveur, vous rassemblez des machines dans un groupe, puis vous évaluez le groupe. La visualisation des dépendances vous aide à regrouper les machines de manière plus précise, avec une grande fiabilité d’évaluation.
- La visualisation des dépendances vous permet d’identifier les machines qui doivent être migrées ensemble. Vous pouvez déterminer si les machines sont en cours d’utilisation, ou si elles peuvent être désactivées au lieu d’être migrées.
- La visualisation des dépendances permet de s’assurer que rien n’est laissé de côté et d’éviter les pannes inopinées lors de la migration.
- La visualisation est particulièrement utile quand vous ne savez pas avec certitude si les machines font partie d’un déploiement d’application que vous souhaitez faire migrer vers Azure.


> [!NOTE]
> La visualisation des dépendances n’est pas disponible dans Azure Government.

## <a name="agent-basedagentless-visualization"></a>Visualisation basée sur un agent ou sans agent

Deux options permettent de déployer la visualisation des dépendances :

- **Basée sur un agent** : La visualisation des dépendances basée sur un agent nécessite l’installation d’agents sur chaque machine locale à analyser.
- **Sans agent** : Avec cette option, vous n’avez pas besoin d’installer d’agents sur les machines qui doivent faire l’objet d’une vérification croisée. Cette option est en préversion et est disponible uniquement pour les machines virtuelles VMware.


## <a name="agent-based-visualization"></a>Visualisation basée sur un agent

**Prérequis** | **Détails** | **En savoir plus**
--- | --- | ---
**Avant le déploiement** | Vous devez disposer d’un projet Azure Migrate en place et de l’outil Azure Migrate : Server Assessment ajouté au projet.<br/><br/>  Vous déployez la visualisation des dépendances après avoir configuré une appliance Azure Migrate pour découvrir vos machines locales. | [Découvrez comment](create-manage-projects.md) créer un projet pour la première fois.<br/><br/> [Découvrez comment](how-to-assess.md) ajouter un outil d’évaluation à un projet existant.<br/><br/> Découvrez comment configurer l’appliance Azure Migrate pour l’évaluation de serveurs [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md) ou physiques.
**Agents nécessaires** | Sur chaque machine à analyser, installez les agents suivants :<br/><br/> [MMA (Microsoft Monitoring Agent)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> Si les machines locales ne sont pas connectées à Internet, vous devez télécharger et installer la passerelle de l’espace de travail Log Analytics sur celles-ci. | En savoir plus sur l’installation de [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) et de [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics** | Azure Migrate utilise la solution [Service Map](../operations-management-suite/operations-management-suite-service-map.md) dans [Journaux d’activité Azure Monitor](../log-analytics/log-analytics-overview.md) pour la visualisation des dépendances.<br/><br/> Vous associez un espace de travail Log Analytics nouveau ou déjà existant à un projet Azure Migrate. L’espace de travail d’un projet Azure Migrate ne peut pas être modifié une fois qu’il a été ajouté. <br/><br/> L’espace de travail doit se trouver dans le même abonnement que le projet Azure Migrate.<br/><br/> L’espace de travail doit résider dans les régions USA Est, Asie Sud-Est ou Europe Ouest. Les espaces de travail des autres régions ne peuvent pas être associés à un projet.<br/><br/> L’espace de travail doit se trouver au sein d’une région dans laquelle [Service Map est pris en charge](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> Dans Log Analytics, l’espace de travail associé à Azure Migrate est marqué avec la clé de projet de migration et le nom du projet.
**Coûts** | La solution Service Map n’entraîne aucun frais pendant les 180 premiers jours (à compter du jour où vous associez l’espace de travail Log Analytics au projet Azure Migrate).<br/><br/> Au bout de 180 jours, des frais Log Analytics standard s’appliquent.<br/><br/> L’utilisation d’une autre solution que Service Map dans l’espace de travail Log Analytics associé entraîne des [frais standard](https://azure.microsoft.com/pricing/details/log-analytics/) pour l’espace de travail Log Analytics.<br/><br/> Lorsque le projet Azure Migrate est supprimé, l’espace de travail ne l’est pas. Une fois le projet supprimé, l’utilisation de Service Map n’est plus gratuite, et chaque nœud est facturé en fonction du niveau payant de l’espace de travail Log Analytics<br/><br/>Si vous avez créé des projets avant la disponibilité générale d’Azure Migrate (28 février 2018), vous avez peut-être fait l’objet de frais supplémentaires pour Service Map. Pour veiller à payer uniquement après 180 jours, nous vous recommandons de créer un autre projet, car les espaces de travail présents avant la disponibilité générale restent facturables.
**Gestion** | Lorsque vous inscrivez des agents dans l’espace de travail, vous utilisez l’ID et la clé fournis par le projet Azure Migrate.<br/><br/> Vous pouvez utiliser l’espace de travail Log Analytics en dehors d’Azure Migrate.<br/><br/> Si vous supprimez le projet Azure Migrate associé, l’espace de travail n’est pas automatiquement supprimé. [Supprimez-le manuellement](../azure-monitor/platform/manage-access.md).<br/><br/> Ne supprimez pas l’espace de travail créé par Azure Migrate, sauf si vous supprimez le projet Azure Migrate. La suppression de l’espace de travail entraînerait un dysfonctionnement de la fonctionnalité de visualisation des dépendances.

## <a name="agentless-visualization"></a>Visualisation sans agent


**Prérequis** | **Détails** | **En savoir plus**
--- | --- | ---
**Avant le déploiement** | Vous devez disposer d’un projet Azure Migrate en place et de l’outil Azure Migrate : Server Assessment ajouté au projet.<br/><br/>  Vous déployez la visualisation des dépendances après avoir configuré une appliance Azure Migrate pour découvrir vos machines VMware locales. | [Découvrez comment](create-manage-projects.md) créer un projet pour la première fois.<br/><br/> [Découvrez comment](how-to-assess.md) ajouter un outil d’évaluation à un projet existant.<br/><br/> Découvrez comment configurer l’appliance Azure Migrate pour l’évaluation des machines virtuelles [VMware](how-to-set-up-appliance-vmware.md).
**Agents nécessaires** | Aucun agent n’est nécessaire sur les machines à analyser.
**Systèmes d’exploitation pris en charge** | Passez en revue les [systèmes d’exploitation](migrate-support-matrix-vmware.md#agentless-dependency-visualization) pris en charge pour la visualisation sans agent.
**Machines virtuelles** | **VMware Tools** : VMware Tools doit être installé et en cours d’exécution sur les machines virtuelles à analyser.<br/><br/> **Compte**  : Sur l’appliance Azure Migrate, vous devez ajouter un compte d’utilisateur ayant accès aux machines virtuelles à des fins d’analyse.<br/><br/> **Machines virtuelles Windows** : Le compte d’utilisateur doit être celui d’un administrateur local ou d’un administrateur de domaine sur la machine.<br/><br/> **Machines virtuelles Linux** : Le privilège de racine est requis sur le compte. Le compte d’utilisateur a également besoin des deux capacités ci-dessous sur les fichiers/bin/netstat et/bin/ls : CAP_DAC_READ_SEARCH et CAP_SYS_PTRACE. | [En savoir plus sur](migrate-appliance.md) l’appliance Azure Migrate.
**VMware** | **vCenter** : L’appliance a besoin d’un compte vCenter Server disposant d’un accès en lecture seule, et de privilèges activés pour Machines virtuelles> Opérations d’invité.<br/><br/> **Hôtes ESXi** : Sur les hôtes ESXi exécutant des machines virtuelles que vous souhaitez analyser, l’appliance Azure Migrate doit être en mesure de se connecter au port TCP 443.
**Données collectées** |  La visualisation des dépendances sans agent fonctionne via la capture de données de connexion TCP des machines pour lesquelles elle est activée. Après le démarrage de la détection des dépendances, l’appliance rassemble les données des machines en les interrogeant toutes les cinq minutes :<br/> - Connexions TCP.<br/> - Noms des processus ayant des connexions actives.<br/> - Noms des applications installées qui exécutent le processus avec des connexions actives.<br/> - Nombre de connexions détectées à chaque intervalle d’interrogation.


## <a name="next-steps"></a>Étapes suivantes
- [Configurer la visualisation des dépendances](how-to-create-group-machine-dependencies.md)
- [Essayer la visualisation des dépendances sans agent](how-to-create-group-machine-dependencies-agentless.md) pour les machines virtuelles VMware
- Passer en revue les [questions courantes](common-questions-discovery-assessment.md#what-is-dependency-visualization) sur la visualisation des dépendances


