---
title: Prise en charge de l’évaluation Hyper-V dans Azure Migrate
description: Découvrez la prise en charge de l’évaluation d’environnement Hyper-V à l’aide de l’outil de découverte et d’évaluation d’Azure Migrate
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 8531d0b2252e6ddff75509046b5a4576b99d339f
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870787"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Tableau de prise en charge pour l’évaluation Hyper-V

Cet article récapitule les prérequis et les conditions de prise en charge lors de la découverte et de l’évaluation de serveurs locaux s’exécutant dans un environnement Hyper-V en vue d’un migration vers Azure à l’aide de l’outil [Azure Migrate : découverte et évaluation](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool). Si vous souhaitez migrer des serveurs s’exécutant sur Hyper-V vers Azure, examinez la [matrice de prise en charge de la migration](migrate-support-matrix-hyper-v-migration.md).

Pour configurer la découverte et l’évaluation des serveurs s’exécutant sur Hyper-V, vous devez créer un projet et y ajouter l’outil Azure Migrate : découverte et évaluation. Une fois l’outil ajouté, vous déployez l’[appliance Azure Migrate](migrate-appliance.md). L’appliance découvre en permanence les serveurs locaux et envoie les métadonnées et données de performances de serveur à Azure. Une fois la découverte terminée, vous rassemblez les serveurs découverts dans des groupes, puis évaluez ceux-ci.

## <a name="limitations"></a>Limites

