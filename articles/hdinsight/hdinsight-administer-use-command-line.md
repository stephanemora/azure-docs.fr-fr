---
title: Gérer des clusters Azure HDInsight avec Azure CLI
description: Découvrez comment utiliser Azure CLI pour gérer des clusters Azure HDInsight. Les types de clusters incluent Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query et ML Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 2c6495454e5ba2449d4b3c74a096681f74610813
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199459"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Gérer des clusters Azure HDInsight avec Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Découvrez comment utiliser [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) pour gérer des clusters Azure HDInsight. L’interface de ligne de commande Azure (CLI) est l’interface de ligne de commande multiplateforme de Microsoft pour la gestion de ressources Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Azure CLI. Si vous n'avez pas installé Azure CLI, consultez [Installer l'interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) pour connaître les étapes à suivre.

* Un cluster Apache Hadoop sur HDInsight. Consultez [Bien démarrer avec HDInsight sur Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure. Si vous prévoyez d’utiliser Azure Cloud Shell, sélectionnez **Essayer** dans le coin supérieur droit du bloc de code. Sinon, entrez la commande suivante :

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Lister les clusters

Utilisez [az hdinsight list](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) pour lister les clusters. Modifiez les commandes ci-dessous en remplaçant `RESOURCE_GROUP_NAME` par le nom de votre groupe de ressources, puis entrez ces commandes :

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>Afficher le cluster

Utilisez [az hdinsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) pour afficher les informations d’un cluster spécifié. Modifiez la commande ci-dessous en remplaçant `RESOURCE_GROUP_NAME` et `CLUSTER_NAME` par les informations appropriées, puis entrez cette commande :

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Suppression des clusters

Utilisez [az hdinsight delete](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) pour supprimer un cluster spécifié. Modifiez la commande ci-dessous en remplaçant `RESOURCE_GROUP_NAME` et `CLUSTER_NAME` par les informations appropriées, puis entrez cette commande :

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Vous pouvez également supprimer un cluster en supprimant le groupe de ressources qui le contient. Notez que cette opération supprime toutes les ressources dans le groupe, y compris le compte de stockage par défaut.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Mise à l’échelle des clusters

Utilisez [az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) pour redimensionner le cluster HDInsight spécifié à la taille voulue. Modifiez la commande ci-dessous en remplaçant `RESOURCE_GROUP_NAME` et `CLUSTER_NAME` par les informations appropriées. Remplacez `WORKERNODE_COUNT` par le nombre de nœuds Worker souhaités pour votre cluster. Pour plus d’informations sur la mise à l’échelle des clusters, consultez [Mettre à l’échelle les clusters HDInsight](./hdinsight-scaling-best-practices.md). Entrez la commande :

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment effectuer différentes tâches d’administration sur un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Gérer des clusters Apache Hadoop dans HDInsight avec le portail Azure](hdinsight-administer-use-portal-linux.md)
* [Administrer HDInsight à l’aide d’Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Prise en main d’Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Bien démarrer avec Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
