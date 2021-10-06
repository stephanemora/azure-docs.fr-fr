---
title: Mise à niveau du système d’exploitation Linux pour Azure Service Fabric
description: Découvrir les options de migration de votre cluster Azure Service Fabric vers un autre système d’exploitation Linux
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 8f52481e7c457445dc842e86f7b05c3568502da4
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278008"
---
# <a name="upgrading-linux-os-for-azure-service-fabric"></a>Mise à niveau du système d’exploitation Linux pour Azure Service Fabric

Ce document décrit le guide de migration de votre cluster Azure Service Fabric pour Linux depuis Ubuntu version 16.04 LTS vers 18.04 LTS. Chaque version de système d’exploitation nécessite un package de runtime SF distinct, qui requiert les étapes décrites dans ce document pour faciliter une migration en douceur.

## <a name="overview"></a>Vue d’ensemble

Voici l’approche générale :

1. Basculez la ressource ARM (Azure Resource Manager) du cluster Service Fabric « vmImage » vers « Ubuntu18_04 » afin d’extraire les futures mises à niveau de code pour cette version de système d’exploitation. Cette incompatibilité de système d’exploitation temporaire par rapport aux types de nœuds existants bloquera les lancements de mises à niveau de code automatiques pour garantir une substitution sécurisée.

    * Évitez d’émettre des mises à niveau manuelles de code de cluster SF lors de la migration du système d’exploitation. En procédant ainsi, les anciens types de nœuds peuvent entrer dans un état qui nécessite une intervention humaine.

2. Pour chaque type de nœud du cluster, créez un autre type de nœud ciblant l’image de système d’exploitation Ubuntu 18.04 pour le groupe de machines virtuelles identiques sous-jacent. Chaque nouveau type de nœud endossera le rôle de son ancien équivalent.

    * Un type de nœud principal doit être créé pour remplacer l’ancien type de nœud marqué comme « isPrimary » : true.
    
    * Les types de nœuds non principaux supplémentaires sont de la même manière marqués comme « isPrimary » : false.

    * Assurez-vous que les charges de travail existantes continuent de fonctionner correctement une fois le nouveau type de nœud de système d’exploitation cible créé. Si vous constatez des problèmes, apportez les modifications requises dans l’application ou les packages d’ordinateurs préinstallés avant de procéder à la suppression de l’ancien type de nœud.
3. Marquez l’ancien type de nœud principal « isPrimary » : false. Cela aboutira à l’exécution d’un ensemble de mises à niveau de longue durée pour la transition de tous les nœuds initiaux.
4. (Pour les types de nœuds de durabilité Bronze UNIQUEMENT) : connectez-vous au cluster via [sfctl](service-fabric-sfctl.md) / [PowerShell](/powershell/module/ServiceFabric) / [FabricClient](/dotnet/api/system.fabric.fabricclient) et désactivez tous les nœuds de l’ancien type de nœud.
5. Supprimez les anciens types de nœuds.

> [!NOTE]
> Az PowerShell génère un nouveau nom DNS pour le type de nœud ajouté de sorte que le trafic externe doit être redirigé vers ce point de terminaison.


## <a name="ease-of-use-steps-for-non-production-clusters"></a>Procédure facile à utiliser pour les clusters hors production

> [!NOTE]
> Les étapes ci-dessous montrent comment créer rapidement un prototype de migration de type de nœud via les applets de commande Az PowerShell dans un cluster de TEST uniquement. Pour les clusters de production confrontés à un réel trafic opérationnel, les mêmes étapes sont censées être effectuées en émettant des mises à niveau ARM, afin de préserver la reproductibilité et une source déclarative cohérente de vérité.

1. Mettez à jour le paramètre vmImage sur la ressource de cluster Service Fabric avec [Update-AzServiceFabricVmImage](/powershell/module/az.servicefabric/update-azservicefabricvmimage) :

    [Azure PowerShell](/powershell/azure/install-az-ps):
    ```powershell
    # Replace subscriptionId, resourceGroup, clusterName with ones corresponding to your cluster.
    $subscriptionId="cea219db-0593-4b27-8bfa-a703332bf433"
    Login-AzAccount; Select-AzSubscription -SubscriptionId $subscriptionId

    $resourceGroup="Group1"
    $clusterName="Contoso01SFCluster"
    # Update cluster vmImage to target OS. This registers the SF runtime package type that is supplied for upgrades.
    Update-AzServiceFabricVmImage -ResourceGroupName $resourceGroup -ClusterName $clusterName -VmImage Ubuntu18_04
    ```

2. Ajoutez un nouvel équivalent de type de nœud pour chacun des types de nœuds existants :

    ```powershell
    $nodeTypeName="nt1u18"
    # You can customize this to fetch a password from a secure store.
    $securePassword = ConvertTo-SecureString -String 'Yourpassword123!@#' -AsPlainText -Force

    # Ensure last upgrade is done - Ready means the next command can be issued.
    (Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup).ClusterState

    # Add new primary node type. Omit the IsPrimaryNodeType parameter for non-primary node types.
    Add-AzServiceFabricNodeType -ResourceGroupName $resourceGroup  -ClusterName $clusterName -NodeType $nodeTypeName -Capacity 5 -VmUserName testuser -VmPassword $securePassword -DurabilityLevel Silver -Verbose -VMImageSku 18.04-LTS -IsPrimaryNodeType $true

    # Redirect traffic to new node type dns
    # dns-Contoso01SFCluster-nt1u18.westus2.cloudapp.azure.com
    ```

