---
title: Mettre à l’échelle les tailles de cluster - Azure HDInsight
description: Mettre à l’échelle un cluster Azure HDInsight en toute flexibilité pour correspondre à votre charge de travail.
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 622261d0f7e602635aa6a638357278a9c63a6ecd
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990521"
---
# <a name="scale-hdinsight-clusters"></a>Mettre à l’échelle les clusters HDInsight

HDInsight fournit l’élasticité en vous offrant la possibilité de monter ou de descendre en puissance le nombre de nœuds de travail dans vos clusters. Cette souplesse, vous permet de réduire un cluster après certaines heures ou le week-end, et développer pendant les pics d’activité.

Si vous disposez d’un traitement par lots périodique, le cluster HDInsight peut être montée en puissance quelques minutes avant cette opération, afin que votre cluster a suffisamment de mémoire et la puissance du processeur.  Plus tard, une fois que le traitement a été effectué et que l’utilisation baisse à nouveau, vous pouvez descendre en puissance le cluster HDInsight afin de réduire le nombre de nœuds de travail.

Vous pouvez mettre à l’échelle un cluster manuellement à l’aide d’une des méthodes décrites ci-dessous, ou utiliser [mise à l’échelle](hdinsight-autoscale-clusters.md) options pour que le système mette automatiquement à l’échelle en réponse à l’UC, mémoire et d’autres mesures.

## <a name="utilities-to-scale-clusters"></a>Utilitaires à l’échelle des clusters

Microsoft fournit les utilitaires suivants pour mettre à l’échelle des clusters :

