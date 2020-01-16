---
title: Supprimer un type de nœud dans Azure Service Fabric
description: Découvrez comment supprimer type de nœud d’un cluster Service Fabric dans Azure.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f3dc3210fdb436038174bb8d9347424f14d3faa3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464494"
---
# <a name="remove-a-service-fabric-node-type"></a>Supprimer un type de nœud Service Fabric
Cet article décrit comment mettre à l’échelle un cluster Azure Service Fabric en supprimant un type de nœud existant d’un cluster. Un cluster Service Fabric est un groupe de machines virtuelles ou physiques connectées au réseau, sur lequel vos microservices sont déployés et gérés. Une machine ou une machine virtuelle faisant partie d’un cluster est appelée un nœud. Les groupes de machines virtuelles identiques constituent une ressource de calcul Azure que vous utilisez pour déployer et gérer une collection de machines virtuelles en tant que groupe. Chaque type de nœud défini dans un cluster Azure est [ configuré comme un groupe identique distinct](service-fabric-cluster-nodetypes.md). Chaque type de nœud peut alors faire l’objet d’une gestion séparée. Après avoir créé un cluster Service Fabric, vous pouvez faire évoluer un cluster horizontalement en supprimant un type de nœud (groupe de machines virtuelles identiques) et tous ses nœuds.  Une mise à l’échelle peut s’effectuer à tout moment, même lorsque des charges de travail sont en cours d’exécution sur le cluster.  Lorsque vous mettez vos nœuds à l’échelle, vos applications sont automatiquement mises à l’échelle.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utilisez la cmdlet [Remove-AzServiceFabricNodeType](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype) pour supprimer un type de nœud Service Fabric.

Trois opérations se produisent lorsque vous appelez Remove-AzServiceFabricNodeType :
1.  Le groupe de machines virtuelles identiques derrière le type de nœud est supprimé.
2.  Le type de nœud est supprimé du cluster.
3.  Pour chaque nœud au sein de ce type de nœud, l’état entier de ce nœud est supprimé du système. S’il existe des services sur ce nœud, alors ils sont tout d’abord déplacés vers un autre nœud. Si le Gestionnaire du cluster ne trouve pas de nœud pour le réplica/service, l’opération est retardée/bloquée.

> [!WARNING]
> Nous vous recommandons de ne pas utiliser fréquemment Remove-AzServiceFabricNodeType pour supprimer un type de nœud d’un cluster de production. Il s’agit d’une commande dangereuse, car elle supprime le groupe de machines virtuelles identiques derrière le type de nœud. 

## <a name="durability-characteristics"></a>Caractéristiques de durabilité
La sécurité est prioritaire par rapport à la vitesse lors de l’utilisation de Remove-AzServiceFabricNodeType. Le type de nœud doit avoir le [niveau de durabilité](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) Silver ou Gold, car :
- Bronze ne vous accorde aucune garantie sur l’enregistrement des informations d’état.
- Les niveaux de durabilité Silver et Gold interceptent toutes les modifications du groupe de machines virtuelles identiques.
- Gold vous permet également de contrôler les mises à jour Azure sous le groupe de machines virtuelles identiques.

Service Fabric « gère » les modifications et les mises à jour sous-jacentes afin d’éviter toute perte de données. Toutefois, lorsque vous supprimez un nœud avec le niveau de durabilité Bronze, vous risquez de perdre les informations d’état. Si vous supprimez un type de nœud principal et que votre application est sans état, vous pouvez utiliser le niveau Bronze. Lorsque vous exécutez des charges de travail avec état en production, la configuration minimale doit être Silver. De même, pour les scénarios de production, le type de nœud principal doit toujours être Silver ou Gold.

### <a name="more-about-bronze-durability"></a>En savoir plus sur la durabilité Bronze

Lorsque vous supprimez un type de nœud de niveau Bronze, tous les nœuds dans le type de nœud s’arrêtent immédiatement. Service Fabric ne recouvre pas les mises à jour du groupe de machines virtuelles identiques de nœuds Bronze, par conséquent, toutes les machines virtuelles s’arrêtent immédiatement. Si vous aviez quoi que ce soit avec état sur ces nœuds, les données sont perdues. Cependant, même si vous étiez sans état, tous les nœuds dans Service Fabric participent à l’anneau, un voisinage entier peut donc être perdu, ce qui peut déstabiliser le cluster lui-même.

## <a name="recommended-node-type-removal-process"></a>Procédure de suppression de nœud recommandée

Pour supprimer le type de nœud, exécutez l’applet de commande [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype).  Ce processus prend un certain temps.  Une fois que toutes les machines virtuelles ont disparu (représentées comme « Down ») fabric:/System/InfrastructureService/[nom_type_nœud] indique un état d’erreur.

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

Ensuite, vous pouvez mettre à jour la ressource de cluster pour supprimer le type de nœud. Vous pouvez utiliser le déploiement du modèle ARM ou modifier la ressource de cluster par le biais d’[Azure Resource Manager](https://resources.azure.com). Cela démarre une mise à niveau du cluster qui supprimera le service fabric:/System/InfrastructureService/[nom_type_nœud] qui est en état d’erreur.

Vous verrez toujours les nœuds comme étant « Down » dans Service Fabric Explorer. Exécutez [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) sur chacun des nœuds à supprimer.


```powershell
$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [caractéristiques de durabilité](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) du cluster.
- En savoir plus les [types de nœud et les groupe de machines virtuelles identiques](service-fabric-cluster-nodetypes.md).
- En savoir plus sur la [mise à l’échelle du cluster Service Fabric](service-fabric-cluster-scaling.md).
