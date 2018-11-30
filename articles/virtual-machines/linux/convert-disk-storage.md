---
title: Convertir le stockage Managed Disks Azure de standard en premium, et vice versa | Microsoft Docs
description: Comment convertir le stockage Managed Disks Azure de standard en premium, et vice versa, à l’aide de l’interface de ligne de commande Azure.
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
ms.openlocfilehash: 0d777b5dcebfba7dbff7c9ea1f4fedad12b3cf1a
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52283810"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Convertir le stockage Managed Disks Azure de standard en premium, et vice versa

Le service Managed Disks offre trois options de stockage : [Premium (SSD)](../windows/premium-storage.md), Standard SSD (préversion) et [Standard HDD](../windows/standard-storage.md). Il vous permet de basculer facilement entre les options avec une interruption minimale adaptée à vos besoins de performances. Ce n’est pas pris en charge pour les disques non gérés. Toutefois, vous pouvez facilement [effectuer des conversions en disques gérés](convert-unmanaged-to-managed-disks.md) pour basculer facilement entre les types de disques.

Cet article vous montre comment convertir des disques gérés de standard en premium, et vice versa, à l’aide de l’interface de ligne de commande Azure. Si vous devez installer ou mettre à niveau l’interface, consultez [Installer Azure CLI](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Avant de commencer

* La conversion nécessite un redémarrage de la machine virtuelle. Par conséquent, planifiez la migration de vos stockages sur disques au cours d’une fenêtre de maintenance préexistante. 
* Si vous utilisez des disques non gérés, tout d’abord [effectuez des conversions en disques gérés](convert-unmanaged-to-managed-disks.md) afin d’utiliser cet article pour basculer entre les options de stockage. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Convertir tous les disques gérés d’une machine virtuelle de standard en premium, et vice versa

L’exemple suivant montre comment faire passer tous les disques d’une machine virtuelle du stockage standard au stockage premium. Pour utiliser des disques gérés premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](sizes.md) qui prend en charge le stockage premium. Cet exemple passe également à une taille prenant en charge le stockage premium.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Convertir un disque géré de standard en premium, et vice versa

Pour votre charge de travail de développement/test, vous souhaiterez peut-être mélanger disques standard et disques premium pour réduire les coûts. Vous pouvez le faire en effectuant une mise à niveau vers le stockage premium, uniquement pour les disques qui nécessitent de meilleures performances. L’exemple suivant montre comment faire passer un seul disque d’une machine virtuelle du stockage standard au stockage premium, et vice versa. Pour utiliser des disques gérés premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](sizes.md) qui prend en charge le stockage premium. Cet exemple passe également à une taille prenant en charge le stockage premium.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd-and-vice-versa"></a>Convertir un disque géré de standard HDD en standard SSD, et vice versa

L’exemple suivant montre comment faire passer un seul disque d’une machine virtuelle de standard HDD à standard SSD.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="convert-using-the-azure-portal"></a>Convertir à l’aide du portail Azure

Vous pouvez également convertir des disques non gérés en disques managés à l’aide du portail Azure.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez la machine virtuelle dans la liste des machines virtuelles sur le portail.
3. Dans le panneau de la machine virtuelle, sélectionnez **Disques** dans le menu.
4. En haut du panneau **Disques**, sélectionnez **Migrer vers des disques gérés**.
5. Si votre machine virtuelle se trouve dans un groupe à haute disponibilité, un avertissement apparaît sur le panneau **Migrer vers des disques gérés**, indiquant que vous devez commencer par convertir le groupe à haute disponibilité. L’avertissement doit avoir un lien sur lequel vous pouvez cliquer pour convertir le groupe à haute disponibilité. Une fois le groupe à haute disponibilité converti, ou si votre machine virtuelle ne se trouve pas dans un groupe à haute disponibilité, cliquez sur **Migrate** pour démarrer le processus de migration de vos disques vers des disques managés. 

Lorsque la migration est terminée, la machine virtuelle est arrêtée, puis redémarrée.

## <a name="next-steps"></a>Étapes suivantes

Créez une copie en lecture seule d’une machine virtuelle en utilisant des [captures instantanées](snapshot-copy-managed-disk.md).

