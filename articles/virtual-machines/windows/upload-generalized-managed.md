---
title: Créer une machine virtuelle à partir d’un disque dur virtuel généralisé chargé
description: Chargez un disque dur virtuel généralisé sur Azure et utilisez-le pour créer des machines virtuelles dans le modèle de déploiement du Gestionnaire de ressources.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: 98e94952481ebfabffb39ba46247717bca4de447
ms.sourcegitcommit: ce9178647b9668bd7e7a6b8d3aeffa827f854151
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109809235"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Charger un disque dur virtuel généralisé et l’utiliser pour créer des machines virtuelles dans Azure

Cet article vous guide dans l’utilisation de PowerShell pour charger un disque dur virtuel d’une machine virtuelle généralisée sur Azure, créer une image à partir du disque dur virtuel et créer une machine virtuelle à partir de cette image. Vous pouvez charger un disque dur virtuel exporté à partir d’un outil de virtualisation local ou d’un autre cloud. L’utilisation de la fonctionnalité [Disques managés](../managed-disks-overview.md) pour la nouvelle machine virtuelle simplifie la gestion de celle-ci et fournit une meilleure disponibilité lorsque la machine virtuelle est placée dans un groupe à haute disponibilité. 

Pour un exemple de script, consultez [Exemple de script pour charger un disque dur virtuel sur Azure et créer une machine virtuelle](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script).

## <a name="before-you-begin"></a>Avant de commencer

- Avant de télécharger un disque dur virtuel dans Azure, vous devez apprendre à [préparer un disque dur virtuel Windows à charger sur Azure](prepare-for-upload-vhd-image.md).
- Consultez [Planification de la migration vers la fonctionnalité Disques managés](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) avant de démarrer la migration vers la [fonctionnalité Disques managés](../managed-disks-overview.md).

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>Généraliser la machine virtuelle source en utilisant Sysprep

Si vous ne l’avez pas déjà fait, vous devez exécuter Sysprep sur la machine virtuelle avant de charger le disque dur virtuel sur Azure. Sysprep supprime toutes les informations personnelles de votre compte, entre autres, et prépare la machine de façon à pouvoir l’utiliser comme image. Pour plus d’informations sur Sysprep, consultez la [Vue d’ensemble de Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Vérifiez que les rôles serveur exécutés sur la machine sont pris en charge par Sysprep. Pour plus d’informations, consultez [Prise en charge de Sysprep pour les rôles serveur](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Si vous prévoyez d’exécuter Sysprep avant de charger votre disque dur virtuel sur Azure pour la première fois, veillez à [préparer votre machine virtuelle](prepare-for-upload-vhd-image.md). 
> 
> 

1. Connectez-vous à la machine virtuelle Windows
1. Ouvrez la fenêtre d’invite de commandes en tant qu’administrateur. 
1. Supprimez le répertoire Panther (C:\Windows\Panther).
1. Remplacez le répertoire par %windir%\system32\sysprep, puis exécutez `sysprep.exe`.
1. Dans la boîte de dialogue **Outil de préparation du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** et vérifiez que la case **Généraliser** est cochée.
1. Dans **Options d’arrêt**, sélectionnez **Arrêter**.
1. Sélectionnez **OK**.
   
    ![Démarrer Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
1. Une fois l’opération Sysprep terminée, elle arrête la machine virtuelle. Ne redémarrez pas la machine virtuelle.


## <a name="upload-the-vhd"></a>Charger le disque dur virtuel 

Vous pouvez désormais charger un disque dur virtuel directement dans un disque managé. Pour plus d’instructions, consultez [Charger un disque dur virtuel dans Azure à l'aide d'Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).



Une fois que le disque dur virtuel est chargé sur le disque managé, vous devez utiliser [Get-AzDisk](/powershell/module/az.compute/get-azdisk) pour récupérer le disque managé.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>Création de l’image
Créez une image managée à partir de votre disque managé de système d’exploitation généralisé. Remplacez les valeurs suivantes par vos propres informations.

Pour commencer, définissez des variables :

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

Créez l’image à l’aide de votre disque managé.

```azurepowershell-interactive
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsState Generalized `
   -OsType Windows `
   -ManagedDiskId $disk.Id
```

Créez l’image.

```azurepowershell-interactive
$image = New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```

## <a name="create-the-vm"></a>Création de la machine virtuelle

Maintenant que vous avez une image, vous pouvez créer une ou plusieurs nouvelles machines virtuelles à partir de l’image. Cet exemple crée une machine virtuelle nommée *myVM* à partir de *myImage* dans le groupe de ressources *myResourceGroup*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Image $image.Id `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Étapes suivantes

Connectez-vous à votre nouvelle machine virtuelle. Pour plus d’informations, consultez [Connexion à une machine virtuelle Azure exécutant Windows](connect-logon.md).
