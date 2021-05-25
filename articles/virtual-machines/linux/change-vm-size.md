---
title: Procédure de redimensionnement d’une machine virtuelle avec Azure CLI
description: Procédure d’exécution d’un scale-up ou d’un scale-down d’une machine virtuelle en en modifiant la taille.
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 02/10/2017
ms.author: mimckitt
ms.openlocfilehash: 9361814a12e2f3c0d8c57852c89299e477f43dcb
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108738140"
---
# <a name="resize-a-virtual-machine-using-azure-cli"></a>Redimensionner une machine virtuelle à l’aide d’Azure CLI 

Après avoir provisionné une machine virtuelle, vous pouvez la mettre à l’échelle en modifiant sa [taille][vm-sizes]. Dans certains cas, vous devez commencer par libérer la machine virtuelle. C’est le cas notamment lorsque la taille souhaitée n’est pas disponible sur le cluster matériel qui héberge la machine virtuelle. Cet article explique la procédure de redimensionnement d’une machine virtuelle avec Azure CLI. 

## <a name="resize-a-vm"></a>Redimensionner une machine virtuelle
Pour redimensionner une machine virtuelle, vous devez installer la dernière version [d’Azure CLI](/cli/azure/install-az-cli2) et vous connecter à un compte Azure avec [az login](/cli/azure/reference-index).

1. Affichez la liste des tailles de machine virtuelle disponibles dans le cluster matériel qui héberge la machine virtuelle à l’aide de la commande [az vm list-vm-resize-options](/cli/azure/vm). L’exemple suivant répertorie les tailles de machine virtuelle pour la machine virtuelle nommée `myVM` dans la région du groupe de ressources `myResourceGroup` :
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Si la taille de machine virtuelle souhaitée est répertoriée, redimensionnez la machine virtuelle avec la commande [az vm resize](/cli/azure/vm). L’exemple suivant redimensionne la machine virtuelle nommée `myVM` à la taille `Standard_DS3_v2` :
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    La machine virtuelle redémarre pendant le processus. Après le redémarrage, votre système d’exploitation existant et les disques de données sont remappés. Tout le contenu du disque temporaire est perdu.

3. Si la taille de machine virtuelle souhaitée n’est pas répertoriée, vous devez d’abord libérer la machine virtuelle avec la commande [az vm deallocate](/cli/azure/vm). Ce processus permet de redimensionner ensuite la machine virtuelle à n’importe quelle taille disponible dans la région, puis de la redémarrer. Les étapes suivantes consistent à libérer, redimensionner, puis démarrer la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Le fait de libérer la machine virtuelle libère également toutes les adresses IP dynamiques affectées à la machine virtuelle. Les disques de données et du système d’exploitation ne sont pas affectés.

## <a name="next-steps"></a>Étapes suivantes
Pour une scalabilité supplémentaire, exécutez plusieurs instances de machine virtuelle et effectuez un scale-out. Pour plus d’informations, consultez [Mettre automatiquement à l’échelle des machines dans un groupe de machines virtuelles identiques][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/tutorial-autoscale-cli.md 
[vm-sizes]:sizes.md
