---
title: Prise en charge de l’évaluation VMware dans Azure Migrate
description: Découvrez la prise en charge pour l’évaluation de machines virtuelles VMware à l’aide d’Azure Migrate Server Assessment.
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 0fd884a7d95edaf6586e7eecd644400c05c15e5a
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92315795"
---
# <a name="support-matrix-for-vmware-assessment"></a>Tableau de prise en charge pour l’évaluation VMware 

Cet article récapitule les prérequis et les conditions de prise en charge lors de la découverte et de l’évaluation de machines virtuelles VMware pour la migration vers Azure, lors de l’utilisation de l’outil [Azure Migrate : Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool). Pour évaluer des machines virtuelles VMware, vous créez un projet Azure Migrate et vous ajoutez l’outil Server Assessment au projet. Une fois l’outil ajouté, vous déployez l’appliance Azure Migrate. Celle-ci découvre en continu les machines locales et envoie leurs métadonnées et leurs données de performances à Azure. Une fois la découverte terminée, vous rassemblez les machines découvertes dans des groupes et effectuez l’évaluation d’un groupe. 

Si vous souhaitez migrer des machines virtuelles VMware vers Azure, passez en revue la [matrice de prise en charge de la migration](migrate-support-matrix-vmware-migration.md).



## <a name="limitations"></a>Limites