|Utilitaire | Description |
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) - ClusterName \<nom du Cluster > - TargetInstanceCount \<NewSize >|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) - ClusterName \<nom du Cluster > - TargetInstanceCount \<NewSize >|
|[Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [redimensionner AZ hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --groupe de ressources \<groupe de ressources >--nom \<nom du Cluster >--nombre d’instances cibles \<NewSize >|
|[Interface de ligne de commande Azure](hdinsight-administer-use-command-line.md)|redimensionnement du cluster Azure hdinsight \<clusterName > \<nombre d’instances cibles > |
|[Portail Azure](https://portal.azure.com)|Ouvrez le volet de votre cluster HDInsight, sélectionnez **taille du Cluster** dans le menu de gauche, puis dans le volet de taille de Cluster, tapez le nombre de nœuds de travail, puis sélectionnez Enregistrer.|  

![Mettre à l’échelle le cluster](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Grâce à ces méthodes, vous pouvez monter ou descendre en puissance votre cluster HDInsight en quelques minutes.

> [!IMPORTANT]  
> * L’interface CLI de Aure classique est déconseillée et doit uniquement être utilisé avec le modèle de déploiement classique. Pour tous les autres déploiements, utilisez le [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * Le module PowerShell AzureRM est déconseillé.  Utilisez le [module de Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) autant que possible.

## <a name="impact-of-scaling-operations"></a>Impact des opérations de mise à l’échelle

Lorsque vous **ajouter** nœuds à votre en cours d’exécution HDInsight cluster (montée en puissance), tous les travaux en attente ou en cours d’exécution ne seront pas affectées. De nouveaux travaux peuvent être soumis en toute sécurité pendant que le processus de mise à l’échelle est en cours d’exécution. Si l’opération de mise à l’échelle échoue pour une raison quelconque, l’échec est géré pour laisser votre cluster dans un état fonctionnel.

Si vous **supprimer** défaillance de nœuds (mise à l’échelle vers le bas), tout est en attente ou en cours d’exécution des travaux à l’issue de l’opération de mise à l’échelle. Cet échec est dû à certains services le redémarrage pendant le processus de mise à l’échelle. Il existe également un risque que votre cluster peut obtenir bloqué en mode sans échec pendant un opération de mise à l’échelle de manuel.

## <a name="how-to-safely-scale-down-a-cluster"></a>Mise à l’échelle en toute sécurité vers le bas d’un cluster

### <a name="scale-down-a-cluster-with-running-jobs"></a>Mettre à l’échelle vers le bas d’un cluster avec des travaux en cours d’exécution

Pour éviter que vos travaux en cours d’exécution échoue pendant une opération de réduction, vous pouvez essayer les trois choses :

1. Attendez que les travaux soient terminés avant la descente de votre cluster.
1. Arrêtez manuellement les travaux.
1. Soumettez à nouveau les travaux une fois l’opération de mise à l’échelle terminée.

Pour afficher une liste d’attente et des travaux en cours d’exécution, vous pouvez utiliser le fils **ResourceManager UI**, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. À partir de la gauche, accédez à **tous les services** > **Analytique** > **HDInsight Clusters**, puis sélectionnez votre cluster.
3. À partir de la vue principale, accédez à **des tableaux de bord de Cluster** > **Ambari domestique**. Entrez vos informations d’identification du cluster.
4. À partir de l’UI Ambari, sélectionnez **YARN** sur la liste des services sur le menu de gauche.  
5. Dans la page YARN, sélectionnez **liens rapides** et placez le curseur sur le nœud principal actif, puis sélectionnez **ResourceManager UI**.

    ![Interface utilisateur ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

Vous pouvez accéder directement à l’interface utilisateur ResourceManager avec `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Une liste de travaux avec leur état actuel apparaît. Dans la capture d’écran, il existe un travail en cours d’exécution :

![Applications de l’interface utilisateur ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Pour arrêter manuellement cette application en cours d’exécution, exécutez la commande suivante à partir de l’interpréteur de commandes SSH :

```bash
yarn application -kill <application_id>
```

Exemple :

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Bloqué en mode sans échec

Lorsque vous faites descendre un cluster, HDInsight utilise les interfaces de gestion d’Apache Ambari pour tout d’abord désactiver les nœuds de travail supplémentaire, lequel répliquer les blocs HDFS vers d’autres nœuds de travail en ligne. Après cela, HDInsight en toute sécurité mise à l’échelle du cluster. HDFS passe en mode sans échec au cours de l’opération de mise à l’échelle et il est censé en sortir une fois la mise à l’échelle est terminée. Dans certains cas, toutefois, HDFS se bloque en mode sans échec pendant une opération de mise à l’échelle en raison de la réplication incomplète de bloc de fichier.

Par défaut, HDFS est configuré avec un `dfs.replication` paramètre 3, qui contrôle le nombre de copies de chaque bloc de fichier est disponible. Chaque copie d’un bloc de fichier est stocké sur un autre nœud du cluster.

Quand HDFS détecte que le nombre attendu de copies de bloc ne sont pas disponible, HDFS bascule en mode sans échec et génère des alertes Ambari. Si HDFS entre en mode sans échec pour une opération de mise à l’échelle, mais ensuite ne peut pas quitter le mode sans échec, car le nombre de nœuds requis n’est pas détecté pour la réplication, le cluster peut se trouver bloqué en mode sans échec.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Exemples d’erreurs lorsque le mode sécurisé est activé

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Vous pouvez examiner les journaux d’activité du nœud de nom dans le dossier `/var/log/hadoop/hdfs/`, aux alentours du moment où le cluster a été mis à l’échelle, pour voir quand il est entré en mode sans échec. Les fichiers journaux sont nommés `Hadoop-hdfs-namenode-hn0-clustername.*`.

La cause principale des erreurs précédentes vient du fait que Hive dépend de fichiers temporaires dans HDFS lors de l’exécution des requêtes. Quand HDFS bascule en mode sans échec, Hive ne peut pas exécuter de requêtes car il ne peut pas écrire dans HDFS. Les fichiers temporaires dans HDFS sont situés dans le lecteur local monté sur les machines virtuelles de nœud de travail individuelles, et répliqué entre les autres nœuds de travail en au moins trois réplicas.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Comment empêcher HDInsight immigration en mode sans échec

Il existe plusieurs façons d’empêcher HDInsight de rester en mode sans échec :

* Arrêter tous les travaux Hive avant la descente en puissance de HDInsight. Vous pouvez également planifier le processus de descente en puissance pour d’éviter tout conflit avec les travaux Hive en cours d’exécution.
* Nettoyer manuellement les fichiers du répertoire de travail temporaire `tmp` Hive dans HDFS avant la descente en puissance.
* Effectuer uniquement la descente en puissance de HDInsight à au moins trois nœuds de travail. Éviter de descendre à un nœud de travail.
* Exécuter la commande pour quitter le mode sans échec, si nécessaire.

Les sections suivantes décrivent ces options.

#### <a name="stop-all-hive-jobs"></a>Arrêter tous les travaux Hive

Arrêtez tous les travaux Hive avant la descente en puissance à un nœud de travail. Si votre charge de travail est planifiée, effectuez votre descente en puissance une fois travail Hive terminé.

Arrêter les travaux Hive avant la mise à l’échelle, permet de réduire le nombre de fichiers de travail dans le dossier temporaire (le cas échéant).

#### <a name="manually-clean-up-hives-scratch-files"></a>Nettoyer manuellement les fichiers de travail Hive

Si Hive a laissé des fichiers temporaires, vous pouvez nettoyer manuellement ces fichiers avant la descente en puissance pour éviter le mode sans échec.

1. Vérifiez quel emplacement est utilisé pour les fichiers temporaires Hive en examinant le `hive.exec.scratchdir` propriété de configuration. Ce paramètre est défini dans `/etc/hive/conf/hive-site.xml`:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Arrêtez les services Hive et vérifiez que toutes les requêtes et tous les travaux sont terminés.
2. Répertorier le contenu du répertoire scratch trouvé au-dessus, `hdfs://mycluster/tmp/hive/` pour voir si elle contient tous les fichiers :

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    S’il existe des fichiers, le résultat ressemble à ce qui suit :

    ```
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

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>HDInsight de mise à l’échelle au moins trois nœuds de travail

Si vos clusters coincés en mode sans échec fréquemment lors de la mise à l’échelle à moins de trois nœuds de travail et les étapes précédentes ne fonctionnent pas, vous pouvez éviter votre cluster accédant à la mode sans échec complètement en laissant au moins trois nœuds de travail.

En conservant les trois nœuds de travail est plus coûteuse que la mise à l’échelle vers le bas pour uniquement un nœud de travail, mais elle empêche votre cluster restent bloquées en mode sans échec.

#### <a name="run-the-command-to-leave-safe-mode"></a>Exécuter la commande pour quitter le mode sans échec

La dernière option consiste à exécuter la commande en mode sans échec de congé. Si vous savez que la raison de HDFS passe en mode sans échec en raison de la réplication incomplète du fichier Hive, vous pouvez exécuter la commande suivante pour quitter le mode sans échec :


```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Mettre à l’échelle vers le bas d’un cluster Apache HBase

Serveurs de région sont équilibrés automatiquement quelques minutes après avoir effectué une opération de mise à l’échelle. Pour équilibrer manuellement les serveurs régionaux, procédez comme suit :

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

* [Automatiquement à l’échelle des clusters Azure HDInsight](hdinsight-autoscale-clusters.md)
* [Présentation d'Azure HDInsight](hadoop/apache-hadoop-introduction.md)
* [Mise à l’échelle des clusters](hdinsight-administer-use-portal-linux.md#scale-clusters)
