---
title: 'Action de script : Installer des packages Python avec Jupyter sur Azure HDInsight'
description: Cette section comporte des instructions détaillées expliquant comment utiliser une action de script pour configurer des blocs-notes Jupyter disponibles avec des clusters HDInsight Spark pour utiliser des packages Python externes.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: c07326cc3a4334f1873eef2dc23da05156a93577
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64574649"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Utilisation d’une action de script pour installer des packages externes Python avec les blocs-notes Jupyter dans des clusters Apache Spark sur HDInsight
> [!div class="op_single_selector"]
> * [À l’aide de la commande magique de cellule](apache-spark-jupyter-notebook-use-external-packages.md)
> * [À l’aide d’une action de script](apache-spark-python-package-installation.md)

Découvrez comment utiliser les actions de script pour configurer un cluster [Apache Spark](https://spark.apache.org/) sur HDInsight pour qu’il utilise des packages **python** externes bénéficiant de la contribution de la communauté, qui ne sont pas inclus dans le cluster.

> [!NOTE]  
> Vous pouvez également configurer un bloc-notes Jupyter à l’aide de la commande magique `%%configure` pour utiliser les packages externes. Pour obtenir des instructions, consultez [Utilisation de packages externes avec les blocs-notes Jupyter dans des clusters Apache Spark sur HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).

Vous pouvez rechercher [l’index de package](https://pypi.python.org/pypi) pour obtenir la liste complète des packages disponibles. Vous pouvez également obtenir une liste des packages disponibles à partir d’autres sources. Par exemple, vous pouvez installer les packages mis à disposition via [conda-forge](https://conda-forge.org/feedstocks/).

Dans cet article, vous allez découvrir comment installer le package [TensorFlow](https://www.tensorflow.org/) sur votre cluster à l’aide d’une action de script, et comment l’utiliser via le bloc-notes Jupyter, à titre d'exemple.

## <a name="prerequisites"></a>Prérequis
Vous devez disposer des éléments suivants :

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Si vous n’avez pas encore de cluster Spark sur HDInsight Linux, vous pouvez exécuter des actions de script lors de la création du cluster. Consultez la documentation sur [Guide d’utilisation des actions de script personnalisées](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Prise en charge des logiciels open source utilisés sur les clusters HDInsight

Le service Microsoft Azure HDInsight utilise un écosystème de technologies open source formées autour d’Apache Hadoop. Microsoft Azure fournit un niveau général de prise en charge pour les technologies open source. Pour plus d’informations, consultez la section **Étendue de la prise en charge** du [site web FAQ du support Azure](https://azure.microsoft.com/support/faq/). Le service HDInsight fournit un niveau supplémentaire de prise en charge pour les composants intégrés.

Deux types de composant open source sont disponibles dans le service HDInsight :

* **Composants intégrés** : ces composants sont préinstallés sur les clusters HDInsight et fournissent la fonctionnalité principale du cluster. Par exemple, Apache Hadoop YARN ResourceManager, le langage de requête Apache Hive (HiveQL) et la bibliothèque Mahout appartiennent à cette catégorie. La liste complète des composants de cluster est disponible dans la page [Nouveautés des versions de cluster Apache Hadoop fournies par HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Composants personnalisés** : en tant qu’utilisateur du cluster, vous pouvez installer ou utiliser dans votre charge de travail tout composant qui est disponible dans la communauté ou que vous avez créé.

> [!IMPORTANT]   
> Les composants fournis avec le cluster HDInsight sont entièrement pris en charge. Le support Microsoft vous aide à isoler et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés bénéficient d’un support commercialement raisonnable pour vous aider à résoudre le problème. La prise en charge de Microsoft peut être en mesure de résoudre le problème, SINON vous pourrez avoir besoin d’associer les chaînes disponibles pour les technologies open source lorsqu’il est possible de recourir à une expertise reconnue concernant cette technologie. Par exemple, vous pouvez utiliser de nombreux sites de communauté, comme : [Forum MSDN pour HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Par ailleurs, les projets Apache ont des sites de projet sur [https://apache.org](https://apache.org), par exemple : [Hadoop](https://hadoop.apache.org/).


## <a name="use-external-packages-with-jupyter-notebooks"></a>Utiliser des packages externes avec les blocs-notes Jupyter

1. À partir du [portail Azure](https://portal.azure.com/), accédez à votre cluster.  

2. Avec votre cluster sélectionné, dans le volet gauche, sous **Paramètres**, sélectionnez **Actions de script**.

3. Sélectionnez **+ Envoyer**.

4. Entrez les valeurs suivantes pour la fenêtre **Envoyer une action de script** :  


    |Paramètre | Valeur |
    |---|---|
    |Type de script | Sélectionnez **- Personnalisé** dans la liste déroulante.|
    |Nom |Entrez `tensorflow` dans la zone de texte.|
    |URI de script bash |Entrez `https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh` dans la zone de texte. |
    |Type(s) de nœud | Cochez les cases **Head** et **Worker**. |

    `tensorflowinstall.sh` contient les commandes suivantes :

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install --yes tensorflow
    ```

5. Sélectionnez **Créer**.  Consultez la documentation sur [Guide d’utilisation des actions de script personnalisées](../hdinsight-hadoop-customize-cluster-linux.md).

6. Attendez la fin du script.  Le volet **Actions de script** va indiquer **De nouvelles actions de script peuvent être envoyées une fois que l’opération de cluster actuelle est terminée** pendant que le script s’exécute.  Vous pouvez voir une barre de progression dans la fenêtre **Opérations en arrière-plan** de l’interface utilisateur Ambari.

7. Ouvrez un notebook Jupyter PySpark.  Consultez [Créer un bloc-notes Jupyter sur Spark HDInsight](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight) pour les étapes à suivre.

    ![Créer un bloc-notes Jupyter](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Créer un bloc-notes Jupyter")

8. Vous allez maintenant `import tensorflow` et exécuter un exemple hello world. Entrez le code suivant :

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    Le résultat ressemble à :
    
    ![Exécution de code TensorFlow](./media/apache-spark-python-package-installation/execution.png "Exécuter le code TensorFlow")

> [!NOTE]  
> Il existe deux installations de Python dans le cluster. Spark utilisera l’installation Anaconda Python située dans `/usr/bin/anaconda/bin` ainsi que l’environnement Python 2.7 par défaut. Pour utiliser Python 3.x et des packages d’installation dans le noyau PySpark3, utilisez le chemin d’accès à l’exécutable `conda` pour cet environnement et le paramètre `-n` pour spécifier l’environnement. Par exemple, la commande `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35` installe le package `ggplot` dans l’environnement Python 3.5 en utilisant le canal `conda-forge`.

## <a name="seealso"></a>Voir aussi
* [Présentation : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Apache Spark avec BI : effectuer une analyse interactive des données à l’aide de Spark sur HDInsight avec des outils décisionnels](apache-spark-use-bi-tools.md)
* [Apache Spark avec Machine Learning : utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide des données des systèmes HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark avec Machine Learning : utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide d’Apache Spark dans HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Création et exécution d’applications
* [Créer une application autonome avec Scala](apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance avec Apache Livy sur un cluster Apache Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utilisation de packages externes avec les blocs-notes Jupyter dans des clusters Apache Spark sur HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Utilisation du plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utiliser le plug-in Azure HDInsight Tools pour IntelliJ IDEA afin de déboguer des applications Apache Spark à distance](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser des blocs-notes Apache Zeppelin avec un cluster Apache Spark sur HDInsight](apache-spark-zeppelin-notebook.md)
* [Noyaux accessibles à Jupyter Notebook dans le cluster Apache Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)
