---
title: Charger un disque dur virtuel dans Azure à l'aide d'Azure PowerShell
description: Apprenez à charger un disque dur virtuel sur un disque managé Azure et à copier un disque managé d’une région vers une autre à l’aide d’Azure PowerShell, via chargement direct.
author: roygara
ms.author: rogarana
ms.date: 05/06/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 8a7e5243428eb88a2757b675c7d66dbfb3c66a30
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459983"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Charger un disque dur virtuel dans Azure à l'aide d'Azure PowerShell

Cet article explique comment charger un disque dur virtuel sur un disque managé Azure à partir de votre ordinateur local. Jusqu'à présent, vous deviez suivre un processus complexe qui exigeait la mise en lots de vos données sur un compte de stockage et la gestion de ce compte de stockage. Désormais, pour charger un disque dur virtuel, vous n'avez plus aucun compte de stockage à gérer ni aucune donnée à indexer sur celui-ci. Il vous suffit de créer un disque managé vierge et d'y chargez directement le disque dur virtuel. Cela simplifie le chargement des machines virtuelles locales dans Azure et vous permet de charger un disque dur virtuel d'un maximum de 32 Tio directement sur un disque managé volumineux.

Si vous fournissez une solution de sauvegarde pour les machines virtuelles IaaS dans Azure, nous vous recommandons d'utiliser le chargement direct afin de restaurer les sauvegardes client sur des disques managés. Si vous chargez un disque dur virtuel dans Azure à partir d'une machine externe, les vitesses dépendent de votre bande passante locale. Si vous utilisez une machine virtuelle Azure, votre bande passante sera identique à celle des disques HDD Standard.

Actuellement, le chargement direct est pris en charge pour les disques managés de type HDD Standard, SSD Standard et SSD Premium. Il n'est pas encore pris en charge pour les disques SSD Ultra.

## <a name="prerequisites"></a>Conditions préalables requises

- Téléchargez la dernière [version d’AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installez le module Azure PowerShell](/powershell/azure/install-Az-ps).
- Si vous envisagez de charger un VHD à partir d’un emplacement local : Un VHD [préparé pour Azure](prepare-for-upload-vhd-image.md) et stocké localement.
- Ou un disque managé dans Azure, si vous envisagez d’effectuer une action de copie.

## <a name="create-an-empty-managed-disk"></a>Créer un disque managé vierge

Pour charger votre disque dur virtuel dans Azure, vous devez créer un disque managé vierge configuré pour ce processus de chargement. Avant de créer ce disque, vous devez prendre connaissance des informations ci-dessous.

Ce type de disque managé présente deux états uniques :

- ReadToUpload, qui signifie que le disque est prêt à recevoir un chargement, mais qu'aucune [signature d'accès partagé](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) n'a été générée.
- ActiveUpload, qui signifie que le disque est prêt à recevoir un chargement et que la SAS a été générée.

Dans l'un ou l'autre de ces états, le disque managé est facturé au [tarif HDD Standard](https://azure.microsoft.com/pricing/details/managed-disks/), quel que soit le type de disque. Par exemple, un P10 est facturé comme un S10. Ceci s’applique jusqu’à ce que `revoke-access` soit appelé sur le disque managé, ce qui est nécessaire pour joindre le disque à une machine virtuelle.

Avant de créer un disque HDD standard vierge pour le chargement, vous devez vous procurer la taille de fichier en octets du disque dur virtuel que vous souhaitez charger. L'exemple de code vous la fournira, mais si vous souhaitez effectuer cette opération vous-même, vous pouvez utiliser : `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`. Cette valeur est utilisée lors de la spécification du paramètre **-UploadSizeInBytes**.

Ensuite, à partir de votre interpréteur de commandes local, créez un disque HDD Standard vierge pour le chargement en spécifiant le paramètre **Upload** dans **-CreateOption** ainsi que le paramètre **-UploadSizeInBytes** dans la cmdlet [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0). Puis appelez [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) pour créer le disque :

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Si vous souhaitez charger un disque SSD Premium ou Standard, remplacez **Standard_LRS** par **Premium_LRS** ou par **StandardSSD_LRS**. Le type SSD Ultra n’est pas encore pris en charge.

Vous venez de créer un disque managé vierge configuré pour le processus de chargement. Pour charger un disque dur virtuel sur le disque, il vous faut une SAS accessible en écriture afin de pouvoir la référencer en tant que destination pour votre chargement.

Pour générer une SAS accessible en écriture de votre disque managé vierge, utilisez la commande suivante :

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>Charger un disque dur virtuel

Maintenant que vous disposez d'une SAS pour votre disque managé vierge, vous pouvez l'utiliser pour définir votre disque managé en tant que destination de votre commande de chargement.

Utilisez AzCopy v10 pour charger votre fichier de disque dur virtuel local sur un disque managé en spécifiant l'URI de la SAS que vous avez générée.

Ce chargement présente le même débit que le disque [HDD Standard](disks-types.md#standard-hdd) correspondant. Par exemple, pour une taille correspondant à S4, vous aurez un débit allant jusqu'à 60 Mio/s. Mais pour une taille correspondant à S70, le débit ira jusqu’à 500 Mio/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Si votre SAS expire pendant le chargement et que vous n'avez pas encore appelé `revoke-access`, vous pouvez obtenir une nouvelle SAS pour poursuivre le chargement en utilisant de nouveau `grant-access`.

Lorsque vous avez terminé le chargement et que vous n’avez plus rien à écrire sur le disque, révoquez la SAS. La révocation de la SAS changera l'état du disque managé et vous permettra de le joindre à une machine virtuelle.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>Copier un disque managé

Le chargement direct simplifie également le processus de copie d’un disque managé. Vous pouvez effectuer une copie au sein d’une même région ou entre des régions différentes (vers une autre région).

Le script suivant effectuera cette opération pour vous. Le processus est similaire aux étapes décrites précédemment, à quelques différences près puisque vous utilisez un disque existant.

> [!IMPORTANT]
> Vous devez ajouter un décalage de 512 quand vous fournissez la taille en octets d’un disque managé d’Azure. En effet, Azure omet le pied de page lors du retour de la taille du disque. Si vous ne le faites pas, la copie échouera. Le script suivant s’en charge pour vous.

Remplacez `<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>`, `<yourOSTypeHere>` et `<yourTargetLocationHere>` (la valeur d'emplacement pourrait par exemple être uswest2) par vos valeurs, puis exécutez le script suivant afin de copier un disque managé.

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez chargé un disque dur virtuel sur un disque managé, vous pouvez joindre votre disque à une machine virtuelle et commencer à l'utiliser.

Pour découvrir comment joindre un disque de données à une machine virtuelle, consultez l’article suivant : [Joindre un disque de données à une machine virtuelle Windows à l'aide de PowerShell](attach-disk-ps.md). Pour utiliser le disque comme disque de système d’exploitation, consultez [Créer une machine virtuelle Windows à partir d’un disque spécialisé](create-vm-specialized.md#create-the-new-vm).
