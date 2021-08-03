---
title: Plan de déploiement d’Azure Automation Update Management
description: Cet article décrit les considérations et les décisions à prendre pour préparer le déploiement d’Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 06/07/2021
ms.topic: conceptual
ms.openlocfilehash: 5b2303b17a525a6ae394996780fe3ebb89388f44
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111855115"
---
# <a name="plan-your-update-management-deployment"></a>Planifier votre déploiement d’Update Management

## <a name="step-1---automation-account"></a>Étape 1 : Compte Automation

Update Management est une fonctionnalité d’Azure Automation et nécessite donc un compte Automation. Vous pouvez utiliser un compte Automation existant dans votre abonnement ou créer un nouveau compte dédié uniquement à Update Management et à aucune autre fonctionnalité d’Automation.

## <a name="step-2---azure-monitor-logs"></a>Étape 2 : Journaux d’activité Azure Monitor

Update Management dépend d’un espace de travail Log Analytics dans Azure Monitor pour stocker les données des journaux d’évaluation et d’état des mises à jour collectées auprès de machines managées. L’intégration avec Log Analytics permet également une analyse détaillée et la création d’alertes dans Azure Monitor. Vous pouvez utiliser un espace de travail existant dans votre abonnement ou en créer un nouveau dédié uniquement à Update Management.

Si vous débutez avec Journaux d’activité Azure Monitor et l’espace de travail Log Analytics, vous devez consulter le guide de déploiement [Concevoir un espace de travail Log Analytics](../../azure-monitor/logs/design-logs-deployment.md). 

## <a name="step-3---supported-operating-systems"></a>Étape 3 : Systèmes d’exploitation pris en charge

Update Management prend en charge des versions spécifiques des systèmes d’exploitation Windows Server et Linux. Avant d’activer Update Management, vérifiez que les machines cibles répondent à la [configuration requise du système d’exploitation](operating-system-requirements.md). 

## <a name="step-4---log-analytics-agent"></a>Étape 4 : Agent Log Analytics

L’[agent Log Analytics](../../azure-monitor/agents/log-analytics-agent.md) pour Windows et Linux est requis pour prendre en charge Update Management. L’agent est utilisé à la fois pour la collecte de données et pour le rôle Runbook Worker hybride du système Automation pour prendre en charge les runbooks Update Management utilisé pour gérer les déploiements d’évaluation et de mise à jour sur la machine. 

Sur les machines virtuelles Azure, si l’agent Log Analytics n’est pas déjà installé, lorsque vous activez Update Management pour la machine virtuelle, il est automatiquement installé à l’aide de l’extension de machine virtuelle Log Analytics pour [Windows](../../virtual-machines/extensions/oms-windows.md) ou [Linux](../../virtual-machines/extensions/oms-linux.md). L’agent est configuré pour envoyer ses rapports à l’espace de travail Log Analytics lié au compte Automation dans lequel Update Management est activé.

