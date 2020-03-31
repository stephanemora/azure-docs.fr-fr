---
title: Configurer les paramètres Spark - Azure HDInsight
description: Comment voir et configurer les paramètres d’Apache Spark pour un cluster Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: 48f19e5da8c7703cc597518246c2f62ebce3ae17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79233481"
---
# <a name="configure-apache-spark-settings"></a>Configurer les paramètres d’Apache Spark

Un cluster Spark HDInsight comporte une installation de la bibliothèque [Apache Spark](https://spark.apache.org/).  Chaque cluster HDInsight inclut des paramètres de configuration par défaut pour tous ses services installés, y compris Spark.  L’un des principaux aspects de la gestion d’un cluster Apache Hadoop HDInsight est le monitoring de la charge de travail, notamment des tâches Spark, pour vérifier qu’elles s’exécutent de manière prévisible. Pour exécuter les travaux Spark dans les meilleures conditions, tenez compte de la configuration physique du cluster au moment d’optimiser sa configuration logique.

Le cluster Apache Spark HDInsight par défaut comporte les nœuds suivants : trois nœuds [Apache ZooKeeper](https://zookeeper.apache.org/), deux nœuds principaux et un ou plusieurs nœuds Worker :

![Architecture de HDInsight Spark](./media/apache-spark-settings/spark-hdinsight-arch.png)

Le nombre de machines virtuelles, et leurs tailles, pour les nœuds de votre cluster HDInsight peuvent également affecter votre configuration Spark. Les valeurs de configuration HDInsight autres que les valeurs par défaut nécessitent souvent des valeurs de configuration Spark spécifiques. Quand vous créez un cluster HDInsight Spark, des tailles de machine virtuelle vous sont suggérées pour chacun des composants. Les [tailles de machine virtuelle Linux à mémoire optimisée](../../virtual-machines/linux/sizes-memory.md) pour Azure sont D12 v2 ou supérieur.

## <a name="apache-spark-versions"></a>Versions d’Apache Spark

Utilisez la version de Spark la mieux adaptée à votre cluster.  Le service HDInsight inclut plusieurs versions de Spark et de HDInsight.  Chaque version de Spark comprend un ensemble de paramètres de cluster par défaut.  

Lorsque vous créez un cluster, différentes versions de Spark sont disponibles. Pour consulter la liste complète, reportez-vous à [Versions et composants de HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).


> [!NOTE]  
> La version par défaut d’Apache Spark dans le service HDInsight peut changer sans préavis. Si vous êtes dépendant d’une version, Microsoft vous recommande de spécifier cette version quand vous créez des clusters à l’aide du SDK .NET, d’Azure PowerShell et d’Azure Classic CLI.

Apache Spark a trois emplacements de configuration système :

* Les propriétés de Spark contrôlent la plupart des paramètres d’application et peuvent être définies à l’aide d’un objet `SparkConf` ou par le biais des propriétés système Java.
* Les variables d’environnement permettent de définir les paramètres par machine, tels que l’adresse IP, par le biais du script `conf/spark-env.sh` sur chaque nœud.
* Vous pouvez configurer la journalisation par le biais de `log4j.properties`.

Quand vous sélectionnez une version particulière de Spark, votre cluster comprend les paramètres de configuration par défaut.  Vous pouvez modifier la configuration par défaut de Spark à l’aide d’un fichier de configuration Spark personnalisé.  Voici un exemple.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

L’exemple ci-dessus remplace plusieurs valeurs par défaut de cinq paramètres de configuration Spark.  Il s’agit du codec de compression, de la taille de fractionnement minimale Apache Hadoop MapReduce, des tailles de bloc parquet, de la partition Spark SQL et des valeurs par défaut de la taille des fichiers ouverts.  Ces modifications de configuration sont choisies, car les données et travaux associés (dans cet exemple, des données génomiques) présentent des caractéristiques particulières dont l’efficacité peut être améliorée à l’aide de ces paramètres de configuration personnalisés.

---

## <a name="view-cluster-configuration-settings"></a>Vérifier les paramètres de configuration du cluster

Vérifiez les paramètres de configuration de cluster HDInsight actuels avant d’effectuer une optimisation des performances sur le cluster. Lancez le tableau de bord HDInsight à partir du portail Azure en cliquant sur le lien **Tableau de bord** dans le volet du cluster Spark. Connectez-vous avec le nom d’utilisateur et le mot de passe de l’administrateur de cluster.

L’interface utilisateur web d’Apache Ambari s’ouvre sur un tableau de bord regroupant les principales métriques d’utilisation des ressources de cluster.  Le tableau de bord Ambari vous montre la configuration d’Apache Spark et d’autres services que vous avez installés. Le tableau de bord inclut un onglet **Config History** (Historique de la configuration), où vous pouvez afficher les informations de configuration pour tous les services installés, y compris Spark.

Pour afficher les valeurs de configuration pour Apache Spark, sélectionnez **Config History** (Historique de la configuration), puis sélectionnez **Spark2**.  Sélectionnez l’onglet **Configs** (Configurations), puis le lien `Spark` (ou `Spark2`, selon votre version) dans la liste des services.  Une liste de valeurs de configuration pour votre cluster apparaît :

![Configurations de Spark](./media/apache-spark-settings/spark-configurations.png)

Pour afficher et changer des valeurs de configuration de Spark spécifiques, sélectionnez un lien dont le titre contient le mot « spark ».  Les configurations de Spark incluent des valeurs de configuration personnalisées et avancées dans les catégories suivantes :

* Custom Spark2-defaults (Valeurs Spark2-defaults personnalisées)
* Custom Spark2-metrics-properties (Valeurs Spark2-metrics-properties personnalisées)
* Advanced Spark2-defaults (Valeurs Spark2-defaults avancées)
* Advanced Spark2-env (Valeurs Spark2-env avancées)
* Advanced spark2-hive-site-override (Valeurs spark2-hive-site-override avancées)

Si vous créez un ensemble de valeurs de configuration autre qu’un ensemble par défaut, vous pouvez également voir l’historique de vos mises à jour de configuration.  Cet historique de configuration peut être utile pour déterminer la configuration (autre que la configuration par défaut) dont les performances sont optimales.

> [!NOTE]  
> Pour afficher, mais pas changer, les paramètres de configuration de cluster Spark courants, sélectionnez l’onglet **Environment** (Environnement) dans l’interface de niveau supérieur **Spark Job UI** (Interface utilisateur des travaux Spark).

## <a name="configuring-spark-executors"></a>Configuration des Exécuteurs Spark

Le diagramme suivant montre les objets Spark clés : le programme de pilote et son contexte Spark associé, ainsi que le gestionnaire de cluster et ses *n* nœuds Worker.  Chaque nœud Worker inclut un Exécuteur, un cache et *n* instances de tâches.

![Objets de cluster](./media/apache-spark-settings/hdi-spark-architecture.png)

Les travaux Spark utilisent des ressources Worker, notamment de la mémoire ; il est donc courant d’ajuster les valeurs de configuration de Spark pour les Exécuteurs des nœuds Worker.

Trois paramètres clés souvent ajustés pour paramétrer les configurations de Spark afin d’améliorer les exigences de l’application sont `spark.executor.instances`, `spark.executor.cores` et `spark.executor.memory`. Un exécuteur est un processus lancé pour une application Spark. Un Exécuteur s’exécute sur le nœud Worker et est responsable des tâches de l’application. Pour chaque cluster, le nombre d’Exécuteurs par défaut et les tailles d’Exécuteur sont calculés en fonction du nombre de nœuds Worker et de leur taille. Ils sont stockés dans `spark-defaults.conf` sur les nœuds principaux du cluster.  Vous pouvez modifier ces valeurs dans un cluster en cours d’exécution en sélectionnant le lien **Custom spark-defaults** (Valeurs spark-defaults personnalisées) dans l’interface utilisateur web d’Ambari.  Une fois que vous avez apporté des modifications, vous êtes invité par l’interface utilisateur à **redémarrer** tous les services concernés.

> [!NOTE]  
> Ces trois paramètres de configuration peuvent être configurés au niveau du cluster (pour toutes les applications qui s’exécutent sur le cluster) et également spécifiés pour chaque application.

Une autre source d’informations sur les ressources utilisées par les Exécuteurs Spark est l’interface utilisateur d’application Spark.  Dans l’interface utilisateur Spark, sélectionnez l’onglet **Executors** (Exécuteurs) pour afficher les vues Summary (Résumé) et Detail (Détails) de la configuration et des ressources utilisées par les Exécuteurs.  Ces vues peuvent vous aider à déterminer s’il faut changer les valeurs par défaut des Exécuteurs Spark pour l’ensemble du cluster ou un ensemble spécifique d’exécutions de travail.

![Exécuteurs Spark](./media/apache-spark-settings/apache-spark-executors.png)

Ou bien, vous pouvez utiliser l’API REST d’Ambari pour vérifier par programmation les paramètres de configuration de cluster HDInsight et Spark.  Pour plus d’informations, voir [Informations de référence sur l’API Apache Ambari sur GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

En fonction de votre charge de travail Spark, vous pouvez déterminer qu’une configuration Spark autre que la configuration par défaut fournit une optimisation des exécutions des travaux Spark.  Vous devez effectuer des tests d’évaluation avec des exemples de charges de travail afin de valider les configurations de cluster différentes des configurations par défaut.  Certains des paramètres courants que vous pouvez envisager d’ajuster sont les suivants :

* `--num-executors` définit le nombre d’Exécuteurs.
* `--executor-cores` définit le nombre de cœurs pour chaque exécuteur. Nous vous recommandons d’utiliser des Exécuteurs de taille moyenne, car d’autres processus consomment également une partie de la mémoire disponible.
* `--executor-memory` contrôle la taille de la mémoire (taille de segment) de chaque Exécuteur sur [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ; laissez de la mémoire pour la charge des exécutions.

Voici un exemple de deux nœuds Worker avec des valeurs de configuration différentes :

![Configurations à deux nœuds](./media/apache-spark-settings/executor-configuration.png)

La liste suivante affiche les paramètres de mémoire clés des exécuteurs Spark.

* `spark.executor.memory` définit la quantité totale de mémoire disponible pour un Exécuteur.
* `spark.storage.memoryFraction` (par défaut environ 60 %) définit la quantité de mémoire disponible pour le stockage de RDD persistants.
* `spark.shuffle.memoryFraction`(par défaut environ 20 %) définit la quantité de mémoire réservée pour la lecture aléatoire.
* `spark.storage.unrollFraction` et `spark.storage.safetyFraction` (globalement environ 30 % de la mémoire totale) : utilisées en interne par Spark, ces valeurs ne doivent pas être changées.

YARN contrôle la somme maximale de mémoire utilisée par les conteneurs sur chaque nœud Spark. Le diagramme suivant montre les relations par nœud entre les objets de configuration YARN et les objets Spark.

![Gestion de la mémoire Spark avec YARN](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Changer des paramètres d’une application exécutée dans un bloc-notes Jupyter

Les clusters Spark dans HDInsight incluent un certain nombre de composants par défaut. Chacun de ces composants inclut des valeurs de configuration par défaut qui peuvent être remplacées en fonction des besoins.

* Spark Core : Spark Core, Spark SQL, Spark streaming APIs, GraphX et Apache Spark MLlib.
* Anaconda : gestionnaire de package python.
* [Apache Livy](https://livy.incubator.apache.org/) : API REST Apache Spark, utilisée pour envoyer des tâches à distance à un cluster Spark HDInsight.
* Bloc-notes [Jupyter](https://jupyter.org/) et [Apache Zeppelin](https://zeppelin.apache.org/) : interface utilisateur interactive sur navigateur pour interagir avec le cluster Spark.
* Pilote ODBC : connecte les clusters Spark dans HDInsight aux outils décisionnels tels que Microsoft Power BI et Tableau.

Pour les applications exécutées dans le bloc-notes Jupyter, utilisez la commande `%%configure` afin d’apporter des modifications à la configuration depuis le bloc-notes. Ces modifications de configuration sont appliquées aux travaux Spark exécutés à partir de votre instance de bloc-notes. Pensez à apporter ces modifications au début de l’application, avant d’exécuter la première cellule de code. La configuration modifiée est appliquée à la session Livy au moment de sa création.

> [!NOTE]  
> Pour changer la configuration ultérieurement dans l’application, utilisez le paramètre `-f` (forcer). Néanmoins, toute la progression de l’application est alors perdue.

Le code ci-dessous montre comment changer la configuration d’une application exécutée dans un bloc-notes Jupyter.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Conclusion

Il existe de nombreux paramètres de configuration de base que vous devez surveiller et régler pour garantir que vos travaux Spark s’exécutent de manière prévisible et performante. Ces paramètres permettent de déterminer la meilleure configuration de cluster Spark pour vos charges de travail particulières.  Vous devez également surveiller l’exécution de travaux Spark à long terme et/ou gourmands en ressources.  Les défis les plus courants concernent la sollicitation de la mémoire due aux configurations incorrectes (en particulier les Exécuteurs de taille incorrecte), aux longues opérations et aux tâches qui entraînent des opérations cartésiennes.

## <a name="next-steps"></a>Étapes suivantes

* [Composants et versions d’Apache Hadoop disponibles avec HDInsight](../hdinsight-component-versioning.md)
* [Gérer les ressources d’un cluster Apache Spark sur HDInsight](apache-spark-resource-manager.md)
* [Configurer des clusters dans HDInsight avec Apache Hadoop, Apache Spark, Apache Kafka, etc.](../hdinsight-hadoop-provision-linux-clusters.md)
* [Configuration Apache Spark](https://spark.apache.org/docs/latest/configuration.html)
* [Exécuter Apache Spark sur Apache Hadoop YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
