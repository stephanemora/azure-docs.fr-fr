---
title: Déplacer une ressource de machine virtuelle Windows dans Azure
description: Déplacement d’une machine virtuelle Windows vers un autre abonnement ou groupe de ressources Azure dans le modèle de déploiement Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: ed29c92d20a6b0d749ec44a22f42ec446ec58650
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919564"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Déplacement d’une machine virtuelle Windows vers un autre abonnement ou groupe de ressources Azure
Cet article vous explique comment déplacer une machine virtuelle Windows entre des groupes de ressources ou des abonnements. Le déplacement entre abonnements peut être pratique si, à l’origine, vous avez créé une machine virtuelle dans un abonnement personnel, et que vous souhaitez à présent la déplacer vers l’abonnement de votre entreprise afin de poursuivre votre travail. Vous n’avez pas besoin de démarrer la machine virtuelle afin de la déplacer, et elle doit continuer de fonctionner pendant le déplacement.

> [!IMPORTANT]
>De nouveaux ID de ressource sont créés dans le cadre du déplacement. Une fois que la machine virtuelle a été déplacée, vous devez mettre à jour vos outils et vos scripts pour utiliser les nouveaux ID de ressource.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Utilisation de PowerShell pour déplacer une machine virtuelle

Pour déplacer une machine virtuelle vers un autre groupe de ressources, vous devez vous assurer que vous déplacez également toutes les ressources dépendantes. Pour obtenir la liste des ID de toutes ces ressources, utilisez la cmdlet [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource).

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

Vous pouvez utiliser la sortie de la commande précédente pour créer une liste d'ID de ressource séparés par des virgules et la transmettre à [Move-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) afin de déplacer chaque ressource vers la destination.

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

Pour déplacer les ressources vers un autre abonnement, incluez le paramètre **DestinationSubscriptionId** .

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Lorsque vous devrez confirmer que vous souhaitez déplacer les ressources spécifiées, saisissez **Y** pour confirmer.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez déplacer de nombreux types de ressources différents entre les groupes de ressources et les abonnements. Pour plus d’informations, consultez la page [Déplacer des ressources vers un nouveau groupe de ressources ou un abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
