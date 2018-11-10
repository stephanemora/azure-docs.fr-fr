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
ms.openlocfilehash: dff9420eb0f91652cc134a37d1b248e2e5b2b681
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004527"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-classic-cli"></a>Supprimer un cluster HDInsight à l’aide de votre navigateur, de PowerShell ou d’Azure Classic CLI

La facturation du cluster HDInsight démarre à la création du cluster et s’arrête à sa suppression. La facturation est effectuée au prorata des minutes écoulées. Par conséquent, vous devez toujours supprimer votre cluster lorsqu’il n’est plus utilisé. Dans ce document, vous découvrirez comment supprimer un cluster avec le portail Azure, Azure PowerShell et Azure Classic CLI.

> [!IMPORTANT]
> La suppression d’un cluster HDInsight ne supprime pas les comptes de stockage Azure ou le Data Lake Store associés au cluster. Vous pouvez réutiliser les données stockées dans ces services à l’avenir.

## <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) et sélectionnez votre cluster HDInsight. Si votre cluster HDInsight n’est pas épinglé au tableau de bord, vous pouvez le rechercher par son nom dans le champ de recherche.
   
    ![recherche dans le portail](./media/hdinsight-delete-cluster/navbar.png)

2. Dans les paramètres de cluster, sélectionnez l’icône **Supprimer**. Lorsque vous y êtes invité, sélectionnez **Oui** pour supprimer le cluster.
   
    ![icône Supprimer](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

À partir d’une invite PowerShell, utilisez la commande suivante pour supprimer le cluster :

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight.

## <a name="azure-classic-cli"></a>Azure Classic CLI

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

À partir d’une invite, utilisez la commande suivante pour supprimer le cluster :

    azure hdinsight cluster delete CLUSTERNAME

Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight.
