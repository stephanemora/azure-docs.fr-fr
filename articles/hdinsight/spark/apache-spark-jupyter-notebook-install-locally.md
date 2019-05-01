---
title: Installer Jupyter localement et le connecter à Spark dans Azure HDInsight
description: Découvrez comment installer un bloc-notes Jupyter localement sur votre ordinateur et le connecter à un cluster Apache Spark.
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: hrasheed
ms.openlocfilehash: 5e9cd4c2a14f94c39c7058f45bf727df8198c053
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691294"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Installer un bloc-notes Jupyter sur votre ordinateur et le connecter à Apache Spark sur HDInsight

Cet article explique comment installer Jupyter Notebook avec les noyaux PySpark (pour Python) et Apache Spark (pour Scala) personnalisés avec Spark Magic et connecter le bloc-notes à un cluster HDInsight. Plusieurs raisons peuvent motiver l’installation de Jupyter sur votre ordinateur local. Différents défis peuvent également se présenter. Consultez la section [Pourquoi dois-je installer Jupyter sur mon ordinateur ?](#why-should-i-install-jupyter-on-my-computer) à la fin de cet article.

Il existe quatre étapes clés impliquées dans l’installation de Jupyter et la connexion à Apache Spark sur HDInsight.

* Configurer un cluster Spark.
* Installation de Jupyter Notebook.
* Installation des noyaux PySpark et Spark avec Spark Magic.
* Configuration de Spark Magic pour accéder au cluster Spark sur HDInsight.

Pour plus d’informations sur les noyaux personnalisés et Spark Magic disponible pour les blocs-notes Jupyter avec le cluster HDInsight, consultez [Noyaux disponibles pour les blocs-notes Jupyter avec les clusters HDInsight Spark Linux sur HDInsight](apache-spark-jupyter-notebook-kernels.md).

> [!IMPORTANT]  
> Les étapes décrites dans l’article fonctionnent uniquement Spark version 2.1.0.

## <a name="prerequisites"></a>Conditions préalables
La configuration requise indiquée ici ne concerne pas l’installation de Jupyter. Elle s’applique à la connexion du bloc-notes Jupyter à un cluster HDInsight une fois le bloc-notes installé.

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster de Apache Spark (version 2.1.0 ou version antérieure) sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).



## <a name="install-jupyter-notebook-on-your-computer"></a>Installer le bloc-notes Jupyter sur votre ordinateur

