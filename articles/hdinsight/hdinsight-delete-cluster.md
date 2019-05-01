---
title: Guide pratique pour supprimer un cluster HDInsight - Azure
description: Informations sur les différentes méthodes que vous pouvez utiliser pour supprimer un cluster HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: eca7b4f8bd7e91bc8dcb9bcc49ed3b981010aaee
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721017"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Suppression d’un cluster HDInsight à l’aide de votre navigateur, PowerShell ou l’interface de ligne de commande Azure

La facturation du cluster HDInsight démarre à la création du cluster et s’arrête à sa suppression. La facturation est effectuée au prorata des minutes écoulées. Par conséquent, vous devez toujours supprimer votre cluster lorsqu’il n’est plus utilisé. Dans ce document, vous allez apprendre à supprimer un cluster à l’aide de la [Azure portal](https://portal.azure.com), [module d’Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/overview)et le [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> La suppression d’un cluster HDInsight ne supprime pas les comptes Stockage Azure ou le Data Lake Storage associés au cluster. Vous pouvez réutiliser les données stockées dans ces services à l’avenir.

## <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Dans le menu de gauche, accédez à **tous les services** > **Analytique** > **HDInsight clusters** et sélectionnez votre cluster.

3. Dans la vue par défaut, sélectionnez le **supprimer** icône. Suivez les instructions pour supprimer votre cluster.
   
    ![icône Supprimer](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell-az-module"></a>Module PowerShell Az Azure

Remplacez `CLUSTERNAME` par le nom de votre cluster HDInsight dans le code ci-dessous. À partir d’une invite PowerShell, entrez la commande suivante pour supprimer le cluster :

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

Remplacez `CLUSTERNAME` avec le nom de votre cluster HDInsight, et `RESOURCEGROUP` par le nom de votre groupe de ressources dans le code ci-dessous.  À partir d’une invite de commandes, entrez la commande suivante pour supprimer le cluster :

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
