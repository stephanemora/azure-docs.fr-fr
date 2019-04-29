---
title: Meilleures pratiques en matière de planification et de mise à l'échelle de la capacité Azure Service Fabric | Microsoft Docs
description: Meilleures pratiques en matière de planification et de mise à l'échelle des clusters et applications Service Fabric
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 425154958e4c60902b56f320f714a011b9095830
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471534"
---
# <a name="capacity-planning-and-scaling"></a>Planification et mise à l’échelle de la capacité

Avant de créer un cluster Azure Service Fabric ou de mettre à l'échelle les ressources de calcul hébergeant votre cluster, il convient d'en planifier la capacité. Pour plus d’informations sur la planification de la capacité, consultez [Planification de la capacité des clusters Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). En plus de prendre en compte les caractéristiques Nodetype et Cluster, prévoyez plus d'une heure pour les opérations de mise à l'échelle d'un environnement de production et ce, quel que soit le nombre de machines virtuelles que vous ajoutez.

## <a name="auto-scaling"></a>Mise à l'échelle automatique
Les opérations de mise à l’échelle doivent se faire via le déploiement d'un modèle de ressource Azure, car il est important de traiter les [configurations de ressource en tant que code]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code), et le recours à une mise à l'échelle automatique du groupe de machines virtuelles identiques se traduira par une définition inexacte du nombre d'instances du groupe de machines virtuelles identiques, augmentant le risque que de futurs déploiement entraînent des opérations de mise à l'échelle involontaires. De manière générale, vous devez utiliser la mise à l'échelle automatique si :

* le déploiement de vos modèles Resource Manager avec une capacité appropriée déclarée ne prend pas en charge votre cas d’utilisation.
  * En plus de la mise à l'échelle manuelle, vous pouvez configurer un [pipeline de livraison et d'intégration continue dans Azure DevOps Services à l’aide de projets de déploiement de groupe de ressources Azure]( https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts), ce qui est couramment déclenché par une application logique qui s’appuie sur les mesures de performances des machines virtuelles interrogées à partir de l'[API REST Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough) ; mettant efficacement à l'échelle les mesures de votre choix, tout en optimisant la valeur Azure Resource Manager.
* Vous devez uniquement mettre à l’échelle horizontale un nœud de groupe de machines virtuelles identiques à la fois.
  * Pour mettre à l'échelle trois nœuds ou plus à la fois, vous devez [mettre à l’échelle un cluster Service Fabric en ajoutant un groupe de machines virtuelles identiques](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), mais il est plus sûr de mettre à l'échelle horizontale un nœud de groupe de machines virtuelles identiques à la fois.
* Si vous bénéficiez d'une fiabilité Silver ou supérieure pour votre cluster Service Fabric, ainsi qu'une durabilité Silver ou supérieure pour tous les groupes identiques, vous configurez des règles de mise à l'échelle automatique.
  * La capacité [minimale] des règles de mise à l'échelle automatique doit être supérieure ou égale à 5 instances de machine virtuelle, et supérieure ou égale à votre niveau de fiabilité minimal pour votre type de nœud principal.

> [!NOTE]
> Azure Service Fabric avec état : /System/InfastructureService/<NODE_TYPE_NAME>, s’exécute sur chaque type de nœud doté d'une durabilité Silver ou supérieure, et constitue le seul service système pris en charge pour s'exécuter dans Azure sur tous les types de nœuds de vos clusters. 

## <a name="vertical-scaling-considerations"></a>Considérations relatives à la mise à l’échelle verticale

La [mise à l’échelle verticale](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) d'un type de nœud dans Azure Service Fabric requiert un certain nombre d’étapes et de considérations. Par exemple : 
* Le cluster doit être intègre avant sa mise à l’échelle, à défaut de quoi, vous ne feriez que le déstabiliser davantage.
* Le **niveau de durabilité Silver ou supérieur** est requis pour tous les types de nœuds Service Fabric Cluster hébergeant des services avec état.

> [!NOTE]
> Le type de nœud principal qui héberge des services système Service Fabric avec état doit présenter un niveau de durabilité Silver ou supérieur. Une fois le niveau Silver activé, les opérations de cluster telles que les mises à niveau, l'ajout ou la suppression de nœuds, etc. seront plus lentes car le système privilégie la sécurité des données par rapport à la vitesse des opérations.

La mise à l’échelle verticale d'un groupe de machines virtuelles identiques est une opération destructrice. Privilégiez une mise à l'échelle horizontale de votre cluster en ajoutant un nouveau groupe de machines virtuelles identiques avec la référence SKU souhaitée, et migrez vos services vers la référence SKU souhaitée pour procéder en toute sécurité une opération de mise à l'échelle verticale. Modifier la référence SKU d'une ressource de groupe de machines virtuelles identiques est une opération destructrice, car cela réinitialise vos hôtes et supprime tout état persistant localement.

