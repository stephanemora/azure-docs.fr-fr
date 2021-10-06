---
title: Ajouter une extension de groupe de machines virtuelles identiques à un type de nœud de cluster managé Service Fabric
description: Voici comment ajouter une extension de groupe de machines virtuelles identiques à un type de nœud de cluster managé Service Fabric
ms.topic: article
ms.date: 8/02/2021
ms.openlocfilehash: 3b0f15f2f83ddad0b8251642fc63db79581a5a97
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547054"
---
# <a name="virtual-machine-scale-set-extension-support-on-service-fabric-managed-cluster-node-types"></a>Prise en charge de l’extension de groupe de machines virtuelles identiques sur un ou des types de nœuds de cluster managé Service Fabric

Chaque type de nœud d’un cluster managé Service Fabric est adossé à un groupe de machines virtuelles identiques. Cela vous permet d’ajouter des [extensions de groupe de machines virtuelles identiques](../virtual-machines/extensions/overview.md) à vos types de nœuds de cluster managé Service Fabric. Les extensions sont de petites applications qui proposent une configuration post-déploiement et une automatisation sur les machines virtuelles Azure. La plateforme Azure héberge de nombreuses extensions, notamment des applications de configuration, de surveillance, de sécurité et utilitaires de machine virtuelle. Les éditeurs prennent une application, l’enveloppent dans une extension et simplifient l’installation. Il vous suffit de définir les paramètres obligatoires. 

## <a name="add-a-virtual-machine-scale-set-extension"></a>Ajouter une extension de groupe de machines virtuelles identiques
Vous pouvez ajouter une extension de groupe de machines virtuelles identiques à un type de nœud de cluster managé Service Fabric à l’aide de la commande PowerShell [Add-AzServiceFabricManagedNodeTypeVMExtension](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension).

Vous pouvez également ajouter une extension de groupe de machines virtuelles identiques sur un type de nœud de cluster managé Service Fabric dans votre modèle Azure Resource Manager, par exemple :

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
    "vmExtensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "type": "KeyVaultForWindows",
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "settings": {
          }
        }
      }
    ]
  }
}
```

Pour plus d’informations sur la configuration de types de nœuds de cluster managé Service Fabric, consultez [Type de nœud de cluster managé](/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les clusters Service Fabric managé, consultez :

> [!div class="nextstepaction"]
> [Questions fréquentes (FAQ) sur les clusters Service Fabric managés](./faq-managed-cluster.yml)
