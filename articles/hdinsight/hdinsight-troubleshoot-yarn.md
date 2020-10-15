---
title: Résoudre les problèmes liés à YARN dans Azure HDInsight
description: Obtenez les réponses aux questions courantes sur l’utilisation d’Apache Hadoop YARN et d’Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: f0c7b966b9fa7580809d2df0f4d05a7146ca0fd1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91871964"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Résoudre les problèmes d’Apache Hadoop YARN avec Azure HDInsight

Découvrez les principaux problèmes rencontrés lors de l’utilisation de charges utiles Apache Hadoop YARN dans Apache Ambari, et leur résolution.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Comment créer une file d’attente YARN dans un cluster ?

### <a name="resolution-steps"></a>Étapes de résolution

Effectuez les étapes suivantes via Ambari pour créer une file d’attente YARN, puis équilibrer l’allocation de capacité entre toutes les files d’attente.

Dans cet exemple, la capacité de deux files d’attente existantes (**default** et **thriftsvr**) est modifiée de 50 % à 25 %, ce qui permet à la nouvelle file d’attente (spark) de bénéficier d’une capacité de 50 %.

| File d'attente | Capacité | Capacité maximale |
| --- | --- | --- |
| default | 25 % | 50 |
| thrftsvr | 25 % | 50 |
| spark | 50 | 50 |

1. Cliquez sur l’icône **Vues Ambari**, puis sur l’icône de grille. Sélectionnez ensuite **YARN Queue Manager** (Gestionnaire de files d’attente YARN).

    ![Tableau de bord Apache Ambari - YARN - Gestionnaire de files d’attente](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png)
2. Sélectionnez la file d’attente **default** (par défaut).

    ![Apache Ambari - YARN - Sélection de la file d’attente par défaut](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png)
3. Modifiez la valeur **Capacity** (Capacité) de 50 % à 25 % pour la file d’attente **default** (par défaut) et faites de **même** pour la file d’attente **thriftsvr** .

    ![Définir la capacité sur 25 % pour les files d’attente default et thriftsvr](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png)
4. Cliquez sur **Add Queue** (Ajouter une file d’attente) pour créer une file d’attente.

    ![Tableau de bord Apache Ambari - YARN - Ajout d’une file d’attente](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png)

5. Nommez la nouvelle file d’attente.

    ![Tableau de bord Apache Ambari - YARN - Nommage d’une file d’attente](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png)  

6. Conservez la valeur de **capacité** de 50 %, puis cliquez sur le bouton **Actions**.

    ![Apache Ambari - YARN - Sélection d’une action](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png)  
7. Sélectionnez **Save and Refresh Queues** (Enregistrer et actualiser les files d’attente).

    ![Sélectionner Save and Refresh Queues](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png)  

Ces modifications se répercutent immédiatement sur l’interface utilisateur du planificateur YARN.

### <a name="additional-reading"></a>Documentation supplémentaire

- [Apache Hadoop YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Comment télécharger les journaux d’activité YARN à partir d’un cluster ?

### <a name="resolution-steps"></a>Étapes de résolution

1. Connectez-vous au cluster HDInsight à l’aide d’un client Secure Shell (SSH). Pour plus d’informations, consultez la section [Documentation supplémentaire](#additional-reading-2).

1. Pour répertorier les ID de toutes les applications YARN en cours d’exécution, exécutez la commande suivante :

    ```apache
    yarn top
    ```

    Les ID sont répertoriés dans la colonne **APPLICATIONID**. Vous pouvez télécharger les journaux d’activité depuis la colonne **APPLICATIONID**.

    ```apache
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Pour télécharger les journaux d’activité de conteneurs YARN pour tous les processus maîtres d’application, utilisez la commande suivante :

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Cette commande crée un fichier journal nommé amlogs.txt.

1. Pour télécharger les journaux d’activité de conteneurs YARN uniquement pour les processus maîtres d’application les plus récents, utilisez la commande suivante :

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Cette commande crée un fichier journal nommé latestamlogs.txt.

1. Pour télécharger les journaux d’activité de conteneurs YARN pour les deux premiers processus maîtres d’application, utilisez la commande suivante :

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    Cette commande crée un fichier journal nommé first2amlogs.txt.

1. Pour télécharger tous les journaux d’activité de conteneurs YARN, utilisez la commande suivante :

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Cette commande crée un fichier journal nommé logs.txt.

1. Pour télécharger le journal de conteneur YARN pour un conteneur spécifique, utilisez la commande suivante :

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    Cette commande crée un fichier journal nommé containerlogs.txt.

### <a name="additional-reading"></a><a name="additional-reading-2"></a>Documentation supplémentaire

- [Se connecter à HDInsight (Apache Hadoop) avec SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop YARN concepts and applications](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow) (Concepts et applications Apache Hadoop Yarn, en anglais)

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

- Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

- Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

- Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour plus d’informations, consultez [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
