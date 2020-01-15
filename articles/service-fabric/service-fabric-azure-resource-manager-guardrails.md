---
title: Barrières de sécurité relatives au déploiement d’un cluster Service Fabric via Azure Resource Manager
description: Cet article fournit une vue d’ensemble des erreurs courantes commises lors du déploiement d’un cluster Service Fabric par le biais de Azure Resource Manager, et explique comment les éviter.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: pepogors
ms.openlocfilehash: fe5ff2a5eeb4b2c73165d1577702eb6af7079b61
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426734"
---
# <a name="service-fabric-guardrails"></a>Barrières de sécurité relatives à Service Fabric 
Lors du déploiement d’un cluster Service Fabric, des barrières de sécurité sont appliquées et entraînent l’échec du déploiement d’Azure Resource Manager si la configuration du cluster n’est pas valide. Les sections suivantes fournissent une vue d’ensemble des problèmes de configuration courants du cluster et des étapes requises pour les atténuer. 

## <a name="durability-mismatch"></a>Incompatibilité de la durabilité
### <a name="overview"></a>Vue d’ensemble
La valeur de durabilité d’un type de nœud Service Fabric est définie dans deux sections différentes d’un modèle Azure Resource Manager : la section sur l’extension de groupes de machines virtuelles identiques de la ressource de groupe de machines virtuelles identiques et la section sur le type de nœud de la ressource de cluster Service Fabric. Il est impératif que la valeur de durabilité de ces sections corresponde. Dans le cas contraire, le déploiement de la ressource échoue.

La section suivante contient un exemple d’incompatibilité de durabilité entre le paramètre de durabilité de l’extension du groupe de machines virtuelles identiques et celui du type de nœud Service Fabric :  

**Paramètre de durabilité de l’extension du groupe de machines virtuelles identiques**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Paramètre de durabilité du type de nœud Service Fabric** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>Messages d’erreur
* Le niveau de durabilité du groupe de machines virtuelles identiques ne correspond pas à celui du type de nœud Service Fabric en cours
* Le niveau de durabilité du groupe de machines virtuelles identiques ne correspond pas à celui du type de nœud Service Fabric cible
* Le niveau de durabilité du groupe de machines virtuelles identiques ne correspond pas au niveau de durabilité Service Fabric en cours ou au niveau de durabilité du type de nœud Service Fabric cible 


### <a name="mitigation"></a>Limitation des risques
Pour corriger un problème d’incompatibilité entre des durabilités, comme indiqué dans les messages d’erreur ci-dessus, procédez comme suit :
1. Mettez à jour le niveau de durabilité dans la section de l’extension du groupe de machines virtuelles identiques ou du type de nœud Service Fabric dans le modèle Resource Manager, afin de vous assurer que les valeurs correspondent.
2. Redéployez le modèle Azure Resource Manager avec les valeurs à jour.

## <a name="next-steps"></a>Étapes suivantes
* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Windows Server : [Création de clusters Service Fabric pour Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Linux : [Créer un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Résoudre les problèmes liés à Service Fabric : [Guides de résolution des problèmes](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
