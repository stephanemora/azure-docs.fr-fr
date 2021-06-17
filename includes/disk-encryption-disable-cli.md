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
ms.openlocfilehash: adc6f5f707835c954f3524fb2217ed2b8dd3a505
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071841"
---
Vous pouvez désactiver le chiffrement avec Azure PowerShell, Azure CLI ou un modèle Resource Manager. 

>[!IMPORTANT]
>La désactivation du chiffrement avec Azure Disk Encryption sur les machines virtuelles Linux est prise en charge seulement pour les volumes de données. Elle n’est pas prise en charge sur les volumes de données ou de système d’exploitation si le volume du système d’exploitation a été chiffré.  

- **Désactiver le chiffrement de disque avec Azure PowerShell :** Pour désactiver le chiffrement, utilisez la cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType DATA]
     ```

- **Désactiver le chiffrement avec Azure CLI :** pour désactiver le chiffrement, utilisez la commande [az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```
- **Désactiver le chiffrement avec un modèle Resource Manager :** utilisez le modèle [Désactiver le chiffrement sur une machine virtuelle Linux en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-running-linux-vm-without-aad) pour désactiver le chiffrement.
     1. Cliquez sur **Déployer dans Azure**.
     2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement, les conditions juridiques et le contrat.
