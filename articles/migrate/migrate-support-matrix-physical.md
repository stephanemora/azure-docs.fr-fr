---
title: Prise en charge pour l’évaluation de serveurs physiques à l’aide d’Azure Migrate
description: Découvrez la prise en charge pour l’évaluation de serveurs physiques à l’aide d’Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 489f95bbbbeb261b56f1a3a86da44f5fcce0adf5
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846574"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Tableau de prise en charge pour l’évaluation de serveurs physiques 

Vous pouvez utiliser le [service Azure Migrate](migrate-overview.md) pour évaluer et migrer des machines vers le cloud Microsoft Azure. Cet article récapitule les paramètres et les limites de la prise en charge de l’évaluation et la migration de serveurs physiques locaux.


## <a name="overview"></a>Vue d’ensemble

Pour évaluer des machines locales pour une migration vers Azure avec cet article, vous ajoutez l’outil Azure Migrate : Server Assessment à un projet Azure Migrate. Vous déployez l’[appliance Azure Migrate](migrate-appliance.md). L’appliance découvre en permanence les machines locales et envoie les données de configuration et de performances à Azure. Après la découverte des machines, vous rassemblez les machines découvertes dans des groupes et effectuez l’évaluation d’un groupe

## <a name="limitations"></a>Limites

**Support** | **Détails**
--- | ---
**Limites d’évaluation**| Découvrez et évaluez jusqu’à 35 000 serveurs physiques dans un même [projet](migrate-support-matrix.md#azure-migrate-projects).
**Limites de projet** | Vous pouvez créer plusieurs projets dans un abonnement Azure. Un projet peut inclure à la fois des machines virtuelles VMware et Hyper-V, et des serveurs physiques, jusqu’aux limites d’évaluation.
**Découverte** | L’appliance Azure Migrate peut découvrir jusqu’à 250 serveurs physiques.
**Évaluation** | Vous pouvez ajouter jusqu’à 35 000 machines dans un groupe unique.<br/><br/> Vous pouvez évaluer jusqu’à 35 000 machines par évaluation.

[Apprenez-en davantage](concepts-assessment-calculation.md) sur les évaluations.




## <a name="physical-server-requirements"></a>Conditions requises des serveurs physiques

| **Support**                | **Détails**               
| :-------------------       | :------------------- |
| **Déploiement de serveur physique**       | Le serveur physique peut être autonome ou déployé dans un cluster. |
| **autorisations**           | **Windows :** Configurez un compte d’utilisateur local sur tous les serveurs Windows que vous souhaitez inclure dans la découverte. Le compte d’utilisateur doit être ajouté à ces groupes : utilisateurs du Bureau à distance, utilisateurs de l’Analyseur de performances et utilisateurs du journal des performances. <br/> **Linux :** Vous devez disposer d’un compte racine sur les serveurs Linux que vous souhaitez découvrir. |
| **Système d’exploitation** | Tous les systèmes d'exploitation [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) et [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) sont pris en charge, à l’exception des suivants :<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="azure-migrate-appliance-requirements"></a>Conditions requises de l’appliance Azure Migrate

Azure Migrate utilise l’[appliance Azure Migrate](migrate-appliance.md) pour la découverte et l’évaluation. L’appliance pour les serveurs physiques peut s’exécuter sur une machine virtuelle ou sur une machine physique. Vous la configurez à l’aide d’un script PowerShell que vous téléchargez à partir du portail Azure.

- En savoir plus sur les [conditions requises de l’appliance](migrate-appliance.md#appliance---physical) pour les serveurs physiques.
- En savoir plus sur les [URL](migrate-appliance.md#url-access) auxquelles l’appliance doit accéder.

## <a name="port-access"></a>Accès au port

Le tableau suivant résume les exigences du port pour l’évaluation.

**Appareil** | **Connection**
--- | ---
**Appliance** | Connexions entrantes sur le port TCP 3389 pour permettre des connexions Bureau à distance avec l’appliance.<br/> Connexions entrantes sur le port 44368 pour accéder à distance à l’application de gestion de l’appliance via l’URL : ``` https://<appliance-ip-or-name>:44368 ```<br/> Connexions sortantes sur les ports 443 (HTTPS), 5671 et 5672 (AQMP) pour l’envoi de métadonnées de découverte et de performances à Azure Migrate.
**Serveurs physiques** | **Windows :** Connexions entrantes sur les ports WinRM 5985 (HTTP) et 5986 (HTTPS) pour extraire les métadonnées de configuration et de performance des serveurs Windows. <br/> **Linux :**  Connexions entrantes sur le port 22 (UDP) pour extraire les métadonnées de configuration et de performance des serveurs Linux. |

## <a name="agent-based-dependency-visualization"></a>Visualisation des dépendances basée sur les agents

La [visualisation des dépendances](concepts-dependency-visualization.md) vous permet de visualiser les dépendances entre les machines que vous voulez évaluer et migrer. Pour la visualisation basée sur les agents, les exigences et les limitations sont résumées dans le tableau suivant.


**Prérequis** | **Détails**
--- | ---
**Déploiement** | Avant de déployer la visualisation des dépendances, vous devez disposer d’un projet Azure Migrate, avec l'outil Azure Migrate : Server Assessment ajouté au projet. Vous déployez la visualisation des dépendances après avoir configuré une appliance Azure Migrate pour découvrir vos machines locales.<br/><br/> La visualisation des dépendances n’est pas disponible dans Azure Government.
**Service Map** | La visualisation des dépendances basée sur les agents utilise la solution [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) dans les [journaux Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Pour déployer, vous associez un espace de travail Log Analytics nouveau ou existant à un projet Azure Migrate.
**Espace de travail Log Analytics** | L’espace de travail doit se trouver dans le même abonnement que le projet Azure Migrate.<br/><br/> Azure Migrate prend en charge les espaces de travail résidant dans les régions USA Est, Asie Sud-Est et Europe Ouest.<br/><br/>  L’espace de travail doit se trouver au sein d’une région dans laquelle [Service Map est pris en charge](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> L’espace de travail d’un projet Azure Migrate ne peut pas être modifié une fois qu’il a été ajouté.
**Frais** | La solution Service Map n’entraîne pas de frais pendant les 180 premiers jours (à compter du jour où vous associez l’espace de travail Log Analytics au projet Azure Migrate).<br/><br/> Au bout de 180 jours, des frais Log Analytics standard s’appliquent.<br/><br/> L’utilisation de solutions autres que Service Map dans l’espace de travail Log Analytics associé entraîne des frais Log Analytics standard.<br/><br/> Si vous supprimez le projet Azure Migrate, l’espace de travail n’est pas supprimé avec celui-ci. Une fois le projet supprimé, l’utilisation de Service Map n’est pas gratuite et chaque nœud est facturé en fonction du niveau payant de l’espace de travail Log Analytics.
**Agents** | La visualisation des dépendances basée sur les agents nécessite l’installation de deux agents sur chaque machine à analyser.<br/><br/> - [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [Agent de dépendances](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
**Connectivité Internet** | Si les machines ne sont pas connectées à Internet, vous devez installer la passerelle Log Analytics sur celles-ci.

## <a name="next-steps"></a>Étapes suivantes

[Préparer l’évaluation des serveurs physiques](tutorial-prepare-physical.md).
