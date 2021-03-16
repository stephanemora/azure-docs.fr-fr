---
title: 'Démarrage rapide : Utiliser l’interface CLI pour créer un cluster Azure Managed Instance pour Apache Cassandra'
description: Utilisez ce guide de démarrage rapide pour créer un cluster Azure Managed Instance pour Apache Cassandra avec Azure CLI.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 86fa7e2e45dacb86b6601b699dca46b1b909fd08
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102424697"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Guide de démarrage rapide : Créer un cluster Azure Managed Instance pour Apache Cassandra à l’aide d’Azure CLI (préversion)

Azure Managed Instance pour Apache Cassandra offre des opérations de déploiement et de mise à l’échelle automatisées pour les centres de données Apache Cassandra open source managés. Ce service vous aide à accélérer les scénarios hybrides et à réduire la maintenance en cours.

> [!IMPORTANT]
> Azure Managed Instance pour Apache Cassandra est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce guide de démarrage rapide vous montre comment utiliser les commandes Azure CLI pour créer un cluster avec Azure Managed Instance pour Apache Cassandra. Il montre également comment créer un centre de données, et comment effectuer un scale-up ou un scale-down dans le centre de données.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Pour cet article, vous avez besoin d’Azure CLI version 2.12.1 ou ultérieure. Si vous utilisez Azure Cloud Shell, sachez que la version la plus récente est déjà installée.

* Un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) connecté à votre environnement autohébergé ou local. Pour plus d’informations sur la connexion d’environnements locaux à Azure, consultez l’article [Connecter un réseau local à Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/).

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.


## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Créer un cluster Managed Instance

1. Connectez-vous au [portail Azure](https://portal.azure.com/)

1. Définissez l’ID de votre abonnement dans Azure CLI :

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. Créez ensuite un réseau virtuel avec un sous-réseau dédié dans votre groupe de ressources :

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```

1. Appliquez certaines autorisations spéciales, qui sont requises par l’instance managée, au réseau virtuel et au sous-réseau. Utilisez la commande `az role assignment create`, en remplaçant `<subscription ID>`, `<resource group name>`, `<VNet name>` et `<subnet name>` par les valeurs appropriées :

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > Les valeurs `assignee` et `role` dans la commande précédente correspondent aux identificateurs fixes du principal de service et du rôle, respectivement.

1. Créez maintenant le cluster dans le réseau virtuel que vous venez de créer. Exécutez la commande suivante, en veillant à utiliser la valeur du paramètre `Resource ID` obtenue dans la commande précédente comme valeur de la variable `delegatedManagementSubnetId` :

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='<Resource_ID>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. Pour finir, créez un centre de données pour le cluster, avec trois nœuds :

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId='<Resource_ID>'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedSubnetId \
      --node-count 3 
   ```

1. Une fois le centre de données créé, vous pouvez effectuer un scale-up ou un scale-down des nœuds qu’il contient. Pour cela, exécutez la commande ci-dessous. Remplacez la valeur du paramètre `node-count` par la valeur souhaitée :

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId= '<Resource_ID>'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>Se connecter au cluster

Azure Managed Instance pour Apache Cassandra ne crée pas de nœuds avec des adresses IP publiques. Pour vous connecter à votre nouveau cluster Cassandra, vous devez créer une autre ressource dans le réseau virtuel. Cette ressource peut être une application ou bien une machine virtuelle sur laquelle [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html), l’outil de requête open source d’Apache, est installé. Vous pouvez utiliser un [modèle Resource Manager](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) pour déployer une machine virtuelle Ubuntu. Après avoir déployé la machine, utilisez SSH pour vous y connecter et installez CQLSH comme indiqué dans les commandes suivantes :

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin du groupe de ressources, de l’instance managée ni d’aucune des ressources associées, vous pouvez les supprimer avec la commande `az group delete` :

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un cluster Azure Managed Instance pour Apache Cassandra avec Azure CLI. Vous pouvez maintenant commencer à utiliser le cluster :

> [!div class="nextstepaction"]
> [Déployer un cluster Apache Spark managé avec Azure Databricks](deploy-cluster-databricks.md)
