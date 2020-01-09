---
title: 'Tutoriel : Créer des images de machine virtuelle personnalisées avec Azure PowerShell'
description: Dans ce didacticiel, vous allez apprendre à utiliser Azure PowerShell pour créer une image de machine virtuelle personnalisée dans Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: aca1f8ce2c125cd90f2e61a7829be09bc81c3938
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464966"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Tutoriel : Créer une image personnalisée d’une machine virtuelle Azure avec Azure PowerShell

Les images personnalisées sont comme des images de la Place de marché, sauf que vous les créez vous-même. Vous pouvez les utiliser pour démarrer des déploiements et en garantir la cohérence sur plusieurs machines virtuelles. Ce tutoriel explique comment créer votre propre image personnalisée d’une machine virtuelle Azure avec PowerShell. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Exécuter Sysprep et généraliser les machines virtuelles
> * Créer une image personnalisée
> * Créer une machine virtuelle à partir d’une image personnalisée
> * Répertorier toutes les images dans votre abonnement
> * Supprimer une image

Dans la préversion publique, nous disposons du service [Azure VM Image Builder](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview). Il vous suffit de décrire vos personnalisations dans un modèle, et celui-ci gère la création d’images selon les étapes de cet article. [Essayez Azure Image Builder (préversion)](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder).

## <a name="before-you-begin"></a>Avant de commencer

Les étapes ci-dessous expliquent comment prendre une machine virtuelle existante et la transformer en une image personnalisée réutilisable que vous pouvez utiliser pour créer de nouvelles instances de machines virtuelles.

Pour exécuter l’exemple dans ce didacticiel, vous devez disposer d’une machine virtuelle. Si nécessaire, cet [exemple de script](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) peut en créer une pour vous. Au cours du didacticiel, remplacez les noms du groupe de ressources et de la machine virtuelle si nécessaire.

## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. 

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez également lancer Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur Entrée pour les exécuter.

## <a name="prepare-vm"></a>Préparer la machine virtuelle

Pour créer une image de machine virtuelle, vous devez préparer la machine virtuelle source en la généralisant, en la libérant, puis en la marquant comme généralisée avec Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Généraliser la machine virtuelle Windows à l’aide de Sysprep

Sysprep supprime toutes les informations personnelles de votre compte, entre autres, et prépare la machine de façon à pouvoir l’utiliser comme image. Pour plus d’informations sur Sysprep, consultez [Utilisation de Sysprep : de Sysprep](https://technet.microsoft.com/library/bb457073.aspx).


1. Connectez-vous à la machine virtuelle.
2. Ouvrez la fenêtre d’invite de commandes en tant qu’administrateur. Remplacez le répertoire par *%windir%\system32\sysprep*, puis exécutez `sysprep.exe`.
3. Dans la boîte de dialogue **Outil de préparation du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** et vérifiez que la case **Généraliser** est cochée.
4. Dans **Options d’arrêt**, sélectionnez **Arrêter**, puis cliquez sur **OK**.
5. Une fois l’opération Sysprep terminée, elle arrête la machine virtuelle. **Ne redémarrez pas la machine virtuelle**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Libérer la machine virtuelle et la marquer comme généralisée

Pour créer une image, la machine virtuelle doit être libérée et marquée comme généralisée dans Azure.

Libérez la machine virtuelle à l’aide de [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Force
```

Définissez l’état de la machine virtuelle sur `-Generalized` à l’aide de [Set-AzVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). 
   
```azurepowershell-interactive
Set-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Generalized
```


## <a name="create-the-image"></a>Création de l’image

Créez à présent une image de la machine virtuelle à l’aide de [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig) et [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage). L’exemple suivant crée une image nommée *myimage* à partir d’une machine virtuelle nommée *myimage*.

Accédez à la machine virtuelle. 

```azurepowershell-interactive
$vm = Get-AzVM `
   -Name myVM `
   -ResourceGroupName myResourceGroup
```

Créez la configuration de l’image.

```azurepowershell-interactive
$image = New-AzImageConfig `
   -Location EastUS `
   -SourceVirtualMachineId $vm.ID 
```

Créez l’image.

```azurepowershell-interactive
New-AzImage `
   -Image $image `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Créer des machines virtuelles à partir de l’image

Maintenant que vous avez une image, vous pouvez créer une ou plusieurs nouvelles machines virtuelles à partir de l’image. La création d’une machine virtuelle à partir d’une image personnalisée est similaire à la création d’une machine virtuelle à l’aide d’une image de la Place de marché. Lorsque vous utilisez une image de la Place de marché, vous devez fournir les informations sur l’image, le fournisseur de l’image, l’offre, la référence et la version. Avec le jeu de paramètres simplifié de la cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm), il suffit de fournir le nom de l’image personnalisée, tant qu’elle se trouve dans le même groupe de ressources. 

Cet exemple crée une machine virtuelle nommée *myVMfromImage* à partir de l’image *myImage*, dans le groupe de ressources *myResourceGroup*.


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```

Nous vous recommandons de limiter le nombre de déploiements simultanés à 20 machines virtuelles provenant d’une seule image. Si vous planifiez des déploiements simultanés à grande échelle de plus de 20 machines virtuelles à partir de la même image personnalisée, utilisez une galerie [Shared Image Gallery](shared-image-galleries.md) avec plusieurs réplicas d’images. 


## <a name="image-management"></a>Gestion d’image 

Voici quelques exemples de tâches d’images managées courantes et comment les exécuter à l’aide de PowerShell.

Répertoriez toutes les images par nom.

```azurepowershell-interactive
$images = Get-AzResource -ResourceType Microsoft.Compute/images 
$images.name
```

Supprimez une image. Cet exemple supprime l’image nommée *myImage* du groupe *myResourceGroup*.

```azurepowershell-interactive
Remove-AzImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous montré comment créer une image de machine virtuelle. Vous avez appris à :

> [!div class="checklist"]
> * Exécuter Sysprep et généraliser les machines virtuelles
> * Créer une image personnalisée
> * Créer une machine virtuelle à partir d’une image personnalisée
> * Répertorier toutes les images dans votre abonnement
> * Supprimer une image

Passez au tutoriel suivant pour en savoir plus sur la création de machines virtuelles hautement disponibles.

> [!div class="nextstepaction"]
> [Créer des machines virtuelles hautement disponibles](tutorial-availability-sets.md)