**Support** | **Détails**
--- | ---
**Limites d’évaluation** | Vous pouvez découvrir et évaluer jusqu’à 35 000 serveurs dans un même [projet](migrate-support-matrix.md#project).
**Limites de projet** | Vous pouvez créer plusieurs projets dans un abonnement Azure. En plus de serveurs sur Hyper-V, un projet peut inclure des serveurs sur VMware et des serveurs physiques, jusqu’aux limites d’évaluation pour chacun d’eux.
**Découverte** | L’appliance Azure Migrate peut découvrir jusqu’à 5 000 serveurs s’exécutant sur Hyper-V.<br/><br/> L’appliance peut se connecter à jusqu’à 300 hôtes Hyper-V.
**Évaluation** | Vous pouvez ajouter jusqu’à 35 000 serveurs dans un groupe unique.<br/><br/> Vous pouvez évaluer jusqu’à 35 000 serveurs par évaluation pour un groupe.

[Apprenez-en davantage](concepts-assessment-calculation.md) sur les évaluations.

## <a name="hyper-v-host-requirements"></a>Configuration requise pour l’hôte Hyper-V

| **Support**                | **Détails**
| :-------------------       | :------------------- |
| **Hôte Hyper-V**       | L’hôte Hyper-V peut être autonome ou déployé dans un cluster.<br/><br/> L’hôte Hyper-V peut exécuter Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2. Les installations minimales de serveurs de ces systèmes d’exploitation sont également prises en charge. <br/>Vous ne pouvez pas évaluer des serveurs situés sur des hôtes Hyper-V exécutant Windows Server 2012.
| **autorisations**           | Vous avez besoin des privilèges Administrateur sur l’hôte Hyper-V. <br/> Si vous ne souhaitez pas attribuer de privilèges Administrateur, créez un compte d’utilisateur local ou de domaine et ajoutez le compte d’utilisateur à ces groupes : Utilisateurs de gestion à distance, Administrateurs Hyper-V et Utilisateurs de l’Analyseur de performances. |
| **Communication à distance PowerShell**   | La [communication à distance PowerShell](/powershell/module/microsoft.powershell.core/enable-psremoting) doit être activée sur chaque hôte Hyper-V. |
| **Réplica Hyper-V**       | Si vous utilisez un réplica Hyper-V (ou si vous avez plusieurs serveurs avec les mêmes identificateurs) et que vous découvrez les serveurs tant originaux que répliqués à l’aide d’Azure Migrate, il se peut que l’évaluation générée par Azure Migrate ne soit pas exacte. |

## <a name="server-requirements"></a>Configuration requise au niveau du serveur

| **Support**                  | **Détails**
| :----------------------------- | :------------------- |
| **Système d’exploitation** | Tous les systèmes d’exploitation peuvent être évalués dans une optique de migration.  |
| **Services d’intégration**       | Les [services d’intégration Hyper-V](/virtualization/hyper-v-on-windows/reference/integration-services) doivent s’exécuter sur les serveurs que vous évaluez afin de capturer les informations du système d’exploitation. |
| **Stockage** | Disque local, DAS, JBOD, espaces de stockage, CSV, SMB. Ces stockages d’hôte Hyper-V sur lesquels sont stockés les disques VHD/VHDX sont pris en charge. <br/> Les contrôleurs virtuels IDE et SCSI sont pris en charge|

## <a name="azure-migrate-appliance-requirements"></a>Conditions requises de l’appliance Azure Migrate

Azure Migrate utilise l’[appliance Azure Migrate](migrate-appliance.md) pour la découverte et l’évaluation. Vous pouvez déployer l’appliance à l’aide d’un disque dur virtuel Hyper-V compressé que vous téléchargez à partir du portail, ou à l’aide d’un [script PowerShell](deploy-appliance-script.md).

- En savoir plus sur les [conditions requises de l’appliance](migrate-appliance.md#appliance---hyper-v) pour Hyper-V.
- Découvrez les URL auxquelles l’appliance doit accéder dans les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls).
- Dans Azure Government, vous devez déployer l’appliance [avec ce script](deploy-appliance-script-government.md).

## <a name="port-access"></a>Accès au port

Le tableau suivant résume les exigences du port pour l’évaluation.

**Appareil** | **Connection**
--- | ---
**Appliance** | Connexions entrantes sur le port TCP 3389 pour permettre des connexions Bureau à distance avec l’appliance.<br/><br/> Connexions entrantes sur le port 44368 pour accéder à distance à l’application de gestion de l’appliance via l’URL : ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Connexions sortantes sur les ports 443 (HTTPS) pour envoyer les métadonnées de découverte et de performances à Azure Migrate.
**Hôte/cluster Hyper-V** | Connexions entrantes sur le port WinRM 5985 (HTTP) ou 5986 (HTTPS) pour extraire les métadonnées et les données de performances des serveurs sur Hyper-V en utilisant une session Common Information Model (CIM).

## <a name="agent-based-dependency-analysis-requirements"></a>Conditions requises de l’analyse des dépendances basées sur un agent

L’[analyse des dépendances](concepts-dependency-visualization.md) vous permet d’identifier les dépendances entre les machines locales que vous souhaitez évaluer et migrer vers Azure. Le tableau récapitule les conditions requises pour la configuration de l’analyse des dépendances basées sur un agent. Actuellement, Hyper-V prend uniquement en charge la visualisation des dépendances basées sur un agent.

**Prérequis** | **Détails**
--- | --- 
**Avant le déploiement** | Vous devez disposer d’un projet, avec l’outil Azure Migrate : découverte et évaluation ajouté au projet.<br/><br/>  Vous déployez la visualisation des dépendances après avoir configuré une appliance Azure Migrate pour découvrir vos serveurs locaux.<br/><br/> [Découvrez comment](create-manage-projects.md) créer un projet pour la première fois.<br/> [Découvrez comment](how-to-assess.md) ajouter l’outil Azure Migrate : découverte et évaluation à un projet existant.<br/> Découvrez comment configurer l’appliance pour la découverte et l’évaluation des [serveurs sur Hyper-V](how-to-set-up-appliance-hyper-v.md).
**Azure Government** | La visualisation des dépendances n’est pas disponible dans Azure Government.
**Log Analytics** | Azure Migrate utilise la solution [Service Map](../azure-monitor/vm/service-map.md) dans [Journaux d’activité Azure Monitor](../azure-monitor/logs/log-query-overview.md) pour la visualisation des dépendances.<br/><br/> Vous associez un espace de travail Log Analytics nouveau ou déjà existant à un projet. Il n’est pas possible de modifier l’espace de travail d’un projet après son ajout. <br/><br/> L’espace de travail doit se trouver dans le même abonnement que le projet.<br/><br/> L’espace de travail doit résider dans les régions USA Est, Asie Sud-Est ou Europe Ouest. Les espaces de travail des autres régions ne peuvent pas être associés à un projet.<br/><br/> L’espace de travail doit se trouver au sein d’une région dans laquelle [Service Map est pris en charge](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Dans Log Analytics, l’espace de travail associé à Azure Migrate est marqué avec la clé de projet de migration et le nom du projet.
**Agents nécessaires** | Sur chaque serveur à analyser, installez les agents suivants :<br/><br/> [MMA (Microsoft Monitoring Agent)](../azure-monitor/agents/agent-windows.md)<br/> [Dependency Agent](../azure-monitor/agents/agents-overview.md#dependency-agent)<br/><br/> Si les serveurs locaux ne sont pas connectés à Internet, vous devez télécharger et installer une passerelle Log Analytics sur ceux-ci.<br/><br/> En savoir plus sur l’installation de [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) et de [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Espace de travail Log Analytics** | L’espace de travail doit se trouver dans le même abonnement que le projet.<br/><br/> Azure Migrate prend en charge les espaces de travail résidant dans les régions USA Est, Asie Sud-Est et Europe Ouest.<br/><br/>  L’espace de travail doit se trouver au sein d’une région dans laquelle [Service Map est pris en charge](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Il n’est pas possible de modifier l’espace de travail d’un projet après son ajout.
**Coûts** | La solution Service Map n’entraîne aucun frais pendant les 180 premiers jours (à compter du jour où vous associez l’espace de travail Log Analytics au projet).<br/><br/> Au bout de 180 jours, des frais Log Analytics standard s’appliquent.<br/><br/> L’utilisation d’une autre solution que Service Map dans l’espace de travail Log Analytics associé entraîne des [frais standard](https://azure.microsoft.com/pricing/details/log-analytics/) pour l’espace de travail Log Analytics.<br/><br/> Lorsque le projet est supprimé, l’espace de travail ne l’est pas. Une fois le projet supprimé, l’utilisation de Service Map n’est plus gratuite, et chaque nœud est facturé en fonction du niveau payant de l’espace de travail Log Analytics<br/><br/>Si vous avez créé des projets avant la disponibilité générale d’Azure Migrate (28 février 2018), vous avez peut-être fait l’objet de frais supplémentaires pour Service Map. Pour veiller à payer uniquement après 180 jours, nous vous recommandons de créer un autre projet, car les espaces de travail présents avant la disponibilité générale restent facturables.
**Gestion** | Lorsque vous inscrivez des agents dans l’espace de travail, vous utilisez l’ID et la clé fournis par le projet.<br/><br/> Vous pouvez utiliser l’espace de travail Log Analytics en dehors d’Azure Migrate.<br/><br/> Si vous supprimez le projet associé, l’espace de travail n’est pas automatiquement supprimé. [Supprimez-le manuellement](../azure-monitor/logs/manage-access.md).<br/><br/> Ne supprimez pas l’espace de travail créé par Azure Migrate, sauf si vous supprimez le projet. La suppression de l’espace de travail entraînerait un dysfonctionnement de la fonctionnalité de visualisation des dépendances.
**Connectivité Internet** | Si les serveurs ne sont pas connectés à Internet, vous devez installer la passerelle Log Analytics sur ceux-ci.
**Azure Government** | L'analyse des dépendances basée sur un agent n'est pas prise en charge.

## <a name="next-steps"></a>Étapes suivantes

[Préparer l’évaluation des serveurs s’exécutant sur Hyper-V](./tutorial-discover-hyper-v.md).