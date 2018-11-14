---
title: 'Action de script : Installer des packages Python avec Jupyter sur Azure HDInsight'
description: Cette section comporte des instructions détaillées expliquant comment utiliser une action de script pour configurer des blocs-notes Jupyter disponibles avec des clusters HDInsight Spark pour utiliser des packages Python externes.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: af25dcff2302827f2291d50972f09b8b5fda6cd3
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51255436"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Utilisation d’une action de script pour installer des packages externes Python avec les blocs-notes Jupyter dans des clusters Apache Spark sur HDInsight
> [!div class="op_single_selector"]
> * [À l’aide de la commande magique de cellule](apache-spark-jupyter-notebook-use-external-packages.md)
> * [À l’aide d’une action de script](apache-spark-python-package-installation.md)
>
>

Découvrez comment utiliser les actions de script pour configurer un cluster Apache Spark sur HDInsight (Linux) pour utiliser des packages **python** externes bénéficiant de la contribution de la communauté, qui ne sont pas inclus dans le cluster.

> [!NOTE]
> Vous pouvez également configurer un bloc-notes Jupyter à l’aide de la commande magique `%%configure` pour utiliser les packages externes. Pour obtenir des instructions, consultez [Utilisation de packages externes avec les blocs-notes Jupyter dans des clusters Apache Spark sur HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).
> 
> 

Vous pouvez rechercher [l’index de package](https://pypi.python.org/pypi) pour obtenir la liste complète des packages disponibles. Vous pouvez également obtenir une liste des packages disponibles à partir d’autres sources. Par exemple, vous pouvez installer les packages mis à disposition via [Anaconda](https://docs.continuum.io/anaconda/pkg-docs) ou [conda-forge](https://conda-forge.org/feedstocks/).

Dans cet article, vous allez découvrir comment installer le package [TensorFlow](https://www.tensorflow.org/) sur votre cluster à l’aide d’une action de script, et comment l’utiliser via le bloc-notes Jupyter, à titre d'exemple.

## <a name="prerequisites"></a>Prérequis
Vous devez disposer des éléments suivants :

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]
   > Si vous n’avez pas encore de cluster Spark sur HDInsight Linux, vous pouvez exécuter des actions de script lors de la création du cluster. Consultez la documentation sur [Guide d’utilisation des actions de script personnalisées](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   > 
   > 
   
   ## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Prise en charge des logiciels open source utilisés sur les clusters HDInsight

Le service Microsoft Azure HDInsight utilise un écosystème de technologies open source formées autour de Hadoop. Microsoft Azure fournit un niveau général de prise en charge pour les technologies open source. Pour plus d’informations, consultez la section **Étendue de la prise en charge** du [site web FAQ du support Azure](https://azure.microsoft.com/support/faq/). Le service HDInsight fournit un niveau supplémentaire de prise en charge pour les composants intégrés.

Deux types de composant open source sont disponibles dans le service HDInsight :

* **Composants intégrés** : ces composants sont préinstallés sur les clusters HDInsight et fournissent la fonctionnalité principale du cluster. Par exemple, YARN ResourceManager, le langage de requête Hive (HiveQL) et la bibliothèque Mahout appartiennent à cette catégorie. Une liste complète des composants de cluster est disponible sur la page [Nouveautés des versions de cluster Hadoop fournies par HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-component-versioning).
* **Composants personnalisés** : en tant qu’utilisateur du cluster, vous pouvez installer ou utiliser dans votre charge de travail tout composant qui est disponible dans la communauté ou que vous avez créé.

> [!WARNING]
> Les composants fournis avec le cluster HDInsight sont entièrement pris en charge. Le support Microsoft vous aide à isoler et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés bénéficient d’un support commercialement raisonnable pour vous aider à résoudre le problème. La prise en charge de Microsoft peut être en mesure de résoudre le problème, SINON vous pourrez avoir besoin d’associer les chaînes disponibles pour les technologies open source lorsqu’il est possible de recourir à une expertise reconnue concernant cette technologie. Vous pouvez, par exemple, utiliser de nombreux sites de communauté, comme le [forum MSDN sur HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [http://apache.org](http://apache.org), par exemple [Hadoop](http://hadoop.apache.org/).


## <a name="use-external-packages-with-jupyter-notebooks"></a>Utiliser des packages externes avec les blocs-notes Jupyter

1. Dans le tableau d’accueil du [portail Azure](https://portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous l’avez épinglé au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**.   

2. Dans le panneau de cluster Spark, cliquez sur **Actions de script** dans le volet gauche. Utilisez le type de script « Personnalisé » et entrez un nom convivial pour l’action de script. Exécutez le script sur les **nœuds de tête et de travail** et laissez le champ des paramètres vide. Le script bash peut être référencé à partir de : https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh Consultez la documentation sur [l’utilisation des actions de script personnalisées](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

   > [!NOTE]
   > Il existe deux installations de Python dans le cluster. Spark utilisera l’installation d’Anaconda Python située dans `/usr/bin/anaconda/bin`. Référencez cette installation dans vos actions personnalisées via `/usr/bin/anaconda/bin/pip` et `/usr/bin/anaconda/bin/conda`.
   > 
   > 

3. Ouvrez un bloc-notes PySpark Jupyter

    ![Créer un bloc-notes Jupyter](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Créer un bloc-notes Jupyter")

4. Un nouveau bloc-notes est créé et ouvert sous le nom Untitled.pynb. Cliquez sur le nom du bloc-notes en haut, puis entrez un nom convivial.

    ![Donnez un nom au bloc-notes](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "Donnez un nom au bloc-notes")

5. Vous allez maintenant `import tensorflow` et exécuter un exemple hello world. 

    Code à copier :

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    Le résultat ressemble à :
    
    ![Exécution de code TensorFlow](./media/apache-spark-python-package-installation/execution.png "Exécuter le code TensorFlow")

## <a name="seealso"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Création et exécution d’applications
* [Créer une application autonome avec Scala](apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance avec Livy sur un cluster Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utilisation de packages externes avec les blocs-notes Jupyter dans des clusters Apache Spark sur HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Utilisation du plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely) (Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA pour déboguer des applications Spark à distance)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)
