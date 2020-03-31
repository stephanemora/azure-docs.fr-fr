---
title: Mise à l’échelle d’un type de nœud Azure Service Fabric
description: Découvrez comment mettre à l’échelle un cluster Service Fabric en ajoutant un groupe de machines virtuelles identiques.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 33d535cb093eeb95e0ce95bdd5722bfd21150a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464227"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Monter en puissance un type de nœud principal de cluster Service Fabric
Cet article explique comment monter en puissance un type de nœud principal de cluster Service Fabric en augmentant les ressources de machine virtuelle. Un cluster Service Fabric est un groupe de machines virtuelles ou physiques connectées au réseau, sur lequel vos microservices sont déployés et gérés. Une machine ou une machine virtuelle faisant partie d’un cluster est appelée un nœud. Les groupes de machines virtuelles identiques constituent une ressource de calcul Azure que vous utilisez pour déployer et gérer une collection de machines virtuelles en tant que groupe. Chaque type de nœud défini dans un cluster Azure est [ configuré comme un groupe identique distinct](service-fabric-cluster-nodetypes.md). Chaque type de nœud peut alors faire l’objet d’une gestion séparée. Une fois que vous avez créé un cluster Service Fabric, vous pouvez mettre à l’échelle le type de nœud d’un cluster verticalement (changement des ressources des nœuds) ou mettre à niveau le système d’exploitation des machines virtuelles du type de nœud.  Une mise à l’échelle peut s’effectuer à tout moment, même lorsque des charges de travail sont en cours d’exécution sur le cluster.  Lorsque vous mettez vos nœuds à l’échelle, vos applications sont automatiquement mises à l’échelle.

