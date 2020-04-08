---
title: Action de script pour les packages Python avec Jupyter sur Azure HDInsight
description: Cette section comporte des instructions détaillées expliquant comment utiliser une action de script pour configurer des blocs-notes Jupyter disponibles avec des clusters HDInsight Spark pour utiliser des packages Python externes.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 659af8b85cb3736d663e79676b04af8041aeabfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129586"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Gérer en toute sécurité l’environnement Python sur Azure HDInsight avec une action de script

> [!div class="op_single_selector"]
> * [À l’aide de la commande magique de cellule](apache-spark-jupyter-notebook-use-external-packages.md)
> * [À l’aide d’une action de script](apache-spark-python-package-installation.md)

HDInsight dispose de deux installations Python intégrées dans le cluster Spark, Anaconda Python 2.7 et Python 3.5. Dans certains cas, les clients doivent personnaliser l’environnement Python, par exemple en installant des packages Python externes ou une autre version de Python. Dans cet article, nous décrivons la meilleure pratique qui consiste à gérer en toute sécurité les environnements Python pour un [cluster Apache Spark](./apache-spark-overview.md) sur HDInsight.

## <a name="prerequisites"></a>Prérequis

Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md). Si vous n’avez pas encore de cluster Spark sur HDInsight, vous pouvez exécuter des actions de script lors de la création du cluster. Consultez la documentation sur [Guide d’utilisation des actions de script personnalisées](../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Prise en charge des logiciels open source utilisés sur les clusters HDInsight

Le service Microsoft Azure HDInsight utilise un écosystème de technologies open source formées autour d’Apache Hadoop. Microsoft Azure fournit un niveau général de prise en charge pour les technologies open source. Pour plus d’informations, voir le [Site web de la FAQ du support Azure](https://azure.microsoft.com/support/faq/). Le service HDInsight fournit un niveau supplémentaire de prise en charge pour les composants intégrés.

Deux types de composant open source sont disponibles dans le service HDInsight :

|Composant |Description |
|---|---|
|Intégré|Ces composants sont préinstallés sur les clusters HDInsight et fournissent les fonctionnalités principales du cluster. Par exemple, Apache Hadoop YARN ResourceManager, le langage de requête Apache Hive (HiveQL) et la bibliothèque Mahout appartiennent à cette catégorie. La liste complète des composants de cluster est disponible dans la page [Nouveautés des versions de cluster Apache Hadoop fournies par HDInsight](../hdinsight-component-versioning.md).|
|Custom|En tant qu’utilisateur du cluster, vous pouvez installer ou utiliser dans votre charge de travail tout composant qui est disponible dans la communauté ou que vous avez créé.|

> [!IMPORTANT]
> Les composants fournis avec le cluster HDInsight sont entièrement pris en charge. Le support Microsoft vous aide à isoler et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés bénéficient d’un support commercialement raisonnable pour vous aider à résoudre le problème. La prise en charge de Microsoft peut être en mesure de résoudre le problème, SINON vous pourrez avoir besoin d’associer les chaînes disponibles pour les technologies open source lorsqu’il est possible de recourir à une expertise reconnue concernant cette technologie. Il existe par exemple de nombreux sites communautaires, comme : [Forum MSDN pour HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Par ailleurs, les projets Apache ont des sites de projet sur [https://apache.org](https://apache.org), par exemple : [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Présentation de l’installation par défaut de Python

Le cluster HDInsight Spark est créé avec l’installation d’Anaconda. Il existe deux installations Python dans le cluster : Anaconda Python 2.7 et Python 3.5. Le tableau ci-dessous montre les paramètres de Python par défaut pour Spark, livy et Jupyter.

| |Python 2.7|Python 3.5|
|----|----|----|
|Path|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|Défini par défaut sur 2.7|N/A|
|Livy|Défini par défaut sur 2.7|N/A|
|Jupyter|Noyau PySpark|Noyau PySpark3|

## <a name="safely-install-external-python-packages"></a>Installer en toute sécurité des packages Python externes

Le cluster HDInsight dépend de l’environnement Python intégré, Python 2.7 et Python 3.5. L’installation directe de packages personnalisés dans ces environnements intégrés par défaut peut entraîner des modifications inattendues de la version de la bibliothèque et interrompre le cluster ultérieurement. Pour installer en toute sécurité des packages Python externes personnalisés pour vos applications Spark, suivez les étapes ci-dessous.

1. Créez un environnement virtuel Python à l’aide de Conda. Un environnement virtuel fournit un espace isolé pour vos projets sans interrompre les autres. Lorsque vous créez l’environnement virtuel Python, vous pouvez spécifier la version de Python que vous souhaitez utiliser. Notez que vous devez toujours créer un environnement virtuel même si vous souhaitez utiliser Python 2.7 et 3.5. Cela permet de s’assurer que l’environnement par défaut du cluster n’est pas endommagé. Exécutez des actions de script sur votre cluster pour tous les nœuds avec le script ci-dessous pour créer un environnement virtuel Python.

    -   `--prefix` spécifie un chemin d’accès à l’emplacement d’un environnement virtuel Conda. Plusieurs configurations doivent être modifiées en fonction du chemin d’accès spécifié ici. Dans cet exemple, nous utilisons py35new, car le cluster a déjà un environnement virtuel nommé PY35.
    -   `python=` spécifie la version de Python pour l’environnement virtuel. Dans cet exemple, nous utilisons la version 3.5, car elle est déjà intégrée au cluster. Vous pouvez également utiliser d’autres versions de Python pour créer l’environnement virtuel.
    -   `anaconda` spécifie le package_spec en tant qu’anaconda pour installer les packages Anaconda dans l’environnement virtuel.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. Installez des packages Python externes dans l’environnement virtuel créé, si nécessaire. Exécutez des actions de script sur votre cluster pour tous les nœuds avec le script ci-dessous pour installer des packages Python externes. Vous devez disposer du privilège sudo ici pour pouvoir écrire des fichiers dans le dossier de l’environnement virtuel.

    Vous pouvez rechercher [l’index de package](https://pypi.python.org/pypi) pour obtenir la liste complète des packages disponibles. Vous pouvez également obtenir une liste des packages disponibles à partir d’autres sources. Par exemple, vous pouvez installer les packages mis à disposition via [conda-forge](https://conda-forge.org/feedstocks/).

    Utilisez la commande ci-dessous si vous souhaitez installer une bibliothèque avec sa dernière version :

    - Utilisez le canal Conda :

        -   `seaborn` est le nom du package que vous souhaitez installer.
        -   `-n py35new` spécifie le nom de l’environnement virtuel qui vient d’être créé. Veillez à modifier le nom correspondant en fonction de la création de votre environnement virtuel.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Ou utilisez le référentiel PyPi, et modifiez `seaborn` et `py35new` en conséquence :
        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install seaborn
        ```

    Utilisez la commande ci-dessous si vous souhaitez installer une bibliothèque avec une version spécifique :

    - Utilisez le canal Conda :

        -   `numpy=1.16.1` représente le nom du package et la version que vous souhaitez installer.
        -   `-n py35new` spécifie le nom de l’environnement virtuel qui vient d’être créé. Veillez à modifier le nom correspondant en fonction de la création de votre environnement virtuel.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Ou utilisez le référentiel PyPi, et modifiez `numpy==1.16.1` et `py35new` en conséquence :

        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install numpy==1.16.1
        ```

    Si vous ne connaissez pas le nom de l’environnement virtuel, vous pouvez utiliser SSH pour accéder au nœud principal du cluster et exécuter `/usr/bin/anaconda/bin/conda info -e` pour afficher tous les environnements virtuels.

3. Modifiez les configurations Spark et Livy et pointez sur l’environnement virtuel créé.

    1. Ouvrez l’interface utilisateur Ambari, accédez à la page Spark2, onglet configurations.

        ![Modifier la configuration Spark et Livy via Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. Développez Advanced livy2-env, puis ajoutez les instructions ci-dessous en bas. Si vous avez installé l’environnement virtuel avec un préfixe différent, modifiez le chemin d’accès correspondant.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Modifier la configuration Livy via Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Développez spark2-env Avancé, remplacez l’instruction export PYSPARK_PYTHON existante en bas. Si vous avez installé l’environnement virtuel avec un préfixe différent, modifiez le chemin d’accès correspondant.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Modifier la configuration Spark via Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Enregistrez les modifications, puis redémarrez les services affectés. Ces modifications nécessitent un redémarrage du service Spark2. L’interface utilisateur Ambari affiche un rappel de redémarrage obligatoire. Cliquez sur Redémarrer pour redémarrer tous les services affectés.

        ![Modifier la configuration Spark via Ambari](./media/apache-spark-python-package-installation/ambari-restart-services.png)

4. Si vous souhaitez utiliser le nouvel environnement virtuel créé sur Jupyter. Vous devez modifier les configurations Jupyter et redémarrer Jupyter. Exécutez des actions de script sur tous les nœuds d’en-tête avec l’instruction ci-dessous pour associer Jupyter au nouvel environnement virtuel créé. Veillez à modifier le chemin d’accès au préfixe que vous avez spécifié pour votre environnement virtuel. Après l’exécution de cette action de script, redémarrez le service Jupyter via l’interface utilisateur Ambari pour rendre cette modification disponible.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Vous pouvez confirmer deux fois l’environnement Python dans Jupyter Notebook en exécutant le code ci-dessous :

    ![Vérifier la version de Python dans Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Problème connu

Il existe un bogue connu pour Anaconda versions 4.7.11, 4.7.12 et 4.8.0. Si vous constatez que vos actions de script se figent à `"Collecting package metadata (repodata.json): ...working..."` et échouent avec `"Python script has been killed due to timeout after waiting 3600 secs"`. Vous pouvez télécharger [ce script](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) et l’exécuter en tant qu’actions de script sur tous les nœuds pour résoudre le problème.

Pour vérifier votre version d’Anaconda, vous pouvez utiliser le protocole SSH pour le nœud d’en-tête de cluster et exécuter `/usr/bin/anaconda/bin/conda --v`.

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md)
* [Apache Spark avec BI : effectuer une analyse interactive des données à l’aide de Spark sur HDInsight avec des outils décisionnels](apache-spark-use-bi-tools.md)
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight)](apache-spark-job-debugging.md)
