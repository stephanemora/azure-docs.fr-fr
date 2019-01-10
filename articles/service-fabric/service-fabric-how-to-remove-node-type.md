---
title: Comment supprimer un type de nœud dans Azure Service Fabric | Microsoft Docs
description: Découvrez comment supprimer un type de nœud dans Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: v-steg
manager: JeanPaul.Connick
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/26/2018
ms.author: v-steg
ms.openlocfilehash: 3704a356763b16a30285baee1aabffdd3aa3f8aa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53981618"
---
# <a name="remove-a-service-fabric-node-type"></a>Supprimer un type de nœud Service Fabric

Utilisez la cmdlet [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) pour supprimer un type de nœud Service Fabric.

Deux opérations se produisent lorsque vous appelez Remove-AzureRmServiceFabricNodeType :
1.  Le groupe de machines virtuelles identiques (VMSS) derrière le type de nœud est supprimé.
2.  Pour tous les nœuds au sein de ce type de nœud, l’état entier de ce nœud est supprimé du système. S’il existe des services sur ce nœud, alors ils sont tout d’abord déplacés vers un autre nœud. Si le Gestionnaire du cluster ne trouve pas de nœud pour le réplica/service, l’opération est retardée/bloquée.

> [!NOTE]
> Nous vous recommandons de ne pas utiliser fréquemment la cmdlet Remove-AzureRmServiceFabricNodeType pour supprimer un type de nœud d’un cluster de production. En effet, cette cmdlet est très dangereuse, car elle supprime le groupe de machines virtuelles identiques derrière le type de nœud. Lorsque vous appelez Remove-AzureRmServiceFabricNodeType, le système n’a aucun moyen de savoir si toutes les conditions sont réunies pour que la suppression ne pose aucun problème. 

## <a name="durability-characteristics"></a>Caractéristiques de durabilité
La sécurité est prioritaire par rapport à la vitesse lors de l’utilisation de Remove-AzureRmServiceFabricNodeType. Nous vous recommandons de l’employer uniquement avec des [niveaux de durabilité](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) Silver ou Gold, car :
- Bronze ne vous accorde aucune garantie sur l’enregistrement des informations d’état.
- Les niveaux de durabilité Silver et Gold interceptent toutes les modifications du groupe de machines virtuelles identiques.
- Gold vous permet également de contrôler les mises à jour Azure sous le groupe de machines virtuelles identiques.

Service Fabric « gère » les modifications et les mises à jour sous-jacentes afin d’éviter toute perte de données. Toutefois, lorsque vous supprimez un nœud avec le niveau de durabilité Bronze, vous risquez de perdre les informations d’état. Si vous supprimez un type de nœud principal et que votre application est sans état, vous pouvez utiliser le niveau Bronze. Lorsque vous exécutez des charges de travail avec état en production, la configuration minimale doit être Silver. De même, pour les scénarios de production, le type de nœud principal doit toujours être Silver ou Gold.

### <a name="more-about-bronze-durability"></a>En savoir plus sur la durabilité Bronze

Lorsque vous supprimez un type de nœud de niveau Bronze, tous les nœuds dans le type de nœud s’arrêtent immédiatement. Service Fabric ne recouvre pas les mises à jour du groupe de machines virtuelles identiques de nœuds Bronze, par conséquent, toutes les machines virtuelles s’arrêtent immédiatement. Si vous aviez quoi que ce soit avec état sur ces nœuds, les données sont perdues. Cependant, même si vous étiez sans état, tous les nœuds dans Service Fabric participent à l’anneau, un voisinage entier peut donc être perdu, ce qui peut affecter le cluster lui-même.

Contrairement à la suppression d’un nœud unique (car, en théorie, vous pouvez supprimer un nœud à la fois), attendez que les réplicas et les services se déplacent et que le système se stabilise avant de supprimer un autre nœud, etc.  Toutefois, si vous supprimez simultanément plusieurs nœuds, votre cluster peut tomber en panne (étant donné que Service Fabric ne recouvre pas les mises à jour de groupe de machines virtuelles identiques avec un niveau de durabilité Bronze).

## <a name="recommended-node-type-removal-process"></a>Procédure de suppression de nœud recommandée

Pour supprimer le type de nœud de la façon la plus sûre et rapide possible :
1.  Si vous utilisez le niveau de durabilité Bronze ou que vous ne souhaitez pas que le système migre des applications qui contiennent des informations d’état qui seront perdues lors de la suppression du type de nœud, commencez par vider les données avec état des nœuds qui seront affectés par la suppression du type de nœud.
2.  Exécutez [Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) sur chacun des nœuds à supprimer.
3.  Exécutez [Remove-AzureRmVmss](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#remove-vms-from-a-scale-set) pour les machines virtuelles qui seront affectées par la suppression du type de nœud.
4. Exécutez [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) pour supprimer le type de nœud.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [caractéristiques de durabilité](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) du cluster.
- En savoir plus les [types de nœud et les groupe de machines virtuelles identiques](service-fabric-cluster-nodetypes.md).
- En savoir plus sur la [mise à l’échelle du cluster Service Fabric](service-fabric-cluster-scaling.md).