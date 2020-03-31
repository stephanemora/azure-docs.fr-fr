---
title: Développer le lecteur de système d’exploitation d’une machine virtuelle Windows dans Azure
description: Développer la taille du lecteur de système d’exploitation d’une machine virtuelle à l’aide d’Azure PowerShell dans le modèle de déploiement Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: c76f57d15cd4cbdad5ded3b7545aab4d57272a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033498"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Extension du lecteur de système d’exploitation d’une machine virtuelle

Lorsque vous créez une machine virtuelle (VM) dans un groupe de ressources en déployant une image à partir d’[Azure Marketplace](https://azure.microsoft.com/marketplace/), le lecteur du système d’exploitation par défaut est de 127 Go (par défaut, certaines images peuvent avoir de plus petits disques de système d’exploitation). Même s’il est possible d’ajouter des disques de données à la machine virtuelle (le nombre dépend de la référence (SKU) choisie) et de plus, il est recommandé d’installer les applications et les charges de travail intensives du processeur sur ces disques supplémentaires, il peut arriver que les clients doivent développer le lecteur du système d’exploitation pour prendre en charge certains scénarios, tels que les suivants :

- Prendre en charge les applications héritées qui installent des composants sur le lecteur du système d’exploitation.
- Migrer un ordinateur physique ou une machine virtuelle depuis un emplacement local avec un lecteur de système d’exploitation plus volumineux.


> [!IMPORTANT]
> Le redimensionnement du disque du système d'exploitation d'une machine virtuelle Azure exige la libération de la machine virtuelle.
>
> Après avoir étendu les disques, vous devez [étendre le volume dans le système d’exploitation](#expand-the-volume-within-the-os) pour tirer parti du disque plus volumineux.
> 


 


## <a name="resize-a-managed-disk"></a>Redimensionner un disque managé

Ouvrez votre Powershell ISE ou une fenêtre Powershell en mode administrateur et suivez les étapes ci-dessous :

1. Connectez-vous à votre compte Microsoft Azure en mode de gestion des ressources et sélectionnez votre abonnement comme suit :
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Définissez le nom du groupe de ressources et le nom de la machine virtuelle comme suit :
   
   ```powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Obtenez une référence à votre machine virtuelle comme suit :
   
   ```powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Arrêtez la machine virtuelle avant de redimensionner le disque comme suit :
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Obtenez une référence vers le disque de système d’exploitation managé. Définissez la taille du disque du système d’exploitation managé sur la valeur souhaitée et mettez à jour le disque comme suit :
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > La nouvelle taille doit être supérieure à la taille du disque actuelle. Le maximum autorisé est de 2 048 Go pour les disques du système d’exploitation. (Il est possible d’étendre la taille de l’objet blob du disque virtuel, mais le système d’exploitation ne peut utiliser que l’espace des premiers 2 048 Go.)
   > 
   > 
6. La mise à jour de la machine virtuelle peut prendre quelques secondes. Une fois que l’exécution de la commande est terminée, redémarrez la machine virtuelle comme suit :
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

Vous avez terminé. Connectez-vous via RDP à la machine virtuelle, ouvrez Gestion de l’ordinateur (ou Gestion des disques) et développez le lecteur à l’aide de l’espace qui vient d’être alloué.

## <a name="resize-an-unmanaged-disk"></a>Redimensionner un disque non managé

Ouvrez votre Powershell ISE ou une fenêtre Powershell en mode administrateur et suivez les étapes ci-dessous :

1. Connectez-vous à votre compte Microsoft Azure en mode de gestion des ressources et sélectionnez votre abonnement comme suit :
   
   ```Powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Définissez le nom du groupe de ressources et le nom de la machine virtuelle comme suit :
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Obtenez une référence à votre machine virtuelle comme suit :
   
   ```Powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Arrêtez la machine virtuelle avant de redimensionner le disque comme suit :
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Définissez la taille du disque du système d’exploitation non managé sur la valeur souhaitée et mettez à jour la machine virtuelle comme suit :
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > La nouvelle taille doit être supérieure à la taille du disque actuelle. Le maximum autorisé est de 2 048 Go pour les disques du système d’exploitation. (Il est possible d’étendre la taille de l’objet blob du disque virtuel, mais le système d’exploitation ne peut utiliser que l’espace des premiers 2 048 Go.)
   > 
   > 
   
6. La mise à jour de la machine virtuelle peut prendre quelques secondes. Une fois que l’exécution de la commande est terminée, redémarrez la machine virtuelle comme suit :
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>Scripts pour le disque du système d’exploitation

Voici le script complet de référence pour les disques managés et non managés :


**Disques managés**

```Powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**Disques non managés**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Redimensionnement des disques de données

Cet article se concentre principalement sur l’extension du disque du système d’exploitation de la machine virtuelle, mais le script peut également servir à étendre les disques de données associés à la machine virtuelle. Par exemple, pour développer le premier disque de données associé à la machine virtuelle, remplacez l’objet `OSDisk` de `StorageProfile` par le tableau `DataDisks` et utilisez un index numérique pour obtenir une référence au premier disque de données associé, comme indiqué ci-dessous :

**Disque managé**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Disque non managé**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



De même, vous pouvez référencer d’autres disques de données associés à la machine virtuelle, à l’aide d’un index comme indiqué ci-dessus ou à l’aide de la propriété **Name** du disque :


**Disque managé**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Disque non managé**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Étendre le volume dans le système d’exploitation

Une fois que vous avez étendu le disque pour la machine virtuelle, vous devez accéder au système d’exploitation et étendre le volume pour qu’il englobe le nouvel espace. Il existe plusieurs méthodes pour étendre une partition. Cette section décrit la connexion de la machine virtuelle à l’aide d’une connexion RDP pour étendre la partition à l’aide de **DiskPart**.

1. Ouvrez une connexion RDP à votre machine virtuelle.

2.  Ouvrez une invite de commandes et tapez **diskpart**.

2.  À l’invite **DISKPART**, tapez `list volume`. Prenez note du volume que vous souhaitez étendre.

3.  À l’invite **DISKPART**, tapez `select volume <volumenumber>`. Cette commande sélectionne le volume *volumenumber* que vous souhaitez étendre dans un espace vide contigu sur le même disque.

4.  À l’invite **DISKPART**, tapez `extend [size=<size>]`. Cette commande étend le volume sélectionné d’après la taille (*size*) en mégaoctets (Mo) indiquée.


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également attacher des disques à l’aide du [portail Azure](attach-managed-disk-portal.md).
