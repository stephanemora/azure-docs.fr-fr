---
title: Répliquer des machines virtuelles Azure exécutées dans des groupes de placement de proximité
description: Découvrez comment répliquer des machines virtuelles Azure exécutées dans des groupes de placement de proximité avec Azure Site Recovery.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: a58ec80c13ee9ae0eceb019ab2fd7909fd6f369b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889278"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>Répliquer des machines virtuelles Azure exécutées dans des groupes de placement de proximité dans une autre région

Cet article explique comment répliquer, basculer et restaurer automatiquement des machines virtuelles exécutées dans un groupe de placement de proximité vers une région secondaire.

[Les groupes de placement de proximité](../virtual-machines/windows/proximity-placement-groups-portal.md) sont une fonctionnalité de regroupement logique de machines virtuelles Azure que vous pouvez utiliser pour réduire la latence du réseau de machines virtuelles associées à vos applications. Lorsque des machines virtuelles sont déployées au sein du même groupe de placement de proximité, elles sont physiquement le plus près possible les unes des autres. Les groupes de placement de proximité sont particulièrement utiles pour répondre aux exigences des charges de travail sensibles à la latence.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Récupération d’urgence avec les groupes de placement de proximité

Dans un scénario classique, vous pouvez faire en sorte que vos machines virtuelles s’exécutent dans un groupe de placement de proximité pour éviter la latence du réseau entre les différents niveaux de votre application. Si cela peut permettre à votre application de bénéficier d’une latence réseau optimale, vous aimeriez protéger ces applications à l’aide de Site Recovery en cas de défaillance au niveau de la région. Site Recovery réplique les données d’une région Azure vers une autre et active les machines dans la région de récupération d’urgence en cas de basculement.

## <a name="considerations"></a>Considérations

- Le mieux à faire est de basculer/restaurer les machines virtuelles dans un groupe de placement de proximité. Toutefois, si la machine virtuelle ne peut pas être réactivée dans le placement de proximité pendant le basculement/la restauration automatique, alors le basculement/la restauration automatique aura quand même lieu, et les machines virtuelles seront créées en dehors d’un groupe de placement de proximité.
- Si un groupe à haute disponibilité est épinglé à un groupe de placement de proximité et que pendant le basculement/la restauration automatique les machines virtuelles dans ce groupe à haute disponibilité ont une contrainte d’allocation, alors elles seront créées en dehors de groupe à haute disponibilité et du groupe de placement de proximité.
- Site Recovery pour les groupes de placement de proximité n’est pas pris en charge pour les disques non managés.

> [!NOTE]
> Azure Site Recovery ne prend pas en charge la restauration automatique à partir de disques managés dans les scénarios Hyper-V vers Azure. Par conséquent, la restauration automatique d'un groupe de placement de proximité Azure vers Hyper-V n'est pas prise en charge.

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-portal"></a>Configurer la reprise d’activité pour les machines virtuelles appartenant à des groupes de placement de proximité via le portail

### <a name="azure-to-azure-via-portal"></a>Azure vers Azure via le portail

Vous pouvez choisir d’activer la réplication pour une machine virtuelle par le biais de la page de reprise d’activité de la machine virtuelle. Vous pouvez également procéder en accédant à un coffre précréé et en activant la réplication dans la section Site Recovery. Voyons comment configurer Site Recovery pour les machines virtuelles qui appartiennent à un groupe de placement de proximité avec ces deux approches :

- Comment sélectionner un groupe de placement de proximité dans la région de reprise d’activité lorsque vous activez la réplication via le panneau de reprise d’activité de la machine virtuelle IaaS :
  1. Accédez à la machine virtuelle. Dans le panneau de gauche, sous « Opérations », sélectionnez « Reprise d’activité ».
  2. Sous l’onglet « Paramètres de base », choisissez la région de reprise d’activité vers laquelle vous souhaitez répliquer la machine virtuelle. Accédez à « Paramètres avancés ».
  3. Ici, vous pouvez voir le groupe de placement de proximité de votre machine virtuelle, ainsi que l’option permettant de sélectionner un groupe de placement de proximité dans la région de reprise d’activité. Site Recovery vous donne également la possibilité d’utiliser un groupe de placement de proximité qu’il crée pour vous si vous choisissez d’utiliser cette option par défaut. Après avoir sélectionné le groupe de placement de proximité de votre choix, accédez à « Réviser + lancer la réplication », puis activez la réplication.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-1.png" alt-text="Activez la réplication.":::

