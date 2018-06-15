---
title: Exemple de script Azure PowerShell - Homologuer deux réseaux virtuels | Microsoft Docs
description: Exemple de script Azure PowerShell - Homologuer deux réseaux virtuels
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 4fe4777b3adf585b2b6ddc862023905e756404d9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2018
ms.locfileid: "31599976"
---
# <a name="peer-two-virtual-networks-script-sample"></a>Exemple de script : Homologuer deux réseaux virtuels

Cet exemple de script crée et connecte deux réseaux virtuels situés dans la même région via le réseau Azure. Après l’exécution du script, vous créerez une homologation entre deux réseaux virtuels.

Vous pouvez exécuter le script à partir d’Azure [Cloud Shell](https://shell.azure.com/powershell) ou à partir d’une installation PowerShell locale. Si vous utilisez PowerShell en local, vous devez exécuter le module AzureRM PowerShell version 5.4.1 ou version ultérieure pour les besoins de ce script. Pour trouver la version installée, exécutez `Get-Module -ListAvailable AzureRM`. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzureRmAccount` pour créer une connexion avec Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.ps1 "Peer two networks")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées :

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une machine virtuelle et toutes les ressources associées. Chaque commande du tableau suivant renvoie à une documentation spécifique :

| Commande | Notes |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. | 
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)| Crée un réseau virtuel et un sous-réseau Azure. |
| [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering) | Crée une homologation entre deux réseaux virtuels.  |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell pour réseau virtuel dans [Exemples PowerShell pour réseau virtuel](../powershell-samples.md).
