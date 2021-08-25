---
title: 'Démarrage rapide : Utiliser l’interface CLI pour créer un cluster Azure Managed Instance pour Apache Cassandra'
description: Utilisez ce guide de démarrage rapide pour créer un cluster Azure Managed Instance pour Apache Cassandra avec Azure CLI.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/15/2021
ms.openlocfilehash: 6387b07aa29a836eae79b6090571f143091d2115
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749506"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Guide de démarrage rapide : Créer un cluster Azure Managed Instance pour Apache Cassandra à l’aide d’Azure CLI (préversion)

Azure Managed Instance pour Apache Cassandra offre des opérations de déploiement et de mise à l’échelle automatisées pour les centres de données Apache Cassandra open source managés. Ce service vous aide à accélérer les scénarios hybrides et à réduire la maintenance en cours.

> [!IMPORTANT]
> Azure Managed Instance pour Apache Cassandra est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce guide de démarrage rapide vous montre comment utiliser les commandes Azure CLI pour créer un cluster avec Azure Managed Instance pour Apache Cassandra. Il montre également comment créer un centre de données, et comment effectuer un scale-up ou un scale-down dans le centre de données.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) connecté à votre environnement autohébergé ou local. Pour plus d’informations sur la connexion d’environnements locaux à Azure, consultez l’article [Connecter un réseau local à Azure](/azure/architecture/reference-architectures/hybrid-networking/).

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!IMPORTANT]
> Pour cet article, vous avez besoin d’Azure CLI version 2.17.1 ou ultérieure. Si vous utilisez Azure Cloud Shell, sachez que la version la plus récente est déjà installée.

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
    > [!NOTE]
    > Le déploiement d’une instance Azure Managed Instance pour Apache Cassandra nécessite un accès à Internet. Le déploiement échoue dans les environnements où l’accès à Internet est limité. Vérifiez que vous ne bloquez pas l’accès dans votre réseau virtuel aux services Azure essentiels qui sont nécessaires au bon fonctionnement de Managed Instance pour Apache Cassandra :
    > - Stockage Azure
    > - Azure KeyVault
    > - Groupes de machines virtuelles identiques Azure
    > - Surveillance Azure
    > - Azure Active Directory
    > - Sécurité Azure

1. Appliquez au réseau virtuel certaines autorisations spéciales qui sont demandées par l’instance managée. Utilisez la commande `az role assignment create`, en remplaçant `<subscription ID>`, `<resource group name>` et `<VNet name>` par les valeurs appropriées :

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > Les valeurs `assignee` et `role` de la commande précédente sont des valeurs fixes. Entrez ces valeurs exactement comme indiqué dans la commande. Sinon, cela entraînera des erreurs lors de la création du cluster. Si vous rencontrez des erreurs lors de l’exécution de cette commande, vous ne disposez peut-être pas des autorisations nécessaires pour l’exécuter. Contactez votre administrateur pour obtenir les autorisations.

1. Ensuite, créez le cluster dans votre réseau virtuel nouvellement créé à l’aide de la commande [az managed-cassandra cluster create](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create). Exécutez la commande suivante comme valeur de la variable `delegatedManagementSubnetId` :

   > [!NOTE]
   > La valeur de la variable `delegatedManagementSubnetId` que vous allez fournir ci-dessous est exactement la même que la valeur de `--scope` que vous avez fournie dans la commande ci-dessus :

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. Enfin, créez un centre de connaissances pour le cluster avec trois nœuds à l’aide de la commande [az managed-cassandra datacenter create](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create) :

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedManagementSubnetId \
      --node-count 3 
   ```

1. Une fois le centre de données créé, vous pouvez effectuer un scale-up ou un scale-down des nœuds qu’il contientn exécutez la commande [az managed-cassandra datacenter update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update). Remplacez la valeur du paramètre `node-count` par la valeur souhaitée :

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>Se connecter au cluster

Azure Managed Instance pour Apache Cassandra ne crée pas de nœuds avec des adresses IP publiques. Pour vous connecter à votre nouveau cluster Cassandra, vous devez créer une autre ressource dans le réseau virtuel. Cette ressource peut être une application ou bien une machine virtuelle sur laquelle [CQLSH](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html), l’outil de requête open source d’Apache, est installé. Vous pouvez utiliser un [modèle Resource Manager](https://azure.microsoft.com/resources/templates/vm-simple-linux/) pour déployer une machine virtuelle Ubuntu. Après avoir déployé la machine, utilisez SSH pour vous y connecter et installez CQLSH comme indiqué dans les commandes suivantes :

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

## <a name="troubleshooting"></a>Dépannage

Si vous rencontrez une erreur lors de l’application des autorisations à votre réseau virtuel, comme *Utilisateur ou principal de service introuvable dans la base de données de graphe pour 'e5007d2c-4b13-4a74-9b6a-605d99f03501'* , vous pouvez appliquer la même autorisation manuellement à partir du portail Azure. Pour appliquer des autorisations à partir du portail, accédez au volet **Contrôle d’accès (IAM)** de votre réseau virtuel existant et ajoutez une attribution de rôle pour « Azure Cosmos DB » au rôle « Administrateur réseau ». Si deux entrées s’affichent lorsque vous recherchez « Azure Cosmos DB », ajoutez les deux entrées comme indiqué dans l’image suivante : 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Appliquer les autorisations" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> L’attribution de rôle Azure Cosmos DB est utilisée à des fins de déploiement uniquement. Azure Managed Instance pour Apache Cassandra n’a aucune dépendance back-end sur Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin du groupe de ressources, de l’instance managée ni d’aucune des ressources associées, vous pouvez les supprimer avec la commande `az group delete` :

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un cluster Azure Managed Instance pour Apache Cassandra avec Azure CLI. Vous pouvez maintenant commencer à utiliser le cluster :

> [!div class="nextstepaction"]
> [Déployer un cluster Apache Spark managé avec Azure Databricks](deploy-cluster-databricks.md)
