---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 82318a94a6a095016fe1177ee486f035d101589c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776783"
---
Vous pouvez désactiver le chiffrement avec Azure PowerShell, Azure CLI ou un modèle Resource Manager. La désactivation du chiffrement de disque de données sur la machine virtuelle Windows lorsque les disques du système d’exploitation et de données ont été chiffrés ne fonctionne pas comme prévu. Désactivez plutôt le chiffrement sur tous les disques.

- **Désactiver le chiffrement de disque avec Azure PowerShell :** Pour désactiver le chiffrement, utilisez la cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Désactiver le chiffrement avec Azure CLI :** pour désactiver le chiffrement, utilisez la commande [az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Désactiver le chiffrement avec un modèle Resource Manager :** 

    1. Cliquez sur **Déployer sur Azure** dans le modèle [Désactiver le chiffrement de disque sur une machine virtuelle Windows en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad).
    2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement, la machine virtuelle, le type de volume, les conditions juridiques et le contrat.
    3.  Cliquez sur **Acheter** pour désactiver le chiffrement de disque sur une machine virtuelle Windows en cours d’exécution. 