**Support** | **Détails**
--- | ---
**Limites de projet** | Vous pouvez créer plusieurs projets dans un abonnement Azure.<br/><br/> Vous pouvez découvrir et évaluer jusqu’à 35 000 machines virtuelles VMware dans un même [projet](migrate-support-matrix.md#azure-migrate-projects). Un projet peut également inclure des serveurs physiques et des machines virtuelles Hyper-V, jusqu’aux limites d’évaluation pour chaque catégorie.
**Découverte** | L’appliance Azure Migrate peut découvrir jusqu’à 10 000 machines virtuelles VMware sur un vCenter Server.
**Évaluation** | Vous pouvez ajouter jusqu’à 35 000 machines dans un groupe unique.<br/><br/> Vous pouvez évaluer jusqu’à 35,000 machines virtuelles par évaluation.

[Apprenez-en davantage](concepts-assessment-calculation.md) sur les évaluations.


## <a name="vmware-requirements"></a>Configuration requise pour VMware

**VMware** | **Détails**
--- | ---
**vCenter Server** | Les machines que vous souhaitez découvrir et évaluer doivent être managées par vCenter Server version 5.5, 6.0, 6.5, 6.7 ou 7.0.<br/><br/> La découverte des machines virtuelles VMware en fournissant les détails de l’hôte ESXi dans l’appliance n’est pas prise en charge actuellement.
**autorisations** | Server Assessment a besoin d’un compte vCenter Server en lecture seule pour la découverte et l’évaluation.<br/><br/> Si vous souhaitez procéder à la découverte d’application ou à la visualisation des dépendances, le compte doit disposer de privilèges pour **Machines virtuelles** > **Opérations d’invité**.

## <a name="vm-requirements"></a>Configuration requise pour les machines virtuelles
**VMware** | **Détails**
--- | ---
**Machines virtuelles VMware** | Tous les systèmes d’exploitation peuvent être évalués dans une optique de migration. 
**Stockage** | Les disques attachés à des contrôleurs SCSI, IDE et SATA sont pris en charge.


## <a name="azure-migrate-appliance-requirements"></a>Conditions requises de l’appliance Azure Migrate

Azure Migrate utilise l’[appliance Azure Migrate](migrate-appliance.md) pour la découverte et l’évaluation. Vous pouvez déployer l’appliance en tant que machine virtuelle VMware à l’aide d’un modèle OVA, importé dans vCenter Server, ou à l’aide d’un [script PowerShell](deploy-appliance-script.md).

- En savoir plus sur les [conditions requises de l’appliance](migrate-appliance.md#appliance---vmware) pour VMware.
- Dans Azure Government, vous devez déployer l’appliance [avec ce script](deploy-appliance-script-government.md).
- Passez en revue les URL auxquelles l’appliance doit accéder dans les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls).


## <a name="port-access-requirements"></a>Conditions relatives à l’accès aux ports

**Appareil** | **Connection**
--- | ---
**Appliance** | Connexions entrantes sur le port TCP 3389 pour permettre des connexions Bureau à distance avec l’appliance.<br/><br/> Connexions entrantes sur le port 44368 pour accéder à distance à l’application de gestion de l’appliance via l’URL : ```https://<appliance-ip-or-name>:44368``` <br/><br/>Connexions sortantes sur le port 443 (HTTPS) pour envoyer les métadonnées de découverte et de performances à Azure Migrate.
**Serveur vCenter** | Connexions entrantes sur le port TCP 443 pour permettre à l’appliance de collecter les métadonnées de configuration et de performances pour les évaluations. <br/><br/> L’appliance se connecte à vCenter sur le port 443 par défaut. Si le serveur vCenter écoute sur un autre port, vous pouvez modifier le port lors de la configuration de la découverte.
**Hôtes ESXi** | Si vous souhaitez effectuer une [découverte des applications](how-to-discover-applications.md) ou une [analyse des dépendances sans agent](concepts-dependency-visualization.md#agentless-analysis), l’appliance se connecte aux hôtes ESXi sur le port TCP 443 pour découvrir des applications et exécuter la visualisation des dépendances sans agent sur les machines virtuelles.

## <a name="application-discovery-requirements"></a>Conditions relatives à la découverte d’application

Outre la découverte des machines, Server Assessment peut découvrir les applications, rôles et fonctionnalités exécutées sur celles-ci. La découverte de votre inventaire d’applications vous permet d’identifier et de planifier un chemin de migration adapté à vos charges de travail locales. 

**Support** | **Détails**
--- | ---
**Machines prises en charge** | La découverte des applications est actuellement prise en charge pour les machines virtuelles VMware uniquement.
**Découverte** | La découverte des applications se fait sans agent. Elle utilise les informations d’identification d’invité de la machine et accède à distance à des machines à l’aide d’appels WMI et SSH.
**Prise en charge des machines virtuelles** | La découverte des applications est prise en charge pour les machines virtuelles exécutant toutes les versions de Windows et Linux.
**vCenter** | Le compte vCenter Server en lecture seule utilisé pour l’évaluation exige des privilèges pour **Machines virtuelles** > **Opérations d’invité** afin d’interagir avec la machine virtuelle pour la découverte d’application.
**Accès à la machine virtuelle** | La découverte d’application a besoin d’un compte d’utilisateur local sur la machine virtuelle pour la découverte d’application.<br/><br/> Azure Migrate prend actuellement en charge l’utilisation d’une seule combinaison d’informations d’identification pour tous les serveurs Windows et d’une seule combinaison d’informations d’identification pour tous les serveurs Linux.<br/><br/> Vous créez un compte d’utilisateur invité pour les machines virtuelles Windows et un compte d’utilisateur standard/normal (accès non-sudo) pour toutes les machines virtuelles Linux.
**Outils VMware** | Les outils VMware doivent être installés et en cours d’exécution sur les machines virtuelles que vous souhaitez découvrir. <br/><br/> La version de VMware Tools doit être ultérieure à 10.2.0.
**PowerShell** | PowerShell version 2.0 ou ultérieure doit être installé sur les machines virtuelles.
**Accès au port** | Sur les hôtes ESXi exécutant des machines virtuelles que vous souhaitez découvrir, l’appliance Azure Migrate doit être en mesure de se connecter au port TCP 443.
**Limites** | Pour la découverte des applications, vous pouvez découvrir jusqu’à 10 000 machines virtuelles sur chaque appliance Azure Migrate.


## <a name="dependency-analysis-requirements-agentless"></a>Conditions relatives à l’analyse des dépendances (sans agent)

L’[analyse des dépendances](concepts-dependency-visualization.md) vous permet d’identifier les dépendances entre les machines locales que vous souhaitez évaluer et faire migrer vers Azure. Le tableau récapitule les conditions requises pour la configuration de l’analyse des dépendances sans agent.

**Prérequis** | **Détails**
--- | --- 
**Avant le déploiement** | Vous devez disposer d’un projet Azure Migrate, avec l’outil Évaluation de serveur ajouté au projet.<br/><br/>  Vous déployez la visualisation des dépendances après avoir configuré une appliance Azure Migrate pour découvrir vos machines VMware locales.<br/><br/> [Découvrez comment](create-manage-projects.md) créer un projet pour la première fois.<br/> [Découvrez comment](how-to-assess.md) ajouter un outil d’évaluation à un projet existant.<br/> [Découvrez comment](how-to-set-up-appliance-vmware.md) configurer l’appliance Azure Migrate pour l’évaluation des machines virtuelles VMware.
**Machines prises en charge** | Actuellement seules les machines virtuelles VMware sont prises en charge.
**Machines virtuelles Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bits)<br/>Microsoft Windows Server 2008 (32 bits) Vérifiez que PowerShell est installé.
**Informations d’identification de vCenter Server** | La visualisation des dépendances a besoin d’un compte vCenter Server disposant d’un accès en lecture seule et de privilèges activés pour Machines virtuelles > Opérations d’invité.
**Autorisations des machines virtuelles Windows** |  Pour l’analyse des dépendances, l’appliance Azure Migrate nécessite un compte d’administrateur de domaine, ou un compte d’administrateur local, pour accéder aux machines virtuelles Windows.
**Machines virtuelles Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.<br/> SUSE Linux Enterprise Server 11 et versions ultérieures
**Compte Linux** | Pour l'analyse des dépendances sur les machines Linux, l'appareil Azure Migrate a besoin d'un compte d'utilisateur racine.<br/><br/> Sinon, le compte d’utilisateur a besoin des autorisations suivantes sur les fichiers /bin/netstat et /bin/ls : CAP_DAC_READ_SEARCH et CAP_SYS_PTRACE. Définissez ces fonctionnalités à l’aide des commandes suivantes : <br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls <br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat
**Agents nécessaires** | Aucun agent n’est nécessaire sur les machines à analyser.
**VMware Tools** | VMware Tools (version ultérieure à 10.2) doit être installé et en cours d’exécution sur chaque machine virtuelle à analyser.
**PowerShell** | PowerShell version 2.0 ou ultérieure doit être installé sur les machines virtuelles Windows.
**Accès au port** | Sur les hôtes ESXi exécutant des machines virtuelles que vous souhaitez analyser, l’appliance Azure Migrate doit être en mesure de se connecter au port TCP 443.


## <a name="dependency-analysis-requirements-agent-based"></a>Conditions relatives à l’analyse des dépendances (basée sur un agent)

L’[analyse des dépendances](concepts-dependency-visualization.md) vous permet d’identifier les dépendances entre les machines locales que vous souhaitez évaluer et faire migrer vers Azure. Le tableau récapitule les conditions requises pour la configuration de l’analyse des dépendances basées sur un agent. 

**Prérequis** | **Détails** 
--- | --- 
**Avant le déploiement** | Vous devez disposer d’un projet Azure Migrate en place et de l’outil Azure Migrate : Server Assessment ajouté au projet.<br/><br/>  Vous déployez la visualisation des dépendances après avoir configuré une appliance Azure Migrate pour découvrir vos machines locales.<br/><br/> [Découvrez comment](create-manage-projects.md) créer un projet pour la première fois.<br/> [Découvrez comment](how-to-assess.md) ajouter un outil d’évaluation à un projet existant.<br/> Découvrez comment configurer l’appliance Azure Migrate pour l’évaluation de serveurs [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md) ou physiques.
**Machines prises en charge** | Pris en charge pour toutes les machines.
**Azure Government** | La visualisation des dépendances n’est pas disponible dans Azure Government.
**Log Analytics** | Azure Migrate utilise la solution [Service Map](../azure-monitor/insights/service-map.md) dans [Journaux d’activité Azure Monitor](../azure-monitor/log-query/log-query-overview.md) pour la visualisation des dépendances.<br/><br/> Vous associez un espace de travail Log Analytics nouveau ou déjà existant à un projet Azure Migrate. L’espace de travail d’un projet Azure Migrate ne peut pas être modifié une fois qu’il a été ajouté. <br/><br/> L’espace de travail doit se trouver dans le même abonnement que le projet Azure Migrate.<br/><br/> L’espace de travail doit résider dans les régions USA Est, Asie Sud-Est ou Europe Ouest. Les espaces de travail des autres régions ne peuvent pas être associés à un projet.<br/><br/> L’espace de travail doit se trouver au sein d’une région dans laquelle [Service Map est pris en charge](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> Dans Log Analytics, l’espace de travail associé à Azure Migrate est marqué avec la clé de projet de migration et le nom du projet.
**Agents nécessaires** | Sur chaque machine à analyser, installez les agents suivants :<br/><br/> [MMA (Microsoft Monitoring Agent)](../azure-monitor/platform/agent-windows.md)<br/> [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> Si les machines locales ne sont pas connectées à Internet, vous devez télécharger et installer la passerelle de l’espace de travail Log Analytics sur celles-ci.<br/><br/> En savoir plus sur l’installation de [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) et de [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Espace de travail Log Analytics** | L’espace de travail doit se trouver dans le même abonnement que le projet Azure Migrate.<br/><br/> Azure Migrate prend en charge les espaces de travail résidant dans les régions USA Est, Asie Sud-Est et Europe Ouest.<br/><br/>  L’espace de travail doit se trouver au sein d’une région dans laquelle [Service Map est pris en charge](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> L’espace de travail d’un projet Azure Migrate ne peut pas être modifié une fois qu’il a été ajouté.
**Coûts** | La solution Service Map n’entraîne aucun frais pendant les 180 premiers jours (à compter du jour où vous associez l’espace de travail Log Analytics au projet Azure Migrate).<br/><br/> Au bout de 180 jours, des frais Log Analytics standard s’appliquent.<br/><br/> L’utilisation d’une autre solution que Service Map dans l’espace de travail Log Analytics associé entraîne des [frais standard](https://azure.microsoft.com/pricing/details/log-analytics/) pour l’espace de travail Log Analytics.<br/><br/> Lorsque le projet Azure Migrate est supprimé, l’espace de travail ne l’est pas. Une fois le projet supprimé, l’utilisation de Service Map n’est plus gratuite, et chaque nœud est facturé en fonction du niveau payant de l’espace de travail Log Analytics<br/><br/>Si vous avez créé des projets avant la disponibilité générale d’Azure Migrate (28 février 2018), vous avez peut-être fait l’objet de frais supplémentaires pour Service Map. Pour veiller à payer uniquement après 180 jours, nous vous recommandons de créer un autre projet, car les espaces de travail présents avant la disponibilité générale restent facturables.
**Gestion** | Lorsque vous inscrivez des agents dans l’espace de travail, vous utilisez l’ID et la clé fournis par le projet Azure Migrate.<br/><br/> Vous pouvez utiliser l’espace de travail Log Analytics en dehors d’Azure Migrate.<br/><br/> Si vous supprimez le projet Azure Migrate associé, l’espace de travail n’est pas automatiquement supprimé. [Supprimez-le manuellement](../azure-monitor/platform/manage-access.md).<br/><br/> Ne supprimez pas l’espace de travail créé par Azure Migrate, sauf si vous supprimez le projet Azure Migrate. La suppression de l’espace de travail entraînerait un dysfonctionnement de la fonctionnalité de visualisation des dépendances.
**Connectivité Internet** | Si les machines ne sont pas connectées à Internet, vous devez installer la passerelle Log Analytics sur celles-ci.
**Azure Government** | L'analyse des dépendances basée sur un agent n'est pas prise en charge.


## <a name="next-steps"></a>Étapes suivantes

- [Passer en revue](best-practices-assessment.md) les meilleures pratiques pour la création d’évaluations.
- [Préparer l’évaluation](./tutorial-discover-vmware.md) VMware.