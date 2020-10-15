---
title: Utilisation d’une machine virtuelle de dépannage Windows dans le portail Azure | Microsoft Docs
description: Découvrez comment résoudre les problèmes de machines virtuelles Windows dans Azure en connectant le disque du système d’exploitation à une machine virtuelle de récupération à l’aide du portail Azure
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: 9b51205fe67bfe5be46491b0238e987fc14f6737
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074351"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Résoudre les problèmes d’une machine virtuelle Windows en connectant le disque du système d’exploitation à une machine virtuelle de récupération à l’aide du portail Azure
Si votre machine virtuelle Windows dans Azure rencontre une erreur de démarrage ou de disque, il vous faudra éventuellement appliquer la procédure de dépannage directement sur le disque dur virtuel. Comme exemple courant, citons l’échec de mise à jour d’une application qui empêche le bon démarrage de la machine virtuelle. Cet article vous explique comment utiliser le portail Azure pour connecter votre disque dur virtuel à une autre machine virtuelle Windows pour corriger les éventuelles erreurs, puis pour régénérer votre machine virtuelle d’origine. 

## <a name="recovery-process-overview"></a>Vue d’ensemble du processus de récupération
Le processus de résolution de problème se présente comme suit :

1. Arrêter la machine virtuelle affectée.
1. Créez une capture instantanée du disque de système d’exploitation de la machine virtuelle.
1. Créez un disque dur virtuel à partir de la capture instantanée.
1. Fixez et montez le disque dur virtuel sur une autre machine virtuelle Windows, à des fins de résolution des problèmes.
1. Connectez-vous à la machine virtuelle de dépannage. Modifiez les fichiers ou exécutez des outils afin de corriger les problèmes sur le disque dur virtuel d’origine.
1. Démontez le disque dur virtuel d’origine et dissociez-le de la machine virtuelle de dépannage.
1. Remplacez le disque du système d'exploitation de la machine virtuelle.

> [!NOTE]
> Cet article ne s’applique pas aux machines virtuelles avec un disque non managé.

## <a name="take-a-snapshot-of-the-os-disk"></a>Effectuer une capture instantanée du disque du système d'exploitation
Une capture instantanée est une copie complète en lecture seule d’un disque dur virtuel (VHD). Nous vous recommandons d'arrêter convenablement la machine virtuelle avant d'effectuer une capture instantanée, de façon à mettre fin à tous les processus en cours. Pour effectuer une capture instantanée d'un disque de système d'exploitation, procédez comme suit :

