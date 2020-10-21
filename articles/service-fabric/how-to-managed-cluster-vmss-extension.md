---
title: Ajouter une extension de groupe de machines virtuelles identiques à un type de nœud de cluster managé Service Fabric (préversion)
description: Voici comment ajouter une extension de groupe de machines virtuelles identiques à un type de nœud de cluster managé Service Fabric
ms.topic: article
ms.date: 09/28/2020
ms.openlocfilehash: 10487bad4fce41c68b4e2cb90c311b986d709eee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91410265"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type-preview"></a>Ajouter une extension de groupe de machines virtuelles identiques à un type de nœud de cluster managé Service Fabric (préversion)

Chaque type de nœud d’un cluster managé Service Fabric est adossé à un groupe de machines virtuelles identiques. Cela vous permet d’ajouter des [extensions de groupe de machines virtuelles identiques](../virtual-machines/extensions/overview.md) à vos types de nœuds de cluster managé Service Fabric.

Vous pouvez ajouter une extension de groupe de machines virtuelles identiques à un type de nœud à l’aide de la cmdlet PowerShell [Add-AzServiceFabricManagedNodeTypeVMExtension](https://docs.microsoft.com/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension?view=azps-4.7.0&preserve-view=true).

Vous pouvez également ajouter une extension de groupe de machines virtuelles identiques sur un type de nœud de cluster managé Service Fabric dans votre modèle Azure Resource Manager, par exemple :

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

Pour plus d’informations sur la configuration de types de nœuds de cluster managé Service Fabric, consultez [Type de nœud de cluster managé](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les clusters Service Fabric managé, consultez :

> [!div class="nextstepaction"]
> [Questions fréquentes (FAQ) sur les clusters Service Fabric managés](./faq-managed-cluster.md)
