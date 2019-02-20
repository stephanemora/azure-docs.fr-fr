---
title: Convertir les disques non gérés d’une machine virtuelle Windows en disques gérés - Azure Managed Disks | Microsoft Docs
description: Conversion d’une machine virtuelle Windows qui utilise des disques non gérés pour qu’elle utilise des disques gérés à l’aide de PowerShell dans le modèle de déploiement Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.openlocfilehash: bcfb227b8ced6b17fe23c1a60468de24f1835ba0
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979953"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Convertir les disques non gérés d’une machine virtuelle Windows en disques gérés

Si vos machines virtuelles Windows existantes utilisent des disques non gérés, vous pouvez les convertir pour qu’elles utilisent des disques gérés par le biais du service [Azure Managed Disks](managed-disks-overview.md). Ce processus convertit le disque du système d’exploitation ainsi que tous les autres disques de données attachés.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="before-you-begin"></a>Avant de commencer


* Consultez [Planification de la migration vers Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Consultez les [questions fréquentes (FAQ) sur la migration vers Managed Disks](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Convertir des machines virtuelles à instance unique
Cette section explique comment convertir vos machines virtuelles Azure à instance unique à partir de disques non gérés vers des disques gérés. (Si vos machines virtuelles sont dans un groupe à haute disponibilité, voir la section suivante.) 

1. Libérez la machine virtuelle à l’aide de la cmdlet [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). L’exemple suivant libère la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` : 

  ```azurepowershell-interactive
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Convertissez la machine virtuelle pour qu’elle utilise des disques managés à l’aide de la cmdlet [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk). Le processus suivant convertit la machine virtuelle précédente, y compris le disque du système d’exploitation et tous les disques de données, et démarre la machine virtuelle :

  ```azurepowershell-interactive
  ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```



## <a name="convert-vms-in-an-availability-set"></a>Convertir des machines virtuelles dans un groupe à haute disponibilité

Si les machines virtuelles que vous souhaitez convertir pour utiliser des disques gérés se trouvent dans un groupe à haute disponibilité, vous devez tout d’abord convertir ce dernier en groupe à haute disponibilité géré.

1. Convertissez le groupe à haute disponibilité à l’aide de la cmdlet [Update-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/update-azavailabilityset). L’exemple suivant met à jour le groupe à haute disponibilité nommé `myAvailabilitySet` dans le groupe de ressources nommé `myResourceGroup` :

  ```azurepowershell-interactive
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Si la région où votre groupe à haute disponibilité se situe n’a que 2 domaines d’erreur gérés, mais que le nombre de domaines d’erreur non gérés est de 3, cette commande affiche une erreur semblable à « Le nombre de domaines d’erreur spécifié 3 doit être compris entre 1 et 2 ». Pour résoudre l’erreur, mettez à jour le domaine par défaut sur 2 et `Sku` sur `Aligned`, comme suit :

  ```azurepowershell-interactive
  $avSet.PlatformFaultDomainCount = 2
  Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Désallouer et convertir des machines virtuelles dans le groupe à haute disponibilité. Le script suivant libère chaque machine virtuelle à l’aide de la cmdlet [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm), la convertit à l’aide de [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk) et la redémarre automatiquement dans le cadre du processus de conversion :

  ```azurepowershell-interactive
  $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
  }
  ```


## <a name="troubleshooting"></a>Résolution de problèmes

Si une erreur survient lors de la conversion, ou si une machine virtuelle est dans un état d’échec en raison de problèmes dans une conversion précédente, exécutez l’applet de commande `ConvertTo-AzVMManagedDisk` à nouveau. Généralement, une simple nouvelle tentative suffit à débloquer la situation.
Avant de lancer la conversion, assurez-vous que toutes les extensions de machine virtuelle sont dans l’état « Provisionnement réussi ». Sinon, la conversion échoue avec le code d’erreur 409.


## <a name="convert-using-the-azure-portal"></a>Convertir à l’aide du portail Azure

Vous pouvez également convertir des disques non gérés en disques managés à l’aide du portail Azure.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez la machine virtuelle dans la liste des machines virtuelles sur le portail.
3. Dans le panneau de la machine virtuelle, sélectionnez **Disques** dans le menu.
4. En haut du panneau **Disques**, sélectionnez **Migrer vers des disques gérés**.
5. Si votre machine virtuelle se trouve dans un groupe à haute disponibilité, un avertissement apparaît sur le panneau **Migrer vers des disques gérés**, indiquant que vous devez commencer par convertir le groupe à haute disponibilité. L’avertissement doit avoir un lien sur lequel vous pouvez cliquer pour convertir le groupe à haute disponibilité. Une fois le groupe à haute disponibilité converti, ou si votre machine virtuelle ne se trouve pas dans un groupe à haute disponibilité, cliquez sur **Migrate** pour démarrer le processus de migration de vos disques vers des disques managés. 

Lorsque la migration est terminée, la machine virtuelle est arrêtée, puis redémarrée.

## <a name="next-steps"></a>Étapes suivantes

[Convertir des disques gérés standard en premium](convert-disk-storage.md)

Créez une copie en lecture seule d’une machine virtuelle en utilisant des [captures instantanées](snapshot-copy-managed-disk.md).

