---
title: Mettre à l’échelle les tailles de cluster - Azure HDInsight
description: Mettre à l’échelle un cluster Apache Hadoop de façon élastique pour qu’il corresponde à votre charge de travail dans Azure HDInsight
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 15d44f95cccf15fd0f7615655f5bbac1b0c35127
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706056"
---
# <a name="scale-azure-hdinsight-clusters"></a>Mettre à l’échelle des clusters Azure HDInsight

HDInsight fournit l’élasticité en vous offrant la possibilité de monter ou de descendre en puissance le nombre de nœuds de travail dans vos clusters. Cette élasticité vous permet de réduire un cluster après certaines heures ou les week-ends, et de le développer pendant les pics d’activité.

Si vous disposez d’un traitement par lots périodique, le cluster HDInsight peut faire l’objet d’un scale up quelques minutes avant cette opération afin d’avoir suffisamment de mémoire et de puissance de processeur.  Plus tard, une fois que le traitement a été effectué et que l’utilisation baisse à nouveau, vous pouvez descendre en puissance le cluster HDInsight afin de réduire le nombre de nœuds de travail.

Vous pouvez mettre à l’échelle un cluster manuellement à l’aide de l’une des méthodes décrites ci-dessous ou utiliser les options de [mise à l’échelle automatique](hdinsight-autoscale-clusters.md) pour que le système fasse automatiquement l’objet d’un scale up ou d’un scale down en réponse aux mesures de processeur, de mémoire et autre.

> [!NOTE]  
> Seuls les clusters ayant la version 3.1.3 de HDInsight ou une version ultérieure sont pris en charge. Si vous n’êtes pas sûr de la version de votre cluster, vous pouvez consulter la page Propriétés.

## <a name="utilities-to-scale-clusters"></a>Utilitaires permettant de mettre à l’échelle des clusters

Microsoft fournit les utilitaires suivants pour la mise à l’échelle des clusters :

