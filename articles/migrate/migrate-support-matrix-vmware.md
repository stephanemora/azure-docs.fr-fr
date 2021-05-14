---
title: Prise en charge de l’évaluation VMware dans Azure Migrate
description: En savoir plus sur la prise en charge de l’évaluation des serveurs s’exécutant dans un environnement VMware avec Azure Migrate Discovery and Assessment
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 8cf976c358d35b151e94ce42fc89cc53d361f699
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108131329"
---
# <a name="support-matrix-for-vmware-assessment"></a>Tableau de prise en charge pour l’évaluation VMware 

Cet article récapitule les prérequis et les conditions de prise en charge lors de la découverte et de l’évaluation de serveurs s’exécutant dans un environnement VMware pour la migration vers Azure à l’aide de l’outil [Azure Migrate : découverte et évaluation](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool). Pour évaluer des serveurs physiques, vous créez un projet qui ajoute l’outil Azure Migrate : découverte et évaluation. Une fois l’outil ajouté, vous déployez l’appliance Azure Migrate. L’appliance découvre en permanence les serveurs locaux et envoie des métadonnées de configuration et de performances à Azure. Une fois la découverte terminée, vous rassemblez les serveurs découverts dans des groupes, puis évaluez ceux-ci.

Si vous souhaitez migrer des serveurs VMware vers Azure, examinez la [matrice de prise en charge de la migration](migrate-support-matrix-vmware-migration.md).



## <a name="limitations"></a>Limites

