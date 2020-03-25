---
title: Exemples Azure PowerShell - Attacher et utiliser des disques de données
description: Ce script permet de créer un groupe de machines virtuelles identiques Azure, puis d’attacher et de préparer les disques de données avec PowerShell.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: sample
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: ea18b6d2271d56d9a2287b92678ef8bfcab891a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76279127"
---
# <a name="attach-and-use-data-disks-with-a-virtual-machine-scale-set-with-powershell"></a>Attacher et utiliser des disques de données avec un groupe de machines virtuelles identiques avec PowerShell
Ce script permet de créer un groupe de machines virtuelles identiques, puis d’attacher et préparer les disques de données.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/use-data-disks/use-data-disks.ps1 "Create a virtual machine scale set with data disks")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Exécutez la commande suivante pour supprimer le groupe de ressources, le groupe identique et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script
Ce script a recours aux commandes suivantes pour créer le déploiement. Chaque élément du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | Crée le groupe de machines virtuelles identiques et toutes les ressources de support, y compris le réseau virtuel, l’équilibreur de charge et les règles NAT. |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Obtient des informations relatives à un groupe de machines virtuelles identiques. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | Ajoute une extension de machine virtuelle pour le script personnalisé afin d’installer une application web de base. |
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss) | Met à jour le modèle de groupe de machines virtuelles identiques pour appliquer l’extension de machine virtuelle. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources et toutes les ressources contenues. |

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell de groupes de machines virtuelles identiques dans la [documentation relative aux groupes de machines virtuelles identiques Azure](../powershell-samples.md).
