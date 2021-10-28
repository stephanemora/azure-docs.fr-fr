---
title: Créer un instantané Azure d’un disque dur virtuel
description: Découvrez comment créer une copie d’une machine virtuelle Azure pour l’utiliser comme sauvegarde ou pour résoudre des problèmes à l’aide du portail, de PowerShell ou de CLI.
author: roygara
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/16/2021
ms.openlocfilehash: 0a63e0f346fedac9b7178f25a19177a9908bc6a4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223004"
---
# <a name="create-a-snapshot-of-a-virtual-hard-disk"></a>Créer un instantané d’un disque dur virtuel

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles

Un instantané est une copie complète en lecture seule d’un disque dur virtuel (VHD). Vous pouvez utiliser un instantané en tant que sauvegarde ponctuelle ou pour résoudre des problèmes liés à une machine virtuelle. Vous pouvez prendre un instantané du système d’exploitation ou de disques durs virtuels de données.

## <a name="create-a-snapshot-of-a-vhd"></a>Créer un instantané d’un disque dur virtuel

Si vous voulez utiliser un instantané pour créer une machine virtuelle, veillez d’abord à arrêter proprement la machine virtuelle. Cette action permet d’effacer tous les processus en cours.

# <a name="portal"></a>[Portail](#tab/portal)

