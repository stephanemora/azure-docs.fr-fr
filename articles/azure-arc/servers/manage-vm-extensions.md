---
title: Gestion des extensions de machine virtuelle avec des serveur activés par Azure Arc
description: Les serveurs activés par Azure Arc peuvent gérer le déploiement d’extensions de machine virtuelle qui permettent d’effectuer des tâches d’automatisation et de configuration post-déploiement sur des machines virtuelles non Azure.
ms.date: 03/01/2021
ms.topic: conceptual
ms.openlocfilehash: 039c52ccbee03636da0f5acc0fc5844be9b646f5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687904"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Gestion des extensions de machine virtuelle avec des serveurs activés par Azure Arc

Les extensions de machine virtuelle sont de petites applications qui permettent d’effectuer des tâches de configuration et d’automatisation de post-déploiement sur des machines virtuelles Azure. Par exemple, si une machine virtuelle exige l’installation d’un logiciel, une protection antivirus ou l’exécution d’un script, vous pouvez utiliser une extension de machine virtuelle.

Les serveurs avec Azure Arc vous permettent de déployer des extensions de machine virtuelle Azure sur des machines virtuelles Linux et Windows hors Azure, ce qui simplifie la gestion de votre machine hybride tout au long de son cycle de vie. Les extensions de machine virtuelle peuvent être gérées à l’aide des méthodes suivantes sur vos machines ou serveurs hybrides gérés par des serveurs compatibles Arc :

