---
title: Guide pratique pour supprimer un cluster HDInsight - Azure
description: Informations sur les différentes méthodes que vous pouvez utiliser pour supprimer un cluster HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 4df4fa29722dd3ad33cf1ce123877f04f9f4b4c1
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360386"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-classic-cli"></a>Supprimer un cluster HDInsight à l’aide de votre navigateur, de PowerShell ou d’Azure Classic CLI

La facturation du cluster HDInsight démarre à la création du cluster et s’arrête à sa suppression. La facturation est effectuée au prorata des minutes écoulées. Par conséquent, vous devez toujours supprimer votre cluster lorsqu’il n’est plus utilisé. Dans ce document, vous découvrirez comment supprimer un cluster avec le [portail Azure](https://portal.azure.com), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) et Azure Classic CLI.

> [!IMPORTANT]  
> La suppression d’un cluster HDInsight ne supprime pas les comptes Stockage Azure ou le Data Lake Storage associés au cluster. Vous pouvez réutiliser les données stockées dans ces services à l’avenir.

## <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) et sélectionnez votre cluster HDInsight. Si votre cluster HDInsight n’est pas épinglé au tableau de bord, vous pouvez le rechercher par son nom dans le champ de recherche.
   
    ![recherche dans le portail](./media/hdinsight-delete-cluster/navbar.png)

2. Dans les paramètres de cluster, sélectionnez l’icône **Supprimer**. Lorsque vous y êtes invité, sélectionnez **Oui** pour supprimer le cluster.
   
    ![icône Supprimer](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

À partir d’une invite PowerShell, utilisez la commande suivante pour supprimer le cluster :

    Remove-AzHDInsightCluster -ClusterName CLUSTERNAME

Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight.

## <a name="azure-classic-cli"></a>Azure Classic CLI

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

À partir d’une invite, utilisez la commande suivante pour supprimer le cluster :

    azure hdinsight cluster delete CLUSTERNAME

Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight.
