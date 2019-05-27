---
title: Gérer des clusters Azure HDInsight à l’aide d’Azure CLI
description: Découvrez comment utiliser l’interface CLI pour gérer les clusters Azure HDInsight. Types de cluster incluent Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query et les Services ML.
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tyfox
ms.openlocfilehash: 7c12831c43762ddc776e8d5701f002be97992cbc
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65859965"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Gérer des clusters Azure HDInsight à l’aide d’Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Découvrez comment utiliser [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) pour gérer les clusters Azure HDInsight. L’interface de ligne de commande Azure (CLI) est l’interface de ligne de commande multiplateforme de Microsoft pour la gestion de ressources Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables

* Azure CLI. Si vous n’avez pas installé l’interface CLI, consultez [installer l’interface CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) pour obtenir des instructions.

* Un cluster Apache Hadoop sur HDInsight. Consultez [prise en main HDInsight sous Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure. Si vous prévoyez d’utiliser Azure Cloud Shell, sélectionnez simplement **essayez-le** dans le coin supérieur droit du bloc de code. Sinon, entrez la commande suivante :

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Énumérer les clusters

Utilisez [liste de hdinsight az](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) pour répertorier les clusters. Modifiez les commandes ci-dessous en remplaçant `RESOURCE_GROUP_NAME` par le nom de votre groupe de ressources, puis entrez les commandes :

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

Utilisez [show de hdinsight az](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) pour afficher des informations pour un cluster spécifié. Modifiez la commande ci-dessous en remplaçant `RESOURCE_GROUP_NAME`, et `CLUSTER_NAME` avec les informations appropriées, puis entrez la commande :

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Suppression des clusters

Utilisez [az hdinsight supprime](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) pour supprimer un cluster spécifié. Modifiez la commande ci-dessous en remplaçant `RESOURCE_GROUP_NAME`, et `CLUSTER_NAME` avec les informations appropriées, puis entrez la commande :

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Vous pouvez également supprimer un cluster en supprimant le groupe de ressources qui le contient. Notez que Ceci supprimera toutes les ressources dans le groupe, notamment le compte de stockage par défaut.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Mise à l’échelle des clusters

Utilisez [az hdinsight redimensionner](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) pour redimensionner le cluster HDInsight spécifié à la taille spécifiée. Modifiez la commande ci-dessous en remplaçant `RESOURCE_GROUP_NAME`, et `CLUSTER_NAME` avec les informations appropriées. Remplacez `TARGET_INSTANCE_COUNT` avec le nombre souhaité de nœuds de travail pour votre cluster. Pour plus d’informations sur la mise à l’échelle des clusters, consultez [HDInsight de mise à l’échelle des clusters](./hdinsight-scaling-best-practices.md). Entrez la commande :

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --target-instance-count TARGET_INSTANCE_COUNT
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment effectuer différentes tâches d'administration sur un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Gérer des clusters Apache Hadoop dans HDInsight avec le portail Azure](hdinsight-administer-use-portal-linux.md)
* [Administrer HDInsight à l’aide d’Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Prise en main d’Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Bien démarrer avec Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
