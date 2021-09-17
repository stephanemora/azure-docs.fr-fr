---
title: Activer la fonctionnalité Disques partagés pour les disques managés Azure
description: Configurer un disque managé Azure avec la fonctionnalité Disques partagés afin de pouvoir le partager avec plusieurs machines virtuelles
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/16/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 594800dcd75de3e5539873f327b5b64b81a8cb1f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698185"
---
# <a name="enable-shared-disk"></a>Activer la fonctionnalité Disques partagés

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Cet article explique comment activer la fonctionnalité Disques partagés pour les disques managés Azure. La fonctionnalité Disques partagés Azure est une nouvelle fonctionnalité pour disques managés Azure qui vous permet de connecter simultanément un disque managé à plusieurs machines virtuelles. La connexion d'un disque managé à plusieurs machines virtuelles vous permet de déployer de nouvelles applications en cluster ou de migrer des applications en cluster existantes vers Azure. 

Si vous recherchez des informations conceptuelles relatives aux disques managés sur lesquels la fonctionnalité Disques partagés est activée, consultez [Disques partagés Azure](disks-shared.md).

## <a name="prerequisites"></a>Prérequis

Les scripts et les commandes de cet article requièrent l’un des éléments suivants :

- Le module Azure PowerShell, version 6.0.0 ou ultérieure.

ou
- La version la plus récente d’Azure CLI.

## <a name="limitations"></a>Limites

