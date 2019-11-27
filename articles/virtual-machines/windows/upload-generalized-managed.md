---
title: Créer une machine virtuelle Azure gérée à partir d’un disque dur virtuel local généralisé
description: Chargez un disque dur virtuel généralisé sur Azure et utilisez-le pour créer des machines virtuelles dans le modèle de déploiement du Gestionnaire de ressources.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: d0995fed61d169cc173ca01767c2e48f4f798b0d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74067436"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Charger un disque dur virtuel généralisé et l’utiliser pour créer des machines virtuelles dans Azure

Cet article vous guide dans l’utilisation de PowerShell pour charger un disque dur virtuel d’une machine virtuelle généralisée sur Azure, créer une image à partir du disque dur virtuel et créer une machine virtuelle à partir de cette image. Vous pouvez charger un disque dur virtuel exporté à partir d’un outil de virtualisation local ou d’un autre cloud. L’utilisation de la fonctionnalité [Disques managés](managed-disks-overview.md) pour la nouvelle machine virtuelle simplifie la gestion de celle-ci et fournit une meilleure disponibilité lorsque la machine virtuelle est placée dans un groupe à haute disponibilité. 

Pour un exemple de script, consultez [Exemple de script pour charger un disque dur virtuel sur Azure et créer une machine virtuelle](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Avant de commencer

- Avant de télécharger un disque dur virtuel dans Azure, vous devez apprendre à [préparer un disque dur virtuel Windows à charger sur Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Consultez [Planification de la migration vers la fonctionnalité Disques managés](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) avant de démarrer la migration vers la [fonctionnalité Disques managés](managed-disks-overview.md).

 


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Généraliser la machine virtuelle source en utilisant Sysprep

Sysprep supprime toutes les informations personnelles de votre compte, entre autres, et prépare la machine de façon à pouvoir l’utiliser comme image. Pour plus d’informations sur Sysprep, consultez la [Vue d’ensemble de Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Vérifiez que les rôles serveur exécutés sur la machine sont pris en charge par Sysprep. Pour plus d’informations, consultez [Prise en charge de Sysprep pour les rôles serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Si vous prévoyez d’exécuter Sysprep avant de charger votre disque dur virtuel sur Azure pour la première fois, veillez à [préparer votre machine virtuelle](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Connectez-vous à la machine virtuelle Windows
2. Ouvrez la fenêtre d’invite de commandes en tant qu’administrateur. Remplacez le répertoire par %windir%\system32\sysprep, puis exécutez `sysprep.exe`.
3. Dans la boîte de dialogue **Outil de préparation du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** et vérifiez que la case **Généraliser** est cochée.
4. Dans **Options d’arrêt**, sélectionnez **Arrêter**.
5. Sélectionnez **OK**.
   
    ![Démarrer Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Une fois l’opération Sysprep terminée, elle arrête la machine virtuelle. Ne redémarrez pas la machine virtuelle.


## <a name="upload-the-vhd-to-your-storage-account"></a>Téléchargement du disque dur virtuel vers votre compte de stockage

Vous pouvez désormais charger un disque dur virtuel directement dans un disque managé. Pour plus d’instructions, consultez [Charger un disque dur virtuel dans Azure à l'aide d'Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).


## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Créer une image gérée à partir du disque dur virtuel téléchargé 

Créez une image managée à partir de votre disque managé de système d’exploitation généralisé. Remplacez les valeurs suivantes par vos propres informations.


Commencez par définir des paramètres :

```powershell
$location = "East US" 
$imageName = "myImage"
```

Créez l’image à l’aide de votre disque dur virtuel généralisé de système d’exploitation.

```powershell
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzImage `
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
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Étapes suivantes

Connectez-vous à votre nouvelle machine virtuelle. Pour plus d’informations, consultez [Connexion à une machine virtuelle Azure exécutant Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