Pour les machines virtuelles ou les serveurs non Azure, l’agent Log Analytics pour Windows ou Linux doit être installé et envoyer ses rapports à l’espace de travail lié. Nous vous recommandons d’installer l’agent Log Analytics pour Windows ou Linux en connectant d’abord votre machine à des [serveurs avec Azure Arc](../../azure-arc/servers/overview.md), puis d’utiliser Azure Policy pour attribuer la stratégie intégrée [Déployer l’agent Log Analytics sur des machines Azure Arc Linux ou Windows](../../governance/policy/samples/built-in-policies.md#monitoring). Autrement, si vous envisagez de superviser les machines avec [VM Insights](../../azure-monitor/vm/vminsights-overview.md), utilisez plutôt l’initiative [Activer Azure Monitor pour machines virtuelles](../../governance/policy/samples/built-in-initiatives.md#monitoring).

Si vous activez une machine actuellement gérée par Operations Manager, l’installation d’un nouvel agent n’est pas nécessaire. Les informations relatives à l’espace de travail sont ajoutées à la configuration des agents lorsque vous connectez le groupe d’administration à l’espace de travail Log Analytics.

L’inscription d’une machine auprès du service Update Management dans plusieurs espaces de travail Log Analytics (également appelé multihébergement) n’est pas prise en charge.

## <a name="step-5---network-planning"></a><a name="ports"></a>Étape 5 : Planification réseau

Pour préparer votre réseau à prendre en charge Update Management, vous devrez peut-être configurer quelques composants d’infrastructure. Par exemple, ouvrez des ports de pare-feu pour transmettre les communications utilisées par Update Management et Azure Monitor.

Pour plus d’informations sur les ports, les URL et la mise en réseau qui sont requis pour Update Management, y compris le rôle Runbook Worker hybride, consultez [Configuration réseau d’Azure Automation](../automation-network-configuration.md). Pour vous connecter au service Automation à partir de vos machines virtuelles Azure en toute sécurité et de façon privée, consultez [Utiliser Azure Private Link](../how-to/private-link-security.md). 

Pour les machines Windows, vous devez également autoriser le trafic vers tous les points de terminaison requis par l’agent Windows Update. Vous trouverez une liste actualisée des points de terminaison requis dans [Problèmes liés à HTTP/au proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Si vous disposez d’un déploiement [Windows Server Update Services](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment) (WSUS) local, vous devez également autoriser le trafic vers le serveur spécifié dans votre [clé WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Pour les machines Red Hat Linux, consultez [Adresses IP des serveurs de distribution de contenu RHUI](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) pour les points de terminaison requis. Pour les autres distributions Linux, reportez-vous à la documentation du fournisseur.

Si vos stratégies de sécurité informatique n’autorisent pas les machines du réseau à se connecter à Internet, vous pouvez configurer une [passerelle Log Analytics](../../azure-monitor/agents/gateway.md), puis configurer la machine pour qu’elle se connecte à Azure Automation et Azure Monitor via la passerelle.

## <a name="step-6---permissions"></a>Étape 6 : Autorisations

Pour créer et gérer des déploiements de mises à jour, vous devez disposer d’autorisations spécifiques. Pour en savoir plus sur ces autorisations, consultez [Accès en fonction du rôle - Update Management](../automation-role-based-access-control.md#update-management-permissions).

## <a name="step-7---windows-update-client"></a>Étape 7 : Client Windows Update

Azure Automation Update Management s’appuie sur le client Windows Update pour télécharger et installer les mises à jour de Windows. Des paramètres spécifiques de stratégie de groupe sont utilisés par l’agent Windows Update (WUA) sur les machines pour se connecter à Windows Server Update Services (WSUS) ou Microsoft Update. Ces paramètres de stratégie de groupe sont également utilisés pour analyser la conformité des mises à jour de logiciels et mettre celles-ci à jour automatiquement. Pour passer en revue nos recommandations, consultez [Configurer les paramètres de Windows Update pour Update Management](configure-wuagent.md).

## <a name="step-8---linux-repository"></a>Étape 8 : Référentiel Linux

Les machines virtuelles créées à partir des images Red Hat Enterprise Linux (RHEL) à la demande, disponibles sur Place de marché Azure, sont inscrites pour accéder à l’infrastructure RHUI (Red Hat Update Infrastructure) déployée dans Azure. Toute autre distribution Linux doit être mise à jour à partir du référentiel des fichiers en ligne de la distribution au moyen des méthodes prises en charge par cette distribution.

Pour classifier les mises à jour sur Red Hat Enterprise version 6, vous devez installer le plug-in yum-security. Sur Red Hat Enterprise Linux 7, le plug-in faisant déjà partie de yum lui-même, il est inutile d’installer quoi que ce soit. Pour plus d’informations, consultez l’[article de base de connaissances](https://access.redhat.com/solutions/10021) suivant sur Red Hat.

## <a name="step-9---plan-deployment-targets"></a>Étape 9 : Planifier les cibles de déploiement

Update Management vous permet de cibler les mises à jour sur un groupe dynamique représentant des machines Azure ou non Azure afin de vous assurer que des machines spécifiques reçoivent toujours les mises à jour appropriées au moment le plus opportun. Un groupe dynamique est résolu au moment du déploiement et est basé sur les critères suivants :

* Abonnement
* Groupes de ressources
* Emplacements
* Balises 

Pour les machines non Azure, un groupe dynamique utilise des recherches enregistrées, également appelées [groupes d’ordinateurs](../../azure-monitor/logs/computer-groups.md). Les déploiements de mises à jour étendus à un groupe d’ordinateurs sont uniquement visibles à partir du compte Automation dans l’option **Planifications de déploiement** d’Update Management, et non à partir d’une machine virtuelle Azure spécifique.

Par ailleurs, les mises à jour peuvent uniquement être gérées pour une certaine machine virtuelle Azure. Les déploiements de mises à jour étendus à la machine spécifique sont visibles à la fois à partir de la machine et du compte Automation dans l’option **Planifications de déploiement** d’Update Management. 

## <a name="next-steps"></a>Étapes suivantes

Activez Update Management et sélectionnez les machines à gérer à l’aide de l’une des méthodes suivantes :

- Un [modèle Azure Resource Manager](enable-from-template.md) est disponible pour déployer Update Management sur un compte Automation et un espace de travail Log Analytics Azure Monitor nouveaux ou existants dans votre abonnement. Il ne configure pas l’étendue des machines qui doivent être gérées ; cette opération fait l’objet d’une étape distincte après l’utilisation du modèle.

- À partir de votre [compte Automation](enable-from-automation-account.md) pour une ou plusieurs machines Azure et non-Azure, notamment des serveurs compatibles avec Arc.

- En utilisant le [runbook](enable-from-runbook.md) **Enable-AutomationSolution** pour automatiser l’intégration des machines virtuelles Azure.

- Pour une [machine virtuelle Azure sélectionnée](enable-from-vm.md) dans la page **Machine virtuelle** du portail Azure. Ce scénario est disponible pour les machines virtuelles Linux et Windows.

- Pour [plusieurs machines virtuelles Azure](enable-from-portal.md), sélectionnez-les dans la page **Machines virtuelles** du portail Azure.