Vous devez installer Python avant de pouvoir installer les blocs-notes Jupyter. Python et Jupyter font partie de la [distribution Anaconda](https://www.anaconda.com/download/). Lorsque vous installez Anaconda, vous installez une distribution de Python. Une fois Anaconda installé, vous ajoutez l’installation de Jupyter en exécutant les commandes appropriées.

1. Téléchargez le [programme d’installation Anaconda](https://www.anaconda.com/download/) pour votre plateforme et exécutez le programme d’installation. Lors de l’exécution de l’assistant d’installation, veillez à sélectionner l’option permettant d’ajouter Anaconda à votre variable PATH.

2. Exécutez la commande suivante pour installer Jupyter.

        conda install jupyter

    Pour plus d’informations sur l’installation de Jupyter, consultez [Installing Jupyter using Anaconda](https://jupyter.readthedocs.io/en/latest/install.html)(Installation de Jupyter à l’aide d’Anaconda).

## <a name="install-the-kernels-and-spark-magic"></a>Installer les noyaux et Spark Magic

Pour obtenir des instructions sur l’installation des noyaux Spark Magic, PySpark et Spark, consultez la [documentation sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation) sur GitHub. La première étape de la documentation vous invite à installer Spark Magic. Remplacez la première étape du lien par les commandes suivantes, selon la version du cluster HDInsight auquel vous vous connectez. Après cela, suivez les étapes restantes de la documentation Spark Magic. Si vous souhaitez installer les différents noyaux, vous devez effectuer l’étape 3 de la section contenant les instructions d’installation.

* Pour les clusters v3.5 et v3.6, installez sparkmagic 0.11.2 en exécutant `pip install sparkmagic==0.11.2`.

* Pour les clusters v3.4, installez sparkmagic 0.2.3 en exécutant `pip install sparkmagic==0.2.3`.

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Configurer Spark Magic pour la connexion au cluster Spark HDInsight

Dans cette section, vous configurez Spark magic que vous avez installé précédemment pour vous connecter à un cluster Apache Spark que vous devez avoir déjà créé dans Azure HDInsight.

1. Démarrez l’interface de Python avec la commande suivante :

    ```
    python
    ```

2. Les informations de configuration de Jupyter sont généralement stockées dans le répertoire de base des utilisateurs. Entrez la commande suivante pour identifier le répertoire de base et créez un dossier appelé **.sparkmagic**.  Le chemin d’accès complet est généré.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. Dans le dossier `.sparkmagic`, créez un fichier appelé **config.json** et ajoutez l’extrait JSON suivant dedans.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```
4. Apportez les modifications suivantes au fichier :

    |Valeur de modèle | Nouvelle valeur |
    |---|---|
    |{USERNAME}|Connexion au cluster, par défaut est Admin.|
    |{CLUSTERDNSNAME}|Nom du cluster|
    |{BASE64ENCODEDPASSWORD}|Un mot de passe pour votre mot de passe réel codage Base64.  Vous pouvez générer un mot de passe en base64 à [ https://www.url-encode-decode.com/base64-encode-decode/ ](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Conserver si vous utilisez `sparkmagic 0.11.23` (clusters v3.5 et v3.6).  Si vous utilisez `sparkmagic 0.2.3` (clusters v3.4), remplacez par `"should_heartbeat": true`.|

    Vous pouvez voir un exemple complet de fichier à [exemple config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Les pulsations sont envoyées pour vous assurer que les sessions ne sont pas divulguées. Lorsqu’un ordinateur se met en veille ou s’arrête, la pulsation n’est pas envoyée, causant ainsi le nettoyage de la session. Pour les clusters v3.4, si vous souhaitez désactiver ce comportement, vous pouvez définir la configuration Livy `livy.server.interactive.heartbeat.timeout` à `0` à partir de l’interface utilisateur Ambari. Pour les clusters v3.5, si vous ne définissez pas la configuration 3.5 ci-dessus, la session ne sera pas supprimée.

5. Démarrez Jupyter. Utilisez la commande suivante depuis l’invite de commandes :

        jupyter notebook

6. Vérifiez que vous pouvez utiliser Spark magic disponible avec les noyaux. Procédez comme suit.

    a. Créer un nouveau bloc-notes. Dans l’angle droit, sélectionnez **New**. Vous devez voir le noyau par défaut **Python 2** ou **Python 3** et les noyaux que vous avez installé. Les valeurs réelles peuvent varier en fonction de vos choix d’installation.  Sélectionnez **PySpark**.

    ![Noyaux de bloc-notes Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Noyaux de bloc-notes Jupyter")

    > [!IMPORTANT]  
    > Après avoir sélectionné **New** passez en revue votre interpréteur de commandes pour toutes les erreurs.  Si vous voyez l’erreur `TypeError: __init__() got an unexpected keyword argument 'io_loop'` vous pouvez être confronté à un problème connu avec certaines versions de tornade.  Dans ce cas, arrêtez le noyau et puis rétrograder votre installation tornade avec la commande suivante : `pip install tornado==4.5.3`.

    b. Exécutez l’extrait de code suivant :

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Votre connexion au cluster HDInsight sera testée, si vous parvenez à récupérer la sortie.

    Si vous souhaitez mettre à jour la configuration du bloc-notes pour vous connecter à un autre cluster, mettez à jour le config.json avec le nouveau jeu de valeurs, comme indiqué à l’étape 3, ci-dessus.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Pourquoi dois-je installer Jupyter sur mon ordinateur ?

Il peut y avoir plusieurs raisons d’installer Jupyter sur son ordinateur et de le connecter à un cluster Apache Spark sur HDInsight.

* Même si les blocs-notes Jupyter sont déjà disponibles sur le cluster Spark dans Azure HDInsight, l’installation de Jupyter sur votre ordinateur vous permet de créer vos blocs-notes localement, de tester votre application sur un cluster en cours d’exécution, puis de charger les blocs-notes sur le cluster. Vous pouvez charger les blocs-notes dans le cluster à l’aide du bloc-notes Jupyter en cours d’exécution ou du cluster, ou en les enregistrant dans le dossier /HdiNotebooks du compte de stockage associé au cluster. Pour plus d’informations sur le stockage des blocs-notes, consultez [Where are Jupyter notebooks stored](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)(Où sont stockés les blocs-notes Jupyter) ?
* Les blocs-notes étant disponibles localement, vous pouvez vous connecter à différents clusters Spark selon les besoins de votre application.
* Vous pouvez utiliser GitHub pour implémenter un système de contrôle de code source et disposer du contrôle de version pour les blocs-notes. Vous pouvez également disposer d’un environnement de collaboration où plusieurs utilisateurs peuvent travailler avec le même bloc-notes.
* Vous pouvez travailler avec les blocs-notes localement sans avoir un cluster activé. Il vous suffit d’un cluster avec lequel tester vos blocs-notes, sans avoir à gérer manuellement vos blocs-notes ou à disposer d’un environnement de développement.
* Il peut être plus facile de configurer votre propre environnement de développement local que de configurer l’installation de Jupyter sur le cluster.  Vous pouvez tirer parti de tous les logiciels que vous avez installés localement sans configurer un ou plusieurs clusters distants.

> [!WARNING]  
> Lorsque Jupyter est installé sur votre ordinateur local, plusieurs utilisateurs peuvent exécuter le même bloc-notes sur le même cluster Spark en même temps. Dans ce cas, plusieurs sessions Livy sont créées. Si vous rencontrez un problème et que vous souhaitez effectuer un débogage, il sera difficile de savoir quelle session Livy appartient à quel utilisateur.  

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
* [Utilisation du plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utiliser le plug-in Azure HDInsight Tools pour IntelliJ IDEA afin de déboguer des applications Apache Spark à distance](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser des blocs-notes Apache Zeppelin avec un cluster Apache Spark sur HDInsight](apache-spark-zeppelin-notebook.md)
* [Noyaux accessibles à Jupyter Notebook dans le cluster Apache Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)