1. Accédez au [portail Azure](https://portal.azure.com). Sélectionnez **Machines virtuelles** sur la barre latérale, puis cliquez sur la machine virtuelle qui rencontre un problème.
1. Dans le volet gauche, sélectionnez **Disques**, puis cliquez sur le nom du disque du système d'exploitation.
    ![Illustration du nom du disque du système d'exploitation](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Sur la page **Vue d'ensemble** du disque du système d'exploitation, sélectionnez **Créer l'instantané**.
1. Créez une capture instantanée au même emplacement que le disque du système d'exploitation.

## <a name="create-a-disk-from-the-snapshot"></a>Créer un disque à partir de la capture instantanée
Pour créer un disque à partir de la capture instantanée, procédez comme suit :

1. Sélectionnez **Cloud Shell** sur le portail Azure.

    ![Illustration d'Ouvrir Cloud Shell](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Exécutez les commandes PowerShell suivantes pour créer un disque managé à partir de la capture instantanée. Vous devez remplacer ces exemples de noms par les noms appropriés.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk.  Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Si les commandes s'exécutent correctement, le nouveau disque apparaît dans le groupe de ressources que vous avez fourni.

## <a name="attach-the-disk-to-another-vm"></a>Joignez le disque à une autre machine virtuelle
Pour les prochaines étapes, vous utilisez une autre machine virtuelle à des fins de résolution des problèmes. Après avoir associé le disque à la machine virtuelle de dépannage, vous pouvez modifier le contenu de celui-ci. Ce processus vous permet de corriger les éventuelles erreurs de configuration ou d'examiner d'autres fichiers journaux du système ou des applications. Pour associer le disque à une autre machine virtuelle, procédez comme suit :

1. Sélectionnez votre groupe de ressources à partir du portail, puis sélectionnez votre machine virtuelle de résolution de problèmes. Sélectionnez **Disques**, **Modifier**, puis cliquez sur **Ajouter un disque de données** :

    ![Attacher un disque existant dans le portail](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Dans la liste **Disques de données**, sélectionnez le disque de système d’exploitation de la machine virtuelle que vous avez identifié. Si vous ne voyez pas le disque de système d’exploitation, assurez-vous que la machine virtuelle de résolution des problèmes et le disque de système d’exploitation se trouvent dans la même région (emplacement). 
3. Sélectionnez **Enregistrer** pour appliquer la modification.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>Monter le disque de données joint sur une machine virtuelle

1. Ouvrez une connexion Bureau à distance à la machine virtuelle de résolution des problèmes. 
2. Dans mla machine virtuelle de résolution des problèmes, ouvez le **Gestionnaire de serveur**, puis sélectionnez **Services de fichiers et de stockage**. 

    ![Sélection de Services de fichiers et de stockage dans le Gestionnaire de serveur](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Le disque de données est automatiquement détecté et attaché. Pour afficher la liste des disques connectés, sélectionnez **Disques**. Vous pouvez sélectionner le disque de données pour afficher les informations sur le volume, notamment la lettre de lecteur. L’exemple suivant présente le disque de données attaché utilisant la lettre **F:** :

    ![Disque attaché et informations sur le volume dans le Gestionnaire de serveur](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Résoudre des problèmes sur le disque dur virtuel d’origine
Avec le disque dur virtuel existant monté, vous pouvez désormais exécuter les procédures de maintenance et de dépannage requises. Une fois que vous avez résolu les problèmes, passez aux étapes suivantes.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Démonter et dissocier le disque dur virtuel d’origine
Une fois les erreurs résolues, dissociez le disque dur virtuel existant de votre machine virtuelle de dépannage. Vous ne pouvez pas utiliser votre disque dur virtuel avec une autre machine virtuelle avant la libération du bail associant le disque dur virtuel à la machine virtuelle de dépannage.

1. Dans la session Bureau à distance sur votre machine virtuelle, ouvrez le **Gestionnaire de serveur**, puis sélectionnez **Services de fichiers et de stockage** :

    ![Sélection de Services de fichiers et de stockage dans le Gestionnaire de serveur](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Sélectionnez **Disques**, puis sélectionnez votre disque de données. Cliquez avec le bouton droit sur votre disque de données et sélectionnez **Mettre hors connexion** :

    ![Définition du disque de données comme étant hors connexion dans le Gestionnaire de serveur](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Dissociez le disque dur virtuel de la machine virtuelle. Sélectionnez votre machine virtuelle dans le portail Azure et cliquez sur **Disques**. 
4. Sélectionnez **Modifier**, sélectionnez le disque de système d’exploitation que vous avez attaché, puis cliquez sur **Détacher** :

    ![Dissocier le disque dur virtuel existant](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Attendez que la machine virtuelle ait correctement dissocié le disque de données avant de continuer.

## <a name="swap-the-os-disk-for-the-vm"></a>Remplacer le disque du système d'exploitation de la machine virtuelle

Le portail Azure prend désormais en charge le remplacement du disque du système d’exploitation de la machine virtuelle. Pour ce faire, procédez comme suit :

1. Accédez au [portail Azure](https://portal.azure.com). Sélectionnez **Machines virtuelles** sur la barre latérale, puis cliquez sur la machine virtuelle qui rencontre un problème.
1. Dans le volet gauche, sélectionnez **Disques**, puis cliquez sur **Remplacer le disque du système d'exploitation**.
        ![Illustration de Remplacer le disque du système d'exploitation sur le portail Azure](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Choisissez le nouveau disque que vous avez réparé, puis entrez le nom de la machine virtuelle pour confirmer le remplacement. Si le disque ne figure pas dans la liste, attendez 10 à 15 minutes après le retrait du disque de la machine virtuelle de dépannage. Assurez-vous également que le disque se trouve au même emplacement que la machine virtuelle.
1. Sélectionnez OK.

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes pour vous connecter à votre machine virtuelle, consultez [Dépannage d’une connexion Bureau à distance à une machine virtuelle Azure](troubleshoot-rdp-connection.md). Pour résoudre les problèmes liés à l’accès aux applications exécutées sur votre machine virtuelle, consultez [Résoudre les problèmes de connectivité des applications sur une machine virtuelle Windows](troubleshoot-app-connection.md).

Pour plus d’informations sur l’utilisation de Resource Manager, consultez la page [Présentation d’Azure Resource Manager](../../azure-resource-manager/management/overview.md).