- Comment sélectionner un groupe de placement de proximité dans la région de reprise d’activité lorsque vous activez la réplication via le panneau Coffre :
  1. Accédez à votre coffre Recovery Services, puis à l’onglet Site Recovery.
  2. Cliquez sur « + Activer Site Recovery », puis sélectionnez «1 : Activer la réplication » sous Machines virtuelles Azure (puisque vous cherchez à répliquer une machine virtuelle Azure).
  3. Renseignez les champs obligatoires de l’onglet « Source », puis cliquez sur « Suivant ».
  4. Sous l’onglet « Machines virtuelles », sélectionnez la liste des machines virtuelles pour lesquelles vous souhaitez activer la réplication, puis cliquez sur « Suivant ».
  5. Vous verrez l’option permettant de sélectionner un groupe de placement de proximité dans la région de reprise d’activité. Site Recovery vous donne également la possibilité d’utiliser un groupe de placement de proximité qu’il crée pour vous si vous choisissez d’utiliser cette option par défaut. Sélectionnez le groupe de placement de proximité de votre choix, puis activez la réplication.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-2.png" alt-text="Activer la réplication via le coffre.":::

Notez qu’il est facile de mettre à jour le groupe de placement de proximité dans la région de reprise d’activité une fois que la réplication a été activée pour la machine virtuelle.

1. Accédez à la machine virtuelle, puis, dans le panneau de gauche, sous « Opérations », sélectionnez « Reprise d’activité ».
2. Accédez au panneau « Calcul et réseau », puis cliquez sur « Modifier » en haut de la page.
3. Vous pouvez voir les options permettant de modifier plusieurs paramètres cibles, y compris les groupes de placement de proximité cibles. Sélectionnez le groupe de placement de proximité vers lequel vous souhaitez faire basculer la machine virtuelle, puis cliquez sur « Enregistrer ».

### <a name="vmware-to-azure-via-portal"></a>VMware vers Azure via le portail

Vous pouvez configurer le groupe de placement de proximité de la machine virtuelle cible après avoir activé la réplication pour la machine virtuelle. Veillez à créer le groupe de placement de proximité séparément dans la région cible, conformément à vos exigences. Ensuite, vous pourrez facilement mettre à jour le groupe de placement de proximité dans la région de reprise d’activité une fois que la réplication aura été activée pour la machine virtuelle.

1. Sélectionnez la machine virtuelle dans le coffre, puis, dans le panneau de gauche, sous « Opérations », sélectionnez « Reprise d’activité ».
2. Accédez au panneau « Calcul et réseau », puis cliquez sur « Modifier » en haut de la page.
3. Vous pouvez voir les options permettant de modifier plusieurs paramètres cibles, y compris les groupes de placement de proximité cibles. Sélectionnez le groupe de placement de proximité vers lequel vous souhaitez faire basculer la machine virtuelle, puis cliquez sur « Enregistrer ».

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-v2a.png" alt-text="Mettre à jour le groupe de placement de proximité V2A":::

### <a name="hyper-v-to-azure-via-portal"></a>Hyper-V vers Azure via le portail

Vous pouvez configurer le groupe de placement de proximité de la machine virtuelle cible après avoir activé la réplication pour la machine virtuelle. Veillez à créer le groupe de placement de proximité séparément dans la région cible, conformément à vos exigences. Ensuite, vous pourrez facilement mettre à jour le groupe de placement de proximité dans la région de reprise d’activité une fois que la réplication aura été activée pour la machine virtuelle.

1. Sélectionnez la machine virtuelle dans le coffre, puis, dans le panneau de gauche, sous « Opérations », sélectionnez « Reprise d’activité ».
2. Accédez au panneau « Calcul et réseau », puis cliquez sur « Modifier » en haut de la page.
3. Vous pouvez voir les options permettant de modifier plusieurs paramètres cibles, y compris les groupes de placement de proximité cibles. Sélectionnez le groupe de placement de proximité vers lequel vous souhaitez faire basculer la machine virtuelle, puis cliquez sur « Enregistrer ».

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-h2a.png" alt-text="Mettre à jour le groupe de placement de proximité H2A":::

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-powershell"></a>Configurer la reprise d’activité pour les machines virtuelles appartenant à des groupes de placement de proximité via PowerShell

