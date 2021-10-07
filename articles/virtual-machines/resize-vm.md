---
title: Redimensionner une machine virtuelle
description: Modifier la taille de la machine virtuelle utilisée pour une machine virtuelle Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/13/2021
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 80387608b8c8ab7069ed989078472102e3d9afe8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700956"
---
# <a name="change-the-size-of-a-virtual-machine"></a>Modifier la taille d’une machine virtuelle 

**S’applique à :** :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles 

Cet article vous explique comment modifier la [taille d’une machine virtuelle](sizes.md).

Une fois que vous avez créé une machine virtuelle, vous pouvez la mettre à l’échelle en modifiant sa taille. Dans certains cas, vous devez commencer par libérer la machine virtuelle. Cela peut se produire si la nouvelle taille n’est pas disponible sur le cluster matériel qui héberge actuellement la machine virtuelle.

Si votre machine virtuelle utilise le stockage Premium, assurez-vous de choisir une version **s** de la taille pour obtenir un support de stockage Premium. Par exemple, choisissez Standard_E4 **s** _v3 au lieu de Standard_E4_v3.

## <a name="change-the-vm-size"></a>Changer de taille de VM

### <a name="portal"></a>[Portail](#tab/portal)

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Ouvrez la page de la machine virtuelle.
1. Dans le menu de gauche, sélectionnez **Taille**.
1. Choisissez une nouvelle taille dans la liste des tailles disponibles, puis sélectionnez **Redimensionner**.


Si la machine virtuelle est en cours d’exécution et que vous modifiez sa taille, elle redémarre. 

Si votre machine virtuelle est toujours en cours d’exécution et que vous ne voyez pas la taille que vous voulez dans la liste, l’arrêt de la machine virtuelle peut permettre d’afficher d’autres tailles.

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Pour redimensionner une machine virtuelle, vous devez installer la dernière version [d’Azure CLI](/cli/azure/install-az-cli2) et vous connecter à un compte Azure avec [az login](/cli/azure/reference-index).

1. Affichez la liste des tailles de machine virtuelle disponibles dans le cluster matériel qui héberge la machine virtuelle à l’aide de la commande [az vm list-vm-resize-options](/cli/azure/vm). L’exemple suivant répertorie les tailles de machine virtuelle pour la machine virtuelle nommée `myVM` dans la région du groupe de ressources `myResourceGroup` :
   
    ```azurecli-interactive
    az vm list-vm-resize-options \
    --resource-group myResourceGroup \
    --name myVM --output table
    ```

2. Si la taille de machine virtuelle souhaitée est répertoriée, redimensionnez la machine virtuelle avec la commande [az vm resize](/cli/azure/vm). L’exemple suivant redimensionne la machine virtuelle nommée `myVM` à la taille `Standard_DS3_v2` :
   
    ```azurecli-interactive
    az vm resize \
    --resource-group myResourceGroup \
    --name myVM \
    --size Standard_DS3_v2
    ```
   
    La machine virtuelle redémarre pendant le processus. Après le redémarrage, votre système d’exploitation existant et les disques de données sont remappés. Tout le contenu du disque temporaire est perdu.

3. Si la taille de machine virtuelle souhaitée n’est pas répertoriée, vous devez d’abord libérer la machine virtuelle avec la commande [az vm deallocate](/cli/azure/vm). Ce processus permet de redimensionner ensuite la machine virtuelle à n’importe quelle taille disponible dans la région, puis de la redémarrer. Les étapes suivantes consistent à libérer, redimensionner, puis démarrer la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :
   
    ```azurecli-interactive
    # Variables will make this easier. Replace the values with your own.
    resourceGroup=myResourceGroup
    vm=myVM
    size=Standard_DS3_v2

    az vm deallocate \
    --resource-group $resourceGroup \
    --name myVM
    az vm resize \
    --resource-group $resourceGroup \
    --name $vm \
    --size $size
    az vm start \
    --resource-group $resourceGroup \
    --name $vm
    ```
   
   > [!WARNING]
   > Le fait de libérer la machine virtuelle libère également toutes les adresses IP dynamiques affectées à la machine virtuelle. Les disques de données et du système d’exploitation ne sont pas affectés.

