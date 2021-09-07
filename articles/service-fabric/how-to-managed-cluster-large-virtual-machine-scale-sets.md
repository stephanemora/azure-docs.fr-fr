---
title: Configurer un type de nœud secondaire pour des groupes de machines virtuelles identiques de grande taille sur un cluster géré par Service Fabric
description: Cet article explique comment configurer un type de nœud secondaire en tant que grand groupe de machines virtuelles identiques
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: 5b4601c347abcecb06e394f645c21141994681fc
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867804"
---
# <a name="service-fabric-managed-cluster-node-type-scaling"></a>Mise à l’échelle d’un type de nœud de cluster géré par Service Fabric

Chaque type de nœud d’un cluster managé Service Fabric est adossé à un groupe de machines virtuelles identiques. Pour permettre aux types de nœuds de cluster gérés de créer des [groupes de machines virtuelles identiques de grande taille](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md), une propriété `multiplePlacementGroups` a été ajoutée à la définition de type de nœud. Par défaut, les types de nœuds de cluster gérés définissent cette propriété sur false pour maintenir la cohérence des domaines d’erreur et de mise à niveau au sein d’un groupe de placement, mais ce paramètre limite la mise à l’échelle d’un type de nœud au-delà de 100 machines virtuelles. Pour déterminer si votre application peut utiliser efficacement des groupes identiques de grande taille, consultez [cette liste d’exigences](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md#checklist-for-using-large-scale-sets).

Étant donné que le fournisseur de ressources de cluster géré par Azure Service Fabric orchestre la mise à l’échelle et utilise des disques managés pour les données, nous pouvons prendre en charge des groupes identiques de grande taille pour les types de nœuds secondaires avec et sans état.

> [!NOTE]
> Il n’est pas possible de modifier cette propriété après le déploiement d’un type de nœud.

## <a name="enable-large-virtual-machine-scale-sets-in-a-service-fabric-managed-cluster"></a>Activer des groupes de machines virtuelles identiques de grande taille dans un cluster géré par Service Fabric
Pour configurer un type de nœud secondaire en tant que groupe identique de grande taille, définissez la propriété **multiplePlacementGroups** sur **true**.
> [!NOTE]
> Cette propriété ne peut pas être définie sur le type de nœud principal.

* L’apiVersion de la ressource de cluster managé Service Fabric doit être **2021-05-01** ou ultérieure.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "dependsOn": [
              "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
            ],
            "properties": {
                "multiplePlacementGroups": true,
                "isPrimary": false,
                "vmImagePublisher": "[parameters('vmImagePublisher')]",
                "vmImageOffer": "[parameters('vmImageOffer')]",
                "vmImageSku": "[parameters('vmImageSku')]",
                "vmImageVersion": "[parameters('vmImageVersion')]",
                "vmSize": "[parameters('nodeTypeSize')]",
                "vmInstanceCount": "[parameters('nodeTypeVmInstanceCount')]",
                "dataDiskSizeGB": "[parameters('nodeTypeDataDiskSizeGB')]"
            }
        }
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déployer une application sur un cluster Service Fabric managé](./tutorial-managed-cluster-deploy-app.md)
