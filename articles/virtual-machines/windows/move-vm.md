---
title: Déplacer une ressource de machine virtuelle Windows dans Azure | Microsoft Docs
description: Déplacement d’une machine virtuelle Windows vers un autre abonnement ou groupe de ressources Azure dans le modèle de déploiement Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: 1daf04e3f878d0748bfa0904259c7b7187481843
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580480"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Déplacement d’une machine virtuelle Windows vers un autre abonnement ou groupe de ressources Azure
Cet article vous explique comment déplacer une machine virtuelle Windows entre des groupes de ressources ou des abonnements. Le déplacement entre abonnements peut être pratique si, à l’origine, vous avez créé une machine virtuelle dans un abonnement personnel, et que vous souhaitez à présent la déplacer vers l’abonnement de votre entreprise afin de poursuivre votre travail.

> [!IMPORTANT]
>Vous ne pouvez pas déplacer des disques managés Azure pour l’instant. 
>
>De nouveaux ID de ressource sont créés dans le cadre du déplacement. Une fois que la machine virtuelle a été déplacée, vous devez mettre à jour vos outils et vos scripts pour utiliser les nouveaux ID de ressource. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Utilisation de PowerShell pour déplacer une machine virtuelle

Pour déplacer une machine virtuelle vers un autre groupe de ressources, vous devez vous assurer que vous déplacez également toutes les ressources dépendantes. Pour obtenir une liste avec l’ID de ressource de chaque ressource, utilisez la cmdlet [Get-AzureRMResource](/powershell/module/azurerm.resources/get-azurermresource).

```azurepowershell-interactive
 Get-AzureRMResource -ResourceGroupName <sourceResourceGroupName> | Format-table -Property ResourceId 
```

Vous pouvez utiliser la sortie de la commande précédente pour créer une liste séparée par des virgules des ID de ressource et la transmettre à [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) pour les déplacer vers la destination. 

```azurepowershell-interactive
Move-AzureRmResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Pour déplacer les ressources vers un autre abonnement, incluez le paramètre **DestinationSubscriptionId** . 

```azurepowershell-interactive
Move-AzureRmResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Lorsque vous devrez confirmer que vous souhaitez déplacer les ressources spécifiées, saisissez **Y** pour confirmer.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez déplacer de nombreux types de ressources différents entre les groupes de ressources et les abonnements. Pour plus d’informations, consultez la page [Déplacer des ressources vers un nouveau groupe de ressources ou un abonnement](../../resource-group-move-resources.md).    

