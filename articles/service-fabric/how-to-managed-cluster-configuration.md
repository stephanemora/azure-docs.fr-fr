---
title: Configurer votre cluster Service Fabric managé (préversion)
description: Découvrez comment configurer votre cluster managé Service Fabric pour les mises à niveau automatiques du système d’exploitation, les règles de groupe de sécurité réseau et bien plus encore.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 44b1b949fe314231cb44f190c31b53903e47a904
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732630"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Options de configuration du cluster Service Fabric managé (préversion)

En plus de sélectionner la [référence SKU du cluster managé Service Fabric](overview-managed-cluster.md#service-fabric-managed-cluster-skus) lors de la création de votre cluster, il existe d’autres façons de le configurer. Dans la préversion actuelle, vous pouvez :

* Configurer les [options de mise en réseau](how-to-managed-cluster-networking.md) pour votre cluster
* Ajouter une [extension de groupe de machines virtuelles identiques](how-to-managed-cluster-vmss-extension.md) à un type de nœud
* Configurer une [identité managée](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) sur vos types de nœuds
* Activer les [mises à niveau automatiques du système d’exploitation](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) pour vos nœuds
* Activer le [chiffrement du disque de données et du système d’exploitation](how-to-enable-managed-cluster-disk-encryption.md) sur vos nœuds

## <a name="enable-automatic-os-image-upgrades"></a>Activer les mises à niveau de l’image du système d’exploitation

Vous pouvez choisir d’activer les mises à niveau automatiques de l’image du système d’exploitation vers les machines virtuelles exécutant vos nœuds de cluster managés. Bien que les ressources du groupe de machines virtuelles identiques soient managées en votre nom avec des clusters managés Service Fabric, vous avez le choix d’activer les mises à niveau automatiques de l’image du système d’exploitation pour vos nœuds de cluster. Comme avec les clusters [Service Fabric classiques](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration), les nœuds de cluster managés ne sont pas mis à niveau par défaut, afin d’éviter toute interruption involontaire de votre cluster.

Pour activer les mises à niveau automatiques du système d’exploitation :

* Utilisez la version `2021-01-01-preview` (ou ultérieure) des ressources *Microsoft.ServiceFabric/managedclusters* et *Microsoft.ServiceFabric/managedclusters/nodetypes*
* Définir la propriété `enableAutoOSUpgrade` du cluster sur *true*
* Définir la propriété `vmImageVersion` de la ressource nodeTypes du cluster sur *latest*

Par exemple :

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

Une fois activé, Service Fabric commence l’interrogation et le suivi des versions d’image du système d’exploitation dans le cluster managé. Si une nouvelle version du système d’exploitation est disponible, les types de nœuds de cluster (groupes de machines virtuelles identiques) sont mis à niveau successivement. Les mises à niveau du runtime Service Fabric sont effectuées uniquement après la confirmation qu’aucune mise à niveau d’une image de système d’exploitation de nœud de cluster n’est en cours.

En cas d’échec d’une mise à niveau, Service Fabric effectue une nouvelle tentative après 24 heures, pour un maximum de trois tentatives. Comme lors des mises à niveau de Service Fabric classiques (non managées), les applications ou les nœuds non sains peuvent bloquer la mise à niveau de l’image du système d’exploitation.

Pour plus d’informations sur les mises à niveau d’images, consultez [Mises à niveau automatiques d’images de système d’exploitation avec des groupes de machines virtuelles identiques Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble des clusters managés Service Fabric](overview-managed-cluster.md)

[Service Fabric cluster templates](https://github.com/Azure-Samples/service-fabric-cluster-templates) (Modèles de cluster Service Fabric)
