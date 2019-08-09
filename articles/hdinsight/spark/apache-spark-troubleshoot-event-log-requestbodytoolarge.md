---
title: NativeAzureFileSystem ... RequestBodyTooLarge s’affiche dans le journal de l’application de diffusion en continu Apache Spark dans Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge s’affiche dans le journal de l’application de diffusion en continu Apache Spark dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 571e02e79714d2e713d4173936120e78e4b067de
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700197"
---
# <a name="scenario-nativeazurefilesystem--requestbodytoolarge-appears-in-log-for-apache-spark-streaming-app-in-azure-hdinsight"></a>Scénario : « NativeAzureFileSystem ... RequestBodyTooLarge » s’affiche dans le journal de l’application de diffusion en continu Apache Spark dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Apache Spark dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

L’erreur `NativeAzureFileSystem ... RequestBodyTooLarge` s’affiche dans le journal du pilote d'une application de diffusion en continu Apache Spark.

## <a name="cause"></a>Cause :

Votre fichier journal des événements Spark a probablement atteint la limite de longueur de fichier pour WASB.

Dans Spark 2.3, chaque application Spark génère un fichier journal des événements Spark. Le fichier journal des événements Spark pour une application de diffusion en continu Spark continue d'augmenter tant que l'application est exécutée. Actuellement, un fichier sur WASB présente une limite de bloc de 50000, et la taille de bloc par défaut est de 4 Mo. Ainsi, dans la configuration par défaut, la taille maximale des fichiers est de 195 Go. Toutefois, Stockage Azure a augmenté la taille de bloc maximale à 100 Mo, ce qui a repoussé la limite des fichiers à 4,75 To. Pour plus d'informations, consultez [Objectifs d'extensibilité et de performances d'Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

## <a name="resolution"></a>Résolution :

Trois solutions sont disponibles pour cette erreur :

* Augmentez la taille de bloc jusqu’à 100 Mo. Dans l’interface utilisateur Ambari, modifiez la propriété de configuration HDFS `fs.azure.write.request.size` (ou créez-la dans la section `Custom core-site`). Définissez la propriété sur une plus grande valeur, par exemple : 33554432. Enregistrez la configuration mise à jour et redémarrez les composants concernés.

* Arrêtez et renvoyez régulièrement le travail spark-streaming.

* Utilisez HDFS pour stocker les journaux des événements Spark. L’utilisation de HDFS à des fins de stockage peut entraîner une perte de données liées aux événements Spark lors de la mise à l’échelle du cluster ou les mises à niveau Azure.

    1. Modifiez `spark.eventlog.dir` et `spark.history.fs.logDirectory` via l’interface utilisateur Ambari :

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Créez des répertoires sur HDFS :

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Redémarrez tous les services concernés via l’interface utilisateur Ambari.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une demande de support à partir du [portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, consultez [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus dans votre abonnement Microsoft Azure. En outre, un support technique est fourni via l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
