---
title: Exemple Azure CLI pour la gestion des groupes de machines virtuelles identiques
description: Cet exemple illustre la façon d’ajouter des disques à un groupe de machines virtuelles identiques. Vous pouvez mettre à niveau des disques et ajouter vos machines virtuelles à l’authentification Azure AD.
author: mimckitt
ms.author: mimckitt
ms.date: 02/04/2021
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1abdf7ae15753d78ac8728f57e9b0cd5dcd9165e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101672595"
---
# <a name="create-and-manage-virtual-machine-scale-set"></a>Créer et gérer un groupe de machines virtuelles identiques

Utilisez ces exemples de commandes pour créer le prototype d’un groupe de machines virtuelles identiques avec Azure CLI.

Ces exemples de commandes illustrent les opérations suivantes :

* Crée un groupe de machines virtuelles identiques
* Ajouter et mettre à niveau des disques nouveaux ou existants sur un groupe identique ou une instance du groupe.
* Ajouter un groupe identique à l’authentification Azure Active Directory (Azure AD).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-commands"></a>Exemples de commandes

```azurecli
# Create a resource group
az group create --name MyResourceGroup --location eastus

# Create virtual machine scale set
az vmss create --resource-group MyResourceGroup --name myScaleSet --instance-count 2 \
    --image UbuntuLTS --upgrade-policy-mode automatic --admin-username azureuser \
    --generate-ssh-keys

# Attach a new managed disk to your scale set
az vmss disk attach --resource-group MyResourceGroup --vmss-name myScaleSet --size-gb 50
```

Après avoir ajouté un nouveau disque de données, formatez et montez le disque. Pour les machines virtuelles Windows, consultez [Attacher un disque de données managé à une machine virtuelle Windows en utilisant le portail Azure](../../virtual-machines/windows/attach-managed-disk-portal.md). Pour les machines virtuelles Linux, consultez [Ajouter un disque à une machine virtuelle Linux](../../virtual-machines/linux/add-disk.md).

```azurecli
# Attach an existing managed disk to a virtual machine instance in your scale set
az vmss disk attach --resource-group MyResourceGroup --disk myDataDisk \
    --vmss-name myScaleSet --instance-id 0

# See the instances in your virtual machine scale set
az vmss list-instances --resource-group MyResourceGroup --name myScaleSet --output table

# See the disks for your virtual machine
az disk list --resource-group MyResourceGroup \
    --query "[*].{Name:name,Gb:diskSizeGb,Tier:accountType}" --output table

# Deallocate the virtual machine
az vmss deallocate --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Resize the disk
az disk update --resource-group MyResourceGroup --name myDataDisk --size-gb 200

# Restart the disk
az vmss restart --resource-group MyResourceGroup --name myScaleSet --instance-ids 0
```

Pour utiliser le disque étendu, étendez la partition sous-jacente. Pour plus d’informations, consultez [Étendre une partition de disque et un système de fichiers](../../virtual-machines/linux/expand-disks.md#expand-a-disk-partition-and-filesystem).

Cet exemple a redimensionné un disque de données. Vous pouvez utiliser cette même procédure pour mettre à jour un disque de système d’exploitation. Pour plus d’informations sur une machine virtuelle Windows, consultez le [Guide pratique pour étendre le lecteur de système d’exploitation d’une machine virtuelle](../../virtual-machines/windows/expand-os-disk.md). Pour plus d’informations sur les machines virtuelles Linux, consultez [Étendre des disques durs virtuels sur une machine virtuelle Linux avec l’interface Azure CLI](../../virtual-machines/linux/expand-disks.md).

```azurecli
# Enable managed service identity on your scale set. This is required to authenticate and interact with other Azure services using bearer tokens.
az vmss identity assign --resource-group MyResourceGroup --name myScaleSet --role Owner \
    --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup

# Connect to Azure AD authentication
az vmss extension set --resource-group MyResourceGroup --name AADLoginForWindows \
    --publisher Microsoft.Azure.ActiveDirectory --vmss-name myScaleSet

# Upgrade one instance of a scale set virtual machine
az vmss update-instances --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Remove a managed disk from the scale set
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --lun 0

# Remove a managed disk from an instance
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --instance-id 1 --lun 0

# Delete the pre-existing disk
az disk delete --resource-group MyResourceGroup --disk myDataDisk
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Après avoir utilisé ces commandes, exécutez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qui lui sont associées.

```azurecli
az group delete --name MyResourceGroup
```

## <a name="azure-cli-references-used-in-this-article"></a>Références Azure CLI utilisées dans cet article

* [az disk delete](/cli/azure/disk#az_disk_delete)
* [az disk list](/cli/azure/disk#az_disk_list)
* [az disk update](/cli/azure/disk#az_disk_update)
* [az group create](/cli/azure/group#az_group_create)
* [az vmss create](/cli/azure/vmss#az_vmss_create)
* [az virtual machine scale set deallocate](/cli/azure/vmss#az_vmss_deallocate)
* [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach)
* [az vmss disk detach](/cli/azure/vmss/disk#az_vmss_disk_detach)
* [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set)
* [az vmss identity assign](/cli/azure/vmss/identity#az_vmss_identity_assign)
* [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances)
* [az vmss restart](/cli/azure/vmss#az_vmss_restart)
* [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)