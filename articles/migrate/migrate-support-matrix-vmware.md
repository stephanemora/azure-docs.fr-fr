---
title: Prise en charge de l’évaluation du serveur VMware dans Azure Migrate
description: En savoir plus sur la prise en charge par Azure Migrate de l’évaluation et de la détection de serveurs dans un environnement VMware.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: de4d66f3ef8195e13ff8b67538901d1ebc7d88aa
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111971061"
---
# <a name="support-matrix-for-vmware-assessment"></a>Tableau de prise en charge pour l’évaluation VMware 

Cet article récapitule les prérequis et les conditions de prise en charge de l’utilisation de l’outil [Azure Migrate : découverte et évaluation](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) pour découvrir et évaluer des serveurs dans un environnement VMware pour la migration vers Azure.

Pour évaluer des serveurs, commencez par créer un projet Azure Migrate. L’outil Azure Migrate : découverte et évaluation est automatiquement ajouté au projet. Déployez ensuite l’appliance Azure Migrate. L’appliance découvre en permanence les serveurs locaux et envoie des métadonnées de configuration et de performances à Azure. Une fois la détection terminée, rassemblez les serveurs détectés dans des groupes et exécutez les évaluations par groupe.

Lorsque vous planifiez la migration de serveurs VMware vers Azure, passez en revue la [matrice de prise en charge de la migration](migrate-support-matrix-vmware-migration.md).

## <a name="limitations"></a>Limites

