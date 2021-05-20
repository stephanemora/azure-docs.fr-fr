---
title: Déployer un cluster managé Service Fabric avec des types de nœud sans état
description: Découvrez comment créer et déployer des types de nœuds sans état dans des clusters managés Service Fabric.
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: a1ea4a9d37ce26ac90c2cbae0420e4dbf8797ee2
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109689282"
---
# <a name="deploy-a-service-fabric-managed-cluster-with-stateless-node-types"></a>Déployer un cluster managé Service Fabric avec des types de nœud sans état

Les types de nœuds Service Fabric viennent avec l’hypothèse inhérente qu’à un moment donné, des services avec état peuvent être placés sur les nœuds. Les types de nœud sans état assouplissent cette hypothèse pour un type de nœud. Si vous assouplissez cette hypothèse, les types de nœuds sans état de nœud bénéficient d’opérations de scale-out plus rapides en supprimant certaines des restrictions relatives aux opérations de réparation et de maintenance.

* Les types de nœuds principaux ne peuvent pas être configurés pour être sans état
* Les types de nœud sans état nécessitent la version d’API **2021-05-01** ou ultérieure


Des exemples de modèles sont disponibles : [Modèle de types de nœuds sans état Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enable-stateless-node-types-in-a-service-fabric-managed-cluster"></a>Activer des types de nœud sans état dans un cluster managé Service Fabric
Pour définir un ou plusieurs types de nœuds comme sans état dans une ressource de type de nœud, définissez la propriété **isStateless** sur **true**. Lors du déploiement d’un cluster Service Fabric avec des types de nœud sans État, vous devez disposer d’au moins un type de nœud principal, qui n’est pas sans état dans le cluster.

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
                "isStateless": true,
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

## <a name="configure-stateless-node-types-with-multiple-availability-zones"></a>Configurer des types de nœud sans état avec plusieurs zones de disponibilité
Pour configurer un type de nœud sans état qui s’étend sur plusieurs zones de disponibilité, suivez [Clusters Service Fabric dans des zones de disponibilité](.\service-fabric-cross-availability-zones.md). 

>[!NOTE]
> La propriété de résilience zonale doit être définie au niveau du cluster et ne peut pas être modifiée sur place.

## <a name="migrate-to-using-stateless-node-types-in-a-cluster"></a>Migrer vers l’utilisation de types de nœud sans état dans un cluster
Pour tous les scénarios de migration, un nouveau type de nœud sans état doit être ajouté. Il est impossible de migrer un type de nœud existant pour qu’il soit sans état. Vous pouvez ajouter un nouveau type de nœud sans état à un cluster managé Service Fabric existant et supprimer les types de nœuds originaux du cluster. 

## <a name="next-steps"></a>Étapes suivantes 

Pour plus d’informations sur les clusters Service Fabric managé, consultez :

> [!div class="nextstepaction"]
> [Questions fréquentes (FAQ) sur les clusters Service Fabric managés](./faq-managed-cluster.md)