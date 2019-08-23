---
title: Résoudre les problèmes liés à Spark dans Azure HDInsight
description: Obtenez les réponses aux questions courantes sur l’utilisation d’Apache Spark et d’Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: c88136fee7a75b8f3b8e504b1ff1e6673a31bcf7
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543162"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Résolution de problèmes Apache Spark à l’aide d’Azure HDInsight

Découvrez les principaux problèmes rencontrés lors de l’utilisation de charges utiles [Apache Spark](https://spark.apache.org/) dans [Apache Ambari](https://ambari.apache.org/), et leur résolution.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Comment configurer une application Apache Spark sur des clusters via Apache Ambari ?

### <a name="resolution-steps"></a>Étapes de résolution

Les valeurs de configuration Spark peuvent être paramétrées afin d’éviter l’exception OutofMemoryError de l’application Apache Spark. Les étapes suivantes montrent des valeurs de configuration Spark par défaut dans Azure HDInsight : 

1. Dans la liste de clusters, sélectionnez **Spark2**.

    ![Sélectionnez le cluster dans la liste](./media/apache-troubleshoot-spark/update-config-1.png)

2. Sélectionnez l'onglet **Configurations** .

    ![Sélectionnez l’onglet Configurations](./media/apache-troubleshoot-spark/update-config-2.png)

3. Dans la liste des configurations, sélectionnez **Custom-spark2-defaults**.

    ![Sélectionnez custom-spark-defaults](./media/apache-troubleshoot-spark/update-config-3.png)

4. Recherchez le paramètre de valeur que vous avez besoin d’ajuster, par exemple **spark.executor.memory**. Dans le cas présent, la valeur de **4608m** est trop élevée.

    ![Sélectionnez le champ spark.executor.memory](./media/apache-troubleshoot-spark/update-config-4.png)

5. Définissez la valeur sur le paramètre recommandé. La valeur **2048m** est recommandée pour ce paramètre.

    ![Remplacez la valeur par 2048m](./media/apache-troubleshoot-spark/update-config-5.png)

6. Enregistrez la valeur, puis la configuration. Dans la barre d’outils, sélectionnez **Enregistrer**.

    ![Enregistrez le paramètre et la configuration](./media/apache-troubleshoot-spark/update-config-6a.png)

    Si des configurations requièrent votre attention, un message s’affiche. Notez les éléments, puis sélectionnez **Proceed Anyway** (Continuer). 

    ![Sélectionnez Proceed Anyway (Continuer)](./media/apache-troubleshoot-spark/update-config-6b.png)

    Notez les modifications apportées à la configuration, puis sélectionnez **Enregistrer**.

    ![Saisissez une note concernant les modifications apportées](./media/apache-troubleshoot-spark/update-config-6c.png)

7. À chaque fois que vous enregistrez une configuration, vous êtes invité à redémarrer le service. Sélectionnez **Redémarrer**.

    ![Sélectionnez Redémarrer](./media/apache-troubleshoot-spark/update-config-7a.png)

    Confirmez le redémarrage.

    ![Sélectionnez Confirm Restart All (Confirmer le redémarrage)](./media/apache-troubleshoot-spark/update-config-7b.png)

    Vous pouvez examiner les processus en cours d’exécution.

    ![Examinez les processus en cours d’exécution](./media/apache-troubleshoot-spark/update-config-7c.png)

8. Vous pouvez ajouter des configurations. Dans la liste des configurations, sélectionnez **Custom-spark2-defaults**, puis **Ajouter une propriété**.

    ![Sélectionnez Ajouter une propriété](./media/apache-troubleshoot-spark/update-config-8.png)

9. Définissez une nouvelle propriété. Vous pouvez définir une propriété unique via une boîte de dialogue de paramètres spécifiques tels que le type de données. Vous pouvez également définir plusieurs propriétés en utilisant une définition par ligne. 

    Dans cet exemple, la propriété **spark.driver.memory** est définie avec une valeur de **4g**.

    ![Définissez une nouvelle propriété](./media/apache-troubleshoot-spark/update-config-9.png)

10. Enregistrez la configuration et redémarrez le service en suivant la procédure décrite aux étapes 6 et 7.

Ces modifications s’appliquent à l’ensemble du cluster, mais elles peuvent être remplacées au moment de l’envoi du travail Spark.

### <a name="additional-reading"></a>Documentation supplémentaire

[Apache Spark job submission on HDInsight clusters](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/) (Envoi de travaux Spark sur des clusters HDInsight)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Comment configurer une application Apache Spark sur des clusters via un bloc-notes Jupyter ?

### <a name="resolution-steps"></a>Étapes de résolution

1. Pour connaître les configurations Spark concernées et les valeurs à définir, reportez-vous à la rubrique À quoi est dû l'échec d'une application Apache Spark avec l'exception OutOfMemoryError ?

2. Dans la première cellule du bloc-notes Jupyter, après la directive **%%configure**, spécifiez les configurations Spark dans un format JSON valide. Modifiez les valeurs si nécessaire :

    ![Ajoutez une configuration](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Documentation supplémentaire

[Apache Spark job submission on HDInsight clusters](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/) (Envoi de travaux Spark sur des clusters HDInsight)


## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Comment configurer une application Apache Spark sur des clusters via Apache Livy ?

### <a name="resolution-steps"></a>Étapes de résolution

1. Pour connaître les configurations Spark concernées et les valeurs à définir, reportez-vous à la rubrique À quoi est dû l'échec d'une application Apache Spark avec l'exception OutOfMemoryError ? 

2. Soumettez la demande de l’application Spark à Livy à l’aide d’un client REST comme cURL. Utilisez une commande similaire à la suivante. Modifiez les valeurs si nécessaire :

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Documentation supplémentaire

[Apache Spark job submission on HDInsight clusters](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/) (Envoi de travaux Spark sur des clusters HDInsight)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Comment configurer une application Apache Spark sur des clusters via spark-submit ?

### <a name="resolution-steps"></a>Étapes de résolution

1. Pour connaître les configurations Spark concernées et les valeurs à définir, reportez-vous à la rubrique À quoi est dû l'échec d'une application Apache Spark avec l'exception OutOfMemoryError ?

2. Lancez spark-shell à l’aide d’une commande semblable à la suivante. Modifiez la valeur des configurations selon les besoins : 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Documentation supplémentaire

[Apache Spark job submission on HDInsight clusters](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/) (Envoi de travaux Spark sur des clusters HDInsight)

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* [Vue d’ensemble de la gestion de la mémoire dans Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Débogage d’une application Spark sur des clusters HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour plus d’informations, consultez [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
