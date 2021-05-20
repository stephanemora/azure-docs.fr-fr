---
title: Ajouter une extension de groupe de machines virtuelles identiques à un type de nœud de cluster managé Service Fabric
description: Voici comment ajouter une extension de groupe de machines virtuelles identiques à un type de nœud de cluster managé Service Fabric
ms.topic: article
ms.date: 5/10/2021
ms.openlocfilehash: 9aaddd912407849133a115116dd769d2090767dd
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109683404"
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
> [Questions fréquentes (FAQ) sur les clusters Service Fabric managés](./faq-managed-cluster.md)
