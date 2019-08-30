---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: db8147717e825d9cc48b7f0704dc5eea0be223a9
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510326"
---
# <a name="using-azure-ultra-disks"></a>Utilisation de disques Ultra Azure

Les disques Ultra Azure permettent un stockage de disque à haut débit avec un nombre élevé d’IOPS et une faible latence homogène pour les machines virtuelles Azure IaaS. Cette nouvelle offre fournit des performances optimales aux mêmes niveaux de disponibilité que nos offres de disques existantes. Entre autres avantages, les disques Ultra permettent de changer dynamiquement les performances des disques SSD en fonction de vos charges de travail, sans avoir à redémarrer les machines virtuelles. Les disques Ultra sont adaptés aux charges de travail qui consomment beaucoup de données, par exemple SAP HANA, les bases de données de niveau supérieur et les charges de travail avec un grand nombre de transactions.

## <a name="check-if-your-subscription-has-access"></a>Vérifier si votre abonnement dispose d’un accès

Si vous vous êtes déjà inscrit aux disques Ultra et que vous souhaitez vérifier si votre abonnement est activé pour les disques Ultra, utilisez l’une des commandes suivantes : 

Interface CLI : `az feature show --namespace Microsoft.Compute --name UltraSSD`

PowerShell : `Get-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName UltraSSD`

Si votre abonnement est activé, le résultat obtenu doit ressembler à ceci :

```bash
{
  "id": "/subscriptions/<yoursubID>/providers/Microsoft.Features/providers/Microsoft.Compute/features/UltraSSD",
  "name": "Microsoft.Compute/UltraSSD",
  "properties": {
    "state": "Registered"
  },
  "type": "Microsoft.Features/providers/features"
}
```

## <a name="determine-your-availability-zone"></a>Déterminer votre zone de disponibilité

Une fois approuvé, vous devez déterminer dans quelle zone de disponibilité vous vous trouvez, afin d’utiliser des disques Ultra. Exécutez l’une des commandes suivantes pour déterminer la zone dans laquelle déployer votre disque Ultra et veillez à remplacer d’abord les valeurs **region**, **vmSize** et **subscription** :

Interface CLI :

```bash
$subscription = "<yourSubID>"
$region = "<yourLocation>, example value is southeastasia"
$vmSize = "<yourVMSize>, example value is Standard_E64s_v3"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell :

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

La réponse aura un format similaire au suivant, où X correspond à la zone à utiliser pour le déploiement dans la région que vous avez choisie. X peut être 1, 2 ou 3. Actuellement, seules trois régions prennent en charge les disques Ultra : USA Est 2, Asie Sud-Est et Europe Nord.

Conservez la valeur **Zones**, qui représente votre zone de disponibilité. Vous en aurez besoin pour déployer un disque Ultra.

|ResourceType  |Nom  |Location  |Zones  |Restriction  |Fonctionnalité  |Valeur  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Si la commande n’a renvoyé aucune réponse, cela signifie que votre inscription à la fonctionnalité est toujours en attente ou que vous utilisez une ancienne version de l’interface CLI ou de PowerShell.

Maintenant que vous savez quelle zone déployer, suivez les étapes de déploiement décrites dans cet article pour déployer une machine virtuelle avec un disque Ultra attaché ou attacher un disque Ultra à une machine virtuelle existante.

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Déployer un disque Ultra à l’aide d’Azure Resource Manager

Tout d’abord, déterminez la taille de la machine virtuelle à déployer. Pour le moment, seules les familles de machines virtuelles DsV3 et EsV3 prennent en charge les disques Ultra. Pour plus d’informations sur les tailles de machine virtuelle, reportez-vous au deuxième tableau de ce [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

Pour créer une machine virtuelle avec plusieurs disques Ultra, consultez l’exemple [Créer une machine virtuelle avec plusieurs disques Ultra](https://aka.ms/UltraSSDTemplate).

Si vous avez l’intention d’utiliser votre propre modèle, assurez-vous que la valeur **apiVersion** pour `Microsoft.Compute/virtualMachines` et `Microsoft.Compute/Disks` est définie sur `2018-06-01` (ou version ultérieure).

Définissez la référence SKU du disque sur **UltraSSD_LRS**, puis définissez la capacité du disque, le nombre d’IOPS, la zone de disponibilité et le début (en Mbits/s) pour créer votre disque Ultra.

Une fois que la machine virtuelle est provisionnée, vous pouvez partitionner et formater les disques de données, puis les configurer pour vos charges de travail.

## <a name="deploy-an-ultra-disk-using-cli"></a>Déployer un disque Ultra via l’interface CLI

Tout d’abord, déterminez la taille de la machine virtuelle à déployer. Pour le moment, seules les familles de machines virtuelles DsV3 et EsV3 prennent en charge les disques Ultra. Pour plus d’informations sur les tailles de machine virtuelle, reportez-vous au deuxième tableau de ce [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

Pour attacher un disque Ultra, vous devez créer une machine virtuelle capable d’utiliser des disques Ultra.

Remplacez ou définissez les variables **$vmname**, **$rgname**, **$diskname**, **$location**, **$password**, **$user** par votre propres valeurs. Définissez **$zone** sur la valeur de votre zone de disponibilité, que vous avez obtenue grâce à la procédure décrite au [début de cet article](#determine-your-availability-zone). Ensuite, exécutez la commande CLI suivante pour créer une machine virtuelle capable de gérer les disques SSD Ultra :

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>Créer un disque Ultra via l’interface CLI

Maintenant que vous disposez d’une machine virtuelle capable d’attacher des disques Ultra, vous pouvez créer un disque Ultra et l’attacher à cette machine.

```azurecli-interactive
$location="eastus2"
$subscription="xxx"
$rgname="ultraRG"
$diskname="ssd1"
$vmname="ultravm1"
$zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Attacher un disque Ultra à une machine virtuelle via l’interface CLI