3. Définissez l’ancien type de nœud principal sur non principal pour remplacer les nœuds initiaux et les services système par le nouveau type de nœud :

    ```powershell
    # Query to ensure background upgrades are done.
    (Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup).ClusterState

    # Update old nodetype to isPrimary: false
    $oldNodeTypeName="nt1"
    Update-AzServiceFabricNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -IsPrimaryNodeType $false -NodeType $oldNodeTypeName -Verbose

    # Ensure node type is showing isPrimary: False before proceeding.
    Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup
    ```

    Exemple de sortie :
    ```
    NodeTypes :
              NodeTypeDescription :
                  Name : nt1
                  PlacementProperties :
                  Capacities :
                  ClientConnectionEndpointPort : 19000
                  HttpGatewayEndpointPort : 19080
                  DurabilityLevel : Bronze
                  ApplicationPorts :
                      StartPort : 20000
                      EndPort : 30000
                  EphemeralPorts :
                      StartPort : 49152
                      EndPort : 65534
                  IsPrimary : False
                  VmInstanceCount : 5
                  ReverseProxyEndpointPort :
              NodeTypeDescription :
                  Name : nt1u18
                  PlacementProperties :
                  Capacities :
                  ClientConnectionEndpointPort : 19000
                  HttpGatewayEndpointPort : 19080
                  DurabilityLevel : Silver
                  ApplicationPorts :
                      StartPort : 20000
                      EndPort : 30000
                  EphemeralPorts :
                      StartPort : 49152
                      EndPort : 65534
                  IsPrimary : True
                  VmInstanceCount : 5
                  ReverseProxyEndpointPort :
    ```

4. Pour supprimer les types de nœuds de durabilité Bronze, commencez par désactiver les nœuds avant de procéder à la suppression de l’ancien type de nœud. Connectez-vous via *ssh* à un nœud de cluster et exécutez les commandes suivantes :

    ```bash
    # as root user:

    # install jq tool to automatically parse JSON responses
    apt-get install jq -fy

    # retrieve the thumbprint to be used for establishing a fabric client
    dataroot=$(cat /etc/servicefabric/FabricDataRoot)
    nodename=_nt1_0
    cat $dataroot/$nodename/Fabric/ClusterManifest.current.xml | grep ClientCertThumbprints
    # 0777FE1A43E306F332D96DA339EF6834D0E4A453

    # verify node count
    sfctl cluster select --endpoint https://Contoso01SFCluster.westus2.cloudapp.azure.com:19080 --pem /var/lib/waagent/0777FE1A43E306F332D96DA339EF6834D0E4A453.pem --no-verify

    # sample command to list all nodes
    sfctl node list

    # for each node part of the node type to be removed, disable the node:
    nodeTypeBeingDisabled=nt1
    nodes=$(sfctl node list | jq --arg nodeTypeBeingDisabled "$nodeTypeBeingDisabled" '.items[] | select(.type==$nodeTypeBeingDisabled) | .name' | sed s/\"//g)
    echo $nodes
    for n in $nodes; do echo "Disabling $n"; sfctl node disable --node-name $n --deactivation-intent RemoveNode --timeout 300; done
    ```

5. Supprimez le type de nœud précédent en supprimant l’attribut de type de nœud de ressource de cluster SF et en désaffectant les ressources réseau et le groupe de machines virtuelles identiques associés.

    ```powershell
    $resourceGroup="Group1"
    $clusterName="Contoso01SFCluster"
    $oldNodeTypeName="nt1"

    # Remove the Service Fabric node type, associated virtual machine scale set resource, and any trailing networking resources that are no longer used. 
    Remove-AzServiceFabricNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -NodeType $oldNodeTypeName
    ```

    > [!NOTE]
    > Dans certains cas, vous pouvez rencontrer l’erreur ci-dessous. Dans ce cas, vous pouvez découvrir via Service Fabric Explorer (SFX) que le service InfrastructureService pour le type de nœud supprimé est dans un état d’erreur. Pour résoudre ce problème, retentez la suppression.
    ```
    Remove-AzServiceFabricNodeType : Code: ClusterUpgradeFailed, Message: Long running operation failed with status 'Failed'
    ```

Une fois qu’il a été confirmé que les charges de travail ont été correctement migrées vers les nouveaux types de nœuds et que les anciens types de nœuds ont été nettoyés, le cluster peut passer aux mises à niveau suivantes de la configuration et de la version de code du runtime Service Fabric.

## <a name="next-steps"></a>Étapes suivantes

* [Gérer les mises à niveau de Service Fabric](service-fabric-cluster-upgrade-version-azure.md)
* Personnaliser les [paramètres de votre cluster Service Fabric](service-fabric-cluster-fabric-settings.md)
* En savoir plus sur les [caractéristiques de durabilité](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) du cluster.
* En savoir plus les [types de nœud et les groupe de machines virtuelles identiques](service-fabric-cluster-nodetypes.md).
* En savoir plus sur la [mise à l’échelle du cluster Service Fabric](service-fabric-cluster-scaling.md).
* [Effectuez un scale-in et un scale-out de votre cluster](service-fabric-cluster-scale-in-out.md)
* [Supprimer un type de nœud dans Azure Service Fabric](service-fabric-how-to-remove-node-type.md)

