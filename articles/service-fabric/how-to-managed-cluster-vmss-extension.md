---
title: Ajouter une extension de groupe de machines virtuelles identiques à un type de nœud de cluster managé Service Fabric
description: Voici comment ajouter une extension de groupe de machines virtuelles identiques à un type de nœud de cluster managé Service Fabric
ms.topic: article
ms.date: 5/10/2021
ms.openlocfilehash: 0c0aac8d7804caeb6c08657b1ed36c45958a0ea5
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113033692"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type"></a>Ajouter une extension de groupe de machines virtuelles identiques à un type de nœud de cluster managé Service Fabric

Chaque type de nœud d’un cluster managé Service Fabric est adossé à un groupe de machines virtuelles identiques. Cela vous permet d’ajouter des [extensions de groupe de machines virtuelles identiques](../virtual-machines/extensions/overview.md) à vos types de nœuds de cluster managé Service Fabric.

Vous pouvez ajouter une extension de groupe de machines virtuelles identiques à un type de nœud à l’aide de la cmdlet PowerShell [Add-AzServiceFabricManagedNodeTypeVMExtension](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension).

Vous pouvez également ajouter une extension de groupe de machines virtuelles identiques sur un type de nœud de cluster managé Service Fabric dans votre modèle Azure Resource Manager, par exemple :

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

Pour plus d’informations sur la configuration de types de nœuds de cluster managé Service Fabric, consultez [Type de nœud de cluster managé](/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les clusters Service Fabric managé, consultez :

> [!div class="nextstepaction"]
> [Questions fréquentes (FAQ) sur les clusters Service Fabric managés](./faq-managed-cluster.yml)
