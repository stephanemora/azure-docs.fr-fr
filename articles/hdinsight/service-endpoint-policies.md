---
title: Configurer des stratégies de point de terminaison de service - Azure HDInsight
description: Découvrez comment configurer des stratégies de point de terminaison de service pour votre réseau virtuel avec Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 09/13/2021
ms.openlocfilehash: 500bbdce924313924b8b6f698dff0e17107ebd79
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646565"
---
# <a name="configure-virtual-network-service-endpoint-policies-for-azure-hdinsight"></a>Configurer des stratégies de point de terminaison de service de réseau virtuel pour Azure HDInsight

Cet article fournit des informations sur la façon d’implémenter des stratégies de point de terminaison de service sur des réseaux virtuels avec Azure HDInsight.

## <a name="background"></a>Arrière-plan

Azure HDInsight vous permet de créer des clusters dans votre propre réseau virtuel. Si vous devez autoriser le trafic sortant de votre réseau virtuel vers d’autres services Azure, comme des comptes de stockage, vous pouvez créer des [stratégies de point de terminaison de service](../virtual-network/virtual-network-service-endpoint-policies-overview.md). Toutefois, les stratégies de point de terminaison de service créées à l’aide du Portail Azure vous permettent uniquement de créer une stratégie pour un compte unique, tous les comptes d’un abonnement ou tous les comptes d’un groupe de ressources.

Toutefois, en tant que service géré, Azure HDInsight collecte les fichiers de données et les fichiers journaux de chaque cluster dans des comptes de stockage spécifiques dans chaque région. Pour que ces données atteignent HDInsight à partir de votre réseau virtuel, il est nécessaire de créer des stratégies de point de terminaison de service qui autorisent le trafic sortant vers des points de collecte de données spécifiques gérés par Azure HDInsight.

## <a name="service-endpoint-policies-for-hdinsight"></a>Stratégies de points de terminaison de service pour HDInsight

Ces stratégies de point de terminaison de service prennent en charge les fonctionnalités suivantes :

- Collecte de journaux et de télémétrie lors de la création du cluster, de l’exécution de tâches et des opérations de plateforme, comme la mise à l’échelle.
- Jointure de disques durs virtuels à des nœuds de cluster récemment créés pour approvisionner les logiciels et les bibliothèques sur votre cluster.

Si les stratégies de point de terminaison de service ne sont pas créées pour activer ce workflow, la création du cluster peut échouer et Azure HDInsight ne pourra pas assurer la prise en charge de vos clusters.

## <a name="create-service-endpoint-policies-for-hdinsight"></a>Créer des stratégies de points de terminaison de service pour HDInsight

Assurez-vous que les stratégies de point de terminaison de service appropriées sont attachées à votre réseau virtuel avant de créer de nouveaux clusters. Dans le cas contraire, la création du cluster peut échouer ou entraîner une erreur.

Utilisez le processus suivant pour créer les stratégies de point de terminaison de service nécessaires :

1. Déterminez la région dans laquelle vous allez créer votre cluster HDInsight.
1. Recherchez cette région dans la [liste des ressources de stratégie de point de terminaison de service](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json), qui donne tous les groupes de ressources pour les comptes de stockage de gestion HDInsight.
1. Sélectionnez la liste des groupes de ressources pour votre région. Vous trouverez ci-dessous un exemple des ressources pour `Canada Central` :

    ```json
    "Canada Central":[
        "/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
        "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/hdi31distrorelease",
        "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/bigdatadistro"
    ],
    ```

1. Insérez cette liste de groupes de ressources dans le script d’installation écrit dans Azure CLI ou Azure PowerShell.

    ```azurecli
    $subscriptionId = "<subscription id>"
    $rgName="<resource group name> "
    $location="<location name>"
    $vnetName="<vnet name>"
    $subnetName="<subnet name>"
    $sepName="<service endpoint policy name>"
    $sepDefName="<service endpoint policy definition name>"
    
    # Set to the right subscription ID
    az account set --subscription $subscriptionId
    
    # setup service endpoint on the virtual network subnet
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoints Microsoft.Storage
    
    # Create Service Endpoint Policy
    az network service-endpoint policy create -g $rgName  -n $sepName -l $location
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    # Be sure to get the most recent list of resource groups from the [list of service endpoint policy resources](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json)
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",`
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/hdi31distrorelease",
    "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/bigdatadistro")
    
    #Assign service resources to the SEP policy.
    az network service-endpoint policy-definition create -g $rgName --policy-name $sepName -n $sepDefName --service "Microsoft.Storage" --service-resources $resources
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoint-policy $sepName
    ```

    Si vous préférez configurer votre stratégie de point de terminaison de service à l’aide de PowerShell, utilisez l’extrait de code suivant.
    
    ```powershell
    #Script to assign SEP 
    $subscriptionId = "<subscription id>"
    $rgName = "<resource group name>"
    $vnetName = "<vnet name>"
    $subnetName = "<subnet Name"
    $location = "Canada Central"
    
    # Connect to your Azure Account
    Connect-AzAccount
    
    # Select the Subscription that you want to use
    Select-AzSubscription -SubscriptionId $subscriptionId
    
    # Retrieve VNet Config
    $vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
    
    # Retrieve Subnet Config
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    # Be sure to get the most recent list of resource groups from the [list of service endpoint policy resources](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json)
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/hdi31distrorelease",
    "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/bigdatadistro")
    
    #Declare service endpoint policy definition
    $sepDef = New-AzServiceEndpointPolicyDefinition -Name "SEPHDICanadaCentral" -Description "Service Endpoint Policy Definition" -Service "Microsoft.Storage" -ServiceResource $resources
    
    # Service Endpoint Policy
    $sep= New-AzServiceEndpointPolicy -ResourceGroupName $rgName -Name "SEPHDICanadaCentral" -Location $location -ServiceEndpointPolicyDefinition $sepDef
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    Set-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet -AddressPrefix $subnet.AddressPrefix -ServiceEndpointPolicy $sep
    ```
> [!IMPORTANT] 
> Nous vous recommandons d’obtenir la dernière [liste des ressources de la stratégie de point de terminaison de service](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json) manuellement ou via Automation. Cela empêchera les problèmes CRUD quand des groupes de ressources supplémentaires seront ajoutés ou supprimés du fichier JSON. 


## <a name="next-steps"></a>Étapes suivantes

* [Architecture de réseau virtuel Azure HDInsight](hdinsight-virtual-network-architecture.md)
* [Configurer une appliance virtuelle réseau](./network-virtual-appliance.md)