[!INCLUDE [virtual-machines-disks-shared-limitations](../../includes/virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Les disques partagés prennent en charge plusieurs systèmes d’exploitation. Consultez les sections [Windows](./disks-shared.md#windows) et [Linux](./disks-shared.md#linux) de l’article conceptuel relatif aux systèmes d’exploitation pris en charge.

## <a name="disk-sizes"></a>Tailles du disque

[!INCLUDE [virtual-machines-disks-shared-sizes](../../includes/virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Déployer des disques partagés

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Déployer un disque SSD Premium en tant que disque partagé

Pour déployer un disque managé avec la fonctionnalité de disque partagé activée, utilisez la nouvelle propriété `maxShares` et définissez une valeur supérieure à 1. Cela permet de partager le disque sur plusieurs machines virtuelles.

> [!IMPORTANT]
> La valeur de `maxShares` peut être définie ou modifiée uniquement lorsqu’un disque est démonté de toutes les machines virtuelles. Consultez les [tailles de disque](#disk-sizes) afin de connaître les valeurs autorisées pour `maxShares`.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Connectez-vous au portail Azure. 
1. Recherchez et sélectionnez **Disques**.
1. Sélectionnez **+ Créer** pour créer un disque.
1. Renseignez les détails et sélectionnez une région appropriée, puis sélectionnez **Modifier la taille**.

    :::image type="content" source="media/disks-shared-enable/create-shared-disk-basics-pane.png" alt-text="Capture d’écran du volet créer un disque géré, changer la taille mise en surbrillance." lightbox="media/disks-shared-enable/create-shared-disk-basics-pane.png":::

1. Sélectionnez la taille de disque SSD Premium souhaitée, puis cliquez sur **OK**.

    :::image type="content" source="media/disks-shared-enable/select-premium-shared-disk.png" alt-text="Capture d’écran de la référence du disque, SSD Premium mis en surbrillance." lightbox="media/disks-shared-enable/select-premium-shared-disk.png":::

1. Continuez le déploiement jusqu'à ce que vous arriviez au volet **Avancé**.
1. Sélectionnez **Oui** pour **Activer le disque partagé** et sélectionnez le nombre de **partages maximum** que vous souhaitez.

    :::image type="content" source="media/disks-shared-enable/enable-premium-shared-disk.png" alt-text="Capture d’écran du volet avancé, activer le disque partagé mis en surbrillance et définir sur Oui." lightbox="media/disks-shared-enable/enable-premium-shared-disk.png":::

1. Sélectionnez **Vérifier + créer**.


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku Premium_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Modèle Resource Manager](#tab/azure-resource-manager)

Avant d’utiliser le modèle suivant, remplacez `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]` et `[parameters('maxShares')]` par vos propres valeurs.

[SSD Premium modèle de disque partagé](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-a-standard-ssd-as-a-shared-disk"></a>Déployer un disque SSD Standard en tant que disque partagé

Pour déployer un disque managé avec la fonctionnalité de disque partagé activée, utilisez la nouvelle propriété `maxShares` et définissez une valeur supérieure à 1. Cela permet de partager le disque sur plusieurs machines virtuelles.

> [!IMPORTANT]
> La valeur de `maxShares` peut être définie ou modifiée uniquement lorsqu’un disque est démonté de toutes les machines virtuelles. Consultez les [tailles de disque](#disk-sizes) afin de connaître les valeurs autorisées pour `maxShares`.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Actuellement, vous ne pouvez pas déployer un disque SSD standard partagé via le Portail Azure. Utilisez l’Azure CLI, le module Azure PowerShell ou un modèle Azure Resource Manager.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku StandardSSD_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType StandardSSD_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Modèle Resource Manager](#tab/azure-resource-manager)

Remplacez les valeurs de ce modèle de Azure Resource Manager par les vôtres, avant de l’utiliser :

```rest
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "StandardSSD_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Déployer un disque Ultra en tant que disque partagé

Pour déployer un disque managé avec la fonctionnalité de disque partagé activée, définissez le paramètre `maxShares` sur une valeur supérieure à 1. Cela permet de partager le disque sur plusieurs machines virtuelles.

> [!IMPORTANT]
> La valeur de `maxShares` peut être définie ou modifiée uniquement lorsqu’un disque est démonté de toutes les machines virtuelles. Consultez les [tailles de disque](#disk-sizes) afin de connaître les valeurs autorisées pour `maxShares`.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Connectez-vous au portail Azure. 
1. Recherchez et sélectionnez **Disques**.
1. Sélectionnez **+ Créer** pour créer un disque.
1. Remplissez les détails, puis sélectionnez **Modifier la taille**.
1. Sélectionnez le disque Ultra pour la **référence SKU de disque**.

    :::image type="content" source="media/disks-shared-enable/select-ultra-shared-disk.png" alt-text="Capture d’écran de la référence SKU de disque Ultra mise en surbrillance." lightbox="media/disks-shared-enable/select-ultra-shared-disk.png":::

1. Sélectionnez la taille de disque souhaitée, puis cliquez sur **OK**.
1. Continuez le déploiement jusqu'à ce que vous arriviez au volet **Avancé**.
1. Sélectionnez **Oui** pour **Activer le disque partagé** et sélectionnez le nombre de **partages maximum** que vous souhaitez.
1. Sélectionnez **Vérifier + créer**.

    :::image type="content" source="media/disks-shared-enable/enable-ultra-shared-disk.png" alt-text="Capture d’écran du volet avancé, activer le disque partagé mis en surbrillance." lightbox="media/disks-shared-enable/enable-ultra-shared-disk.png":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

##### <a name="regional-disk-example"></a>Exemple de disque régional

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>Exemple de disque zonale

Cet exemple est quasiment identique au précédent, sauf qu’il crée un disque dans la zone de disponibilité 1.

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>Exemple de disque régional

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>Exemple de disque zonale

Cet exemple est quasiment identique au précédent, sauf qu’il crée un disque dans la zone de disponibilité 1.

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[Modèle Resource Manager](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>Exemple de disque régional

Avant d’utiliser le modèle suivant, remplacez `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]`, `[parameters('maxShares')]`, `[parameters('diskIOPSReadWrite')]`, `[parameters('diskMBpsReadWrite')]`, `[parameters('diskIOPSReadOnly')]` et `[parameters('diskMBpsReadOnly')]` par vos propres valeurs.

[Modèle d’ultra-disques partagés régionaux](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>Exemple de disque zonale

Avant d’utiliser le modèle suivant, remplacez `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]`, `[parameters('maxShares')]`, `[parameters('diskIOPSReadWrite')]`, `[parameters('diskMBpsReadWrite')]`, `[parameters('diskIOPSReadOnly')]` et `[parameters('diskMBpsReadOnly')]` par vos propres valeurs.

[Modèle d’ultra-disques partagés zonaux](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>Utilisation de disques partagés Azure avec vos machines virtuelles

Une fois que vous avez déployé un disque partagé avec `maxShares>1`, vous pouvez monter le disque sur une ou plusieurs de vos machines virtuelles.

> [!NOTE]
> Si vous déployez un ultra-disque, assurez-vous qu’il correspond à la configuration requise. Pour plus d’informations, consultez [Utilisation de disques Ultra Azure](disks-enable-ultra-ssd.md).

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Commandes PR SCSI prises en charge

Une fois que vous avez monté le disque partagé sur vos machines virtuelles dans votre cluster, vous pouvez établir un quorum et lire/écrire sur le disque à l’aide de demandes de tirage (pull requests) SCSI. Les commandes PR suivantes sont disponibles lors de l’utilisation de disques partagés Azure :

Pour interagir avec le disque, démarrez avec la liste persistent-reservation-action :

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

Lorsque vous utilisez PR_RESERVE, PR_PREEMPT_RESERVATION ou PR_RELEASE_RESERVATION, fournissez l’un des persistent-reservation-type suivants :

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

Vous devez également fournir une clé persistent-reservation-key lorsque vous utilisez PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION ou PR_RELEASE-RESERVATION.


## <a name="next-steps"></a>Étapes suivantes

Si vous préférez utiliser des modèles d’Azure Resource Manager pour déployer votre disque, les exemples de modèles suivants sont disponibles :
- [SSD Premium](https://aka.ms/SharedPremiumDiskARMtemplate)
- [Ultra-disques régionaux](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [Ultra-disques zonaux](https://aka.ms/SharedUltraDiskARMtemplateZonal)
