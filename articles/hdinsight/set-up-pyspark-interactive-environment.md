---
title: Environnement interactif PySpark avec Azure HDInsight Tools
description: Découvrez comment utiliser Azure HDInsight Tools pour Visual Studio Code pour créer et envoyer des requêtes et des scripts.
keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 2a725f3c5c9e1428079807b5b76dbe72d416a9c7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393671"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Définir l’environnement interactif de PySpark pour Visual Studio Code

Les étapes suivantes expliquent comment configurer l'environnement interactif PySpark dans VS Code.

Nous utilisons la commande **python/pip** pour créer un environnement virtuel au niveau de votre chemin d'accueil. Si vous souhaitez utiliser une autre version, vous devez modifier manuellement la version par défaut de la commande **python/pip**. Pour plus de détails, voir [update-alternatives](https://linux.die.net/man/8/update-alternatives).

1. Installez [Python](https://www.python.org/downloads/) et [pip](https://pip.pypa.io/en/stable/installing/).

   + Installez Python à partir de [https://www.python.org/downloads/](https://www.python.org/downloads/).
   + Installez pip depuis [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (si non installé à partir de l'installation de Python).
   + Utilisez les commandes suivantes pour vérifier que Python et pip sont bien installés. (facultatif)

        ![Commande pour vérifier la version de Python et de pip](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Il est recommandé d'installer manuellement Python plutôt que d'utiliser la version par défaut de macOS.

2. Installez **virtualenv** en exécutant la commande ci-dessous.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Autres packages

Si vous rencontrez un message d’erreur, installez les packages requis en exécutant les commandes suivantes :

   ![Installer le package libkrb5 pour Python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Redémarrez VS Code, puis revenez à l’éditeur de script qui exécute **HDInsight : PySpark Interactive**.

## <a name="next-steps"></a>Étapes suivantes

### <a name="demo"></a>Démonstration

* HDInsight pour VS Code : [Vidéo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Outils et extensions

* [Utiliser Azure HDInsight Tools pour Visual Studio Code](hdinsight-for-vscode.md)
* [Utiliser Azure Toolkit for IntelliJ pour créer et soumettre des applications Apache Spark Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Utiliser Azure Toolkit for IntelliJ pour déboguer des applications Apache Spark à distance par SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utiliser Azure Toolkit for IntelliJ pour déboguer des applications Apache Spark à distance par VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser HDInsight Tools dans Azure Toolkit for Eclipse pour créer des applications Apache Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Utiliser des blocs-notes Apache Zeppelin avec un cluster Apache Spark sur HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Noyaux accessibles à Jupyter Notebook dans un cluster Apache Spark pour HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualiser des données Apache Hive à l’aide de Microsoft Power BI dans Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Utiliser Apache Zeppelin pour exécuter des requêtes Apache Hive dans Azure HDInsight](./interactive-query/hdinsight-connect-hive-zeppelin.md)
