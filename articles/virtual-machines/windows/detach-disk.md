---
title: Détacher un disque de données d’une machine virtuelle Windows - Azure| Microsoft Docs
description: Détachez un disque de données d’une machine virtuelle dans Azure à l’aide du modèle de déploiement Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 750bb275ef936b3911503cd4c0f50674d3dff2d1
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981143"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Détachement d’un disque de données d’une machine virtuelle Windows

Lorsque vous n’avez plus besoin d’un disque de données qui est attaché à une machine virtuelle, vous pouvez le détacher facilement. Cela supprime le disque de la machine virtuelle, mais pas du stockage.

> [!WARNING]
> Si vous détachez un disque, il n’est pas supprimé automatiquement. Si vous êtes abonné au stockage Premium, vous continuerez à engager des frais de stockage pour le disque. Pour plus d’informations, consultez [Tarifs et facturation du stockage Premium](premium-storage.md#pricing-and-billing).
>
>

Si vous souhaitez réutiliser les données du disque, vous pouvez l’attacher à la même machine virtuelle ou à une autre.


[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="detach-a-data-disk-using-powershell"></a>Détacher un disque de données avec PowerShell

Vous pouvez supprimer *à chaud* un disque de données à l’aide de PowerShell, mais vérifiez qu’il n’est pas activement utilisé avant de le détacher de la machine virtuelle.

Dans cet exemple, nous supprimons le disque nommé **myDisk** de la machine virtuelle **myVM** dans le groupe de ressources **myResourceGroup**. Vous commencez par supprimer le disque à l'aide de la cmdlet [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk). Puis vous mettez à jour l'état de la machine virtuelle à l'aide de la cmdlet [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) pour finaliser le processus de suppression du disque de données.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

Le disque reste dans le stockage, mais il n’est plus attaché à une machine virtuelle.


## <a name="detach-a-data-disk-using-the-portal"></a>Détacher un disque de données avec le portail

1. Dans le menu de gauche, sélectionnez **Machines virtuelles**.
2. Sélectionnez la machine virtuelle qui possède le disque de données que vous souhaitez détacher, puis cliquez sur **Arrêter** pour libérer la machine virtuelle.
3. Dans le volet de la machine virtuelle, sélectionnez **Disques**.
4. En haut du volet **Disques**, sélectionnez **Modifier**.
5. Dans le volet **Disques**, à l’extrême droite du disque de données que vous souhaitez détacher, cliquez sur le bouton de détachement ![image du bouton détacher](./media/detach-disk/detach.png).
5. Une fois que le disque a été supprimé, cliquez sur **Enregistrer** en haut du volet.
6. Dans le volet de la machine virtuelle, cliquez sur **Présentation**, puis cliquez sur le bouton **Démarrer** en haut du volet pour redémarrer la machine virtuelle.

Le disque reste dans le stockage, mais il n’est plus attaché à une machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes
Si vous souhaitez réutiliser le disque de données, vous pouvez simplement [l’attacher à une autre machine virtuelle](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

