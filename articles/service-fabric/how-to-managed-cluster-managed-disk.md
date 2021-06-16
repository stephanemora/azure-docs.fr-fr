---
title: Sélectionner des types de disques managés pour des nœuds de cluster managé Service Fabric
description: Apprenez à sélectionner des types de disques managés pour des nœuds de cluster managé Service Fabric et à les configurer dans un modèle ARM.
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: 054f2b68bfc2a8177e792824dd3c507bf6b4523b
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111961045"
---
# <a name="select-managed-disk-types-for-service-fabric-managed-cluster-nodes"></a>Sélectionner des types de disques managés pour des nœuds de cluster managé Service Fabric

Les clusters managés Azure Service Fabric utilisent des disques managés pour tous les besoins de stockage, y compris les données d’application, pour des scénarios tels que les acteurs et les collections fiables. Les disques managés Azure sont des volumes de stockage de niveau bloc qui sont gérés par Azure et utilisés avec des machines virtuelles Azure. Les disques managés sont comme un disque physique sur un serveur local, mais virtualisé. Avec les disques managés, il vous suffit de spécifier la taille de disque ainsi que le type de disque et de provisionner le disque. Une fois que vous avez provisionné le disque, Azure s’occupe du reste. Pour plus d’informations sur les disques managés, consultez [Présentation des disques managés Azure](../virtual-machines/managed-disks-overview.md).

## <a name="managed-disk-types"></a>Types de disques managés

Les clusters managés Azure Service Fabric prennent en charge les types de disques managés suivants :
* HDD Standard
    * Stockage localement redondant HDD Standard. Idéal pour un accès de secours non critique et occasionnel. 
* SSD Standard *par défaut*
    * Stockage localement redondant SSD Standard. Idéal pour les serveurs web, les applications d’entreprise peu utilisées et les environnements dev/test.
* SSD Premium *compatibles avec des tailles de machines virtuelles spécifiques*. Pour plus d’informations, consultez [SSD Premium](../virtual-machines/disks-types.md#premium-ssd)
    * Stockage localement redondant SSD Premium. Idéal pour les charges de travail de production et sensibles aux performances.

>[!NOTE]
> Tout disque temporaire associé à la taille de la machine virtuelle ne sera *pas* utilisé pour stocker des données liées à Service Fabric ou à l’application.

## <a name="specifying-a-service-fabric-managed-cluster-disk-type"></a>Spécification d’un type de disque de cluster managé Service Fabric

Pour spécifier un type de disque de cluster managé Service Fabric, vous devez inclure la valeur suivante dans la définition de ressource de cluster managé.

* La propriété **dataDiskType**, qui spécifie le type de disque managé à utiliser pour vos nœuds.

Les valeurs possibles sont les suivantes :
* « Standard_LRS »
* « StandardSSD_LRS »
* « Premium_LRS »
>[!NOTE]
> Tous les types de disques managés ne sont pas disponibles pour toutes les tailles de machines virtuelles. Pour plus d’informations, consultez [Quels sont les types de disques disponibles dans Azure ?](../virtual-machines/disks-types.md)

```json
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters",
    "dataDiskType": "StandardSSD_LRS"
    
}
```

Des exemples de modèles sont disponibles et incluent cette spécification : [Service Fabric managed cluster templates](https://github.com/Azure-Samples/service-fabric-cluster-templates).