- [Portail Azure](manage-vm-extensions-portal.md)
- [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- [Modèles Azure Resource Manager](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>Principaux avantages

La prise en charge des extensions de machine virtuelle des serveurs activés par Azure Arc offre les avantages clés suivants :

- Collectez les données de journaux à des fins d’analyse avec [Journaux dans Azure Monitor](../../azure-monitor/logs/data-platform-logs.md) en activant l’extension de machine virtuelle de l’agent Log Analytics. Ce point s’avère utile pour effectuer une analyse complexe de données issues de sources de différents types.

- Avec [Azure Monitor pour machines virtuelles](../../azure-monitor/vm/vminsights-overview.md), analyse les performances de vos machines virtuelles Windows et Linux et supervise leurs processus et dépendances vis-à-vis d’autres ressources et les processus externes. Pour cela, il convient d’activer les extensions de machine virtuelle de l’agent Log Analytics et de Dependency Agent.

- Télécharge et exécute des scripts sur des machines connectées hybrides à l’aide de l’extension de script personnalisé. Elle est utile pour la configuration de post-déploiement, l’installation de logiciels ou toute autre tâche de configuration ou de gestion.

- Actualisation automatique des certificats stockés dans [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="availability"></a>Disponibilité

La fonctionnalité d’extension de machine virtuelle n’est disponible que dans la liste des [régions prises en charge](overview.md#supported-regions). Veillez à intégrer votre machine dans l’une de ces régions.

## <a name="extensions"></a>Extensions

Dans cette version, nous prenons en charge les extensions de machine virtuelle suivantes sur les machines Windows et Linux.

Pour découvrir le package d’agent Azure Connected Machine et obtenir des détails sur le composant Agent Extension, consultez [Vue d’ensemble de l’agent](agent-overview.md#agent-component-details).

### <a name="windows-extensions"></a>Extensions Windows

|Extension |Serveur de publication |Type |Informations supplémentaires |
|----------|----------|-----|-----------------------|
|Analyseur de vulnérabilité intégré à Azure Defender |Qualys |WindowsAgent.AzureSecurityCenter |[Solution intégrée d'évaluation des vulnérabilités d'Azure Defender pour les machines Azure et hybrides](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Extension de script personnalisé |Microsoft.Compute | CustomScriptExtension |[Extension de script personnalisée Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|Agent Log Analytics |Microsoft.EnterpriseCloud.Monitoring |MicrosoftMonitoringAgent |[Extension de machine virtuelle Log Analytics pour Windows](../../virtual-machines/extensions/oms-windows.md)|
|Azure Monitor pour machines virtuelles (insights) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentWindows | [Extension de machine virtuelle Dependency Agent pour Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Synchronisation du certificat Azure Key Vault | Microsoft.Azure.Key.Vault |KeyVaultForWindows | [Extension de machine virtuelle Key Vault pour Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|Agent Azure Monitor |Microsoft.Azure.Monitor |AzureMonitorWindowsAgent |[Installer l’agent Azure Monitor (préversion)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Extensions Linux

|Extension |Serveur de publication |Type |Informations supplémentaires |
|----------|----------|-----|-----------------------|
|Analyseur de vulnérabilité intégré à Azure Defender |Qualys |LinuxAgent.AzureSecurityCenter |[Solution intégrée d'évaluation des vulnérabilités d'Azure Defender pour les machines Azure et hybrides](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Extension de script personnalisé |Microsoft.Azure.Extensions |CustomScript |[Extension de script personnalisé Linux version 2](../../virtual-machines/extensions/custom-script-linux.md) |
|Agent Log Analytics |Microsoft.EnterpriseCloud.Monitoring |OmsAgentForLinux |[Extension de machine virtuelle Log Analytics pour Linux](../../virtual-machines/extensions/oms-linux.md) |
|Azure Monitor pour machines virtuelles (insights) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentLinux |[Extension de machine virtuelle Dependency Agent pour Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Synchronisation du certificat Azure Key Vault | Microsoft.Azure.Key.Vault |KeyVaultForLinux | [Extension de machine virtuelle Key Vault pour Linux](../../virtual-machines/extensions/key-vault-linux.md) |
|Agent Azure Monitor |Microsoft.Azure.Monitor |AzureMonitorLinuxAgent |[Installer l’agent Azure Monitor (préversion)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>Prérequis

Cette fonctionnalité dépend des fournisseurs de ressources Azure suivants de votre abonnement :

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

S’ils ne sont pas déjà inscrits, suivez la procédure [Inscription de fournisseurs de ressources Azure](agent-overview.md#register-azure-resource-providers).

Veillez à consulter la documentation relative à chacune des extensions de machine virtuelle indiquées dans le tableau précédent pour savoir si elle présente une configuration réseau ou système requise. Vous éviterez ainsi des problèmes de connectivité avec des services et des fonctionnalités Azure qui s’appuient sur cette extension de machine virtuelle.

### <a name="log-analytics-vm-extension"></a>Extension de machine virtuelle Log Analytics

L’extension de machine virtuelle de l’agent Log Analytics pour Linux nécessite l’installation de Python 2.x sur la machine cible.

### <a name="azure-key-vault-vm-extension-preview"></a>Extension de machine virtuelle Azure Key Vault (préversion)

L’extension de machine virtuelle Key Vault (préversion) ne prend pas en charge les systèmes d’exploitation Linux suivants :

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

Le déploiement de l’extension de machine virtuelle Key Vault (préversion) n’est pris en charge qu’avec :

- L’interface Azure CLI
- Azure PowerShell
- Modèle Azure Resource Manager

Avant de déployer l’extension, vous devez effectuer les opérations suivantes :

1. [Créer un coffre et un certificat](../../key-vault/certificates/quick-create-portal.md) (auto-signé ou à importer).

2. Accordez au serveur compatible avec Azure Arc l’accès au secret du certificat. Si vous utilisez la [préversion du RBAC](../../key-vault/general/rbac-guide.md), recherchez le nom de la ressource Azure Arc et attribuez-lui le rôle **Utilisateur de secrets de Key Vault (préversion)** . Si vous utilisez la [stratégie d’accès à Key Vault](../../key-vault/general/assign-access-policy-portal.md), affectez au secret des autorisations **Get** pour l’identité attribuée par le système de la ressource Azure Arc.

### <a name="connected-machine-agent"></a>Agent Connected Machine

Vérifiez que votre machine correspond aux [versions prises en charge](agent-overview.md#supported-operating-systems) des systèmes d’exploitation Windows et Linux pour l’agent Azure Connected Machine.

La version minimale de l’agent Connected Machine prise en charge avec cette fonctionnalité sur Windows et Linux est la version 1.0.

Pour mettre à niveau votre machine vers la version exigée de l’agent, consultez [Mettre à niveau l’agent](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez déployer, gérer et supprimer des extensions de machine virtuelle à l’aide d’[Azure CLI](manage-vm-extensions-cli.md), d’[Azure PowerShell](manage-vm-extensions-powershell.md), du [portail Azure](manage-vm-extensions-portal.md) ou de [modèles Azure Resource Manager](manage-vm-extensions-template.md).
