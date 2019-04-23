---
title: Convertir Azure stockage managed disks standard vers Premium ou Premium à Standard | Microsoft Docs
description: Comment convertir Azure stockage managed disks standard vers Premium ou Premium à Standard à l’aide de l’interface CLI.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 18730f662f36000e1efc826c35bebde79dbf0e79
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013617"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Convertir Azure stockage managed disks standard vers Premium ou Premium à Standard

Il existe quatre [types de disques](disks-types.md) pour Azure des disques gérés : Stockage sur disque Ultra Azure, Premium SSD, disque SSD Standard et Standard HDD. Vous pouvez facilement basculer entre Premium SSD, disque SSD Standard et disques durs Standard en fonction de vos besoins en performances avec peu de temps mort. Cette fonctionnalité n’est pas pris en charge pour les disques non gérés ou Ultra stockage sur disque. Toutefois, vous pouvez facilement [convert non gérés vers des disques gérés](convert-unmanaged-to-managed-disks.md) pour être en mesure de basculer entre les types de disque.

Cet article explique comment convertir des disques gérés Standard vers Premium ou Premium à Standard à l’aide de l’interface CLI. Pour installer ou mettre à niveau de l’outil, consultez [installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Avant de commencer

* Conversion de disque nécessite un redémarrage de la machine virtuelle (VM), par conséquent, planifiez la migration de votre stockage sur disque pendant une fenêtre de maintenance préexistante.
* Pour les disques non gérés, première [convertir en disques gérés](convert-unmanaged-to-managed-disks.md) afin que vous pouvez basculer entre les options de stockage.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Basculer l’ensemble des disques gérés d’une machine virtuelle entre Standard et Premium

Cet exemple montre comment convertir tous les disques d’une machine virtuelle à partir de Standard vers le stockage Premium ou de Premium vers le stockage Standard. Pour utiliser des disques managés Premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](sizes.md) qui prend en charge le stockage Premium. Cet exemple passe également à une taille qui prend en charge le stockage Premium.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Basculer les disques gérés individuels entre Standard et Premium

Pour votre charge de travail de développement/test, vous souhaiterez avoir une combinaison de disques Standard et Premium pour réduire les coûts. Vous pouvez choisir de mettre à niveau uniquement ces disques qui ont besoin de meilleures performances. Cet exemple montre comment convertir un disque de machine virtuelle unique à partir de Standard vers le stockage Premium ou de Premium vers le stockage Standard. Pour utiliser des disques managés Premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](sizes.md) qui prend en charge le stockage Premium. Cet exemple passe également à une taille qui prend en charge le stockage Premium.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Basculer entre Standard HDD et SSD Standard des disques gérés

Cet exemple montre comment convertir un disque de machine virtuelle unique à partir du disque dur Standard pour disque SSD Standard ou d’un disque SSD Standard pour les disques durs Standard.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Basculer entre Standard et Premium des disques gérés dans le portail Azure

Procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez la machine virtuelle à partir de la liste des **machines virtuelles**.
3. Si la machine virtuelle n’est pas arrêtée, sélectionnez **arrêter** en haut de la machine virtuelle **vue d’ensemble** volet et attendez que la machine virtuelle à arrêter.
4. Dans le volet de la machine virtuelle, sélectionnez **disques** à partir du menu.
5. Sélectionnez le disque que vous souhaitez convertir.
6. Sélectionnez **Configuration** dans le menu.
7. Modification la **type de compte** à partir de **HDD Standard** à **Premium SSD** ou à partir de **Premium SSD** à **Standard HDD**.
8. Sélectionnez **enregistrer**et fermez le volet de disque.

La mise à jour du type de disque est instantanée. Vous pouvez redémarrer votre machine virtuelle après la conversion.

## <a name="next-steps"></a>Étapes suivantes

Effectuer une copie en lecture seule d’une machine virtuelle à l’aide de [instantanés](snapshot-copy-managed-disk.md).
