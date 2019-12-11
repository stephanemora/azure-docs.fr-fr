---
title: Apache Hadoop et stockage avec transfert sécurisé – Azure HDInsight
description: Découvrez comment créer des clusters HDInsight à l’aide de comptes de stockage Azure doté du transfert sécurisé.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/24/2018
ms.openlocfilehash: ed8e20509c4a3f941d6f215dfc476c87e9a813a7
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687779"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Créer un cluster Apache Hadoop à l’aide de comptes de stockage avec transfert sécurisé dans Azure HDInsight

La fonctionnalité [Transfert sécurisé requis](../storage/common/storage-require-secure-transfer.md) améliore la sécurité de votre compte de stockage Azure en appliquant toutes les demandes à votre compte via une connexion sécurisée. Cette fonctionnalité et le schéma wasbs sont uniquement pris en charge par les clusters HDInsight 3.6 ou version ultérieure.

## <a name="prerequisites"></a>Prérequis

Avant de commencer cet article, vous devez disposer des éléments suivants :

* **Abonnement Azure** : Pour créer un compte d’essai gratuit d’une durée d’un mois, accédez à [azure.microsoft.com/free](https://azure.microsoft.com/free).
* **Un compte de stockage Azure doté du transfert sécurisé**. Pour obtenir des instructions, consultez [Créez un compte de stockage.](../storage/common/storage-quickstart-create-account.md) et [Exiger un transfert sécurisé](../storage/common/storage-require-secure-transfer.md). L’activation du transfert de stockage sécurisé après la création d’un cluster nécessite des étapes supplémentaires qui ne sont pas décrites dans cet article.
* **Un conteneur d’objets blob dans le compte de stockage**.

## <a name="create-cluster"></a>Créer un cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Cette section vous permet de créer un cluster Hadoop dans HDInsight à l’aide d’un [modèle Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Ce modèle se trouve sur [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/). Aucune expérience avec le modèle Resource Manager n’est requise dans le cadre de cet article. Pour obtenir d’autres méthodes de création de cluster et comprendre les propriétés utilisées dans cet article, consultez [Création de clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Cliquez sur l’image suivante pour vous connecter à Azure et ouvrir le modèle Resource Manager dans le portail Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Suivez les instructions pour créer le cluster avec les spécifications suivantes : 

    - Indiquez HDInsight version 3.6. La version 3.6 ou une version ultérieure est requise.
    - Indiquez un compte de stockage doté du transfert sécurisé.
    - Utilisez un nom court pour le compte de stockage.
    - Le compte de stockage et le conteneur d’objets blob doivent être créés au préalable.

      Pour obtenir des instructions, consultez [Créer un cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

Si vous utilisez l’action de script pour fournir vos propres fichiers de configuration, vous devez utiliser wasbs dans les paramètres suivants :

- fs.defaultFS (site principal)
- spark.eventLog.dir 
- spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>Ajouter des comptes de stockage

Plusieurs options vous permettent d’ajouter des comptes de stockage dotés du transfert sécurisé :

- Modifiez le modèle Azure Resource Manager dans la dernière section.
- Créez un cluster à l’aide du [portail Azure](https://portal.azure.com) et indiquez le compte de stockage lié.
- Utilisez l’action de script pour ajouter des comptes de stockage doté du transfert sécurisé à un cluster HDInsight existant. Pour plus d’informations, consultez [Ajouter des comptes de stockage supplémentaires à HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à créer un cluster HDInsight et à activer le transfert sécurisé pour les comptes de stockage.

Pour en savoir plus sur l’analyse des données avec HDInsight, consultez les articles suivants :

* Pour en savoir plus sur l’utilisation d’[Apache Hive](https://hive.apache.org/) avec HDInsight, notamment sur l’exécution des requêtes Hive à partir de Visual Studio, consultez [Utiliser Apache Hive avec HDInsight][hdinsight-use-hive].
* Pour plus d’informations sur [Apache Pig](https://pig.apache.org/), un langage utilisé pour transformer les données, consultez [Utiliser Apache Pig avec HDInsight][hdinsight-use-pig].
* Pour plus d’informations sur [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), qui permet d’écrire des programmes pour traiter les données sur Hadoop, consultez [Utiliser Apache Hadoop MapReduce avec HDInsight][hdinsight-use-mapreduce].
* Pour en savoir plus sur l’utilisation des outils HDInsight pour Visual Studio afin d’analyser les données sur HDInsight, consultez la page [Prise en main des outils Apache Hadoop de Visual Studio pour HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Pour plus d’informations sur la façon dont HDInsight stocke les données ou sur la façon d’obtenir des données dans HDInsight, consultez les articles suivants :

* Pour plus d’informations sur la façon dont HDInsight utilise Stockage Azure, consultez la page [Utilisation de Stockage Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Pour plus d’informations sur le téléchargement de données dans HDInsight, consultez la page [Téléchargement de données dans HDInsight][hdinsight-upload-data].

Pour en savoir plus sur la création ou la gestion d’un cluster HDInsight, consultez les articles suivants :

* Pour en savoir plus sur la gestion de votre cluster HDInsight sous Linux, consultez [Gérer des clusters HDInsight à l’aide de l’interface utilisateur Web d’Ambari](hdinsight-hadoop-manage-ambari.md).
* Pour en savoir plus sur les options que vous pouvez sélectionner pendant la création d’un cluster HDInsight, consultez la page [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Si vous maîtrisez Linux et Apache Hadoop, mais que vous souhaitez connaître les spécificités de Hadoop sur HDInsight, consultez la page [Utilisation de HDInsight sur Linux](hdinsight-hadoop-linux-information.md). Cet article vous fournit les informations suivantes :

  * les URL correspondant aux services hébergés sur le cluster, tels qu’[Apache Ambari](https://ambari.apache.org/)et [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)
  * l'emplacement des fichiers [Apache Hadoop](https://hadoop.apache.org/) et des exemples sur le système de fichiers local
  * l'utilisation du stockage Azure (WASB) au lieu de [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html), en tant que stockage de données par défaut

[1]: ../HDInsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