Condition requise | Détails
--- | ---
**Limites de projet** | Vous pouvez créer plusieurs projets Azure Migrate dans un abonnement Azure.<br /><br /> Vous pouvez découvrir et évaluer jusqu’à 50 000 serveurs dans un environnement VMware d’un même [projet](migrate-support-matrix.md#project). Un projet peut inclure des serveurs physiques et des serveurs d’un environnement Hyper-V, dans les limites de l’évaluation.
**Découverte** | L’appliance Azure Migrate peut découvrir jusqu’à 10 000 serveurs sur un serveur exécutant vCenter Server.
**Évaluation** | Vous pouvez ajouter jusqu’à 35 000 serveurs dans un groupe unique.<br /><br /> Vous pouvez évaluer jusqu’à 35 000 serveurs par évaluation.

En savoir plus sur les [évaluations](concepts-assessment-calculation.md).

## <a name="vmware-requirements"></a>Configuration requise pour VMware

VMware | Détails
--- | ---
**vCenter Server** | Les serveurs que vous souhaitez découvrir et évaluer doivent être gérés par vCenter Server version 7.0, 6.7, 6.5, 6.0 ou 5.5.<br /><br /> La découverte des serveurs via la fourniture des détails de l’hôte ESXi dans l’appliance n’est pas prise en charge actuellement.
**autorisations** | L’outil Azure Migrate : découverte et évaluation nécessite un compte vCenter Server en lecture seule.<br /><br /> Si vous souhaitez utiliser l’outil pour l’inventaire logiciel et l’analyse des dépendances sans agent, le compte doit disposer de privilèges pour les opérations d’invité sur les machines virtuelles VMware.

## <a name="server-requirements"></a>Configuration requise au niveau du serveur

VMware | Détails
--- | ---
**Systèmes d’exploitation** | Tous les systèmes d’exploitation Windows et Linux peuvent être évalués pour la migration.
**Stockage** | Les disques attachés à des contrôleurs SCSI, IDE et SATA sont pris en charge.

## <a name="azure-migrate-appliance-requirements"></a>Conditions requises de l’appliance Azure Migrate

Azure Migrate utilise l’[appliance Azure Migrate](migrate-appliance.md) pour la découverte et l’évaluation. Vous pouvez déployer l’appliance en tant que serveur dans votre environnement VMware en utilisant un modèle VMware OVA (Open Virtualization Appliance) importé dans vCenter Server ou un [script PowerShell](deploy-appliance-script.md). En savoir plus sur les [conditions requises de l’appliance pour VMware](migrate-appliance.md#appliance---vmware).

Voici d’autres exigences pour l’appliance :

- Dans Azure Government, vous devez déployer l’appliance à l’aide d’un [script](deploy-appliance-script-government.md).
- L’appliance doit pouvoir accéder à des URL spécifiques dans des [clouds publics](migrate-appliance.md#public-cloud-urls) et des [clouds du secteur public](migrate-appliance.md#government-cloud-urls).

## <a name="port-access-requirements"></a>Conditions relatives à l’accès aux ports

Appareil | Connexion
--- | ---
**Appliance Azure Migrate** | Connexions entrantes sur le port TCP 3389 pour permettre des connexions Bureau à distance avec l’appliance.<br /><br /> Connexions entrantes sur le port 44368 pour accéder à distance à l’application de gestion de l’appliance à l’aide de l’URL `https://<appliance-ip-or-name>:44368`. <br /><br />Connexions sortantes sur le port 443 (HTTPS) pour envoyer les métadonnées de découverte et de performances à Azure Migrate.
**vCenter Server** | Connexions entrantes sur le port TCP 443 pour permettre à l’appliance de collecter les métadonnées de configuration et de performances pour les évaluations. <br /><br /> L’appliance se connecte à vCenter sur le port 443 par défaut. Si vCenter Server écoute sur un autre port, vous pouvez modifier le port lors de la configuration de la découverte.
**Hôtes ESXi** | Pour effectuer une [découverte de l’inventaire des logiciels](how-to-discover-applications.md) ou une [analyse des dépendances sans agent](concepts-dependency-visualization.md#agentless-analysis), l’appliance se connecte aux hôtes ESXi sur le port TCP 443 pour découvrir l’inventaire des logiciels et les dépendances sur les serveurs.

## <a name="application-discovery-requirements"></a>Conditions relatives à la découverte d’application

Outre la découverte de serveurs, Azure Migrate : découverte et évaluation peut effectuer un inventaire des logiciels exécutés sur les serveurs. La découverte d’application vous permet d’identifier et de planifier un chemin de migration adapté à vos charges de travail locales.

Support | Détails
--- | ---
**Serveurs pris en charge** | Actuellement prise en charge uniquement pour des serveurs se trouvant dans votre environnement VMware. Vous pouvez effectuer une découverte d’applications sur un maximum de 10 000 serveurs à partir de chaque appliance Azure Migrate.
**Systèmes d’exploitation** | Les serveurs exécutant toutes les versions de Windows et Linux sont pris en charge.
**Configuration requise pour les machines virtuelles** | Pour effectuer une découverte de l’inventaire des logiciels, les outils VMware doivent être installés et en cours d’exécution sur vos serveurs. <br /><br /> La version des outils VMware doit être la version 10.2.1 ou une version ultérieure.<br /><br /> PowerShell version 2.0 ou ultérieure doit être installé sur les serveurs Windows.
**Découverte** | La découverte d’applications sur des serveurs s’effectue à partir de vCenter Server à l’aide d’outils VMware installés sur les serveurs. L’appliance recueille les informations sur l’inventaire des logiciels à partir du serveur exécutant vCenter Server par l’intermédiaire d’API vSphere. La découverte d’application opère sans agent. Aucun agent n’est installé sur le serveur et l’appliance ne se connecte pas directement aux serveurs. WMI et SSH doivent être activés et disponibles respectivement sur les serveurs Windows et Linux.
**Compte d’utilisateur de vCenter Server** | Pour interagir avec les serveurs en vue de la découverte d’applications, le compte vCenter Server en lecture seule utilisé pour l’évaluation doit disposer de privilèges pour les opérations d’invité sur les machines virtuelles VMware.
**Accès au serveur** | Vous pouvez ajouter plusieurs informations d’identification de domaine et hors domaine (Windows/Linux) au gestionnaire de configuration de l’appliance pour la découverte d’applications.<br /><br /> Vous devez disposer d’un compte d’utilisateur invité pour les serveurs Windows et d’un compte d’utilisateur standard (accès non `sudo`) pour tous les serveurs Linux.
**Accès au port** | L’appliance Azure Migrate doit être en mesure de se connecter au port TCP 443 sur les hôtes ESXi exécutant les serveurs sur lesquels vous souhaitez effectuer une découverte d’application. Le serveur exécutant vCenter Server retourne une connexion d’hôte ESXi pour télécharger le fichier contenant les détails de l’inventaire des logiciels.

## <a name="sql-server-instance-and-database-discovery-requirements"></a>Exigences en matière de découverte de base de données et d’instance SQL Server

La [découverte d’applications](how-to-discover-applications.md) identifie des instances SQL Server. À l’aide de ces informations, l’appliance tente de se connecter aux diverses instances SQL Server grâce aux informations d’identification pour l’authentification Windows ou SQL Server fournies dans le gestionnaire de configuration de l’appliance. Une fois connectée, l’appliance recueille les données de configuration et de performances pour les instances et bases de données SQL Server. Les données de configuration de SQL Server sont mises à jour une fois par jour. Les données de performances sont capturées toutes les 30 secondes.

Support | Détails
--- | ---
**Serveurs pris en charge** | Actuellement seuls des serveurs exécutant SQL Server sont pris en charge dans votre environnement VMware. Vous pouvez découvrir jusqu’à 300 instances SQL Server ou 6 000 bases de données SQL, selon la valeur la plus faible.
**Serveurs Windows** | Windows Server 2008 et les versions ultérieures sont pris en charge.
**Serveurs Linux** | Non pris en charge actuellement.
**Mécanisme d’authentification** | Les authentifications Windows et SQL Server sont toutes deux prises en charge. Vous pouvez fournir des informations d’identification des deux types d’authentification dans le gestionnaire de configuration de l’appliance.
**Accès à SQL Server** | Azure Migrate nécessite un compte d’utilisateur Windows membre du rôle serveur sysadmin.
**Versions de SQL Server** | SQL Server 2008 et les versions ultérieures sont pris en charge.
**Éditions de SQL Server** | Les éditions Enterprise, Standard, Développeur et Express sont prises en charge.
**Configuration de SQL prise en charge** | Actuellement, seule la découverte des instances SQL Server autonomes et des bases de données correspondantes est prise en charge.<br /><br /> L’identification de cluster de basculement et de groupes de disponibilité AlwaysOn n’est pas prise en charge.
**Services SQL pris en charge** | Seul le moteur de base de données SQL Server est pris en charge. <br /><br /> La découverte de SQL Server Reporting Services (SSRS), SQL Server Integration Services (SSIS) et SQL Server Analysis Services (SSAS) n’est pas prise en charge.

> [!NOTE]
> Azure Migrate chiffre les communications entre l’appliance Azure Migrate et les instances SQL Server sources lorsque la propriété [TrustServerCertificate](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) est définie sur `true`. La couche transport utilise SSL pour chiffrer le canal et contourner la chaîne de certificat pour valider l’approbation. Le serveur d’appliance doit être configuré pour [approuver l’autorité racine du certificat](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).
>
> Si aucun certificat n’a été provisionné sur le serveur au démarrage, SQL Server génère un certificat auto-signé qui servira au chiffrement des paquets de connexion. [Plus d’informations](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)
>

## <a name="dependency-analysis-requirements-agentless"></a>Conditions relatives à l’analyse des dépendances (sans agent)

L’[analyse des dépendances](concepts-dependency-visualization.md) vous permet d’identifier les dépendances entre les serveurs locaux que vous souhaitez évaluer et migrer vers Azure. Le tableau suivant récapitule les conditions requises pour la configuration de l’analyse des dépendances sans agent :

Support | Détails
--- | --- 
**Serveurs pris en charge** | Actuellement prise en charge uniquement pour des serveurs se trouvant dans votre environnement VMware.
**Serveurs Windows** | Windows Server 2016<br /> Windows Server 2012 R2<br /> Windows Server 2012<br /> Windows Server 2008 R2 (64 bits)<br />Microsoft Windows Server 2008 (32 bits)
**Serveurs Linux** | Red Hat Enterprise Linux 7, 6, 5<br /> Ubuntu Linux 16.04, 14.04<br /> Debian 8, 7<br /> Oracle Linux 7, 6<br /> CentOS 7, 6, 5<br /> SUSE Linux Enterprise Server 11 et versions ultérieures
**Configuration requise au niveau du serveur** | Les outils VMware (10.2.1 et versions ultérieures) doivent être installés et en cours d’exécution sur les serveurs que vous voulez analyser.<br /><br /> PowerShell, versions 2.0 ou ultérieures, doit être installé sur les serveurs.
**Méthode de détection** |  Les informations de dépendance entre serveurs sont collectées à partir des outils VMware sur le serveur exécutant vCenter Server. L’appliance recueille les informations à partir du serveur à l’aide d’API vSphere. Aucun agent n’est installé sur le serveur, et l’appliance ne se connecte pas directement aux serveurs. WMI et SSH doivent être activés et disponibles respectivement sur les serveurs Windows et Linux.
**Compte vCenter** | Le compte en lecture seule utilisé par Azure Migrate pour l’évaluation doit disposer de privilèges pour les opérations d’invité sur des machines virtuelles VMware.
**Autorisations de serveur Windows** |  Un compte d’utilisateur (local ou de domaine) avec des autorisations d’administrateur sur les serveurs.
**Compte Linux** | Un compte d’utilisateur racine ou un compte disposant de ces autorisations sur les fichiers /bin/netstat et /bin/ls : <br />CAP_DAC_READ_SEARCH<br /> CAP_SYS_PTRACE<br /><br /> Définissez ces fonctionnalités à l’aide des commandes suivantes :<br /><code>sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls<br /> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat</code>
**Accès au port** | L’appliance Azure Migrate doit pouvoir se connecter au port TCP 443 sur les hôtes ESXi exécutant les serveurs dont vous souhaitez découvrir les dépendances. Le serveur exécutant vCenter Server retourne une connexion d’hôte ESXi pour télécharger le fichier contenant les données de dépendance.

## <a name="dependency-analysis-requirements-agent-based"></a>Conditions relatives à l’analyse des dépendances (basée sur un agent)

L’[analyse des dépendances](concepts-dependency-visualization.md) vous permet d’identifier les dépendances entre les serveurs locaux que vous souhaitez évaluer et migrer vers Azure. Le tableau suivant récapitule les conditions requises pour la configuration de l’analyse des dépendances basées sur un agent :

Condition requise | Détails
--- | ---
**Avant le déploiement** | Vous devez disposer d’un projet, avec l’outil Azure Migrate : découverte et évaluation ajouté au projet.<br /><br />Déployez la visualisation des dépendances après avoir configuré une appliance Azure Migrate pour découvrir vos serveurs locaux.<br /><br />Découvrez comment [créer un projet pour la première fois](create-manage-projects.md).<br /> Découvrez comment [ajouter un outil de découverte et d’évaluation à un projet existant](how-to-assess.md).<br /> Découvrez comment configurer l’appliance Azure Migrate pour l’évaluation de serveurs [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md) ou physiques.
**Serveurs pris en charge** | Pris en charge pour tous les serveurs dans votre environnement local.
**Log Analytics** | Azure Migrate utilise la solution [Service Map](../azure-monitor/vm/service-map.md) dans [Journaux d’activité Azure Monitor](../azure-monitor/logs/log-query-overview.md) pour la visualisation des dépendances.<br /><br /> Vous associez un espace de travail Log Analytics nouveau ou déjà existant à un projet. Il n’est pas possible de modifier l’espace de travail d’un projet après son ajout. <br /><br /> L’espace de travail doit se trouver dans le même abonnement que le projet.<br /><br /> L’espace de travail doit se situer dans les régions USA Est, Asie Sud-Est ou Europe Ouest. Les espaces de travail des autres régions ne peuvent pas être associés à un projet.<br /><br /> L’espace de travail doit se trouver dans une [région dans laquelle Service Map est pris en charge](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br /><br /> Dans Log Analytics, l’espace de travail associé à Azure Migrate est marqué avec la clé de projet et le nom du projet.
**Agents nécessaires** | Sur chaque serveur à analyser, installez les agents suivants :<br />- [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)<br />- [Agent de dépendances](../azure-monitor/agents/agents-overview.md#dependency-agent)<br /><br /> Si les serveurs locaux ne sont pas connectés à Internet, téléchargez et installez la passerelle Log Analytics sur ceux-ci.<br /><br /> En savoir plus sur l’installation de l’[agent de dépendances](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) et de [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Espace de travail Log Analytics** | L’espace de travail doit se trouver dans le même abonnement que le projet.<br /><br /> Azure Migrate prend en charge les espaces de travail situés dans les régions USA Est, Asie Sud-Est et Europe Ouest.<br /><br />  L’espace de travail doit se trouver au sein d’une région dans laquelle [Service Map est pris en charge](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br /><br /> Il n’est pas possible de modifier l’espace de travail d’un projet après son ajout.
**Coût** | La solution Service Map n’entraîne aucuns frais pendant les 180 premiers jours (à compter du jour où vous associez l’espace de travail Log Analytics au projet).<br /><br /> Au bout de 180 jours, des frais Log Analytics standard s’appliquent.<br /><br /> L’utilisation d’une autre solution que Service Map dans l’espace de travail Log Analytics associé entraîne des [frais standard](https://azure.microsoft.com/pricing/details/log-analytics/) pour l’espace de travail Log Analytics.<br /><br /> Lorsque le projet est supprimé, l’espace de travail ne l’est pas. Une fois le projet supprimé, l’utilisation de Service Map n’est plus gratuite et chaque nœud est facturé en fonction du niveau payant de l’espace de travail Log Analytics.<br /><br />Si vous avez créé des projets avant la disponibilité générale d’Azure Migrate (28 février 2018), vous avez peut-être fait l’objet de frais supplémentaires pour Service Map. Pour garantir que des frais ne vous sont facturés qu’après 180 jours, nous vous recommandons de créer un projet. Les espaces de travail créés avant la disponibilité générale sont toujours facturables.
**Gestion** | Lorsque vous inscrivez des agents dans l’espace de travail, utilisez l’ID et la clé fournis par le projet.<br /><br /> Vous pouvez utiliser l’espace de travail Log Analytics en dehors d’Azure Migrate.<br /><br /> Si vous supprimez le projet associé, l’espace de travail n’est pas automatiquement supprimé. [Supprimez-le manuellement](../azure-monitor/logs/manage-access.md).<br /><br /> Ne supprimez pas l’espace de travail créé par Azure Migrate, sauf si vous supprimez le projet. La suppression de l’espace de travail entraînerait un dysfonctionnement de la fonctionnalité de visualisation des dépendances.
**Connectivité Internet** | Si les serveurs ne sont pas connectés à Internet, installez la passerelle Log Analytics sur ceux-ci.
**Azure Government** | L'analyse des dépendances basée sur un agent n'est pas prise en charge.

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue les [meilleures pratiques relatives à l’évaluation](best-practices-assessment.md).
- Découvrez comment [vous préparer une évaluation VMware](./tutorial-discover-vmware.md).