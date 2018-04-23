---
title: 'Exemples PowerShell Azure : groupe identique redondant dans une zone | Microsoft Docs'
description: Exemples Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 73f6f1c5a61fd7d60666df2bff99ee67a41c9cb8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-zone-redundant-virtual-machine-scale-set-with-powershell"></a>Créer un groupe de machines virtuelles identiques redondant dans une zone à l’aide de PowerShell
Ce script crée un groupe de machines virtuelles identiques exécutant Windows Server 2016 sur plusieurs zones de disponibilité. Une fois que vous avez exécuté le script, vous pouvez accéder à la machine virtuelle via RDP.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1 "Create zone-redundant scale set")]

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