### <a name="powershell"></a>[PowerShell](#tab/powershell)

**Utilisez PowerShell pour redimensionner une machine virtuelle qui ne se trouve pas dans un groupe à haute disponibilité.**

Définissez des variables. Remplacez les valeurs par vos propres informations.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Listez les tailles de machines virtuelles qui sont disponibles dans la région où la machine virtuelle est hébergée. 
   
```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Si la taille voulue est répertoriée, exécutez les commandes suivantes pour redimensionner la machine virtuelle. Si la taille souhaitée n’est pas répertoriée, passez à l’étape 3.
   
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Si la taille voulue n’est pas répertoriée, exécutez les commandes suivantes pour libérer la machine virtuelle, la redimensionner et la redémarrer. Remplacez **\<newVMsize>** par la taille voulue.
   
```azurepowershell-interactive
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Le fait de libérer la machine virtuelle libère toutes les adresses IP dynamiques affectées à la machine virtuelle. Les disques de données et du système d’exploitation ne sont pas affectés. 
> 
> 

**Utilisez PowerShell pour redimensionner une machine virtuelle qui se trouve dans un groupe à haute disponibilité**

Si la nouvelle taille d’une machine virtuelle se trouvant dans un groupe à haute disponibilité n’est pas disponible sur le cluster matériel qui l’héberge actuellement, toutes les machines virtuelles du groupe à haute disponibilité doivent être libérées pour redimensionner la machine virtuelle. Vous devrez peut-être mettre à jour la taille des autres machines virtuelles du groupe à haute disponibilité après le redimensionnement d’une des machines virtuelles. Pour redimensionner une machine virtuelle dans un groupe à haute disponibilité, procédez comme suit.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Liste les tailles de machines virtuelles qui sont disponibles sur le cluster matériel sur lequel la machine virtuelle est hébergée. 
   
```azurepowershell-interactive
Get-AzVMSize `
-ResourceGroupName $resourceGroup `
-VMName $vmName 
```

Si la taille voulue n’est pas répertoriée, exécutez les commandes suivantes pour redimensionner la machine virtuelle. Si elle n’est pas répertoriée, passez à la section suivante.
   
```azurepowershell-interactive
$vm = Get-AzVM `
-ResourceGroupName $resourceGroup `
-VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM `
-VM $vm `
-ResourceGroupName $resourceGroup
```
    
Si la taille voulue n’est pas répertoriée, passez aux étapes suivantes pour libérer toutes les machines virtuelles du groupe à haute disponibilité, les redimensionner et les redémarrer.

Arrêtez toutes les machines virtuelles du groupe à haute disponibilité.
   
```azurepowershell-interactive
$availabilitySetName = "<availabilitySetName>"
$as = Get-AzAvailabilitySet `
-ResourceGroupName $resourceGroup `
-Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines |  Stop-AzVM -Force -NoWait  
```

Redimensionnez et redémarrez toutes les machines virtuelles du groupe à haute disponibilité.
   
```azurepowershell-interactive
$availabilitySetName = "<availabilitySetName>"
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines | Foreach-Object { $_.HardwareProfile.VmSize = $newSize }
$virtualMachines | Update-AzVM
$virtualMachines | Start-AzVM
```

---

## <a name="next-steps"></a>Étapes suivantes

Pour une évolutivité supplémentaire, exécutez plusieurs instances de machine virtuelle et effectuez un scale-out. Pour plus d’informations, consultez [Mettre automatiquement à l’échelle des machines dans un groupe identique de machines virtuelles](../virtual-machine-scale-sets/tutorial-autoscale-powershell.md).
