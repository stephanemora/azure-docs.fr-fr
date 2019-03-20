---
title: Gérer des clusters Apache Hadoop à l’aide d’Azure Classic CLI - Azure HDInsight
description: Découvrez comment utiliser Azure Classic CLI pour gérer des clusters Apache Hadoop dans Azure HDInsight.
services: hdinsight
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: tyfox
ms.openlocfilehash: 61be4088034d8cc557c67c5b6de4972766654a39
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202382"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-using-the-azure-classic-cli"></a>Gérer des clusters Apache Hadoop dans HDInsight à l’aide d’Azure Classic CLI
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Découvrez comment utiliser [Azure Classic CLI](../cli-install-nodejs.md) pour gérer des clusters [Apache Hadoop](https://hadoop.apache.org/) dans Azure HDInsight. L’interface Classic CLI est implémentée dans Node.js. Elle peut être utilisée sur toute plateforme prenant en charge Node.js, y compris Windows, Mac et Linux.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>Conditions préalables
Avant de commencer cet article, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Azure Classic CLI** : pour obtenir des informations sur son installation et sa configuration, consultez [Installer et configurer Azure Classic CLI](../cli-install-nodejs.md).
* **Connectez-vous à Azure**en utilisant la commande suivante :

    ```cli
    azure login
    ```
  
    Pour plus d’informations sur l’authentification à l’aide d’un compte professionnel ou scolaire, consultez [Se connecter à un abonnement Azure à partir d’Azure Classic CLI](/cli/azure/authenticate-azure-cli).
* **Passez en mode Azure Resource Manager**en exécutant la commande suivante :
  
    ```cli
    azure config mode arm
    ```

Pour obtenir de l’aide, utilisez le commutateur **-h** .  Par exemple : 

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Créer des clusters avec l’interface de ligne de commande
Consultez [Créer des clusters dans HDInsight à l’aide d’Azure Classic CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Énumération et affichage des détails de cluster
Utilisez les commandes suivantes pour énumérer et afficher les détails de cluster :

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![Vue de ligne de commande de la liste de clusters][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Suppression des clusters
Utilisez la commande suivante pour supprimer un cluster :

```cli
azure hdinsight cluster delete <Cluster Name>
```

Vous pouvez également supprimer un cluster en supprimant le groupe de ressources qui le contient. Notez que cette opération supprime toutes les ressources dans le groupe, notamment le compte de stockage par défaut.

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>Mise à l’échelle des clusters
Pour modifier la taille du cluster Apache Hadoop :

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>Activer / désactiver l’accès HTTP pour un cluster

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris comment effectuer différentes tâches d'administration sur un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Gérer des clusters Apache Hadoop dans HDInsight avec le portail Azure](hdinsight-administer-use-portal-linux.md)
* [Administration de HDInsight à l'aide d'Azure PowerShell][hdinsight-admin-powershell]
* [Prise en main d’Azure HDInsight][hdinsight-get-started]
* [Guide pratique pour utiliser Azure Classic CLI][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Énumération et affichage des clusters"
