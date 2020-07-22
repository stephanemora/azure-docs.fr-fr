---
title: Exemple de script Azure PowerShell - IIS avec DSC
description: Exemple de script Azure PowerShell - IIS avec DSC
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.openlocfilehash: 52ce81cb69470adf000465b97e976e5e43514da2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86501133"
---
# <a name="create-an-iis-vm-with-powershell"></a>Créer une machine virtuelle IIS avec PowerShell

Ce script crée une machine virtuelle Azure exécutant Windows Server 2016, puis utilise l’extension DSC de la machine virtuelle Azure pour installer et configurer IIS. Une fois que vous avez exécuté le script, vous pouvez accéder au site IIS par défaut sur l’adresse IP publique de la machine virtuelle.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-dsc/create-windows-vm-iis-dsc.ps1 "Create VM IIS DSC")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer le déploiement. Chaque élément du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Crée la machine virtuelle et la connecte à la carte réseau, au réseau virtuel, au sous-réseau et au groupe de sécurité réseau. Cette commande ouvre également le port 80 et définit les informations d’identification administratives. |
| [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) | Ajoutez une extension de machine virtuelle à la machine virtuelle. Dans cet exemple, l’extension DSC sert à installer IIS. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources et toutes les ressources contenues. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell de machine virtuelle dans la [documentation relative aux machines virtuelles Windows Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
