---
title: 'Exemple de script Azure PowerShell : Créer un environnement de test pour Pare-feu Azure'
description: 'Exemple de script Azure PowerShell : Créer un environnement de test pour Pare-feu Azure.'
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 8/13/2018
ms.author: victorh
ms.openlocfilehash: 23f10280cd34927e2e74cb7c5001850bedc6dd35
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967538"
---
# <a name="create-an-azure-firewall-test-environment"></a>Créer un environnement de test pour Pare-feu Azure

Cet exemple de script crée un pare-feu et un environnement réseau de test. Le réseau dispose d’un réseau virtuel, doté de trois sous-réseaux : *AzureFirewallSubnet*, *ServersSubnet* et *JumpboxSubnet*. Les sous-réseaux ServersSubnet et JumpboxSubnet sont équipés chacun d’un serveur Windows Server à 2 cœurs.

Le pare-feu se trouve dans AzureFirewallSubnet et est configuré à l’aide d’une collection de règles d’application avec une seule règle qui autorise l’accès à www.microsoft.com.

Un itinéraire défini par l’utilisateur est créé et pointe le trafic réseau provenant du sous-réseau ServersSubnet via le pare-feu, où les règles de pare-feu sont appliquées.

Vous pouvez exécuter le script à partir d’Azure [Cloud Shell](https://shell.azure.com/powershell) ou à partir d’une installation PowerShell locale. 

Si vous utilisez PowerShell en local, ce script requiert la dernière version du module AzureRM PowerShell. Pour trouver la version installée, exécutez `Get-Module -ListAvailable AzureRM`. 

Si vous devez procéder à une mise à niveau, vous pouvez utiliser `PowerShellGet`, qui est intégré à Windows 10 et Windows Server 2016.

> [!NOTE]
>Les autres versions de Windows vous obligent à installer `PowerShellGet` avant de pouvoir les utiliser. Vous pouvez exécuter `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path` pour déterminer s’il est installé sur votre système. Si la sortie est vide, vous devez installer la dernière version de [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616).

Pour plus d’informations, consultez [Installer Azure PowerShell sur Windows avec PowerShellGet](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0).

Toute installation d’Azure PowerShell existante effectuée avec l’outil Web Platform Installer sera en conflit avec l’installation de PowerShellGet et devra être supprimée.

Rappelez-vous que si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzureRmAccount` pour créer une connexion avec Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées :

```powershell
Remove-AzureRmResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, un réseau virtuel et les groupes de sécurité réseau. Chaque commande du tableau suivant renvoie à une documentation spécifique :

| Commande | Notes |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Crée un objet de configuration de sous-réseau. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Crée un réseau virtuel et un sous-réseau frontal Azure. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Crée des règles de sécurité à attribuer à un groupe de sécurité réseau. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) |Crée des règles NSG qui autorisent ou bloquent des ports spécifiques sur des sous-réseaux donnés. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Associe les groupes de sécurité réseau à des sous-réseaux. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Crée une adresse IP publique pour accéder à la machine virtuelle à partir d’Internet. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Crée des interfaces réseau virtuelles et les attache aux sous-réseaux frontaux et principaux du réseau virtuel. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Crée une configuration de machine virtuelle. Cette configuration inclut des informations telles que le nom de la machine virtuelle, le système d’exploitation et les informations d’identification d’administration. La configuration est utilisée lors de la création de machines virtuelles. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Création d’une machine virtuelle |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Supprime un groupe de ressources et toutes les ressources contenues. |
|[New-AzureRmFirewall](https://github.com/Azure/azure-powershell/blob/Networking-AzureFirewall/src/ResourceManager/Network/Commands.Network/help/New-AzureRmFirewall.md)| Crée un nouveau pare-feu Azure.|
|[Get-AzureRmFirewall](https://github.com/Azure/azure-powershell/blob/Networking-AzureFirewall/src/ResourceManager/Network/Commands.Network/help/Get-AzureRmFirewall.md)|Obtient un objet de pare-feu Azure.|
|[New-AzureRmFirewallApplicationRule](https://github.com/Azure/azure-powershell/blob/Networking-AzureFirewall/src/ResourceManager/Network/Commands.Network/help/New-AzureRmFirewallApplicationRule.md)|Crée une nouvelle règle d’application de pare-feu Azure.|
|[Set-AzureRmFirewall](https://github.com/Azure/azure-powershell/blob/Networking-AzureFirewall/src/ResourceManager/Network/Commands.Network/help/Set-AzureRmFirewall.md)|Valide les modifications apportées à l’objet de pare-feu Azure.|


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/overview).