**Prérequis** | **Détails**
--- | ---
**Limites de projet** | Vous pouvez créer plusieurs projets dans un abonnement Azure.<br/><br/> Vous pouvez découvrir et évaluer jusqu’à 50 000 serveurs d’un environnement VMware un même [projet](migrate-support-matrix.md#project). Un projet peut également inclure des serveurs physiques et des serveurs d’un environnement Hyper-V, dans les limites de l’évaluation.
**Découverte** | L’appliance Azure Migrate peut découvrir jusqu’à 10 000 serveurs sur un vCenter Server.
**Évaluation** | Vous pouvez ajouter jusqu’à 35 000 serveurs dans un groupe unique.<br/><br/> Vous pouvez évaluer jusqu’à 35 000 serveurs par évaluation.

[Apprenez-en davantage](concepts-assessment-calculation.md) sur les évaluations.


## <a name="vmware-requirements"></a>Configuration requise pour VMware

**VMware** | **Détails**
--- | ---
**vCenter Server** | Les serveurs que vous souhaitez découvrir et évaluer doivent être gérés par vCenter Server version 5.5, 6.0, 6.5, 6.7 ou 7.0.<br/><br/> La découverte des serveurs via la fourniture des détails de l’hôte ESXi dans l’appliance n’est pas prise en charge actuellement.
**autorisations** | Azure Migrate : découverte et évaluation nécessite un compte vCenter Server en lecture seule pour la découverte et l’évaluation.<br/><br/> Si vous souhaitez procéder à la découverte de l’inventaire des logiciels et à l’analyse des dépendances sans agent, le compte doit disposer de privilèges activés pour **Machines virtuelles** > **Opérations d’invité**.

## <a name="server-requirements"></a>Configuration requise au niveau du serveur
**VMware** | **Détails**
--- | ---
**Systèmes d’exploitation pris en charge** | Tous les systèmes d’exploitation Windows et Linux peuvent être évalués pour la migration.
**Stockage** | Les disques attachés à des contrôleurs SCSI, IDE et SATA sont pris en charge.


## <a name="azure-migrate-appliance-requirements"></a>Conditions requises de l’appliance Azure Migrate

Azure Migrate utilise l’[appliance Azure Migrate](migrate-appliance.md) pour la découverte et l’évaluation. Vous pouvez déployer l’appliance en tant que serveur dans votre environnement VMware en utilisant un modèle OVA importé dans vCenter Server, ou un [script PowerShell](deploy-appliance-script.md).

- En savoir plus sur les [conditions requises de l’appliance](migrate-appliance.md#appliance---vmware) pour VMware.
- Dans Azure Government, vous devez déployer l’appliance [avec ce script](deploy-appliance-script-government.md).
- Passez en revue les URL auxquelles l’appliance doit accéder dans les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls).


## <a name="port-access-requirements"></a>Conditions relatives à l’accès aux ports

**Appareil** | **Connection**
--- | ---
**Appliance** | Connexions entrantes sur le port TCP 3389 pour permettre des connexions Bureau à distance avec l’appliance.<br/><br/> Connexions entrantes sur le port 44368 pour accéder à distance à l’application de gestion de l’appliance via l’URL : ```https://<appliance-ip-or-name>:44368``` <br/><br/>Connexions sortantes sur le port 443 (HTTPS) pour envoyer les métadonnées de découverte et de performances à Azure Migrate.
**Serveur vCenter** | Connexions entrantes sur le port TCP 443 pour permettre à l’appliance de collecter les métadonnées de configuration et de performances pour les évaluations. <br/><br/> L’appliance se connecte à vCenter sur le port 443 par défaut. Si le serveur vCenter écoute sur un autre port, vous pouvez modifier le port lors de la configuration de la découverte.
**Hôtes ESXi** | Si vous souhaitez effectuer une [découverte de l’inventaire des logiciels](how-to-discover-applications.md) ou une [analyse des dépendances sans agent](concepts-dependency-visualization.md#agentless-analysis), l’appliance se connecte aux hôtes ESXi sur le port TCP 443 pour découvrir l’inventaire des logiciels et les dépendances sur les serveurs.

## <a name="application-discovery-requirements"></a>Conditions relatives à la découverte d’application

Outre la découverte des serveurs, Azure Migrate : découverte et évaluation peut découvrir l’inventaire logiciel exécuté sur les serveurs. La découverte d’application vous permet d’identifier et de planifier un chemin de migration adapté à vos charges de travail locales.

**Support** | **Détails**
--- | ---
**Serveurs pris en charge** | Actuellement prise en charge uniquement pour des serveurs se trouvant dans votre environnement VMware. Vous pouvez effectuer une découverte d’application sur jusqu’à 10 000 serveurs à partir de chaque appliance Azure Migrate.
**Systèmes d’exploitation** | Les serveurs exécutant toutes les versions de Windows et Linux sont pris en charge.
**Configuration requise pour les machines virtuelles** | Pour effectuer une découverte de l’inventaire des logiciels, les outils VMware doivent être installés et en cours d’exécution sur les serveurs. <br/><br/> La version de VMware Tools doit être ultérieure à 10.2.0.<br/><br/> PowerShell version 2.0 ou ultérieure doit être installé sur les serveurs Windows.
**Découverte** | La découverte d’application sur des serveurs s’effectue à partir du vCenter Server à l’aide d’outils VMware installés sur les serveurs. L’appliance recueille les informations sur l’inventaire des logiciels à partir du vCenter Server à l’aide d’API vSphere. La découverte d’application opère sans agent. Aucun agent n’est installé sur le serveur et l’appliance ne se connecte pas directement aux serveurs. WMI et SSH doivent être activés et disponibles respectivement sur les serveurs Windows et Linux.
**Compte d’utilisateur de vCenter Server** | Le compte en lecture seule vCenter Server utilisé pour l’évaluation exige des privilèges pour **Machines virtuelles** > **Opérations d’invité** afin d’interagir avec les serveurs pour la découverte d’application.
**Accès au serveur** | Vous pouvez ajouter plusieurs informations d’identification de domaine et hors domaine (Windows/Linux) sur le gestionnaire de configuration de l’appliance pour la découverte d’application.<br/><br/> Vous avez besoin d’un compte d’utilisateur invité pour les serveurs Windows et d’un compte d’utilisateur standard/normal (accès non-sudo) pour tous les serveurs Linux.
**Accès au port** | L’appliance Azure Migrate doit être en mesure de se connecter au port TCP 443 sur les hôtes ESXi exécutant les serveurs sur lesquels vous souhaitez effectuer une découverte d’application. Le vCenter Server retourne une connexion d’hôte ESXI pour télécharger le fichier contenant les détails de l’inventaire des logiciels.

## <a name="requirements-for-discovery-of-sql-server-instances-and-databases"></a>Configuration requise pour la découverte d’instances et de bases de données SQL Server

La [découverte d’application](how-to-discover-applications.md) identifie les instances SQL Server. À l’aide de ces informations, l’appliance tente de se connecter aux diverses instances SQL Server grâce aux informations d’identification pour l’authentification Windows ou SQL Server fournies sur l’appliance. Une fois connectée, l’appliance recueille les données de configuration et de performances des instances et bases de données SQL Server. Les données de configuration SQL Server sont mises à jour toutes les 24 heures, et les données de performances capturées toutes les 30 secondes.

**Support** | **Détails**
--- | ---
**Serveurs pris en charge** | Actuellement prise en charge uniquement pour des serveurs SQL Server se trouvant dans votre environnement VMware. Vous pouvez découvrir jusqu’à 300 instances SQL Server ou 6 000 bases de données SQL.
**Serveurs Windows** | Windows Server 2008 et les versions ultérieures sont pris en charge.
**Serveurs Linux** | Non pris en charge actuellement.
**Mécanisme d’authentification** | Les authentifications Windows et SQL Server sont toutes deux prises en charge. Vous pouvez fournir des informations d’identification des deux types d’authentification sur le gestionnaire de configuration d’appliance.
**Accès à SQL Server** | Azure Migrate nécessite un compte d’utilisateur Windows membre du rôle serveur sysadmin.
**Versions de SQL Server** | SQL Server 2008 et les versions ultérieures sont pris en charge.
**Éditions de SQL Server** | Les éditions Enterprise, Standard, Développeur et Express sont prises en charge.
**Configuration de SQL prise en charge** | Actuellement, seule la découverte des instances SQL Server autonomes et des bases de données correspondantes est prise en charge.<br/> L’identification de cluster de basculement et de groupes de disponibilité AlwaysOn n’est pas prise en charge.
**Services SQL pris en charge** | Seul le moteur de base de données SQL Server est pris en charge. <br/> La découverte de SQL Server Reporting Services (SSRS), SQL Server Integration Services (SSIS) et SQL Server Analysis Services (SSAS) n’est pas prise en charge.

> [!Note]
> Azure Migrate chiffre la communication entre l’appliance Azure Migrate et les instances SQL Server (avec la propriété Chiffrer la connexion définie sur TRUE). Ces connexions sont chiffrées avec [**TrustServerCertificate**](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (défini sur true). La couche transport utilise le protocole SSL pour chiffrer le canal et contourner la chaîne de certificats afin de valider l’approbation. Le serveur d’appliance doit être configuré pour [**approuver l’autorité racine du certificat**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Si aucun certificat n’a été approvisionné sur le serveur à son démarrage, SQL Server génère un certificat auto-signé qui est utilisé pour chiffrer les paquets d’ouverture de session. [**En savoir plus**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

## <a name="dependency-analysis-requirements-agentless"></a>Conditions relatives à l’analyse des dépendances (sans agent)

L’[analyse des dépendances](concepts-dependency-visualization.md) vous permet d’identifier les dépendances entre les machines locales que vous souhaitez évaluer et migrer vers Azure. Le tableau récapitule les conditions requises pour la configuration de l’analyse des dépendances sans agent.

**Support** | **Détails**
--- | --- 
**Serveurs pris en charge** | Actuellement prise en charge uniquement pour des serveurs se trouvant dans votre environnement VMware.
**Serveurs Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bits)<br/>Microsoft Windows Server 2008 (32 bits)
**Serveurs Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.<br/> SUSE Linux Enterprise Server 11 et versions ultérieures.
**Configuration requise au niveau du serveur** | Les outils VMware (postérieurs à la version 10.2.0) doivent être installés et en cours d’exécution sur les serveurs que vous voulez analyser.<br/><br/> PowerShell, versions 2.0 ou ultérieures, doit être installé sur les serveurs.
**Méthode de détection** |  Les informations de dépendance entre serveurs sont collectées à partir du vCenter Server, à l’aide d’outils VMware installés sur le serveur. L’appliance recueille les informations à partir du serveur VMware vCenter, à l’aide d’API vSphere. Aucun agent n’est installé sur le serveur, et l’appliance ne se connecte pas directement aux serveurs. WMI et SSH doivent être activés et disponibles respectivement sur les serveurs Windows et Linux.
**Compte vCenter** | Le compte en lecture seule utilisé par Azure Migrate pour l’évaluation nécessite des privilèges activés pour **Machines virtuelles > Opérations invité**.
**Autorisations de serveur Windows** |  Un compte d’utilisateur (local ou de domaine) avec des autorisations administratives sur les serveurs.
**Compte Linux** | Compte d’utilisateur racine ou compte disposant de ces autorisations sur les fichiers /bin/netstat et /bin/ls : CAP_DAC_READ_SEARCH et CAP_SYS_PTRACE.<br/><br/> Définissez ces fonctionnalités à l’aide des commandes suivantes : <br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat
**Accès au port** | L’appliance Azure Migrate doit être en mesure de se connecter au port TCP 443 sur les hôtes ESXi exécutant les serveurs dont vous souhaitez découvrir les dépendances. Le vCenter Server retourne une connexion d’hôte ESXI pour télécharger le fichier contenant les données de dépendances.


## <a name="dependency-analysis-requirements-agent-based"></a>Conditions relatives à l’analyse des dépendances (basée sur un agent)

L’[analyse des dépendances](concepts-dependency-visualization.md) vous permet d’identifier les dépendances entre les machines locales que vous souhaitez évaluer et migrer vers Azure. Le tableau récapitule les conditions requises pour la configuration de l’analyse des dépendances basées sur un agent.

**Prérequis** | **Détails** 
--- | --- 
**Avant le déploiement** | Vous devez disposer d’un projet, avec l’outil Azure Migrate : découverte et évaluation ajouté au projet.<br/><br/>  Vous déployez la visualisation des dépendances après avoir configuré une appliance Azure Migrate pour découvrir vos serveurs locaux.<br/><br/> [Découvrez comment](create-manage-projects.md) créer un projet pour la première fois.<br/> [Découvrez comment](how-to-assess.md) ajouter un outil de découverte et d’évaluation à un projet existant.<br/> Découvrez comment configurer l’appliance Azure Migrate pour l’évaluation de serveurs [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md) ou physiques.
**Serveurs pris en charge** | Pris en charge pour tous les serveurs dans votre environnement local.
**Log Analytics** | Azure Migrate utilise la solution [Service Map](../azure-monitor/vm/service-map.md) dans [Journaux d’activité Azure Monitor](../azure-monitor/logs/log-query-overview.md) pour la visualisation des dépendances.<br/><br/> Vous associez un espace de travail Log Analytics nouveau ou déjà existant à un projet. Il n’est pas possible de modifier l’espace de travail d’un projet après son ajout. <br/><br/> L’espace de travail doit se trouver dans le même abonnement que le projet.<br/><br/> L’espace de travail doit résider dans les régions USA Est, Asie Sud-Est ou Europe Ouest. Les espaces de travail des autres régions ne peuvent pas être associés à un projet.<br/><br/> L’espace de travail doit se trouver au sein d’une région dans laquelle [Service Map est pris en charge](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Dans Log Analytics, l’espace de travail associé à Azure Migrate est marqué avec la clé de projet et le nom du projet.
**Agents nécessaires** | Sur chaque serveur à analyser, installez les agents suivants :<br/><br/> [MMA (Microsoft Monitoring Agent)](../azure-monitor/agents/agent-windows.md)<br/> [Dependency Agent](../azure-monitor/agents/agents-overview.md#dependency-agent)<br/><br/> Si les serveurs locaux ne sont pas connectés à Internet, vous devez télécharger et installer une passerelle Log Analytics sur ceux-ci.<br/><br/> En savoir plus sur l’installation de [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) et de [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Espace de travail Log Analytics** | L’espace de travail doit se trouver dans le même abonnement que le projet.<br/><br/> Azure Migrate prend en charge les espaces de travail résidant dans les régions USA Est, Asie Sud-Est et Europe Ouest.<br/><br/>  L’espace de travail doit se trouver au sein d’une région dans laquelle [Service Map est pris en charge](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Il n’est pas possible de modifier l’espace de travail d’un projet après son ajout.
**Coût** | La solution Service Map n’entraîne aucun frais pendant les 180 premiers jours (à compter du jour où vous associez l’espace de travail Log Analytics au projet).<br/><br/> Au bout de 180 jours, des frais Log Analytics standard s’appliquent.<br/><br/> L’utilisation d’une autre solution que Service Map dans l’espace de travail Log Analytics associé entraîne des [frais standard](https://azure.microsoft.com/pricing/details/log-analytics/) pour l’espace de travail Log Analytics.<br/><br/> Lorsque le projet est supprimé, l’espace de travail ne l’est pas. Une fois le projet supprimé, l’utilisation de Service Map n’est plus gratuite, et chaque nœud est facturé en fonction du niveau payant de l’espace de travail Log Analytics<br/><br/>Si vous avez créé des projets avant la disponibilité générale d’Azure Migrate (28 février 2018), vous avez peut-être fait l’objet de frais supplémentaires pour Service Map. Pour veiller à payer uniquement après 180 jours, nous vous recommandons de créer un autre projet, car les espaces de travail présents avant la disponibilité générale restent facturables.
**Gestion** | Lorsque vous inscrivez des agents dans l’espace de travail, vous utilisez l’ID et la clé fournis par le projet.<br/><br/> Vous pouvez utiliser l’espace de travail Log Analytics en dehors d’Azure Migrate.<br/><br/> Si vous supprimez le projet associé, l’espace de travail n’est pas automatiquement supprimé. [Supprimez-le manuellement](../azure-monitor/logs/manage-access.md).<br/><br/> Ne supprimez pas l’espace de travail créé par Azure Migrate, sauf si vous supprimez le projet. La suppression de l’espace de travail entraînerait un dysfonctionnement de la fonctionnalité de visualisation des dépendances.
**Connectivité Internet** | Si les serveurs ne sont pas connectés à Internet, vous devez installer la passerelle Log Analytics sur ceux-ci.
**Azure Government** | L'analyse des dépendances basée sur un agent n'est pas prise en charge.


## <a name="next-steps"></a>Étapes suivantes

- [Passer en revue](best-practices-assessment.md) les meilleures pratiques pour la création d’évaluations.
- [Préparer l’évaluation](./tutorial-discover-vmware.md) VMware.