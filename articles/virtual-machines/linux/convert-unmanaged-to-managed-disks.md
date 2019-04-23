---
title: Convertir les disques non managés d’une machine virtuelle Linux dans Azure en disques managés - Azure Disques managés | Microsoft Docs
description: Guide pratique pour convertir les disques non managés d’une machine virtuelle Linux en disques managés à l’aide d’Azure CLI dans le modèle de déploiement Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 39a42891a73bd64731dd19aa22214a62d913d975
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700823"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Convertir les disques non managés d’une machine virtuelle Linux en disques managés

Si vos machines virtuelles Linux existantes utilisent des disques non managés, vous pouvez les convertir pour qu’elles utilisent [Azure Disques managés](../linux/managed-disks-overview.md). Ce processus convertit le disque du système d’exploitation ainsi que tous les autres disques de données attachés.

Cet article explique comment convertir des machines virtuelles avec Azure CLI. Si vous devez installer ou mettre à niveau l’interface, consultez [Installer Azure CLI](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Avant de commencer
* Consultez les [questions fréquentes (FAQ) sur la migration vers la fonctionnalité Disques managés](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>Convertir des machines virtuelles à instance unique
Cette section explique comment convertir vos machines virtuelles Azure à instance unique à partir de disques non managés vers des disques managés. (Si vos machines virtuelles sont dans un groupe à haute disponibilité, voir la section suivante.) Vous pouvez utiliser ce processus pour convertir les machines virtuelles qui utilisent des disques non managés premium (SSD) afin qu’elles utilisent des disques managés premium, ou qui utilisent des disques non managés standard (HDD) afin qu’elles utilisent des disques managés standard.

1. Libérez la machine virtuelle à l’aide de [az vm deallocate](/cli/azure/vm). L’exemple suivant libère la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Convertissez la machine virtuelle afin qu’elle utilise des disques managés à l’aide de la commande [az vm convert](/cli/azure/vm). Le processus suivant convertit la machine virtuelle nommée `myVM`, y compris le disque du système d’exploitation et tous les disques de données :

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Démarrez la machine virtuelle une fois la conversion vers les disques managés effectuée à l’aide de la commande [az vm start](/cli/azure/vm). L’exemple suivant démarre la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Convertir des machines virtuelles dans un groupe à haute disponibilité

Si les machines virtuelles que vous souhaitez convertir pour utiliser des disques managés se trouvent dans un groupe à haute disponibilité, vous devez tout d’abord convertir ce dernier en groupe à haute disponibilité géré.

Toutes les machines virtuelles dans le groupe à haute disponibilité doivent être libérées avant de convertir le groupe à haute disponibilité. Prévoyez de convertir toutes les machines virtuelles vers des disques managés une fois que le groupe à haute disponibilité a lui-même été converti en groupe à haute disponibilité géré. Vous pouvez ensuite démarrer toutes les machines virtuelles et poursuivre un fonctionnement normal.

1. Répertoriez toutes les machines virtuelles contenues dans un groupe à haute disponibilité à l’aide de la commande [az vm availability-set list](/cli/azure/vm/availability-set). L’exemple suivant répertorie toutes les machines virtuelles dans le groupe à haute disponibilité nommé `myAvailabilitySet` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Libérez toutes les machines virtuelles à l’aide de la commande [az vm deallocate](/cli/azure/vm). L’exemple suivant libère la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Convertissez le groupe à haute disponibilité à l’aide de la commande [az vm availability-set convert](/cli/azure/vm/availability-set). L’exemple suivant convertit le groupe à haute disponibilité nommé `myAvailabilitySet` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Convertissez toutes les machines virtuelles vers des disques managés à l’aide de la commande [az vm convert](/cli/azure/vm). Le processus suivant convertit la machine virtuelle nommée `myVM`, y compris le disque du système d’exploitation et tous les disques de données :

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Démarrez toutes les machines virtuelles une fois la conversion vers des disques managés effectuée à l’aide de la commande [az vm start](/cli/azure/vm). L’exemple suivant démarre la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les options de stockage, voir la page [Vue d’ensemble d’Azure Disques managés](../windows/managed-disks-overview.md).