Si votre machine virtuelle se trouve dans une région/zone de disponibilité capable d’utiliser les disques Ultra, vous pouvez utiliser des disques Ultra sans avoir à créer de machine virtuelle.

```bash
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Ajuster les performances d’un disque Ultra via l’interface CLI

Les disques Ultra offrent une capacité unique qui vous permet d’ajuster leurs performances. La commande suivante illustre l’utilisation de cette fonctionnalité :

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Déployer un disque Ultra à l’aide de PowerShell

Tout d’abord, déterminez la taille de la machine virtuelle à déployer. Pour le moment, seules les familles de machines virtuelles DsV3 et EsV3 prennent en charge les disques Ultra. Pour plus d’informations sur les tailles de machine virtuelle, reportez-vous au deuxième tableau de ce [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

Pour pouvoir utiliser des disques Ultra, vous devez créer une machine virtuelle capable d’utiliser ce type de disque. Remplacez ou définissez les variables **$resourcegroup** et **$vmName** par vos propres valeurs. Définissez **$zone** sur la valeur de votre zone de disponibilité, que vous avez obtenue grâce à la procédure décrite au [début de cet article](#determine-your-availability-zone). Ensuite, exécutez la commande [New-AzVm](/powershell/module/az.compute/new-azvm) suivante pour créer une machine virtuelle capable de gérer les disques SSD Ultra :

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-an-ultra-disk-using-powershell"></a>Créer un disque Ultra à l’aide de PowerShell

Maintenant que vous disposez d’une machine virtuelle capable d’utiliser des disques Ultra, vous pouvez créer un disque Ultra et l’attacher à cette machine :

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Attacher un disque Ultra à une machine virtuelle à l’aide de PowerShell

Si votre machine virtuelle se trouve dans une région/zone de disponibilité capable d’utiliser les disques Ultra, vous pouvez utiliser des disques Ultra sans avoir à créer de machine virtuelle.

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Ajuster les performances d’un disque Ultra à l’aide de PowerShell

Les disques Ultra présentent une capacité unique, qui vous permet d’ajuster leurs performances. L’exemple de commande suivant ajuste les performances sans nécessiter le détachement du disque :

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez essayer ce nouveau type de disque, [demandez l’accès via cette enquête](https://aka.ms/UltraDiskSignup).