> [!WARNING]
> Ne commencez pas à modifier la référence SKU de la machine virtuelle de la propriété nodetype principale en cas d’absence d’intégrité du cluster. En cas de non-intégrité du cluster, vous ne ferez que le déstabiliser encore plus en essayant de modifier la référence SKU de la machine virtuelle.
>
> Nous vous recommandons de ne pas modifier la référence des machines virtuelles d’un type de nœud/d’un groupe identique présentant un niveau de [durabilité inférieur à Silver](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Modifier la taille de référence (SKU) des machines virtuelles est une opération d’infrastructure sur place destructrice de données. Faute de pouvoir ne serait-ce que retarder ou surveiller cette modification, il est possible que l’opération occasionne une perte de données pour les services avec état ou provoque d’autres problèmes opérationnels imprévus, même pour les charges de travail sans état. Cela signifie le type de votre nœud principal, qui exécute les services système de la structure de service avec état, ou tout type de nœud exécutant les charges de travail de votre d’application avec état.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Mettre à niveau la taille et le système d’exploitation des machines virtuelles du type de nœud principal
Voici la procédure pour mettre à jour la taille et le système d’exploitation des machines virtuelles du type de nœud principal.  Après la mise à niveau, la taille des machines virtuelles du type de nœud principal sera Standard D4_V2 et leur système d’exploitation sera Windows Server 2016 Datacenter avec Conteneurs.

> [!WARNING]
> Avant de tenter cette procédure sur un cluster de production, nous vous recommandons d’examiner les exemples de modèles et de vérifier la procédure sur un cluster de test. Par ailleurs, le cluster n’est pas disponible pendant un temps. Vous ne pouvez pas modifier en parallèle plusieurs groupes de machines virtuelles identiques déclarés comme un même NodeType. Vous devrez effectuer des opérations de déploiement séparées pour appliquer les modifications à chacun des groupes de machines virtuelles identiques.

1. Déployez le cluster initial avec deux types de nœuds et deux groupes identiques (un groupe identique par type de nœud) en utilisant ces exemples de fichiers de [modèle](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) et de [paramètres](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json).  Les deux groupes identiques ont la taille Standard D2_V2 et exécutent Windows Server 2012 R2 Datacenter.  Attendez que le cluster procède à la mise à niveau de référence.   
2. Facultatif : Déployez un exemple avec état dans le cluster.
3. Après avoir décidé de mettre à niveau les machines virtuelles du type de nœud principal, ajoutez un nouveau groupe identique au type de nœud principal en utilisant ces exemples de fichiers de [modèle](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) et de [paramètres](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) afin que le type de nœud principal compte deux groupes identiques.  Les services système et les logiciels des utilisateurs peuvent migrer entre les machines virtuelles dans les deux groupes identiques.  Les machines virtuelles du nouveau groupe identique ont la taille Standard D4_V2 et exécutent Windows Server Datacenter 2016 avec Conteneurs.  Un nouvel équilibreur de charge et une adresse IP publique sont également ajoutés avec le nouveau groupe identique.  
    Pour trouver le nouveau groupe identique dans le modèle, recherchez la ressource « Microsoft.Compute/virtualMachineScaleSets » nommée par le paramètre *vmNodeType2Name*.  Le nouveau groupe identique est ajouté au type de nœud principal à l’aide des propriétés->virtualMachineProfile->extensionProfile->extensions->propriétés->paramètres->paramètre nodeTypeRef.
4. Vérifiez l’intégrité du cluster et de l’ensemble des nœuds.
5. Désactivez les nœuds dans l’ancien groupe identique du type de nœud principal avec l’intention de les supprimer. Vous pouvez les désactiver tous en même temps, auquel cas les opérations sont mises en file d’attente. Attendez que tous les nœuds soient désactivés, ce qui peut prendre un certain temps.  Au fur et à mesure que les anciens nœuds du type de nœud sont désactivés, les services système et les nœuds initiaux (seed) migrent vers les machines virtuelles du nouveau groupe identique du type de nœud principal.
6. Supprimez l’ancien groupe identique du type de nœud principal.
7. Supprimez l’équilibreur de charge associé à l’ancien groupe identique. Le cluster n’est pas disponible pendant que la nouvelle adresse IP publique et l’équilibreur de charge sont configurés pour le nouveau groupe identique.  
8. Stockez les paramètres DNS de l’adresse IP publique associée à l’ancien groupe identique du type de nœud principal dans une variable et supprimez cette adresse IP publique.
9. Remplacez les paramètres DNS de l’adresse IP publique associée au nouveau groupe identique du type de nœud principal par les paramètres DNS de l’adresse IP publique supprimée.  Le cluster est à nouveau accessible.
10. Supprimez l’état des nœuds du cluster.  Si le niveau de durabilité de l’ancien groupe identique était Argent ou Or, cette étape est effectuée automatiquement par le système.
11. Si vous avez déployé l’application avec état dans une étape précédente, vérifiez que l’application fonctionne.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.json" -Verbose

# Connect to the cluster and check the cluster health.
$ClusterName= "sfupgradetest.southcentralus.cloudapp.azure.com:19000"
$thumb="F361720F4BD5449F6F083DDE99DC51A86985B25B"

Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Get-ServiceFabricClusterHealth

# Deploy a new scale set into the primary node type.  Create a new load balancer and public IP address for the new scale set.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.json" -Verbose

# Check the cluster health again. All 15 nodes should be healthy.
Get-ServiceFabricClusterHealth

# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}

Write-Host "Checking node status..."
foreach($name in $nodeNames){
 
    $state = Get-ServiceFabricNode -NodeName $name 

    $loopTimeout = 50

    do{
        Start-Sleep 5
        $loopTimeout -= 1
        $state = Get-ServiceFabricNode -NodeName $name
        Write-Host "$name state: " $state.NodeDeactivationInfo.Status
    }

    while (($state.NodeDeactivationInfo.Status -ne "Completed") -and ($loopTimeout -ne 0))
    

    if ($state.NodeStatus -ne [System.Fabric.Query.NodeStatus]::Disabled)
    {
        Write-Error "$name node deactivation failed with state" $state.NodeStatus
        exit
    }
}

# Remove the scale set
$scaleSetName="NTvm1"
Remove-AzVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [ajouter un type de nœud à un cluster](virtual-machine-scale-set-scale-node-type-scale-out.md).
* Découvrez-en plus sur l’[extensibilité des applications](service-fabric-concepts-scalability.md).
* [Procédez à une montée et une descente en puissance d’un cluster Azure](service-fabric-tutorial-scale-cluster.md).
* [Mettez à l’échelle un cluster Azure par programmation](service-fabric-cluster-programmatic-scaling.md), à l’aide du kit de développement logiciel de calcul Azure.
* [Augmentez ou diminuez la taille des instances d’un cluster autonome](service-fabric-cluster-windows-server-add-remove-nodes.md).

