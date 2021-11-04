---
title: Gérer un pool de disques Azure (préversion)
description: Découvrez comment ajouter des disques managés à un pool de disques Azure ou désactiver la prise en charge iSCSI sur un disque.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 59f03b2484bed39a3c562efc6cfb4176b5173964
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083196"
---
# <a name="manage-an-azure-disk-pool-preview"></a>Gérer un pool de disques Azure (préversion)

Cet article explique comment ajouter un disque managé à un pool de disques Azure (préversion) et comment désactiver la prise en charge iSCSI sur un disque qui a été ajouté à un pool de disques.

## <a name="add-a-disk-to-a-pool"></a>Ajouter un disque à un pool

Votre disque doit remplir les conditions suivantes pour pouvoir être ajouté au pool de disques :
- Il doit s'agir d'un disque SSD Premium, SSD Standard ou d'un disque Ultra intégré dans les mêmes région et zone de disponibilité que le pool de disques.
    - Les disques Ultra doivent avoir une taille de secteur de disque de 512 octets.
- Il doit s'agir d'un disque partagé dont la valeur maxShares est supérieure ou égale à deux.
- Vous devez [fournir les autorisations RBAC du fournisseur de ressources StoragePool aux disques qui seront ajoutés au pool de disques](disks-pools-deploy.md#assign-storagepool-resource-provider-permissions).

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Accédez à votre pool de disques, sélectionnez **Disques** sous **Paramètres**.
1. Sélectionnez **Attacher un disque existant** et sélectionnez vos disques.
1. Une fois que vous avez choisi tous les disques que vous souhaitez attacher, sélectionnez **Enregistrer**.

    :::image type="content" source="media/disk-pools-manage/manage-disk-pool-add.png" alt-text="Capture d’écran du panneau Disques pour votre pool de disques.":::

    Maintenant que vous avez attaché votre disque, vous devez activer ses numéros d’unité logique.

1. Sous **Paramètres**, sélectionnez **iSCSI**.
1. Sélectionnez **Ajouter un numéro d’unité logique** sous **Disques activés pour iSCSI**.
1. Sélectionnez le disque que vous avez attaché précédemment.
1. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/disk-pools-manage/enable-disk-luns.png" alt-text="Capture d’écran du panneau iSCSI, numéros d’unité logique ajoutés et activés.":::

Maintenant que vous avez attaché votre disque et activé le numéro d’unité logique (LUN), vous devez le créer et l’attacher en tant que magasin de données iSCSI à votre cloud privé Azure VMware Solution. Pour plus d’informations, consultez [Attacher le numéro d’unité logique iSCSI](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md#attach-the-iscsi-lun).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="prerequisites"></a>Configuration requise

Installer le module Azure PowerShell, [version 6.1.0 ou ultérieure](/powershell/module/az.diskpool/?view=azps-6.1.0&preserve-view=true).

Installez le module Pool de disques à l’aide de la commande suivante :

```azurepowershell
Install-Module -Name Az.DiskPool -RequiredVersion 0.3.0 -Repository PSGallery
```

### <a name="add-a-disk-pool"></a>Ajouter un pool de disques

Le script suivant ajoute un disque supplémentaire au pool de disques et l’expose sur iSCSI. Il conserve les disques existants dans le pool de disques sans aucune modification.

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<yourDiskName>" #Provide the name of the disk you want to add
$lunName ='<LunName>' #Provide the Lun name of the added disk
$diskIds = @()

#Add the disk to disk pool
$DiskPool = Get-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName
$DiskPoolDiskIDs = $DiskPool.Disk.Id
foreach ($Id in $DiskPoolDiskIDs)
{
$diskIds += ($Id)
}

$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName
$diskIds += ,($disk.Id)
Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskIds

#Get the existing iSCSI LUNs and add the new disk
$target = Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName
$existingLuns = $target.Lun
$luns = @()
foreach ($lun in $existingLuns)
{
$tmpLunName = $lun.Name
$tmpId = $lun.ManagedDiskAzureResourceId
$tmplun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $tmpId -Name $tmpLunName
$luns += ,($tmplun)
}

$newlun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $disk.Id -Name $lunName
$luns += ,($newlun)
Update-AzDiskPoolIscsiTarget -Name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName -Lun $luns
```

Maintenant que vous avez attaché votre disque et activé le numéro d’unité logique (LUN), vous devez le créer et l’attacher en tant que magasin de données iSCSI à votre cloud privé Azure VMware Solution. Pour plus d’informations, consultez [Attacher le numéro d’unité logique iSCSI](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md#attach-the-iscsi-lun).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites"></a>Prérequis

Installez la [dernière version](/cli/azure/disk-pool) d’Azure CLI.

Si vous ne l’avez pas déjà fait, installez l’extension Pool de disques à l’aide de la commande suivante :

```azurecli
az extension add -n diskpool
```

### <a name="add-a-disk-pool---cli"></a>Ajouter un pool de disques - CLI

Le script suivant ajoute un disque supplémentaire au pool de disques et l’expose sur iSCSI. Il conserve les disques existants dans le pool de disques sans aucune modification.

```azurecli
# Add a disk to a disk pool

# Initialize parameters
resourceGroupName="<yourResourceGroupName>"
diskPoolName="<yourDiskPoolName>"
iscsiTargetName="<youriSCSITargetName>"
diskName="<yourDiskName>"
lunName="<LunName>"

diskPoolUpdateArgs=("$@")
diskPoolUpdateArgs+=(--resource-group $resourceGroupName --Name $diskPoolName)

diskIds=$(echo $(az disk-pool show --name $diskPoolName --resource-group $resourceGroupName --query disks[].id -o json) | sed -e 's/\[ //g' -e 's/\ ]//g' -e 's/\,//g')
for disk in $diskIds; do
    diskPoolUpdateArgs+=(--disks $(echo $disk | sed 's/"//g'))
done

diskId=$(az disk show --resource-group $resourceGroupName --name $diskName --query id | sed 's/"//g')
diskPoolUpdateArgs+=(--disks $diskId)

az disk-pool update "${diskPoolUpdateArgs[@]}"

# Get existing iSCSI LUNs and expose added disk as a new LUN
targetUpdateArgs=("$@")
targetUpdateArgs+=(--resource-group $resourceGroupName --disk-pool-name $diskPoolName --name $iscsiTargetName)

luns=$(az disk-pool iscsi-target show --name $iscsiTargetName --disk-pool-name $diskPoolName --resource-group $resourceGroupName --query luns)
lunsCounts=$(echo $luns | jq length)

for (( i=0; i < $lunCounts; i++ )); do
    tmpLunName=$(echo $luns | jq .[$i].name | sed 's/"//g')
    tmpLunId=$(echo $luns | jq .[$i].managedDiskAzureResourceId | sed 's/"//g')
    targetUpdateArgs+=(--luns name=$tmpLunName managed-disk-azure-resource-id=$tmpLunId)
done

targetUpdateArgs+=(--luns name=$lunName managed-disk-azure-resource-id=$diskId)

az disk-pool iscsi-target update "${targetUpdateArgs[@]}"
```

Maintenant que vous avez attaché votre disque et activé le numéro d’unité logique (LUN), vous devez le créer et l’attacher en tant que magasin de données iSCSI à votre cloud privé Azure VMware Solution. Pour plus d’informations, consultez [Attacher le numéro d’unité logique iSCSI](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md#attach-the-iscsi-lun).

---

## <a name="disable-iscsi-on-a-disk-and-remove-it-from-the-pool"></a>Désactiver iSCSI sur un disque et le supprimer du pool

Avant de désactiver la prise en charge iSCSI sur un disque, vérifiez qu’il n’existe aucune connexion iSCSI en suspens au numéro d’unité logique iSCSI sur lequel le disque est exposé. Lorsqu’un disque est retiré du pool de disques, il n’est pas automatiquement supprimé. Cela permet d’éviter toute perte de données, cependant vous serez toujours facturé pour le stockage des données. Si vous n’avez pas besoin des données stockées sur un disque, vous pouvez supprimer manuellement le disque. Cette opération supprimera le disque et toutes les données qu’il contient et empêchera les frais supplémentaires.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Accédez à votre pool de disques, sélectionnez **iSCSI** sous **Paramètres**.
1. Sous **Disques activés pour iSCSI**, sélectionnez les disques que vous souhaitez supprimer et sélectionnez **Supprimer le numéro d’unité logique**.
1. Sélectionnez **Enregistrer** et attendez que l’opération soit terminée.

    :::image type="content" source="media/disk-pools-manage/remove-disk-lun.png" alt-text="Capture d’écran du panneau Pool de disques iSCSI, suppression des numéros d’unité logique de disque.":::

    Maintenant que vous avez désactivé le numéro d’unité logique (LUN), vous pouvez supprimer vos disques du pool de disques.

1. Sélectionnez **Disques** sous **Paramètres**.
1. Sélectionnez **Supprimer le disque du pool de disques** et sélectionnez vos disques.
1. Sélectionnez **Enregistrer**.

Une fois l’opération terminée, votre disque est complètement supprimé du pool de disques.

:::image type="content" source="media/disk-pools-manage/remove-disks-from-pool.png" alt-text="Capture d’écran du panneau Disques du pool de disques. Disques en cours de suppression du pool.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<NameOfDiskYouWantToRemove>" #Provide the name of the disk you want to remove
$lunName ='<LunForDiskYouWantToRemove>' #Provide the Lun name of the disk you want to remove
$diskIds = @()

#Get the existing iSCSI LUNs and remove it from iSCS target
$target = Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName
$existingLuns = $target.Lun
$luns = @()
foreach ($lun in $existingLuns)
{
if ($lun.Name -notlike $lunName)
{
$tmpLunName = $lun.Name
$tmpId = $lun.ManagedDiskAzureResourceId
$tmplun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $tmpId -Name $tmpLunName
$luns += ,($tmplun)
}
}

Update-AzDiskPoolIscsiTarget -Name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName -Lun $luns

#Remove the disk from disk pool
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName
$DiskPool = Get-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName
$DiskPoolDiskIDs = $DiskPool.Disk.Id
foreach ($Id in $DiskPoolDiskIDs)
{
if ($Id -notlike $disk.Id)
{
$diskIds += ($Id)
}
}

Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskIds
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
# Disable iSCSI on a disk and remove it from the pool

# Initialize parameters
resourceGroupName="<yourResourceGroupName>"
diskPoolName="<yourDiskPoolName>"
iscsiTargetName="<youriSCSITargetName>"
diskName="<yourDiskName>"
lunName="<LunName>"

# Get existing iSCSI LUNs and remove it from iSCSI target
targetUpdateArgs=("$@")
targetUpdateArgs+=(--resource-group $resourceGroupName --disk-pool-name $diskPoolName --name $iscsiTargetName)

luns=$(az disk-pool iscsi-target show --name $iscsiTargetName --disk-pool-name $diskPoolName --resource-group $resourceGroupName --query luns)
lunCounts=$(echo $luns | jq length)

for (( i=0; i < $lunCounts; i++ )); do
    tmpLunName=$(echo $luns | jq .[$i].name | sed 's/"//g')
    if [ $tmpLunName != $lunName ]; then
        tmpLunId=$(echo $luns | jq .[$i].managedDiskAzureResourceId | sed 's/"//g')
        targetUpdateArgs+=(--luns name=$tmpLunName managed-disk-azure-resource-id=$tmpLunId)
    fi
done

az disk-pool iscsi-target update "${targetUpdateArgs[@]}"

# Remove disk from pool
diskId=$(az disk show --resource-group $resourceGroupName --name $diskName -- query id | sed 's/"//g')

diskPoolUpdateArgs=("$@")
diskPoolUpdateArgs+=(--resource-group $resourceGroupName --name $diskPoolName)

diskIds=$(az disk-pool show --name $diskPoolName --resource-group $resourceGroupName --query disks[].id -o json)
diskLength=$(echo diskIds | jq length)

for (( i=0; i < $diskLength; i++ )); do
    tmpDiskId=$(echo $diskIds | jq .[$i] | sed 's/"//g')

    if [ $tmpDiskId != $diskId ]; then
        diskPoolUpdateArgs+=(--disks $tmpDiskId)
    fi
done

az disk-pool update "${diskPoolUpdateArgs[@]}"
```

---
## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment déplacer un pool de disques vers un autre abonnement, consultez [Déplacer un pool de disques vers un autre abonnement](disks-pools-move-resource.md).
- Pour savoir comment annuler l’approvisionnement d’un pool de disques, voir [Annuler l’approvisionnement d’un pool de disques Azure](disks-pools-deprovision.md).
