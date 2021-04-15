---
title: Prise en charge de la découverte et de l’évaluation de serveurs physiques dans Azure Migrate
description: 'En savoir plus sur la prise en charge de la découverte et de l’évaluation de serveurs physiques avec Azure Migrate : découverte et évaluation'
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 09110bb15f853f20b0be9d8c4c6f5088e7a55d8b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382978"
---
# <a name="support-matrix-for-physical-server-discovery-and-assessment"></a>Matrice de prise en charge pour la découverte et l’évaluation de serveurs physiques 

Cet article résume les conditions préalables et les exigences de prise en charge lors de l’évaluation de serveurs physiques pour la migration vers Azure et utilise l’outil [Azure Migrate : découverte et évaluation](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool). Si vous souhaitez migrer des serveurs physiques vers Azure, passez en revue la [matrice de prise en charge de la migration](migrate-support-matrix-physical-migration.md).

Pour évaluer des serveurs physiques, vous créez un projet et ajoutez l’outil Azure Migrate : découverte et évaluation. Une fois l’outil ajouté, vous déployez l’[appliance Azure Migrate](migrate-appliance.md). L’appliance découvre en permanence les serveurs locaux et envoie les métadonnées et données de performances des serveurs à Azure. Une fois la découverte terminée, vous rassemblez les serveurs découverts dans des groupes, puis évaluez ceux-ci.

## <a name="limitations"></a>Limites

