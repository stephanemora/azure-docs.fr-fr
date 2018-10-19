---
title: Fichier Include
description: Fichier Include
services: storage
author: jboeshart
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: jaboes
ms.custom: include file
ms.openlocfilehash: c7db8eaf57bf29e17b4543e99a44655030aa6172
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45979666"
---
# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Utilisation de disques gérés dans les modèles Azure Resource Manager

Ce document décrit les différences entre les disques gérés et les disques non gérés lorsque vous utilisez des modèles Azure Resource Manager pour configurer des machines virtuelles. Ces exemples vous permettent de mettre à jour les modèles existants qui utilisent des disques non managés en les remplaçant par des disques managés. Pour référence, nous utilisons le modèle [101-vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) comme guide. Vous pouvez consulter le modèle utilisant des [disques gérés](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) et une version antérieure utilisant des [disques non gérés](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) si vous voulez les comparer directement.

## <a name="unmanaged-disks-template-formatting"></a>Mise en forme de modèle de disques non gérés

Pour commencer, nous nous intéressons à la façon dont les disques non managés sont déployés. Lorsque vous créez des disques non gérés, vous avez besoin d’un compte de stockage pour héberger les fichiers de disque dur virtuel. Vous pouvez créer un compte de stockage ou en utiliser un existant. Cet article vous explique comment créer un compte de stockage. Pour ce faire, créez une ressource de compte de stockage dans le bloc de ressources, comme indiqué ci-dessous.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2016-01-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

Dans l’objet de machine virtuelle, ajoutez une dépendance sur le compte de stockage afin de garantir qu’il est créé avant la machine virtuelle. Dans la section `storageProfile`, indiquez l’URI complet de l’emplacement du disque dur virtuel, qui fait référence au compte de stockage et qui est nécessaire pour le disque du système d’exploitation et les disques de données.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Mise en forme de modèle de disques gérés

Avec Azure Managed Disks, le disque devient une ressource de niveau supérieur et ne requiert plus qu’un compte de stockage soit créé par l’utilisateur. Les disques gérés ont d’abord été exposés dans la version de l’API `2016-04-30-preview`. Ils sont disponibles dans toutes les versions ultérieures de l’API et correspondent désormais au type de disque par défaut. Les sections suivantes abordent les paramètres par défaut et décrivent en détail comment personnaliser davantage vos disques.

> [!NOTE]
> Il est recommandé d’utiliser une version d’API plus récente que `2016-04-30-preview` en raison de modifications intervenues entre les versions `2016-04-30-preview` et `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Paramètres de disque géré par défaut

Pour créer une machine virtuelle avec des disques gérés, vous n’avez plus besoin créer la ressource de compte de stockage et vous pouvez mettre à jour votre ressource de machine virtuelle comme suit. Notez en particulier que `apiVersion` reflète `2017-03-30`, et que `osDisk` et `dataDisks` ne font plus référence à un URI spécifique pour le disque dur virtuel. Lors d’un déploiement sans spécification de propriétés supplémentaires, le disque utilise un type de stockage qui dépend de la taille de la machine virtuelle. Par exemple, si vous utilisez une taille de machine virtuelle compatible Premium (tailles avec « s » dans leur nom, comme Standard_D2s_v3), le système utilise le stockage Premium_LRS. Pour spécifier un type de stockage, utilisez le paramètre de référence (SKU) du disque. Si aucun nom n’est spécifié, il prend le format `<VMName>_OsDisk_1_<randomstring>` pour le disque de système d’exploitation et `<VMName>_disk<#>_<randomstring>` pour chaque disque de données. Par défaut, le chiffrement de disque Azure est désactivé ; la mise en cache est Lecture/Écriture pour le disque de système d’exploitation et Aucune pour les disques de données. Vous avez pu remarquer dans l’exemple ci-dessous qu’il existe toujours une dépendance de compte de stockage, bien que cela concerne uniquement le stockage de diagnostics et n’est pas nécessaire pour le stockage de disques.

```json
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Utilisation d’une ressource de disque géré de niveau supérieur

Au lieu de spécifier la configuration du disque dans l’objet de machine virtuelle, vous pouvez créer une ressource de disque de niveau supérieur et l’attacher dans le cadre de la création de la machine virtuelle. Par exemple, vous pouvez créer une ressource de disque comme suit pour l’utiliser comme disque de données.

```json
{
    "type": "Microsoft.Compute/disks",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "apiVersion": "2017-03-30",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

Dans l’objet de machine virtuelle, faites référence à l’objet de disque devant être attaché. Spécifier l’ID de ressource du disque managé créé dans la propriété `managedDisk` permet d’attacher le disque lors de la création de la machine virtuelle. La propriété `apiVersion` de la ressource de la machine virtuelle est définie sur `2017-03-30`. Une dépendance sur la ressource de disque est créée pour garantir qu’elle est correctement créée avant la création de la machine virtuelle. 

```json
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Créer des groupes à haute disponibilité gérés avec des machines virtuelles à l’aide de disques gérés

Pour créer des groupes à haute disponibilité gérés avec des machines virtuelles à l’aide de disques gérés, ajoutez l’objet `sku` à la ressource de groupe à haute disponibilité et définissez la propriété `name` sur `Aligned`. Cette propriété garantit que les disques de chaque machine virtuelle sont suffisamment isolés les uns des autres pour éviter les points uniques de défaillance. Vous pouvez aussi remarquer que la propriété `apiVersion` de la ressource de groupe à haute disponibilité est définie sur `2017-03-30`.

```json
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/availabilitySets",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="standard-ssd-disks"></a>Disques SSD Standard

Les paramètres suivants sont nécessaires dans le modèle Resource Manager pour créer des disques SSD Standard :

* *apiVersion* pour Microsoft.Compute doit être défini sur `2018-04-01` (ou version ultérieure)
* Pour *managedDisk.storageAccountType*, indiquez `StandardSSD_LRS`.

L’exemple suivant illustre la section *properties.storageProfile.osDisk* d’une machine virtuelle qui utilise des disques SSD Standard :

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Si vous souhaitez un exemple de modèle complet de création d’un disque SSD Standard, consultez [Créer une machine virtuelle à partir d’une image Windows avec des disques de données SSD Standard](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Personnalisations et scénarios supplémentaires

Pour obtenir des informations complètes sur les spécifications de l’API REST, veuillez consulter la [documentation de l’API REST portant sur la création d’un disque géré](/rest/api/manageddisks/disks/disks-create-or-update). Vous trouverez d’autres scénarios, ainsi que des valeurs par défaut et acceptables qui peuvent être envoyées à l’API via des déploiements de modèle. 

## <a name="next-steps"></a>Étapes suivantes

* Pour des modèles complets qui utilisent des disques gérés, consultez les liens suivants du référentiel de démarrage rapide Azure.
    * [Machine virtuelle Windows avec disques gérés](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Machine virtuelle Linux avec disques gérés](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [Liste complète des modèles de disque géré](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* Consultez le document [Vue d’ensemble d’Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md) pour en savoir plus sur les disques gérés.
* Passez en revue la documentation de référence sur les modèles pour les ressources de machine virtuelle en consultant le document [de référence sur le modèle Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines).
* Passez en revue la documentation de référence sur les modèles pour les ressources de disque en consultant le document [de référence sur le modèle Microsoft.Compute/disks](/azure/templates/microsoft.compute/disks).
* Pour plus d’informations sur l’utilisation de disques managés dans des groupes de machines virtuelles Azure identiques, consultez le document [Utiliser des disques de données avec des groupes identiques](https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks).
