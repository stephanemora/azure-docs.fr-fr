---
title: Groupes de machines virtuelles identiques Azure - Disques de données associés
description: Découvrez comment utiliser des disques de données associés à des groupes de machines virtuelles identiques grâce à la présentation de cas d’utilisation spécifiques.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 4/25/2017
ms.author: manayar
ms.openlocfilehash: c7fd4d89fcc66fb4110029be45ad94e21faea0e0
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278167"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Groupes de machines virtuelles identiques Azure et disques de données associés
Pour développer votre espace de stockage, les [groupes de machines virtuelles identiques](/azure/virtual-machine-scale-sets/) Azure prennent en charge les instances de machine virtuelle avec des disques de données associés. Vous pouvez associer des disques de données lorsque le groupe identique est créé ou sur un groupe identique existant.

> [!NOTE]
> Lorsque vous créez un groupe identique avec des disques de données associés, vous devez monter et formater les disques à partir d’une machine virtuelle pour les utiliser (comme pour les machines virtuelles Azure autonomes). Une méthode pratique pour effectuer ce processus consiste à utiliser une extension de script personnalisé qui appelle un script pour partitionner et formater tous les disques de données sur une machine virtuelle. Pour obtenir des exemples, consultez [Azure CLI](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Créer et gérer des disques dans un groupe identique
Pour plus d’informations sur la création d’un groupe identique avec des disques de données associés, préparer et formater ou ajouter et supprimer des disques de données, consultez l’un des didacticiels suivants :

- [Azure CLI](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

Le reste de cet article décrit les cas d’usage spécifiques tels que les clusters Service Fabric qui nécessitent des disques de données, ou l’association de disques de données existants à du contenu pour un groupe identique.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Créer un cluster Service Fabric avec des disques de données associés
Chaque [type de nœud](../service-fabric/service-fabric-cluster-nodetypes.md) d’un cluster [Service Fabric](/azure/service-fabric) exécuté dans Azure est alimenté par un groupe de machines virtuelles identiques. Avec un modèle Azure Resource Manager, vous pouvez associer des disques de données aux groupes identiques constituant le cluster Service Fabric. Vous pouvez utiliser un [modèle existant](https://github.com/Azure-Samples/service-fabric-cluster-templates) comme point de départ. Dans le modèle, incluez une section _dataDisks_ dans l’élément _storageProfile_ des ressources _Microsoft.Compute/virtualMachineScaleSets_, puis déployez-le. Dans l’exemple suivant, un disque de données de 128 Go est associé :

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

Vous pouvez automatiquement partitionner, formater et monter les disques de données lors du déploiement du cluster. Ajoutez une extension de script personnalisé à l’élément _extensionProfile_ de l’instance _virtualMachineProfile_ du ou des groupes de machines.

Pour préparer automatiquement les disques de données dans un cluster Windows, ajoutez le contenu suivant :

```json
{
    "name": "customScript",
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Pour préparer automatiquement les disques de données dans un cluster Linux, ajoutez le contenu suivant :
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>Ajout de disques de données préremplis dans un groupe identique existant
Les disques de données spécifiés dans le modèle de groupe identique sont toujours vides. Toutefois, vous pouvez attacher un disque de données existant à une machine virtuelle spécifique dans un groupe identique. Cette fonctionnalité est disponible en préversion, et des exemples sont présentés sur [GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk). Si vous souhaitez propager les données sur toutes les machines virtuelles du groupe identique, vous pouvez dupliquer votre disque de données et l’attacher à chaque machine virtuelle du groupe identique, vous pouvez créer une image personnalisée qui contient les données et configurer le groupe identique à partir de cette image personnalisée, ou vous pouvez utiliser Azure Files ou une offre de stockage de données similaire.


## <a name="additional-notes"></a>Remarques supplémentaires
La prise en charge des disques managés Azure et des disques de données associés de groupe identique ests disponible dans les versions [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) et ultérieures de l’APi Microsoft.Compute.API.

La prise en charge du portail Azure pour les disques de données associés dans des groupes identiques est initialement limitée. Selon vos besoins, vous pouvez utiliser des modèles Azure, la CLI, PowerShell, des kits de développement logiciel (SDK) et l’API REST pour gérer les disques associés.