Les [propriétés de nœud et contraintes de placement](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) de Service Fabric sont utilisées par votre cluster pour savoir où héberger vos services d'applications. En cas de mise à l'échelle verticale de votre type de nœud principal, déclarez des valeurs de propriétés identiques pour `"nodeTypeRef"`, ce qui se trouve dans l'extension Service Fabric du groupe de machines virtuelles identiques. L’extrait de modèle Resource Manager suivant affiche les propriétés que vous déclarerez, avec la même valeur pour les nouveaux groupes de machines virtuelles identiques approvisionnées, et est uniquement pris en charge en tant qu'état temporaire pour votre cluster :

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Ne laissez pas votre cluster s'exécuter avec plusieurs groupes de machines virtuelles identiques utilisant la même propriété `nodeTypeRef` au-delà du laps de temps nécessaire à l'opération de mise à l'échelle verticale.
> Validez toujours les opérations dans des environnements de test avant de modifier l'environnement de production. Par défaut, les services système de cluster Service Fabric présentent une contrainte de placement pour cibler uniquement le type de nœud principal.

Une fois les propriétés de nœud et les contraintes de placement déclarées, procédez comme suit sur une instance de machine virtuelle à la fois. Ceci permet d’arrêter correctement les services système (et vos services avec état) sur l’instance de machine virtuelle que vous supprimez pendant que de nouveaux réplicas sont créés ailleurs.
1. À partir de PowerShell, exécutez `Disable-ServiceFabricNode` avec l'intention « RemoveNode » pour désactiver le nœud que vous vous apprêtez à supprimer. Supprimez le type de nœud présentant le nombre le plus élevé. Par exemple, en présence d'un cluster à six nœuds, supprimez l'instance de machine virtuelle « MyNodeType_5 ».
2. Exécutez `Get-ServiceFabricNode` pour vous assurer que le nœud a bien été désactivé. Si ce n’est pas le cas, patientez jusqu'à ce que le nœud soit désactivé. Cette opération peut prendre plusieurs heures pour chaque nœud. Ne continuez pas tant que le nœud n'a pas été désactivé.
3. Réduisez une à une le nombre de machines virtuelles dans ce type de nœud. L'instance de machine virtuelle la plus élevée va à présent être supprimée.
4. Répétez les étapes 1 à 3 selon vos besoins, mais ne faites jamais descendre en puissance le nombre d’instances sur les types de nœuds principaux sur une valeur inférieure à celle garantie par le niveau de fiabilité. Pour obtenir la liste des instances recommandées, consultez [Planification de la capacité du cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

## <a name="horizontal-scaling"></a>Mise à l’échelle horizontale

Dans Service Fabric, la mise à l'échelle horizontale peut se faire [manuellement](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) ou [par programmation](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Si vous mettez à l’échelle un type de nœud doté d'une durabilité Silver ou Gold, le processus sera lent.

### <a name="scaling-out"></a>Montée en charge

Montez en charge un cluster Service Fabric en augmentant le nombre d'instances d'un groupe de machines virtuelles identiques donné. Cette montée en charge peut se faire par programmation, à l'aide du client Azure et de l'ID du groupe de machines virtuelles souhaité.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Pour une montée en charge manuelle, mettez à jour la capacité dans la propriété de référence SKU de la ressource [Groupe de machines virtuelles identiques](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) souhaitée.
```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Mise à l'échelle

La mise à l'échelle requiert une plus grande attention que la montée en charge. Par exemple : 
* Les services système Service Fabric s’exécutent sur le type de nœud principal de votre cluster. Vous ne devez jamais arrêter ou faire descendre en puissance le nombre d’instances pour ces types de nœuds sur une valeur d'instances inférieure à celle garantie par le niveau de fiabilité. 
* Pour un service avec état, un certain nombre de nœuds doivent toujours fonctionner afin de maintenir la disponibilité et de conserver l’état de votre service. Au minimum, le nombre de nœuds doit être égal au nombre de jeux de réplicas cibles du service/de la partition.

Pour une mise à l'échelle manuelle, procédez comme suit :

1. À partir de PowerShell, exécutez `Disable-ServiceFabricNode` avec l'intention « RemoveNode » pour désactiver le nœud que vous vous apprêtez à supprimer. Supprimez le type de nœud présentant le nombre le plus élevé. Par exemple, en présence d'un cluster à six nœuds, supprimez l'instance de machine virtuelle « MyNodeType_5 ».
2. Exécutez `Get-ServiceFabricNode` pour vous assurer que le nœud a bien été désactivé. Si ce n’est pas le cas, patientez jusqu'à ce que le nœud soit désactivé. Cette opération peut prendre plusieurs heures pour chaque nœud. Ne continuez pas tant que le nœud n'a pas été désactivé.
3. Réduisez une à une le nombre de machines virtuelles dans ce type de nœud. L'instance de machine virtuelle la plus élevée va à présent être supprimée.
4. Répétez les étapes 1 à 3 selon vos besoins, mais ne faites jamais descendre en puissance le nombre d’instances sur les types de nœuds principaux sur une valeur inférieure à celle garantie par le niveau de fiabilité. Pour obtenir la liste des instances recommandées, consultez [Planification de la capacité du cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Pour une mise à l'échelle manuelle, mettez à jour la capacité dans la propriété de référence SKU de la ressource [Groupe de machines virtuelles identiques](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) souhaitée.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

1. Répétez les étapes 1 à 3 selon vos besoins. Ne faites jamais descendre en puissance le nombre d’instances sur les types de nœuds principaux sur une valeur inférieure à celle garantie par le niveau de fiabilité. Pour plus d’informations sur les niveaux de fiabilité et le nombre d’instances requises, consultez [Planification de la capacité du cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Vous devez préparer le nœud à l'arrêt pour une mise à l'échelle par programmation. Pour ce faire, vous devez identifier le nœud à supprimer présentant l'instance la plus élevée et le désactiver. Par exemple : 

```c#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Après avoir identifié le nœud à supprimer, désactivez-le et supprimez-le en utilisant la même instance `FabricClient` (`client` dans le cas présent) et le même nom d'instance de nœud (`instanceIdString` dans le cas présent) que ceux utilisés dans le code ci-dessus :

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> Lorsque vous mettez à l’échelle un cluster, vous verrez l’instance de nœud/la machine virtuelle supprimée affiché dans un état défectueux dans Service Fabric Explorer. Pour obtenir une explication de ce comportement, consultez [comportements que vous pouvez observer dans Service Fabric Explorer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer).
> 
> Vous pouvez :
> * Appelez [commande Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) avec le nom de nœud approprié.
> * Déployer [application service fabric à l’échelle automatique d’assistance](https://github.com/Azure/service-fabric-autoscale-helper/) sur votre cluster, ce qui garantit la mise à l’échelle vers le bas les nœuds sont effacés du Service Fabric Explorer.

## <a name="reliability-levels"></a>Niveaux de fiabilité

Le [niveau de fiabilité](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) est une propriété de votre ressource de cluster Service Fabric, et ne peut pas être configuré différemment pour les types de nœuds individuels. Il contrôle le facteur de réplication des services système du cluster, et correspond à un paramètre au niveau de la ressource de cluster. Le niveau de fiabilité détermine le nombre minimal de nœuds que doit contenir votre type de nœud principal. Le niveau de fiabilité peut avoir les valeurs suivantes :
* Platinum : exécutez les services système avec un nombre de jeux de réplicas cible égal à sept et neuf nœuds initiaux.
* Gold : exécutez les services système avec un nombre de jeux de réplicas cible égal à sept et sept nœuds initiaux.
* Silver : exécutez les services système avec un nombre de jeux de réplicas cible égal à cinq et cinq nœuds initiaux.
* Bronze : exécutez les services système avec un nombre de jeux de réplicas cible égal à trois et trois nœuds initiaux.

Le niveau de fiabilité minimal recommandé est Silver.

Le niveau de fiabilité est défini dans la section des propriétés de la [ressource Microsoft.servicefabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), comme suit :

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Niveaux de durabilité

> [!WARNING]
> Les types de nœud s’exécutant avec un niveau de durabilité Bronze n’obtiennent _aucun privilège_. Cela signifie que les travaux d’infrastructure qui affectent vos charges de travail sans état ne seront ni arrêtés ni différés. Utilisez le niveau de durabilité Bronze uniquement pour les types de nœuds qui exécutent des charges de travail sans état. Pour les charges de travail de production, exécutez un niveau Silver ou supérieur pour garantir la cohérence de l'état. Sélectionnez la fiabilité qui convient en fonction des éléments contenus dans la [documentation relative à la planification de la capacité](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Le niveau de durabilité doit être défini dans deux ressources. Profil d'extension de la [ressource de groupe de machines virtuelles identiques](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) :

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

Puis, sous `nodeTypes` dans la ressource [Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Étapes suivantes

* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Windows Server : [Création de clusters Service Fabric pour Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Linux : [Créer un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* En savoir plus sur les [options de prise en charge de Service Fabric](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
