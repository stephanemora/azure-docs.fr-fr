---
title: Gérer les ressources Azure Managed Instance pour Apache Cassandra avec Azure CLI
description: Découvrez les commandes courantes permettant d’automatiser la gestion de votre instance managée Azure pour Apache Cassandra à l’aide d’Azure CLI.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/15/2021
ms.author: thvankra
ms.openlocfilehash: ea28bf21424f0624b4f1bb5856a17672c1c7b106
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875446"
---
# <a name="manage-azure-managed-instance-for-apache-cassandra-resources-using-azure-cli-preview"></a>Gérer les ressources Azure Managed Instance pour Apache Cassandra avec Azure CLI (préversion)

Cet article décrit les commandes courantes permettant d’automatiser la gestion de vos clusters Azure Managed Instance pour Apache Cassandra à l’aide d’Azure CLI.

> [!IMPORTANT]
> Azure Managed Instance pour Apache Cassandra est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Pour cet article, vous avez besoin d’Azure CLI version 2.17.1 ou ultérieure. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.
>
> Les ressources managées Azure Managed Instance pour Apache Cassandra ne peuvent pas être renommées, car cela enfreint les règles de traitement des URI de ressources par Azure Resource Manager.

## <a name="azure-managed-instance-for-apache-cassandra-clusters"></a>Clusters Azure Managed Instance pour Apache Cassandra

Les sections suivantes montrent comment gérer les clusters Azure Managed Instance pour Apache Cassandra, notamment :

* [Créer un cluster Managed Instance](#create-cluster)
* [Supprimer un cluster Managed Instance](#delete-cluster)
* [Obtenir les détails du cluster](#get-cluster-details)
* [Obtenir le statut des nœuds de cluster](#get-cluster-status)
* [Lister les clusters par groupe de ressources](#list-clusters-resource-group)
* [Lister les clusters par ID d’abonnement](#list-clusters-subscription)

### <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Créer un cluster Managed Instance

Créez un cluster Azure Managed Instance pour Apache Cassandra à l’aide de la commande [az managed-cassandra cluster create](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
location='West US'
delegatedManagementSubnetId='/subscriptions/<subscription id>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/management'
initialCassandraAdminPassword='myPassword'

# You can override the cluster name if the original name is not legal for an Azure resource.
# overrideClusterName='ClusterNameIllegalForAzureResource'
# the default Cassandra version is v3.11

az managed-cassandra cluster create \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName \
    --location $location \
    --delegated-management-subnet-id $delegatedManagementSubnetId \
    --initial-cassandra-admin-password $initialCassandraAdminPassword \
```

### <a name="delete-a-managed-instance-cluster"></a><a id="delete-cluster"></a>Supprimer un cluster Managed Instance

Supprimez un cluster à l’aide de la commande [az managed-cassandra cluster delete](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_delete) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster delete \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-details"></a><a id="get-cluster-details"></a>Obtenir les détails du cluster

Récupérez les détails du cluster à l’aide de la commande [az managed-cassandra cluster show](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_show) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster show \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-node-status"></a><a id="get-cluster-status"></a>Obtenir le statut des nœuds de cluster

Récupérez les détails du nœud de cluster à l’aide de la commande [az managed-cassandra cluster node-status](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_node_status) :

```azurecli-interactive
clusterName='cassandra-hybrid-cluster'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster node-status \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="list-the-clusters-by-resource-group"></a><a id="list-clusters-resource-group"></a>Lister les clusters par groupe de ressources

Répertoriez les clusters par groupe de ressources à l’aide de la commande [az managed-cassandra cluster list](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_list) :

```azurecli-interactive
subscriptionId='MySubscriptionId'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster list\
    --resource-group $resourceGroupName
```

### <a name="list-clusters-by-subscription-id"></a><a id="list-clusters-subscription"></a>Lister les clusters par ID d’abonnement

Répertoriez les clusters par ID d’abonnement à l’aide de la commande [az managed-cassandra cluster list](/cli/azure/managed-cassandra?view=azure-cli-latest&preserve-view=true) :

```azurecli-interactive
# set your subscription id
az account set -s <subscription id>

az managed-cassandra cluster list
```

## <a name="the-managed-instance-datacenters"></a><a id="managed-instance-datacenter"></a>Les centres de données d’instance managée

Les sections suivantes montrent comment gérer les centres de données Azure Managed Instance pour Apache Cassandra, notamment :

* [Créer un centre de données](#create-datacenter)
* [Supprimer un centre de données](#delete-datacenter)
* [Obtenir les détails d’un centre de données](#get-datacenter-details)
* [Mettre à jour ou à l’échelle un centre de données](#update-datacenter)
* [Obtenir les centres de données dans un cluster](#get-datacenters-cluster)

### <a name="create-a-datacenter"></a><a id="create-datacenter"></a>Créer un centre de données

Créez un centre de données à l’aide de la commande [az managed-cassandra datacenter create](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus2'
delegatedSubnetId='/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter create \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --data-center-location $dataCenterLocation \
    --delegated-subnet-id $delegatedSubnetId \
    --node-count 3 
```

### <a name="delete-a-datacenter"></a><a id="delete-datacenter"></a>Supprimer un centre de données

Supprimez un centre de données à l’aide de la commande [az managed-cassandra datacenter delete](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_delete) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter delete \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="get-datacenter-details"></a><a id="get-datacenter-details"></a>Obtenir les détails d’un centre de données

Récupérez des informations sur le centre de données à l’aide de la commande [az managed-cassandra datacenter show](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_show) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter show \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="update-or-scale-a-datacenter"></a><a id="update-datacenter"></a>Mettre à jour ou à l’échelle un centre de données

Mettez à jour ou à l’échelle un centre de données (pour mettre à l’échelle, modifiez la valeur nodeCount) à l’aide de la commande [az managed-cassandra datacenter update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus'
delegatedSubnetId= '/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter update \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --node-count 13 
```

### <a name="get-the-datacenters-in-a-cluster"></a><a id="get-datacenters-cluster"></a>Obtenir les centres de données dans un cluster

Récupérez les centres de données dans un cluster à l’aide de la commande [az managed-cassandra datacenter list](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_list) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra datacenter list \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName
```

## <a name="next-steps"></a>Étapes suivantes

* [Créer un cluster Managed Instance à partir du portail Azure](create-cluster-portal.md)
* [Déployer un cluster Apache Spark managé avec Azure Databricks](deploy-cluster-databricks.md)
