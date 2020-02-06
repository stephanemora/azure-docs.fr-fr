---
title: Prise en charge de l’évaluation VMware dans Azure Migrate
description: Découvrez la prise en charge de l’évaluation VMware dans Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 8ed20ecd37eacdcb771db7c166ff8fc22b96cb89
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846180"
---
# <a name="support-matrix-for-vmware-assessment"></a>Tableau de prise en charge pour l’évaluation VMware 

Cet article résume les paramètres et les limites de l’évaluation de machines virtuelles VMware avec [Azure Migrate : Évaluation de serveur](migrate-services-overview.md#azure-migrate-server-migration-tool). Si vous recherchez des informations sur la migration de machines virtuelles VMware vers Azure, consultez le [tableau de prise en charge de la migration](migrate-support-matrix-vmware-migration.md).

## <a name="overview"></a>Vue d’ensemble

Pour évaluer des machines locales pour une migration vers Azure avec cet article, vous ajoutez l’outil Azure Migrate : Server Assessment à un projet Azure Migrate. Vous déployez l’[appliance Azure Migrate](migrate-appliance.md). L’appliance découvre en permanence les machines locales et envoie les données de configuration et de performances à Azure. Après la découverte des machines, vous rassemblez les machines découvertes dans des groupes et effectuez l’évaluation d’un groupe.


## <a name="limitations"></a>Limites

**Support** | **Détails**
--- | ---
**Limites d’évaluation**| Découvrez et évaluez jusqu’à 35 000 machines virtuelles VMware dans un même [projet](migrate-support-matrix.md#azure-migrate-projects).
**Limites de projet** | Vous pouvez créer plusieurs projets dans un abonnement Azure. Un projet peut inclure à la fois des machines virtuelles VMware et Hyper-V, et des serveurs physiques, jusqu’aux limites d’évaluation.
**Découverte** | L’appliance Azure Migrate peut découvrir jusqu’à 10 000 machines virtuelles VMware sur un vCenter Server.
**Évaluation** | Vous pouvez ajouter jusqu’à 35 000 machines dans un groupe unique.<br/><br/> Vous pouvez évaluer jusqu’à 35,000 machines virtuelles par évaluation.

[Apprenez-en davantage](concepts-assessment-calculation.md) sur les évaluations.


## <a name="application-discovery"></a>Découverte des applications

En plus de découvrir les machines, Azure Migrate : Server Assessment peut découvrir les applications, rôles et fonctionnalités exécutées sur les machines. La découverte de votre inventaire d’applications vous permet d’identifier et de planifier un chemin de migration adapté à vos charges de travail locales. 

**Support** | **Détails**
--- | ---
**Découverte** | La découverte est sans agent, en utilisant les informations d’identification de l’invité de la machine, et en accédant à distance à des machines à l’aide des appels WMI et SSH.
**Machines prises en charge** | Machines virtuelles VMware locales.
**Système d’exploitation de la machine** | Toutes les versions de Windows et Linux.
**informations d’identification de vCenter** | Compte vCenter Server disposant d’un accès en lecture seule, et privilèges activés pour les Machines virtuelles > Opérations d’invité.
**Informations d’identification de machine virtuelle** | Prend actuellement en charge l’utilisation d’une information d’identification pour tous les serveurs Windows et d’une information d’identification pour tous les serveurs Linux.<br/><br/> Vous créez un compte d’utilisateur invité pour les machines virtuelles Windows et un compte d’utilisateur standard/normal (accès non-sudo) pour toutes les machines virtuelles Linux.
**Outils VMware** | Les outils VMware doivent être installés et en cours d’exécution sur les machines virtuelles que vous souhaitez découvrir.
**Accès au port** | Sur les hôtes ESXi exécutant des machines virtuelles que vous souhaitez découvrir, l’appliance Azure Migrate doit être en mesure de se connecter au port TCP 443.
**Limites** | Pour la découverte d’application, vous pouvez en découvrir jusqu’à 10 000 par appliance. 

## <a name="vmware-requirements"></a>Configuration requise pour VMware

**VMware** | **Détails**
--- | ---
**vCenter Server** | Les machines que vous souhaitez découvrir et évaluer doivent être gérées par vCenter Server version 5.5, 6.0, 6.5 ou 6.7.
**Autorisations (évaluation)** | Compte vCenter Server en lecture seule.
**Autorisations (découverte d’application)** | Compte vCenter Server disposant d’un accès en lecture seule et privilèges activés pour les **machines virtuelles > opérations d’invité**.
**Autorisations (visualisation des dépendances)** | Compte vCenter Server disposant d’un accès en lecture seule, et privilèges activés pour **Machines virtuelles** > **Opérations d’invité**.


## <a name="azure-migrate-appliance-requirements"></a>Conditions requises de l’appliance Azure Migrate

Azure Migrate utilise l’[appliance Azure Migrate](migrate-appliance.md) pour la découverte et l’évaluation. L’appliance pour VMware est déployée à l’aide d’un modèle OVA importé dans vCenter Server. 

- En savoir plus sur les [conditions requises de l’appliance](migrate-appliance.md#appliance---vmware) pour VMware.
- En savoir plus sur les [URL](migrate-appliance.md#url-access) auxquelles l’appliance doit accéder.

## <a name="port-access"></a>Accès au port

**Appareil** | **Connection**
--- | ---
Appliance | Connexions entrantes sur le port TCP 3389 pour permettre des connexions Bureau à distance avec l’appliance.<br/><br/> Connexions entrantes sur le port 44368 pour accéder à distance à l’application de gestion de l’appliance via l’URL : ```https://<appliance-ip-or-name>:44368``` <br/><br/>Connexions sortantes sur les ports 443 (HTTPS), 5671 et 5672 (AMQP) pour l’envoi de métadonnées de découverte et de performances à Azure Migrate.
Serveur vCenter | Connexions entrantes sur le port TCP 443 pour permettre à l’appliance de collecter les métadonnées de configuration et de performances pour les évaluations. <br/><br/> L’appliance se connecte à vCenter sur le port 443 par défaut. Si le serveur vCenter écoute sur un autre port, vous pouvez modifier le port lors de la configuration de la découverte.
Hôtes ESXi | **Requis uniquement pour la [détection des application](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#application-discovery) et la [visualisation des dépendances sans agent](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-dependency-visualization)** <br/><br/> L’application matérielle se connecte aux hôtes ESXi sur le port TCP 443 pour détecter des applications et exécuter la visualisation des dépendances sans agent sur les machines virtuelles s’exécutant sur les hôtes.

## <a name="agent-based-dependency-visualization"></a>Visualisation des dépendances basée sur les agents

La [visualisation des dépendances](concepts-dependency-visualization.md) vous permet de visualiser les dépendances entre les machines que vous souhaitez évaluer et migrer. Les exigences et limitations applicables à la visualisation basée sur les agents sont résumées dans le tableau suivant


**Prérequis** | **Détails**
--- | ---
**Déploiement** | Avant de déployer la visualisation des dépendances, vous devez disposer d’un projet Azure Migrate, avec l'outil Azure Migrate : Server Assessment ajouté au projet. Vous déployez la visualisation des dépendances après avoir configuré une appliance Azure Migrate pour découvrir vos machines locales.<br/><br/> La visualisation des dépendances n’est pas disponible dans Azure Government.
**Service Map** | La visualisation des dépendances basée sur les agents utilise la solution [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) dans les [journaux Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Pour déployer, vous associez un espace de travail Log Analytics nouveau ou existant à un projet Azure Migrate.
**Espace de travail Log Analytics** | L’espace de travail doit se trouver dans le même abonnement que le projet Azure Migrate.<br/><br/> Azure Migrate prend en charge les espaces de travail résidant dans les régions USA Est, Asie Sud-Est et Europe Ouest.<br/><br/>  L’espace de travail doit se trouver au sein d’une région dans laquelle [Service Map est pris en charge](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> L’espace de travail d’un projet Azure Migrate ne peut pas être modifié une fois qu’il a été ajouté.
**Frais** | La solution Service Map n’entraîne pas de frais pendant les 180 premiers jours (à compter du jour où vous associez l’espace de travail Log Analytics au projet Azure Migrate).<br/><br/> Au bout de 180 jours, des frais Log Analytics standard s’appliquent.<br/><br/> L’utilisation de solutions autres que Service Map dans l’espace de travail Log Analytics associé entraîne des frais Log Analytics standard.<br/><br/> Si vous supprimez le projet Azure Migrate, l’espace de travail n’est pas supprimé avec celui-ci. Une fois le projet supprimé, l’utilisation de Service Map n’est pas gratuite et chaque nœud est facturé en fonction du niveau payant de l’espace de travail Log Analytics.
**Agents** | La visualisation des dépendances basée sur les agents nécessite l’installation de deux agents sur chaque machine à analyser.<br/><br/> - [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [Agent de dépendances](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
**Connectivité Internet** | Si des machines ne sont pas connectées à Internet, vous devez installer la passerelle Log Analytics sur celles-ci.


## <a name="agentless-dependency-visualization"></a>Visualisation des dépendances sans agent

Cette option est actuellement en préversion. [Plus d’informations](how-to-create-group-machine-dependencies-agentless.md) Ces conditions sont résumées dans le tableau suivant.

**Prérequis** | **Détails**
--- | ---
**Déploiement** | Avant de déployer la visualisation des dépendances, vous devez disposer d’un projet Azure Migrate, avec l'outil Azure Migrate : Server Assessment ajouté au projet. Vous déployez la visualisation des dépendances après avoir configuré une appliance Azure Migrate pour découvrir vos machines locales.
**Prise en charge des machines virtuelles** | Actuellement seules les machines virtuelles VMware sont prises en charge.
**Machines virtuelles Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bits)
**Machines virtuelles Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Compte Windows** |  La visualisation a besoin d’un compte d’utilisateur avec un accès invité.
**Compte Linux** | La visualisation a besoin d’un compte d’utilisateur avec un privilège de racine.<br/><br/> Sinon, le compte d’utilisateur a besoin des autorisations suivantes sur les fichiers /bin/netstat et /bin/ls : CAP_DAC_READ_SEARCH et CAP_SYS_PTRACE.
**Agents de machine virtuelle** | Aucun agent n’est nécessaire sur les machines virtuelles.
**Outils VMware** | Les outils VMware doivent être installés et en cours d’exécution sur les machines virtuelles que vous souhaitez analyser.
**informations d’identification de vCenter** | Compte vCenter Server disposant d’un accès en lecture seule, et privilèges activés pour les Machines virtuelles > Opérations d’invité.
**Accès au port** | Sur les hôtes ESXi exécutant des machines virtuelles que vous souhaitez analyser, l’appliance Azure Migrate doit être en mesure de se connecter au port TCP 443.



## <a name="next-steps"></a>Étapes suivantes

- [Passer en revue](best-practices-assessment.md) les meilleures pratiques pour la création d’évaluations.
- [Préparer l’évaluation](tutorial-prepare-vmware.md) VMware.
