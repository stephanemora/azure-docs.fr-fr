---
title: Azure HDInsight Tools - Définir l’environnement interactif de PySpark pour Visual Studio Code
description: Découvrez comment utiliser Azure HDInsight Tools pour Visual Studio Code pour créer et envoyer des requêtes et des scripts.
keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: 82aebd780b7b821675a6ee891cc81e2b1c6fdafb
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437212"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Définir l’environnement interactif de PySpark pour Visual Studio Code

Les étapes suivantes vous montrent comment installer des packages Python en exécutant **HDInsight : PySpark Interactive**.


## <a name="set-up-the-pyspark-interactive-environment-on-macos-and-linux"></a>Définir l’environnement interactif de PySpark sur MacOS et Linux
Si vous utilisez **Python 3.x**, vous devez utiliser la commande **pip3** pour les étapes suivantes :

1. Assurez-vous de l’installation de **Python** et de **pip**.
 
    ![Version de Python et de pip](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Installez Jupyter.
    ```
    sudo pip install jupyter
    ```
   Le message d’erreur suivant peut s’afficher sur Linux et MacOS :

   ![Erreur 1](./media/set-up-pyspark-interactive-environment/error1.png)

   ```Resolve:
    sudo pip uninstall asyncio
    sudo pip install trollies
    ```

3. Installez **libkrb5-dev** (pour Linux). Le message d’erreur suivant peut s’afficher :

   ![Erreur 2](./media/set-up-pyspark-interactive-environment/error2.png)
       
   ```Resolve:
   sudo apt-get install libkrb5-dev 
   ```

3. Installez **sparkmagic**.
   ```
   sudo pip install sparkmagic
   ```

4. Vérifiez que **ipywidgets** est correctement installé en exécutant :
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![Installer les noyaux wrapper](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Installez les noyaux wrapper. Exécutez **pip show sparkmagic**. La sortie indique le chemin d’accès pour l’installation de **sparkmagic**. 

    ![Emplacement de sparkmagic](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. Accédez à l’emplacement puis exécutez :

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![Installation de jupyter kernelspec](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. Vérifiez l’état de l’installation.

    ```
    jupyter-kernelspec list
    ```
    ![liste de jupyter kernelspec](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    Pour les noyaux disponibles : 
    - **python2** et **pysparkkernel** correspondent à **python 2.x**. 
    - **python3** et **pyspark3kernel** correspondent à **python 3.x**. 

8. Redémarrez VS Code, puis revenez à l’éditeur de script qui exécute **HDInsight : PySpark Interactive**.

## <a name="next-steps"></a>Étapes suivantes

### <a name="demo"></a>Démonstration
* HDInsight pour VS Code : [Vidéo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utiliser Azure HDInsight Tools pour Visual Studio Code](hdinsight-for-vscode.md)
* [Utiliser Azure Toolkit for IntelliJ pour créer et soumettre des applications Apache Spark Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Utiliser Azure Toolkit for IntelliJ pour déboguer des applications Apache Spark à distance par SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utiliser Azure Toolkit for IntelliJ pour déboguer des applications Apache Spark à distance par VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser HDInsight Tools dans Azure Toolkit for Eclipse pour créer des applications Apache Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utiliser des blocs-notes Apache Zeppelin avec un cluster Apache Spark sur HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Noyaux accessibles à Jupyter Notebook dans un cluster Apache Spark pour HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualiser des données Apache Hive à l’aide de Microsoft Power BI dans Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Utiliser Apache Zeppelin pour exécuter des requêtes Apache Hive dans Azure HDInsight](hdinsight-connect-hive-zeppelin.md)
