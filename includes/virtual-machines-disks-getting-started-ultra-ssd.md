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
ms.openlocfilehash: 495326c172f900dc8bcff78b0df38f2cb64ed27e
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546545"
---
# <a name="enable-and-deploy-azure-ultra-ssds-preview"></a>Activez et déployez SSDs ultra Azure (version préliminaire)

Les lecteurs SSD ultra Azure (SSD) (version préliminaire) offre un débit élevé, e/s élevé et une latence faible homogène stockage sur disque pour machines virtuelles (VM) Azure IaaS. Cette nouvelle offre fournit des performances optimales aux mêmes niveaux de disponibilité que nos offres de disques existantes. Un des avantages majeurs de SSDs ultra sont la possibilité de modifier dynamiquement les performances du disque SSD, ainsi que vos charges de travail sans avoir à redémarrer vos machines virtuelles. SSDs ultra sont adaptées aux charges de travail gourmandes en données telles que SAP HANA, les bases de données de niveau supérieur et les charges de travail nécessitant beaucoup de la transaction.

Actuellement, SSDs ultra sont en version préliminaire et vous devez [inscrire](https://aka.ms/UltraSSDPreviewSignUp) dans la version d’évaluation afin d’y accéder.

## <a name="determine-your-availability-zone"></a>Déterminer votre zone de disponibilité

Une fois approuvé, vous devez déterminer quelle zone de disponibilité vous vous trouvez dans, afin d’utiliser des disques SSD ultra. Exécutez une des commandes suivantes pour déterminer la zone dans laquelle dans l’est des États-Unis 2 pour déployer votre disque ultra pour :

PowerShell : `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

Interface CLI : `az vm list-skus --resource-type disks --query “[?name==UltraSSD_LRS]”`

La réponse sera similaire à celui ci-dessous, où X correspond à la zone à utiliser pour le déploiement dans l’est des États-Unis 2. X peut être 1, 2 ou 3.

Conserver le **Zones** valeur, elle représente votre zone de disponibilité et vous en aurez besoin pour déployer un disque SSD ultra.

|ResourceType  |Nom  |Lieu  |Zones  |Restriction  |Fonctionnalité  |Valeur  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

Si aucune réponse à partir de la commande, votre inscription à la fonctionnalité est toujours en attente ou pas encore approuvées.

Maintenant que vous connaissez la zone dans laquelle effectuer le déploiement, suivez les étapes de déploiement de cet article pour déployer vos premières machines virtuelles avec des disques Ultra SSD.

## <a name="deploy-an-ultra-ssd-using-azure-resource-manager"></a>Déployer un SSD ultra à l’aide d’Azure Resource Manager

Tout d’abord, déterminez la taille de machine virtuelle à déployer. Dans le cadre de cette préversion, uniquement les familles de machines virtuelles DsV3 et EsV3 sont prises en charge. Pour plus d’informations sur les tailles de machine virtuelle, reportez-vous au deuxième tableau de ce [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

Si vous souhaitez créer une machine virtuelle avec plusieurs disques SSD ultra, reportez-vous à l’exemple [créer une machine virtuelle avec plusieurs SSD ultra](https://aka.ms/UltraSSDTemplate).

Si vous avez l’intention d’utiliser votre propre modèle, assurez-vous que **apiVersion** pour `Microsoft.Compute/virtualMachines` et `Microsoft.Compute/Disks` est défini comme `2018-06-01` (ou version ultérieure).

La valeur est la référence (SKU) disque **UltraSSD_LRS**, puis définissez la capacité du disque, e/s, zone de disponibilité et un débit en Mbits/s pour créer un disque ultra.

Une fois que la machine virtuelle est provisionnée, vous pouvez partitionner et formater les disques de données, puis les configurer pour vos charges de travail.

## <a name="deploy-an-ultra-ssd-using-cli"></a>Déployer un SSD ultra à l’aide de CLI

Tout d’abord, déterminez la taille de machine virtuelle à déployer. Dans le cadre de cette préversion, uniquement les familles de machines virtuelles DsV3 et EsV3 sont prises en charge. Pour plus d’informations sur les tailles de machine virtuelle, reportez-vous au deuxième tableau de ce [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

Pour utiliser des disques SSD ultra, vous devez créer une machine virtuelle qui est capable d’utiliser des disques SSD ultra.

Remplacer ou définir le **$vmname**, **$rgname**, **$diskname**, **$location**, **$password**, **$user** variables par vos propres valeurs. Définissez **$zone** à la valeur de votre zone de disponibilité que vous avez obtenu à partir de la [le début de cet article](#determine-your-availability-zone). Puis exécutez la commande CLI suivante pour créer une machine virtuelle d’activé ultra :

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled --zone $zone --authentication-type password --admin-password $password --admin-username $user --attach-data-disks $diskname --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-ssd-using-cli"></a>Créer un SSD ultra à l’aide de CLI

Maintenant que vous avez une machine virtuelle qui est capable d’utiliser des disques SSD ultra, vous pouvez créer et attacher un disque SSD ultra à celui-ci.

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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-cli"></a>Ajuster les performances d’un SSD ultra à l’aide de CLI

SSDs ultra offrent une fonctionnalité unique qui vous permet d’ajuster leurs performances, la commande suivante illustre comment utiliser cette fonctionnalité :

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-ssd-using-powershell"></a>Déployer un SSD ultra à l’aide de PowerShell

Tout d’abord, déterminez la taille de machine virtuelle à déployer. Dans le cadre de cette préversion, uniquement les familles de machines virtuelles DsV3 et EsV3 sont prises en charge. Pour plus d’informations sur les tailles de machine virtuelle, reportez-vous au deuxième tableau de ce [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

Pour utiliser des disques SSD ultra, vous devez créer une machine virtuelle qui est capable d’utiliser des disques SSD ultra. Remplacer ou définir le **$resourcegroup** et **$vmName** variables par vos propres valeurs. Définissez **$zone** à la valeur de votre zone de disponibilité que vous avez obtenu à partir de la [le début de cet article](#determine-your-availability-zone). Puis exécutez la commande suivante [New-AzVm](/powershell/module/az.compute/new-azvm) commande pour créer une ultra activé la machine virtuelle :

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

### <a name="create-an-ultra-ssd-using-powershell"></a>Créer un SSD ultra à l’aide de PowerShell

Maintenant que vous avez une machine virtuelle qui est capable d’utiliser des disques SSD ultra, vous pouvez créer et attacher un disque SSD ultra à celui-ci :

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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-powershell"></a>Ajuster les performances d’un SSD ultra à l’aide de PowerShell

SSDs ultra ont une capacité unique qui vous permet d’ajuster leurs performances, la commande suivante est un exemple qui ajuste les performances sans avoir à détacher le disque :

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez essayer le nouveau type de disque [demander l’accès à la version préliminaire avec cette enquête](https://aka.ms/UltraSSDPreviewSignUp).