### <a name="prerequisites"></a>Prérequis 

1. Assurez-vous que vous disposez du module Azure PowerShell Az. Si vous devez installer ou mettre à niveau Azure PowerShell, consultez le [guide sur l’installation et la configuration d’Azure PowerShell](/powershell/azure/install-az-ps).
2. La version Azure PowerShell Az minimale doit être 4.1.0. Pour vérifier la version actuelle, utilisez la commande ci-dessous :

    ```
    Get-InstalledModule -Name Az
    ```

### <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>Configurer Site Recovery pour Machines virtuelles Microsoft Azure dans le groupe de placement de proximité

> [!NOTE]
> Vérifiez que vous avez l’ID unique du groupe de placement de proximité cible sous la main. Si vous créez un groupe de placement de proximité, vérifiez la commande [ici](../virtual-machines/windows/proximity-placement-groups.md#create-a-proximity-placement-group) et, si vous utilisez un groupe de placement de proximité existant, utilisez la commande que vous trouverez [ici](../virtual-machines/windows/proximity-placement-groups.md#list-proximity-placement-groups).

### <a name="azure-to-azure"></a>Azure vers Azure

1. [Connectez-vous](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription) à votre compte Azure et sélectionnez votre abonnement.
2. Récupérez les détails de la machine virtuelle que vous envisagez de répliquer comme indiqué [ici](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated).
3. [Créez](./azure-to-azure-powershell.md#create-a-recovery-services-vault) votre coffre Recovery Services et [définissez](./azure-to-azure-powershell.md#set-the-vault-context) le contexte du coffre.
4. Préparez le coffre pour commencer à répliquer les machines virtuelles. Cela implique la création d’un [objet Service Fabric](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) pour les régions principales et de récupération.
5. [Créez](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric) un conteneur de protection Site Recovery pour la structure principale et la structure de récupération.
6. [Créez](./azure-to-azure-powershell.md#create-a-replication-policy) une stratégie de réplication.
7. Créez un mappage de conteneur de protection entre le conteneur principal et le conteneur de protection de récupération à l’aide de [ces](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) étapes ; créez un mappage de conteneur de protection pour la restauration automatique comme indiqué [ici](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover).
8. Créez un compte de stockage de cache en suivant [ces](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account) étapes.
9. Créez les mappages réseau requis comme indiqué [ici](./azure-to-azure-powershell.md#create-network-mappings).
10. Pour répliquer une machine virtuelle Azure avec des disques managés, utilisez le cmdlet PowerShell ci-dessous :

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)
#Make sure to replace the variables $OSdiskName with OS disk name.

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

#Make sure to replace the variables $datadiskName with data disk name.

#Data disk
$datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
$datadiskId1 = $datadisk[0].Id
$RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
$RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

Lorsque vous activez la réplication pour plusieurs disques de données, utilisez l’applet de commande PowerShell ci-dessous :

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)
#Make sure to replace the variables $OSdiskName with OS disk name.

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig

#Data disk

# Add data disks
Foreach( $disk in $VM.StorageProfile.DataDisks)
{
    $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
    $dataDiskId1 = $datadisk[0].Id
    $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
    $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name
    $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $dataDiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType
    $diskconfigs += $DataDisk1ReplicationConfig
}

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

Lors de l’activation de la réplication de zone à zone avec PPG, la commande de démarrage de la réplication est échangée avec l’applet de commande PowerShell -

```azurepowershell
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
```

Une fois l’opération de lancement de réplication terminée, les données de la machine virtuelle sont répliquées vers la région de récupération.

Le processus de réplication démarre en envoyant une copie des disques de réplication de la machine virtuelle dans la région de récupération. Cette phase est appelée phase de réplication initiale.

Une fois cette première phase terminée, la réplication passe à la phase de synchronisation différentielle. À ce stade, la machine virtuelle est protégée et une opération de basculement test peut être effectuée. L’état de réplication de l’élément répliqué qui représente la machine virtuelle passe à l’état Protégé après la réplication initiale.

Surveillez l’état de réplication et l’intégrité de la réplication de la machine virtuelle en obtenant les détails sur l’élément de réplication protégé qui lui correspond.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. Pour effectuer un basculement test, valider et supprimer le basculement test et suivez [ces](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover) étapes.
12. Pour basculer, suivez les étapes mentionnées [ici](./azure-to-azure-powershell.md#fail-over-to-azure).
13. Pour reprotéger et effectuer une restauration automatique vers la région source, utilisez le cmdlet PowerShell ci-dessous :

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```

14. Pour désactiver la réplication, suivez [ces étapes](./azure-to-azure-powershell.md#disable-replication).

### <a name="vmware-to-azure-via-powershell"></a>VMware vers Azure via PowerShell

1. Assurez-vous de [préparer vos serveurs VMware locaux](./vmware-azure-tutorial-prepare-on-premises.md) pour la récupération d’urgence vers Azure.
2. Connectez-vous à votre compte Azure et sélectionnez votre abonnement comme spécifié [ici](./vmware-azure-disaster-recovery-powershell.md#log-into-azure).
3. [Configurez](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault) un coffre Recovery Services et [définissez son contexte](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context).
4. [Validez](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration) l’inscription de votre coffre.
5. [Créez](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy) une stratégie de réplication.
6. [Ajoutez](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms) un serveur vCenter et découvrez les machines virtuelles. Puis, [créez](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication) des comptes de stockage pour la réplication.
7. Pour répliquer des machines virtuelles VMware, vérifiez les détails ici et suivez le cmdlet PowerShell ci-dessous :

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg"

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $targetPpg.Id
```

8. Vous pouvez vérifier l’état et l’intégrité de la réplication pour la machine virtuelle avec la cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

9. Configurez les paramètres de basculement en suivant les étapes indiquées [ici](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings).
10. [Exécutez](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover) un basculement test.
11. Basculez vers Azure à l’aide de [ces](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure) étapes.

### <a name="hyper-v-to-azure-via-powershell"></a>Hyper-V vers Azure via PowerShell

1. Assurez-vous de [préparer vos serveurs Hyper-V locaux](./hyper-v-prepare-on-premises-tutorial.md) pour la récupération d’urgence vers Azure.
2. [Connectez-vous](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account) à Azure.
3. [Configurez](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault) votre coffre et [définissez](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context) le contexte du coffre Recovery Services.
4. [Créez](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site) un site Hyper-V.
5. [Installez](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent) le fournisseur et l’agent.
6. [Créez](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy) une stratégie de réplication.
7. Activez la réplication à l’aide des étapes ci-dessous : 
    
    a. Récupérez l’élément protégeable correspondant à la machine virtuelle que vous souhaitez protéger comme suit :

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. Protéger la machine virtuelle. Si la machine virtuelle que vous protégez compte plusieurs disques, spécifiez le disque du système d’exploitation avec le paramètre OSDiskName.
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```
    c. Attendez que les machines virtuelles basculent à l’état protégé après la réplication initiale. Cette opération prendra un certain temps compte tenu de certains facteurs, notamment la quantité de données à répliquer et la bande passante en amont disponible pour Azure. Lorsque l’état protégé est en place, les paramètres State et StateDescription de la tâche s’actualisent comme suit : 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. Mettez à jour les propriétés de récupération (telles que la taille du rôle de machine virtuelle) et le réseau Azure auquel attacher la carte réseau de la machine virtuelle après le basculement.

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. Exécuter un test de [basculement](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover).


## <a name="next-steps"></a>Étapes suivantes

Pour effectuer une reprotection et une restauration automatique de VMware vers Azure, suivez les étapes décrites [ici](./vmware-azure-prepare-failback.md).

Pour effectuer le basculement d’Hyper-V vers Azure, suivez les étapes présentées [ici](./site-recovery-failover.md) et pour effectuer la restauration automatique, suivez les étapes décrites [ici](./hyper-v-azure-failback.md).

Pour plus d’informations, consultez [Basculement dans Site Recovery](site-recovery-failover.md).
