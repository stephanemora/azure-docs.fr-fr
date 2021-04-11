---
title: Convertir les disques managés d’un type de disque à l’autre à l’aide d’Azure CLI
description: Comment convertir les disques managés Azure d’un type de disque à l’autre à l’aide d’Azure CLI.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.subservice: disks
ms.openlocfilehash: 35ba681ae35e60d7cd275c9e649b2463d2dc30a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558455"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Convertir un stockage sur disques managés Azure de Standard en Premium, et vice versa

Il existe quatre types de disques managés Azure : Disques Ultra, SSD Premium, SSD Standard et HDD Standard Azure. Vous pouvez basculer vers des disques SSD Premium, SSD Standard et HDD Standard, selon vos besoins en performances. Il n'est pas encore possible de passer d'un disque Ultra à un autre type de disque ou l'inverse : vous devez en déployer un nouveau.

Cette fonctionnalité n’est pas prise en charge pour les disques non managés. En revanche, vous pouvez facilement [convertir un disque non managé en disque managé](convert-unmanaged-to-managed-disks.md) pour pouvoir basculer entre les différents types de disques.

Cet article explique comment convertir des disques managés d’un type vers un autre à l’aide d’Azure CLI. Pour installer ou mettre à niveau l’outil, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Avant de commencer

* La conversion de disque nécessite un redémarrage de la machine virtuelle. Par conséquent, planifiez la migration de vos stockages sur disques au cours d’une fenêtre de maintenance préexistante.
* Pour des disques non managés, commencez par les [convertir en disques managés](convert-unmanaged-to-managed-disks.md) afin de pouvoir basculer entre les options de stockage.


## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Faire basculer tous les disques managés d’une machine virtuelle d’un compte vers un autre

Cet exemple montre comment convertir tous les disques d’une machine virtuelle en stockage Premium. Toutefois, en modifiant la variable sku de cet exemple, vous pouvez convertir les disques de la machine virtuelle en disques SSD standard ou HDD standard. Notez que pour utiliser des disques managés Premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](../sizes.md) qui prend en charge le stockage Premium. Cet exemple bascule également vers une taille prenant en charge le stockage Premium.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
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
## <a name="switch-individual-managed-disks-from-one-disk-type-to-another"></a>Faire basculer des disques managés d’un type de disque vers un autre

Pour votre charge de travail de développement/test, vous pouvez mélanger des disques Standard et Premium afin de réduire les coûts. Vous pouvez choisir de mettre à niveau seulement les disques nécessitant de meilleures performances. Cet exemple montre comment faire passer un disque de machine virtuelle du stockage Standard au stockage Premium. Toutefois, en modifiant la variable sku de cet exemple, vous pouvez convertir les disques de la machine virtuelle en disques SSD standard ou HDD standard. Pour utiliser des disques managés Premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](../sizes.md) qui prend en charge le stockage Premium. Cet exemple bascule également vers une taille prenant en charge le stockage Premium.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
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

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Faire basculer des disques managés d’un type de disque vers un autre

Procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez la machine virtuelle dans la liste des **machines virtuelles**.
3. Si la machine virtuelle n’est pas arrêtée, sélectionnez **Arrêter** en haut du volet **Vue d’ensemble** de la machine virtuelle et attendez que celle-ci s’arrête.
4. Dans le menu du volet pour la machine virtuelle, sélectionnez **Disques**.
5. Sélectionnez le disque à convertir.
6. Sélectionnez **Configuration** dans le menu.
7. Remplacez le **Type de compte** d’origine du disque par le type de disque souhaité.
8. Sélectionnez **Enregistrer**, puis fermez le volet du disque.

La mise à jour du type de disque est instantanée. Vous pouvez redémarrer votre machine virtuelle après la conversion.

## <a name="next-steps"></a>Étapes suivantes

Créez une copie en lecture seule d’une machine virtuelle en utilisant des [captures instantanées](snapshot-copy-managed-disk.md).