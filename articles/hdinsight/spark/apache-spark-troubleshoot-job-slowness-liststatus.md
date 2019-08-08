---
title: Le traitement des travaux de diffusion en continu Apache Spark dans Azure HDInsight prend plus de temps que d’habitude
description: Le traitement des travaux de diffusion en continu Apache Spark dans Azure HDInsight prend plus de temps que d’habitude
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 7c7baaaa5fd08a102b3b55c38fb2c4bf892480c5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679406"
---
# <a name="scenario-apache-spark-streaming-jobs-take-longer-than-usual-to-process-in-azure-hdinsight"></a>Scénario : Le traitement des travaux de diffusion en continu Apache Spark dans Azure HDInsight prend plus de temps que d’habitude

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Apache Spark dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Vous constatez que certains travaux de diffusion en continu Apache Spark sont lents ou que leur traitement est plus long que d’habitude. Pour les applications de diffusion en continu Spark, chaque lot de messages correspond à un travail soumis à Spark. Le traitement d’un travail dure normalement X secondes, mais il se peut qu’il dure 2 ou 3 minutes de plus que d’habitude dans certains cas.

## <a name="cause"></a>Cause :

L’une des causes possibles est que le producteur Apache Kafka prend plus de 2 minutes pour terminer l’écriture de données dans le cluster Kafka. Pour déboguer davantage le problème lié à Kafka, vous pouvez ajouter une journalisation au code qui utilise un producteur Kafka pour envoyer des messages, et le mettre en corrélation avec les journaux du cluster Kafka.

Il se peut également que ce problème soit lié à des lectures et écritures fréquentes dans WASB, qui entraînent un retard des micro-lots suivants. L’implémentation WASB du paramètre `Filesystem.listStatus` est très lente en raison d’un algorithme `O(n!)`, qui supprime les doublons. Cet algorithme utilise trop de mémoire en raison de la conversion supplémentaire de `BlobListItem` en `FileMetadata`, puis en `FileStatus`. Par exemple, l’algorithme prend plus de 30 minutes pour répertorier 700 000 fichiers. Par conséquent, si le paramètre `ListBlobs` est appelé de façon agressive par SparkSQL pour chaque micro-lot, les micro-lots suivants prennent du retard, ce qui se traduit par des retards de planification élevés. [Ce correctif](https://issues.apache.org/jira/browse/HADOOP-15547) résout le problème, mais s’il est manquant dans votre environnement, le paramètre `ListBlobs` sera confronté à une latence élevée. Par ailleurs, même si vous supprimez des fichiers toutes les heures, la liste du serveur back-end doit effectuer une itération sur toutes les lignes (y compris les lignes supprimées), car le processus garbage collection (GC) n’est pas encore terminé. Bien que le correctif puisse résoudre certains problèmes, le problème lié au processus garbage collection peut toujours entraîner un retard lors du traitement des flux de lots.

## <a name="resolution"></a>Résolution :

Appliquez le correctif [HADOOP-15547](https://issues.apache.org/jira/browse/HADOOP-15547). Si ce n’est pas possible, vous pouvez utiliser HDFS en tant qu’emplacement du point de contrôle. Définissez `checkpointDirectory` sur une valeur similaire à ce qui suit : `hdfs://mycluster/checkpoint`.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une demande de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus dans votre abonnement Microsoft Azure. En outre, le support technique est fourni via l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
