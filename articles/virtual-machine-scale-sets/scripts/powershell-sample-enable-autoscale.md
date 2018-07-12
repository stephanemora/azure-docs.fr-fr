---
title: Exemples Azure PowerShell - Activer la mise à l’échelle basée sur l’hôte | Microsoft Docs
description: Exemples Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: ca484a6f1b2dafa3688efe0e50bb2c35f954a8b6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38697413"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-powershell"></a>Mettre à l’échelle automatiquement un groupe de machines virtuelles identiques avec PowerShell
Ce script crée un groupe de machines virtuelles identiques exécutant Windows Server 2016 et utilise des métriques basées sur l’hôte pour mettre à l’échelle automatiquement tandis que la charge du processeur change.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/auto-scale-host-metrics/auto-scale-host-metrics.ps1 "Automatically scale a virtual machine scale set")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Exécutez la commande suivante pour supprimer le groupe de ressources, le groupe identique et toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script
Ce script a recours aux commandes suivantes pour créer le déploiement. Chaque élément du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Crée le groupe de machines virtuelles identiques et toutes les ressources de support, y compris le réseau virtuel, l’équilibreur de charge et les règles NAT. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Obtient des informations relatives à un groupe de machines virtuelles identiques. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Ajoute une extension de machine virtuelle pour le script personnalisé afin d’installer une application web de base. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Met à jour le modèle de groupe de machines virtuelles identiques pour appliquer l’extension de machine virtuelle. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Obtient des informations sur l’adresse IP publique assignée qui est utilisée par l’équilibreur de charge. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Supprime un groupe de ressources et toutes les ressources contenues. |

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell de groupes de machines virtuelles identiques dans la [documentation relative aux groupes de machines virtuelles identiques Azure](../powershell-samples.md).