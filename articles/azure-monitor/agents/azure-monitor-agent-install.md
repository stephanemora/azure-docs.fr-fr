---
title: Installer l’agent Azure Monitor
description: Options d’installation de l’agent Azure Monitor sur les machines virtuelles Azure et les serveurs avec Azure Arc.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/21/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 13e025845dd4fbd51519f85f1879cc11c6ea102d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706899"
---
# <a name="install-the-azure-monitor-agent"></a>Installer l’agent Azure Monitor
Cet article présente les différentes options actuellement disponibles pour installer l’[agent Azure Monitor](azure-monitor-agent-overview.md) sur les machines virtuelles Azure et les serveurs avec Azure Arc, ainsi que les options permettant de créer des [associations avec des règles de collecte de données](data-collection-rule-azure-monitor-agent.md) qui définissent les données que l’agent doit collecter.

## <a name="prerequisites"></a>Prérequis
Les conditions préalables suivantes sont requises avant l’installation de l’agent Azure Monitor.

- L’[identité du système managé](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) doit être activée sur les machines virtuelles Azure. Cela n’est pas nécessaire pour les serveurs avec Azure Arc. L’identité système est activée automatiquement si l’agent est installé dans le cadre du processus de [création et d’affectation d’une règle de collecte de données à l’aide du portail Azure](#install-with-azure-portal).
- L’[étiquette de service AzureResourceManager](../../virtual-network/service-tags-overview.md) doit être activée sur le réseau virtuel pour la machine virtuelle.
- La machine virtuelle doit avoir accès aux points de terminaison HTTPS suivants :
  - *.ods.opinsights.azure.com
  - *.ingest.monitor.azure.com
  - *.control.monitor.azure.com

> [!IMPORTANT]
> L’agent Azure Monitor ne prend actuellement pas en charge les liaisons privées.

## <a name="virtual-machine-extension-details"></a>Détails de l’extension de machine virtuelle
L’agent Azure Monitor est implémenté en tant qu’[extension de machine virtuelle Azure](../../virtual-machines/extensions/overview.md) avec les détails dans le tableau suivant. Il peut être installé à l’aide de l’une des méthodes d’installation des extensions de machine virtuelle, notamment celles décrites dans cet article.

| Propriété | Windows | Linux |
|:---|:---|:---|
| Serveur de publication | Microsoft.Azure.Monitor  | Microsoft.Azure.Monitor |
| Type      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |

## <a name="extension-versions"></a>Versions d’extension
Il est vivement recommandé d’effectuer la mise à jour vers les versions GA et ultérieures au lieu d’utiliser les versions préliminaires.

| Date de sortie | Notes de publication | Windows | Linux |
|:---|:---|:---|:---|:---|
| Juin 2021 | Disponibilité générale annoncée. <ul><li>Toutes les fonctionnalités à l’exception de la destination des métriques sont désormais en disponibilité générale</li><li>Qualité de production, sécurité et conformité</li><li>Disponibilité dans toutes les régions publiques</li><li>Améliorations des performances et de la mise à l’échelle pour l’EPS le plus élevé</li></ul> [En savoir plus](https://azure.microsoft.com/updates/azure-monitor-agent-and-data-collection-rules-now-generally-available/) | 1.0.12.0 | 1.9.1.0 |
| Juillet 2021 | <ul><li>Prise en charge des proxies directs</li><li>Prise en charge de la passerelle Log Analytics</li></ul> [En savoir plus](https://azure.microsoft.com/updates/general-availability-azure-monitor-agent-and-data-collection-rules-now-support-direct-proxies-and-log-analytics-gateway/) | 1.1.1.0 | 1.10.5.0 |
| Août 2021 | Résolution du problème autorisant les métriques Azure Monitor comme seule destination | 1.1.2.0 | 1.10.9.0 (ne pas utiliser 1.10.7.0) |
| Septembre 2021 | Résolution du problème à l’origine de la perte de données lors du redémarrage de l’agent | 1.1.3.1 | 1.12.2.0 |


## <a name="install-with-azure-portal"></a>Installer avec Portail Azure
Pour installer l’agent Azure Monitor à l’aide du portail Azure, suivez le processus de [création d’une règle de collecte de données](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) dans le portail Azure. Cela vous permet d’associer la règle de collecte de données à au moins une machine virtuelle Azure ou à des serveurs avec Azure Arc. L’agent est installé sur l’une de ces machines virtuelles sur laquelle il n’est pas encore installé.


## <a name="install-with-resource-manager-template"></a>Installer avec un modèle Resource Manager
Vous pouvez utiliser des modèles Resource Manager pour installer l’agent Azure Monitor sur des machines virtuelles Azure et sur des serveurs avec Azure Arc, ainsi que pour créer une association avec des règles de collecte de données. Vous devez créer une règle de collecte des données avant de créer l’association.

Obtenez des exemples de modèles pour l’installation de l’agent et la création de l’association à partir des éléments suivants : 

- [Modèle d’installation de l’agent Azure Monitor (Azure et Azure Arc)](../agents/resource-manager-agent.md#azure-monitor-agent-preview) 
- [Modèle de création d’une association avec une règle de collecte de données](./resource-manager-data-collection-rules.md)

Installez les modèles à l’aide de [n’importe quelle méthode de déploiement pour les modèles Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md) comme les commandes suivantes.

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>Installer avec PowerShell
Vous pouvez installer l’agent Azure Monitor sur des machines virtuelles Azure et sur des serveurs avec Azure Arc à l’aide de la commande PowerShell pour ajouter une extension de machine virtuelle. 

### <a name="azure-virtual-machines"></a>Machines virtuelles Azure
Utilisez les commandes PowerShell suivantes pour installer l’agent Azure Monitor sur des machines virtuelles Azure.
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.5
```
---

### <a name="azure-arc-enabled-servers"></a>Serveurs avec Azure Arc
Utilisez les commandes PowerShell suivantes pour installer l’agent Azure Monitor sur des serveurs avec Azure Arc.
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location>
```
---
## <a name="azure-cli"></a>Azure CLI
Vous pouvez installer l’agent Azure Monitor sur des machines virtuelles Azure et sur des serveurs avec Azure Arc à l’aide de la commande Azure CLI pour ajouter une extension de machine virtuelle. 

### <a name="azure-virtual-machines"></a>Machines virtuelles Azure
Utilisez les commandes CLI suivantes pour installer l’agent Azure Monitor sur des machines virtuelles Azure.
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Serveurs avec Azure Arc
Utilisez les commandes CLI suivantes pour installer l’agent Azure Monitor sur des serveurs avec Azure Arc.

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --type AzureMonitorWindowsAgent --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --type AzureMonitorLinuxAgent --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location>
```
---


## <a name="next-steps"></a>Étapes suivantes

- [Créez une règle de collecte de données](data-collection-rule-azure-monitor-agent.md) pour collecter des données à partir de l’agent et les envoyer à Azure Monitor.