**Support** | **Détails**
--- | ---
**Limites d’évaluation** | Vous pouvez découvrir et évaluer jusqu’à 35 000 serveurs physiques dans un même [projet](migrate-support-matrix.md#project).
**Limites de projet** | Vous pouvez créer plusieurs projets dans un abonnement Azure. En plus de serveurs physiques, un projet peut inclure des serveurs sur VMware et sur Hyper-V, jusqu’aux limites d’évaluation pour chacun d’eux.
**Découverte** | L’appliance Azure Migrate peut découvrir jusqu’à 1000 serveurs physiques.
**Évaluation** | Vous pouvez ajouter jusqu’à 35 000 serveurs dans un groupe unique.<br/><br/> Vous pouvez évaluer jusqu’à 35 000 serveurs par évaluation.

[Apprenez-en davantage](concepts-assessment-calculation.md) sur les évaluations.

## <a name="physical-server-requirements"></a>Conditions requises des serveurs physiques

**Déploiement de serveur physique :** Le serveur physique peut être autonome ou déployé dans un cluster.

**Système d’exploitation :** Tous les systèmes d’exploitation Windows et Linux peuvent être évalués pour la migration.

**Autorisations :**

- Pour les serveurs Windows, utilisez un compte de domaine pour les serveurs joints à un domaine et un compte local pour ceux qui ne le sont pas. Le compte d’utilisateur doit être ajouté à ces groupes : Utilisateurs de gestion à distance, Utilisateurs de l’Analyseur de performances et Utilisateurs du Journal des performances.
    > [!Note]
    > Pour Windows Server 2008 et 2008 R2, vérifiez que WMF 3.0 est installé sur les serveurs et que le compte de domaine/local utilisé pour accéder aux serveurs a été ajouté aux groupes suivants : Utilisateurs de l'Analyseur de performances, Utilisateurs du journal des performances et WinRMRemoteWMIUsers.
- Pour les serveurs Linux, vous devez disposer d’un compte racine sur les serveurs Linux que vous souhaitez découvrir. Vous pouvez également définir un compte non racine doté des fonctionnalités requises à l’aide des commandes suivantes :

**Commande** | **Objectif**
--- | --- |
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk _(si /usr/sbin/fdisk n’est pas présent)_ | Collecter les données de configuration du disque
setcap "cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_setuid,<br>cap_setpcap,cap_net_bind_service,cap_net_admin,cap_sys_chroot,cap_sys_admin,<br>cap_sys_resource,cap_audit_control,cap_setfcap=+eip" /sbin/lvm | Collecter les données de performances du disque
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode | Collecter le numéro de série du BIOS
chmod a+r /sys/class/dmi/id/product_uuid | Collecter le GUID du BIOS

## <a name="azure-migrate-appliance-requirements"></a>Conditions requises de l’appliance Azure Migrate

Azure Migrate utilise l’[appliance Azure Migrate](migrate-appliance.md) pour la découverte et l’évaluation. L’appliance pour les serveurs physiques peut s’exécuter sur une machine virtuelle ou sur un serveur physique.

- En savoir plus sur les [conditions requises de l’appliance](migrate-appliance.md#appliance---physical) pour les serveurs physiques.
- Découvrez les URL auxquelles l’appliance doit accéder dans les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls).
- Vous configurez l’appliance à l’aide d’un [script PowerShell](how-to-set-up-appliance-physical.md) que vous téléchargez à partir du Portail Azure.
Dans Azure Government, déployez l’appliance [avec ce script](deploy-appliance-script-government.md).

## <a name="port-access"></a>Accès au port

Le tableau suivant résume les exigences du port pour l’évaluation.

**Appareil** | **Connection**
--- | ---
**Appliance** | Connexions entrantes sur le port TCP 3389 pour permettre des connexions Bureau à distance avec l’appliance.<br/><br/> Connexions entrantes sur le port 44368 pour accéder à distance à l’application de gestion de l’appliance via l’URL : ``` https://<appliance-ip-or-name>:44368 ```.<br/><br/> Connexions sortantes sur les ports 443 (HTTPS) pour envoyer les métadonnées de découverte et de performances à Azure Migrate.
**Serveurs physiques** | **Windows :** Connexion entrante sur le port WinRM 5985 (HTTP) ou 5986 pour extraire les métadonnées de configuration et de performances des serveurs Windows. <br/><br/> **Linux :**  Connexions entrantes sur le port 22 (TCP) pour extraire les métadonnées de configuration et de performances des serveurs Linux. |

## <a name="agent-based-dependency-analysis-requirements"></a>Conditions requises de l’analyse des dépendances basées sur un agent

L’[analyse des dépendances](concepts-dependency-visualization.md) vous permet d’identifier les dépendances entre les machines locales que vous souhaitez évaluer et migrer vers Azure. Le tableau récapitule les conditions requises pour la configuration de l’analyse des dépendances basées sur un agent. Actuellement, seule l’analyse des dépendances basées sur un agent est prise en charge pour les serveurs physiques.

**Prérequis** | **Détails**
--- | ---
**Avant le déploiement** | Vous devez disposer d’un projet, avec l’outil Azure Migrate : découverte et évaluation ajouté au projet.<br/><br/>  Vous déployez la visualisation des dépendances après avoir configuré une appliance Azure Migrate pour découvrir vos serveurs locaux.<br/><br/> [Découvrez comment](create-manage-projects.md) créer un projet pour la première fois.<br/> [Découvrez comment](how-to-assess.md) ajouter un outil d’évaluation à un projet existant.<br/> Découvrez comment configurer l’appliance Azure Migrate pour l’évaluation de serveurs [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md) ou physiques.
**Azure Government** | La visualisation des dépendances n’est pas disponible dans Azure Government.
**Log Analytics** | Azure Migrate utilise la solution [Service Map](../azure-monitor/vm/service-map.md) dans [Journaux d’activité Azure Monitor](../azure-monitor/logs/log-query-overview.md) pour la visualisation des dépendances.<br/><br/> Vous associez un espace de travail Log Analytics nouveau ou déjà existant à un projet. Il n’est pas possible de modifier l’espace de travail d’un projet après son ajout. <br/><br/> L’espace de travail doit se trouver dans le même abonnement que le projet.<br/><br/> L’espace de travail doit résider dans les régions USA Est, Asie Sud-Est ou Europe Ouest. Les espaces de travail des autres régions ne peuvent pas être associés à un projet.<br/><br/> L’espace de travail doit se trouver au sein d’une région dans laquelle [Service Map est pris en charge](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Dans Log Analytics, l’espace de travail associé à Azure Migrate est marqué avec la clé de projet de migration et le nom du projet.
**Agents nécessaires** | Sur chaque serveur à analyser, installez les agents suivants :<br/><br/> [MMA (Microsoft Monitoring Agent)](../azure-monitor/agents/agent-windows.md)<br/> [Dependency Agent](../azure-monitor/agents/agents-overview.md#dependency-agent)<br/><br/> Si les serveurs locaux ne sont pas connectés à Internet, vous devez télécharger et installer une passerelle Log Analytics sur ceux-ci.<br/><br/> En savoir plus sur l’installation de [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) et de [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Espace de travail Log Analytics** | L’espace de travail doit se trouver dans le même abonnement qu’un projet.<br/><br/> Azure Migrate prend en charge les espaces de travail résidant dans les régions USA Est, Asie Sud-Est et Europe Ouest.<br/><br/>  L’espace de travail doit se trouver au sein d’une région dans laquelle [Service Map est pris en charge](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Il n’est pas possible de modifier l’espace de travail d’un projet après son ajout.
**Coûts** | La solution Service Map n’entraîne aucun frais pendant les 180 premiers jours (à compter du jour où vous associez l’espace de travail Log Analytics au projet).<br/><br/> Au bout de 180 jours, des frais Log Analytics standard s’appliquent.<br/><br/> L’utilisation d’une autre solution que Service Map dans l’espace de travail Log Analytics associé entraîne des [frais standard](https://azure.microsoft.com/pricing/details/log-analytics/) pour l’espace de travail Log Analytics.<br/><br/> Lorsque le projet est supprimé, l’espace de travail ne l’est pas. Une fois le projet supprimé, l’utilisation de Service Map n’est plus gratuite, et chaque nœud est facturé en fonction du niveau payant de l’espace de travail Log Analytics<br/><br/>Si vous avez créé des projets avant la disponibilité générale d’Azure Migrate (28 février 2018), vous avez peut-être fait l’objet de frais supplémentaires pour Service Map. Pour veiller à payer uniquement après 180 jours, nous vous recommandons de créer un autre projet, car les espaces de travail présents avant la disponibilité générale restent facturables.
**Gestion** | Lorsque vous inscrivez des agents dans l’espace de travail, vous utilisez l’ID et la clé fournis par le projet.<br/><br/> Vous pouvez utiliser l’espace de travail Log Analytics en dehors d’Azure Migrate.<br/><br/> Si vous supprimez le projet associé, l’espace de travail n’est pas automatiquement supprimé. [Supprimez-le manuellement](../azure-monitor/logs/manage-access.md).<br/><br/> Ne supprimez pas l’espace de travail créé par Azure Migrate, sauf si vous supprimez le projet. La suppression de l’espace de travail entraînerait un dysfonctionnement de la fonctionnalité de visualisation des dépendances.
**Connectivité Internet** | Si les serveurs ne sont pas connectés à Internet, vous devez installer la passerelle Log Analytics sur ceux-ci.
**Azure Government** | L'analyse des dépendances basée sur un agent n'est pas prise en charge.

## <a name="next-steps"></a>Étapes suivantes

[Préparez la découverte et l’évaluation de serveurs physiques](./tutorial-discover-physical.md).