|Utilitaire | Description|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<nom_cluster> -TargetInstanceCount \<NewSize>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<nom_cluster> -TargetInstanceCount \<NewSize>|
|[Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --resource-group \<groupe_ressources> --name \<nom_cluster> --target-instance-count \<NewSize>|
|[Interface de ligne de commande Azure](hdinsight-administer-use-command-line.md)|azure hdinsight cluster resize \<clusterName> \<nombre_instances_cibles> |
|[Portail Azure](https://portal.azure.com)|Ouvrez le volet de votre cluster HDInsight, sélectionnez **Taille de cluster** dans le menu de gauche, puis, dans le volet Taille de cluster, entrez le nombre de nœuds Worker, puis sélectionnez Enregistrer.|  

![Portail Azure - Option de mise à l’échelle de cluster](./media/hdinsight-scaling-best-practices/scale-cluster-blade1.png)

Grâce à ces méthodes, vous pouvez monter ou descendre en puissance votre cluster HDInsight en quelques minutes.

> [!IMPORTANT]  
> * L’interface de ligne de commande Azure Classic est dépréciée et doit uniquement être utilisée avec le modèle de déploiement classique. Pour tous les autres déploiements, utilisez [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * Le module PowerShell AzureRM est déconseillé.  Utilisez le [module Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) autant que possible.

## <a name="impact-of-scaling-operations"></a>Impact des opérations de mise à l’échelle

Lorsque vous **ajoutez** des nœuds à votre cluster HDInsight en cours d’exécution (scale up), tous les travaux en attente ou en cours d’exécution ne sont pas affectés. De nouveaux travaux peuvent être soumis en toute sécurité pendant que le processus de mise à l’échelle est en cours d’exécution. Si l’opération de mise à l’échelle échoue pour une raison quelconque, l’échec est géré pour laisser le cluster dans un état fonctionnel.

Si vous **supprimez** des nœuds (scale down), tous les travaux en attente ou en cours d’exécution échouent à la fin de l’opération de mise à l’échelle. Cet échec est dû au redémarrage des services au cours du processus de mise à l’échelle. Il existe également un risque que votre cluster soit bloqué en mode sans échec pendant une opération de mise à l’échelle manuelle.

L’impact de la modification du nombre de nœuds de données varie en fonction de chaque type de cluster pris en charge par HDInsight :

* Apache Hadoop

    Vous pouvez augmenter de façon continue le nombre de nœuds de travail dans un cluster Hadoop exécuté sans affecter aucune tâche en attente ou en cours. De nouvelles tâches peuvent également être soumises lorsque l'opération est en cours. Les défaillances dans l'opération de mise à l'échelle sont correctement gérées de sorte que le cluster reste toujours fonctionnel.

    Lorsqu’un cluster Hadoop est diminué par la réduction du nombre de nœuds de données, certains services du cluster sont redémarrés. Ce comportement entraîne l’échec de toutes les tâches en cours d’exécution ou en attente lors de la réalisation de l’opération de mise à l’échelle. Toutefois, vous pouvez soumettre à nouveau les tâches une fois l'opération terminée.

* Apache HBase

    Vous pouvez ajouter ou supprimer des nœuds en continu dans votre cluster HBase lorsque celui-ci s’exécute. Les serveurs régionaux sont équilibrés automatiquement quelques minutes après la fin de l’opération de mise à l’échelle. Cependant, vous pouvez équilibrer manuellement des serveurs régionaux en vous connectant au nœud principal du cluster et en exécutant les commandes suivantes à partir d’une fenêtre d’invite de commandes :

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Pour plus d’informations sur l’utilisation de l’interpréteur de commandes HBase, consultez [Prise en main d’un exemple Apache HBase dans HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Vous pouvez ajouter ou supprimer des nœuds de données en continu dans votre cluster Storm lorsque celui-ci s'exécute. Néanmoins, une fois l’opération de mise à l’échelle terminée, vous devrez rééquilibrer la topologie.

    Cela peut se faire de deux façons à l’aide de :

  * l'interface utilisateur Web de Storm
  * l’outil d’interface de ligne de commande (CLI)

    Pour plus d’informations, consultez la documentation [Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    L’interface utilisateur web de Storm est disponible dans le cluster HDInsight :

    ![Rééquilibrage de mise à l’échelle HDInsight Storm](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Voici un exemple de commande CLI pour rééquilibrer la topologie Storm :

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Scale down d’un cluster en toute sécurité

### <a name="scale-down-a-cluster-with-running-jobs"></a>Effectuer un scale down sur un cluster dont des travaux sont en cours d’exécution

Pour éviter l’échec de vos travaux en cours d’exécution pendant une opération de scale down, vous pouvez essayer trois choses :

1. Attendez la fin des travaux avant de procéder au scale down du cluster.
1. Mettez fin aux travaux manuellement.
1. Soumettez à nouveau les travaux une fois l’opération de mise à l’échelle terminée.

Pour afficher la liste des travaux en attente ou en cours d’exécution, vous pouvez utiliser **l’interface utilisateur Resource Manager** de YARN en procédant comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre cluster.  Pour obtenir des instructions, consultez la page [Énumération et affichage des clusters](./hdinsight-administer-use-portal-linux.md#showClusters). Le cluster est ouvert dans une nouvelle page du portail.
2. À partir de la vue principale, accédez à **Tableaux de bord du cluster** > **Accueil Ambari**. Entrez les informations d’identification du cluster.
3. Dans l’interface utilisateur d’Ambari, sélectionnez **YARN** dans la liste des services du menu de gauche.  
4. Dans la page YARN, sélectionnez **Quick Links** (Liens rapides), placez le curseur sur le nœud principal actif, puis sélectionnez **ResourceManager UI** (Interface utilisateur ResourceManager).

    ![Apache Ambari - Liens rapides, interface utilisateur ResourceManager](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

Vous pouvez accéder directement à l’interface utilisateur ResourceManager avec `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Une liste de travaux avec leur état actuel apparaît. La capture d’écran indique un travail en cours d’exécution :

![Applications de l’interface utilisateur ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Pour arrêter manuellement cette application en cours d’exécution, exécutez la commande suivante à partir de l’interpréteur de commandes SSH :

```bash
yarn application -kill <application_id>
```

Par exemple :

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Blocage en mode sans échec

Lorsque vous procédez au scale down d’un cluster, HDInsight utilise les interfaces de gestion d’Apache Ambari pour commencer par désactiver les nœuds Worker supplémentaires, qui répliquent leurs blocs HDFS vers d’autres nœuds Worker en ligne. Après cela, HDInsight met à l’échelle le cluster en toute sécurité. HDFS bascule en mode sans échec lors d’opération de mise à l’échelle, et il est censé en sortir une fois la mise à l’échelle terminée. Dans certains cas, toutefois, HDFS se bloque en mode sans échec pendant une opération de mise à l’échelle en raison de la réplication incomplète du bloc de fichiers.

Par défaut, HDFS est configuré avec un paramètre `dfs.replication` de valeur 1, qui contrôle le nombre de copies disponibles de chaque bloc de fichiers. Chaque copie d’un bloc de fichiers est stockée sur un nœud différent du cluster.

Quand HDFS détecte que le nombre attendu de copies de bloc ne sont pas disponibles, HDFS bascule en mode sans échec, et Ambari génère des alertes. Si HDFS bascule en mode sans échec pendant une opération de mise à l’échelle sans pouvoir quitter ce mode ensuite, car le nombre de nœuds requis n’est pas détecté pour la réplication, le cluster peut se retrouver bloqué en mode sans échec.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Exemples d’erreurs lorsque le mode sécurisé est activé

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Vous pouvez examiner les journaux d’activité du nœud de nom dans le dossier `/var/log/hadoop/hdfs/`, aux alentours du moment où le cluster a été mis à l’échelle, pour voir quand il est entré en mode sans échec. Les fichiers journaux sont nommés `Hadoop-hdfs-namenode-hn0-clustername.*`.

La cause principale des erreurs précédentes vient du fait que Hive dépend de fichiers temporaires dans HDFS lors de l’exécution des requêtes. Quand HDFS bascule en mode sans échec, Hive ne peut pas exécuter de requêtes car il ne peut pas écrire dans HDFS. Les fichiers temporaires dans HDFS sont situés dans le lecteur local monté sur les machines virtuelles de nœud de travail individuelles, et répliqué entre les autres nœuds de travail en au moins trois réplicas.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Guide pratique pour empêcher le blocage de HDInsight en mode sans échec

Il existe plusieurs façons d’empêcher HDInsight de rester en mode sans échec :

* Arrêter tous les travaux Hive avant la descente en puissance de HDInsight. Vous pouvez également planifier le processus de descente en puissance pour d’éviter tout conflit avec les travaux Hive en cours d’exécution.
* Nettoyer manuellement les fichiers du répertoire de travail temporaire `tmp` Hive dans HDFS avant la descente en puissance.
* Effectuer uniquement la descente en puissance de HDInsight à au moins trois nœuds de travail. Éviter de descendre à un nœud de travail.
* Exécuter la commande pour quitter le mode sans échec, si nécessaire.

Les sections suivantes décrivent ces options.

#### <a name="stop-all-hive-jobs"></a>Arrêter tous les travaux Hive

Arrêtez tous les travaux Hive avant la descente en puissance à un nœud de travail. Si votre charge de travail est planifiée, effectuez votre descente en puissance une fois travail Hive terminé.

L’arrêt des travaux Hive avant la mise à l’échelle permet de réduire le nombre de fichiers de travail dans le dossier temporaire (le cas échéant).

#### <a name="manually-clean-up-hives-scratch-files"></a>Nettoyer manuellement les fichiers de travail Hive

Si Hive a laissé des fichiers temporaires, vous pouvez nettoyer manuellement ces fichiers avant la descente en puissance pour éviter le mode sans échec.

1. Vérifiez l’emplacement utilisé pour les fichiers temporaires Hive en examinant la propriété de configuration `hive.exec.scratchdir`. Ce paramètre est défini dans `/etc/hive/conf/hive-site.xml` :

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Arrêtez les services Hive et vérifiez que toutes les requêtes et tous les travaux sont terminés.
2. Dressez la liste du contenu du répertoire de travail `hdfs://mycluster/tmp/hive/` trouvé ci-dessus pour voir s’il contient des fichiers :

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    S’il existe des fichiers, le résultat ressemble à ce qui suit :

    ```output
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Si vous savez que Hive a fini d’utiliser ces fichiers, vous pouvez les supprimer. Assurez-vous que Hive ne contient aucune requête en cours d’exécution en consultant la page de l’interface utilisateur Yarn ResourceManager.

    Exemple de ligne de commande pour supprimer des fichiers de HDFS :

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Mettre à l’échelle HDInsight vers trois nœuds Worker ou plus

Si les clusters sont bloqués fréquemment en mode sans échec lors du scale down vers moins de trois nœuds Worker et que les étapes précédentes ne fonctionnent pas, vous pouvez éviter qu’ils ne basculent en mode sans échec en conservant au moins trois nœuds Worker.

Conserver trois nœuds Worker est plus coûteux que de procéder au scale down vers un seul nœud Worker, mais cela empêche le cluster de se bloquer en mode sans échec.

#### <a name="run-the-command-to-leave-safe-mode"></a>Exécuter la commande pour quitter le mode sans échec

La dernière option consiste à exécuter la commande pour quitter le mode sans échec. Si vous savez que HDFS passe en mode sans échec en raison de la réplication incomplète des fichiers Hive, vous pouvez exécuter la commande suivante pour quitter le mode sans échec :

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Procéder au scale down d’un cluster Apache HBase

Les serveurs de région sont équilibrés automatiquement quelques minutes après la fin d’une opération de mise à l’échelle. Pour équilibrer manuellement les serveurs de région, procédez comme suit :

1. Connectez-vous au cluster HDInsight à l’aide de SSH : Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Lancez l’interpréteur de commandes HBase :

    ```bash
    hbase shell
    ```

3. Utilisez la commande suivante pour équilibrer manuellement les serveurs de région :

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Étapes suivantes

* [Mettre à l’échelle automatiquement les clusters Azure HDInsight](hdinsight-autoscale-clusters.md)
* [Présentation d'Azure HDInsight](hadoop/apache-hadoop-introduction.md)