Pour créer un instantané à l’aide du portail Azure, effectuez ces étapes.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**.
1. Recherchez et sélectionnez **Capture instantanée**.
1. Dans la fenêtre **Capture instantanée**, sélectionnez **Créer**. La fenêtre **Créer une capture instantanée** s’affiche.
1. Dans **Groupe de ressources**, sélectionnez un [groupe de ressources](../azure-resource-manager/management/overview.md#resource-groups) existant ou entrez le nom d’un nouveau groupe.
1. Entrez un **nom**, puis sélectionnez une **région** et un **type d’instantané** pour le nouvel instantané. Si vous voulez stocker votre instantané dans le stockage résilient interzone, vous avez besoin de sélectionner une région qui prend en charge des [zones de disponibilité](../availability-zones/az-overview.md). Pour obtenir la liste des régions compatibles, consultez [Régions Azure avec zones de disponibilité](../availability-zones/az-region.md#azure-regions-with-availability-zones).
1. Dans **Abonnement source**, sélectionnez l’abonnement qui contient le disque managé à sauvegarder.
1. Dans **Disque source**, sélectionnez le disque managé dont vous souhaitez obtenir une capture instantanée.
1. Dans **Type de stockage**, sélectionnez **HDD Standard**, sauf si vous avez besoin d’un stockage redondant interzone ou d’un stockage haute performance pour votre instantané.
1. Si nécessaire, configurez les paramètres sous les onglets **Chiffrement**, **Réseau** et **Étiquettes**. Dans le cas contraire, les paramètres par défaut sont utilisés pour votre instantané.
1. Sélectionnez **Revoir + créer**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Cet exemple nécessite l’utilisation de [Cloud Shell](https://shell.azure.com/bash) ou l’installation d’[Azure CLI](/cli/azure/).

Effectuez ces étapes pour prendre un instantané avec les applets de commande `New-AzSnapshotConfig` et `New-AzSnapshot`. Cet exemple suppose qu’il existe une machine virtuelle nommée *myVM* dans le groupe de ressources *myResourceGroup*. L’exemple de code fourni crée un instantané dans le même groupe de ressources et dans la même région que votre machine virtuelle source.

Tout d’abord, vous allez utiliser l’applet de commande [New-AzSnapshotConfig](/powershell/module/az.compute/new-azsnapshotconfig) pour créer un objet instantané configurable. Vous pouvez ensuite utiliser l’applet de commande [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) pour prendre un instantané du disque.

1. Définissez les paramètres nécessaires. Mettez à jour les valeurs pour les adapter à votre environnement.

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

1. Utilisez l’applet de commande [Get-AzVM](/powershell/module/az.compute/get-azvm) pour obtenir la machine virtuelle contenant le disque dur virtuel à copier.

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

1. Créez la configuration de capture instantanée. Dans cet exemple, l’instantané est celui du disque du système d’exploitation. Par défaut, l’instantané utilise le stockage standard localement redondant. Nous vous recommandons de stocker vos instantanés dans un stockage standard plutôt que premium, quel que soit le type de stockage du disque parent ou du disque cible. Les instantanés premium engendrent des coûts supplémentaires.

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```

   Si vous voulez stocker votre instantané dans un stockage résilient interzone, vous devez créer l’instantané dans une région qui prend en charge des [zones de disponibilité](/azure/availability-zones/az-overview et inclure le paramètre `-SkuName Standard_ZRS`. Pour obtenir la liste des régions qui prennent en charge des zones de disponibilité, consultez [Régions Azure avec zones de disponibilité](../availability-zones/az-region.md#azure-regions-with-availability-zones).

1. Prenez la capture instantanée.

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```

1. Utilisez l’applet de commande [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) pour vérifier que votre instantané existe.

    ```azurepowershell-interactive
    Get-AzSnapshot `
        -ResourceGroupName $resourceGroupName
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Cet exemple demande d’utiliser [Cloud Shell](https://shell.azure.com/bash) ou d’installer [Azure CLI](/cli/azure/).

Effectuez les étapes suivantes pour prendre un instantané avec la commande `az snapshot create` et le paramètre `--source-disk`. Cet exemple suppose qu’il existe une machine virtuelle nommée *myVM* dans le groupe de ressources *myResourceGroup*. L’exemple de code fourni crée un instantané dans le même groupe de ressources et dans la même région que votre machine virtuelle source.

1. Obtenez l’ID du disque avec [az vm show](/cli/azure/vm#az_vm_show).

    ```azurecli-interactive
    osDiskId=$(az vm show \
       -g myResourceGroup \
       -n myVM \
       --query "storageProfile.osDisk.managedDisk.id" \
       -o tsv)
    ```

1. Prenez une capture instantanée nommée *osDisk-backup* à l’aide de la commande [az snapshot create](/cli/azure/snapshot#az_snapshot_create). Dans cet exemple, l’instantané est celui du disque du système d’exploitation. Par défaut, l’instantané utilise le stockage standard localement redondant. Nous vous recommandons de stocker vos instantanés dans un stockage standard plutôt que premium, quel que soit le type de stockage du disque parent ou du disque cible. Les instantanés premium engendrent des coûts supplémentaires.

    ```azurecli-interactive
    az snapshot create \
        -g myResourceGroup \
        --source "$osDiskId" \
        --name osDisk-backup
    ```

    Si vous voulez stocker votre instantané dans le stockage résilient interzone, vous devez le créer dans une région qui prend en charge des [zones de disponibilité](../availability-zones/az-overview.md) et inclure le paramètre `--sku Standard_ZRS` facultatif. La liste des [zones de disponibilité](../availability-zones/az-region.md#azure-regions-with-availability-zones) se trouve ici.
    
1. Utilisez [az snapshot list](/cli/azure/snapshot#az_snapshot_list) pour vérifier que votre instantané existe.
    
    ```azurecli-interactive
    az snapshot list \
       -g myResourceGroup \
       - table
    ```

---

## <a name="next-steps"></a>Étapes suivantes

Créer une machine virtuelle à partir d’un instantané. Créer un disque managé à partir d’un instantané et l’attacher en tant que disque de système d’exploitation.

# <a name="portal"></a>[Portail](#tab/portal)

Pour plus d’informations, consultez l’exemple indiqué dans [Créer une machine virtuelle à partir d’un disque dur virtuel à l’aide du portail Azure](windows/create-vm-specialized-portal.md).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour plus d’informations, consultez l’exemple indiqué dans [Créer une machine virtuelle Windows à partir d’un disque spécialisé à l’aide de PowerShell](windows/create-vm-specialized.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Pour plus d’informations, consultez l’exemple indiqué dans [Créer une machine virtuelle Linux complète avec Azure CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot?toc=%2fcli%2fmodule%2ftoc.json).

---