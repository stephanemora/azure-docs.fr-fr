---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7515c061467419412608bb8103136791845ae093
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133545"
---
# <a name="enable-and-deploy-azure-ultra-ssds-preview"></a>Activer et déployer des disques Ultra SSD d’Azure (préversion)

Les disques Ultra SSD d’Azure (préversion) fournissent un stockage de disque avec un haut débit, un nombre d’IOPS élevé et une faible latence homogène pour les machines virtuelles IaaS Azure. Cette nouvelle offre fournit des performances optimales aux mêmes niveaux de disponibilité que nos offres de disques existantes. Entre autres avantages, la technologie Ultra SSD permet de changer dynamiquement les performances de disques SSD en fonction de vos charges de travail et ce, sans avoir à redémarrer les machines virtuelles. Les disques Ultra SSD sont adaptés aux charges de travail qui consomment beaucoup de données, comme SAP HANA, les bases de données de niveau supérieur et les charges de travail avec un grand nombre de transactions.

Actuellement, cette technologie est en préversion et vous devez vous [inscrire](https://aka.ms/UltraSSDPreviewSignUp) pour pouvoir y accéder.

## <a name="determine-your-availability-zone"></a>Déterminer votre zone de disponibilité

Une fois approuvé, vous devez déterminer dans quelle zone de disponibilité vous vous trouvez, afin de pouvoir utiliser des disques Ultra SSD. Exécutez l’une des commandes suivantes pour déterminer dans quelle zone de la région USA Est 2 vous devez déployer votre disque SSD Ultra :

PowerShell : `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

Interface CLI : `az vm list-skus --resource-type disks --query "[?name=='UltraSSD_LRS'].locationInfo"`

La réponse est similaire au formulaire ci-dessous, dans lequel X correspond à la zone à choisir pour le déploiement dans la région USA Est 2. X peut être 1, 2 ou 3.

Conservez la valeur **Zones**, qui représente votre zone de disponibilité. Vous en aurez besoin pour déployer un disque Ultra SSD.

|ResourceType  |Nom  |Lieu  |Zones  |Restriction  |Fonctionnalité  |Valeur  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Si la commande n’a renvoyé aucune réponse, cela signifie que votre inscription à la préversion est toujours en attente ou qu’elle n’a pas encore été approuvée.

Maintenant que vous connaissez la zone dans laquelle effectuer le déploiement, suivez les étapes de déploiement de cet article pour déployer vos premières machines virtuelles avec des disques Ultra SSD.

## <a name="deploy-an-ultra-ssd-using-azure-resource-manager"></a>Déployer un disque Ultra SSD avec Azure Resource Manager

Tout d’abord, déterminez la taille de la machine virtuelle à déployer. Dans le cadre de cette préversion, uniquement les familles de machines virtuelles DsV3 et EsV3 sont prises en charge. Pour plus d’informations sur les tailles de machine virtuelle, reportez-vous au deuxième tableau de ce [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

Pour créer une machine virtuelle avec plusieurs disques Ultra SSD, consultez l’exemple de [création d’une machine virtuelle avec plusieurs disques Ultra SSD](https://aka.ms/UltraSSDTemplate).

Si vous avez l’intention d’utiliser votre propre modèle, assurez-vous que la valeur **apiVersion** pour `Microsoft.Compute/virtualMachines` et `Microsoft.Compute/Disks` est définie sur `2018-06-01` (ou version ultérieure).

Définissez la référence SKU du disque sur **UltraSSD_LRS**, puis définissez la capacité du disque, le nombre d’IOPS, la zone de disponibilité et le début (en Mbits/s) pour créer votre disque Ultra.

Une fois que la machine virtuelle est provisionnée, vous pouvez partitionner et formater les disques de données, puis les configurer pour vos charges de travail.

## <a name="deploy-an-ultra-ssd-using-cli"></a>Déployer un disque SSD Ultra à l’aide de CLI

Tout d’abord, déterminez la taille de la machine virtuelle à déployer. Dans le cadre de cette préversion, uniquement les familles de machines virtuelles DsV3 et EsV3 sont prises en charge. Pour plus d’informations sur les tailles de machine virtuelle, reportez-vous au deuxième tableau de ce [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

Pour pouvoir utiliser des disques SSD Ultra, vous devez créer une machine virtuelle capable d’utiliser ce type de disque.

Remplacez ou définissez les variables **$vmname**, **$rgname**, **$diskname**, **$location**, **$password**, **$user** par votre propres valeurs. Définissez **$zone** sur la valeur de votre zone de disponibilité, que vous avez obtenue grâce à la procédure décrite au [début de cet article](#determine-your-availability-zone). Ensuite, exécutez la commande CLI suivante pour créer une machine virtuelle capable de gérer les disques SSD Ultra :

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled --zone $zone --authentication-type password --admin-password $password --admin-username $user --attach-data-disks $diskname --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-ssd-using-cli"></a>Créer un disque SSD Ultra à l’aide de CLI

Maintenant que vous avez créé une machine virtuelle capable d’utiliser des disques SSD Ultra, vous pouvez créer et associer un disque SSD Ultra à cette machine.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an Ultra SSD disk
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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-cli"></a>Ajuster les performances d’un disque SSD Ultra à l’aide de CLI

Les disques SSDs Ultra offrent une fonctionnalité unique, qui vous permet d’ajuster leurs performances. La commande suivante illustre l’utilisation de cette fonctionnalité :

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-ssd-using-powershell"></a>Déployer un disque SSD Ultra à l’aide de PowerShell

Tout d’abord, déterminez la taille de la machine virtuelle à déployer. Dans le cadre de cette préversion, uniquement les familles de machines virtuelles DsV3 et EsV3 sont prises en charge. Pour plus d’informations sur les tailles de machine virtuelle, reportez-vous au deuxième tableau de ce [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

Pour pouvoir utiliser des disques SSD Ultra, vous devez créer une machine virtuelle capable d’utiliser ce type de disque. Remplacez ou définissez les variables **$resourcegroup** et **$vmName** par vos propres valeurs. Définissez **$zone** sur la valeur de votre zone de disponibilité, que vous avez obtenue grâce à la procédure décrite au [début de cet article](#determine-your-availability-zone). Ensuite, exécutez la commande [New-AzVm](/powershell/module/az.compute/new-azvm) suivante pour créer une machine virtuelle capable de gérer les disques SSD Ultra :

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

### <a name="create-an-ultra-ssd-using-powershell"></a>Créer un disque SSD Ultra à l’aide de PowerShell

Maintenant que vous avez créé une machine virtuelle capable d’utiliser des disques SSD Ultra, vous pouvez créer et associer un disque SSD Ultra à cette machine :

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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-powershell"></a>Ajuster les performances d’un disque SSD Ultra à l’aide de PowerShell

Les disques SSD Ultra présentent une capacité unique, qui vous permet d’ajuster leurs performances. L’exemple de commande suivant ajuste les performances sans nécessiter le détachement du disque :

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez essayer ce nouveau type de disque, [demandez l’accès à la préversion via cette enquête](https://aka.ms/UltraSSDPreviewSignUp).