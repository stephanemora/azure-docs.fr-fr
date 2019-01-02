---
title: Azure HDInsight Tools - Utiliser Visual Studio Code pour Hive, LLAP ou PySpark | Microsoft Docs
description: Découvrez comment utiliser Azure HDInsight Tools pour Visual Studio Code pour créer et envoyer des requêtes et des scripts.
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: fe186966dbddb5aa743e465f897aa5a1a0e07db1
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840203"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Utiliser les Azure HDInsight Tools pour Visual Studio Code

Découvrez comment utiliser Azure HDInsight Tools pour Visual Studio Code (VS Code) pour créer et envoyer des tâches Apache Hive de traitement par lots, des requêtes Hive interactives et des scripts PySpark pour Apache Spark. Nous décrivons d’abord comment installer les outils HDInsight dans VS Code, puis nous expliquons comment envoyer des tâches Hive et Spark. 

Vous pouvez installer Azure HDInsight Tools sur les plateformes prises en charge par VSCode, qui sont Windows, Linux et MacOS. Les prérequis pour les différentes plateformes sont détaillés ci-dessous.


## <a name="prerequisites"></a>Prérequis

Avant de poursuivre cet article, vérifiez que vous avez les éléments nécessaires suivants :

- Un cluster HDInsight. Pour créer un cluster, consultez [Prise en main de HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono est nécessaire uniquement pour les plateformes Linux et MacOS.

## <a name="install-the-hdinsight-tools"></a>Installer HDInsight Tools
   
Une fois que vous avez tous les éléments prérequis, installez Azure HDInsight Tools pour VSCode. 

### <a name="to-install-azure-hdinsight-tools"></a>Pour installer Azure HDInsight Tools

1. Ouvrez Visual Studio Code.

2. Dans le volet gauche, sélectionnez **Extensions**. Saisissez **HDInsight** dans la zone de recherche.

3. À côté de **Azure HDInsight Tools**, cliquez sur **Installer**. Après quelques secondes, le bouton **Installer** est remplacé par le bouton **Recharger**.

4. Cliquez sur **Recharger** pour activer l’extension **Azure HDInsight Tools**.

5. Sélectionnez **Recharger la fenêtre** pour confirmer. **Azure HDInsight Tools** est maintenant affiché dans le volet **Extensions**.

   ![installation de Python avec HDInsight pour Visual Studio Code](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Ouvrir un espace de travail HDInsight

Vous devez créer un espace de travail dans VS Code avant de pouvoir vous connecter à Azure.

### <a name="to-open-a-workspace"></a>Pour ouvrir un espace de travail

1. Dans le menu **Fichier**, sélectionnez **Ouvrir le dossier**. Ensuite, désignez un dossier existant en tant que dossier de travail, ou créez-en un nouveau. Le dossier s’affiche dans le volet gauche.

2. Dans le volet gauche, cliquez sur l’icône **Nouveau fichier** en regard du dossier de travail.

   ![Nouveau fichier](./media/hdinsight-for-vscode/new-file.png)

3. Nommez le nouveau fichier avec l’extension de fichier .hql (requêtes Hive) ou .py (script Spark). 

## <a name="connect-to-hdinsight-cluster"></a>Se connecter à un cluster HDInsight

Avant de pouvoir envoyer des scripts à des clusters HDInsight à partir de Visual Studio Code, vous devez vous connecter à votre compte Azure, ou lier un cluster (à l’aide du nom d’utilisateur/mot de passe Ambari ou d’un compte joint au domaine).

### <a name="to-connect-to-azure"></a>Pour vous connecter à Azure

1. Créez un dossier de travail et un fichier de script si vous n’en avez pas.

2. Cliquez avec le bouton droit sur l’éditeur de script puis, dans le menu contextuel, sélectionnez **HDInsight : Connexion**. Vous pouvez également appuyer sur **Ctrl+Maj+P** et entrer **HDInsight : Connexion**.

    ![Connexion à HDInsight Tools pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Connectez-vous en suivant les instructions de connexion affichées dans le volet **OUTPUT**.
    + Pour un environnement global, la connexion à HDInsight déclenche le processus de connexion à Azure.

        ![Instructions de connexion pour Azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + Pour d’autres environnements, suivez les instructions de connexion.

        ![Instructions de connexion pour un autre environnement](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

    Une fois que vous êtes connecté, le nom de votre compte Azure s’affiche dans la barre d’état, dans l’angle inférieur gauche de la fenêtre VS Code. 

    > [!NOTE]
    > En raison d’un problème courant relatif à l’authentification Azure, vous devez ouvrir un navigateur en mode privé ou incognito. Si l’authentification à deux facteurs est activée pour votre compte Azure, nous vous recommandons d’utiliser l’authentification par téléphone au lieu du code PIN.
  

4. Cliquez avec le bouton droit sur l’éditeur de script pour ouvrir le menu contextuel. À partir du menu contextuel, vous pouvez effectuer les travaux suivants :

    - Se déconnecter
    - Énumérer les clusters
    - Définir les clusters par défaut
    - Envoyer des requêtes Hive interactives
    - Envoyer des scripts de commandes par lot Hive
    - Envoyer des requêtes PySpark interactives
    - Envoyer un script de commandes par lot PySpark
    - Set configuration

<h3 id="linkcluster">Pour lier un cluster</h3>

Vous pouvez lier un cluster normal à l’aide d’un nom d’utilisateur [Apache Ambari](https://ambari.apache.org/) managé ou lier un cluster de sécurité Hadoop de Pack Sécurité Entreprise à l’aide d’un nom d’utilisateur de domaine (par exemple : user1@contoso.com).
1. Ouvrez la palette de commandes en appuyant sur **Ctrl+Maj+P**, puis entrez **HDInsight : Lier un cluster**.

   ![commande de lien du cluster](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Entrez HDInsight cluster URL -> input Username -> input Password -> select cluster type -> un indicateur indique la réussite de l’opération si la vérification est passée.
   
   ![boîte de dialogue de lien du cluster](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]
   > Le nom d’utilisateur lié et son mot de passe sont utilisés si le cluster est à la fois connecté à un abonnement Azure et lié à un cluster. 
   
3. Vous pouvez voir un cluster lié à l’aide de la commande **List cluster**. Vous pouvez désormais soumettre un script à ce cluster lié.

   ![cluster lié](./media/hdinsight-for-vscode/linked-cluster.png)

4. Vous pouvez également dissocier un cluster en entrant **HDInsight : Dissocier un cluster** à partir de la palette de commandes.


### <a name="to-link-a-generic-apache-livy-endpoint"></a>Pour lier un point de terminaison Apache Livy générique

1. Ouvrez la palette de commandes en appuyant sur **Ctrl+Maj+P**, puis entrez **HDInsight : Lier un cluster**.
2. Sélectionnez **Generic Livy Endpoint** (point de terminaison Livy générique).
3. Entrez le point de terminaison Livy générique, par exemple : http://10.172.41.42:18080.
4. Sélectionnez **Basic** (De base) si avez besoin d’une autorisation pour le point de terminaison Livy générique. Autrement, sélectionnez **None** (Aucune).
5. Entrez le nom d’utilisateur si vous avez sélectionné **Basic** (De base) à l’étape 4.
6. Entrez le mot de passe si vous avez sélectionné **Basic** (De base) à l’étape 4.
7. Le point de terminaison Livy générique a été correctement lié.

   ![cluster Livy générique lié](./media/hdinsight-for-vscode/link-cluster-process-generic-livy.png)

## <a name="list-hdinsight-clusters"></a>Afficher la liste des clusters HDInsight

Pour tester la connexion, vous pouvez afficher la liste de vos clusters HDInsight :

### <a name="to-list-hdinsight-clusters-under-your-azure-subscription"></a>Pour afficher la liste des clusters HDInsight dans votre abonnement Azure
1. Ouvrez un espace de travail, puis connectez-vous à Azure. Pour en savoir plus, consultez les sections [Ouvrir un espace de travail HDInsight](#open-hdinsight-workspace) et [Connexion à Azure](#connect-to-hdinsight-cluster).

2. Cliquez avec le bouton droit sur l’éditeur de script, puis cliquez sur **HDInsight : Lister les clusters** dans le menu contextuel. 

3. Les clusters HDInsight s’affichent dans le volet **Output**.

    ![Définir la configuration du cluster par défaut](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Définir un cluster par défaut
1. Ouvrez un espace de travail et connectez-vous à Azure. Consultez [Ouvrir un espace de travail HDInsight](#open-hdinsight-workspace) et [Connexion à Azure](#connect-to-hdinsight-cluster).

2. Cliquez avec le bouton droit sur l’éditeur de script, puis cliquez sur **HDInsight : Définir le cluster par défaut**. 

3. Sélectionnez un cluster à utiliser comme cluster par défaut pour le fichier de script actuel. Les outils mettent automatiquement à jour le fichier de configuration **.VSCode\settings.json**. 

   ![Définir la configuration du cluster par défaut](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Configurer l’environnement Azure
1. Ouvrez la palette de commandes en appuyant sur **CTRL+MAJ+P**.

2. Entrez **HDInsight : Définir l’environnement Azure**.

3. Sélectionnez un environnement, tel qu’Azure ou AzureChina, comme entrée de connexion par défaut.

4. Entre temps, l’outil a déjà enregistré votre entrée de connexion par défaut dans **.VSCode\settings.json**. Elle est également directement mise à jour dans ce fichier de configuration. 

   ![Définir la configuration de l’entrée de connexion par défaut](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Envoyer des requêtes, scripts de commandes par lot Hive interactifs

HDInsight Tools pour VS Code vous permet d’envoyer des requêtes, scripts de commandes par lot Hive interactifs à des clusters HDInsight.

1. Créez un dossier de travail et un fichier de script Hive si vous n’en avez pas.

2. Connectez-vous à votre compte Azure ou liez des clusters.

3. Copiez et collez le code suivant dans votre fichier Hive, puis enregistrez-le.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
4. Cliquez avec le bouton droit sur l’éditeur de script, puis cliquez sur **HDInsight : Hive Interactive** pour envoyer la requête, ou utilisez le raccourci **Ctrl+Alt+I**. Sélectionnez **HDInsight : Hive Batch** pour envoyer le script, ou utilisez le raccourci clavier **Ctrl+Alt+H**. 

5. Sélectionnez un cluster autre que le cluster par défaut. HDInsight Tools vous permet également d’envoyer un bloc de code au lieu du fichier de script entier à partir du menu contextuel. Peu après, le résultat de la requête s’affiche dans un nouvel onglet.

   ![Résultat de la requête Hive interactive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Volet **RÉSULTATS** : Vous pouvez enregistrer le résultat complet dans un fichier CSV, JSON ou Excel dans un chemin local, ou enregistrer seulement certaines lignes du résultat.

    - Volet **MESSAGES** : Cliquez sur un numéro de **ligne** pour accéder à la première ligne du script en cours d’exécution.

## <a name="submit-interactive-pyspark-queries"></a>Envoyer des requêtes PySpark interactives

### <a name="to-submit-interactive-pyspark-queries-to-hdinsight-spark-clusters"></a>Pour envoyer des requêtes PySpark interactives à des clusters HDInsight Spark.

1. Créez un dossier de travail et un fichier de script avec l’extension .py si vous n’en avez pas.

2. Connectez-vous à votre compte Azure, si ce n’est déjà fait.

3. Copiez et collez le code suivant dans le fichier de script :
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```
4. Mettez en surbrillance ce script. Cliquez avec le bouton droit sur l’éditeur de script, puis sélectionnez **HDInsight : PySpark Interactive**, ou utilisez le raccourci **Ctrl+Alt+I**.

5. Si vous n’avez pas encore installé l’extension **Python** dans VS Code, sélectionnez le bouton **Install** (Installer) tel qu’indiqué dans l’illustration suivante :

    ![installation de Python avec HDInsight pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Installez l’environnement Python dans votre système, si ce n’est déjà fait. 
   - Pour Windows, téléchargez et installez [Python](https://www.python.org/downloads/). Vérifiez ensuite la présence de `Python` et `pip` dans votre chemin système.

   - Pour les instructions relatives à MacOS et Linux, consultez [Définir l’environnement interactif PySpark pour Visual Studio Code](set-up-pyspark-interactive-environment.md).

7. Sélectionnez un cluster auquel envoyer la requête PySpark. Peu après, le résultat de la requête s’affiche sous le nouvel onglet à droite :

   ![Résultat de l’envoi du travail Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. L’outil prend également en charge la requêtes avec **Clause SQL**.

   ![Résultat de l’envoi du travail Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) L’état de l’envoi s’affiche en bas à gauche dans la barre d’état lors de l’exécution des requêtes. N’envoyez pas d’autres requêtes lorsque l’état est **PySpark Kernel (busy)**. 

>[!NOTE]
>Les clusters peuvent mettre à jour les informations de session. La variable et la fonction définies, ainsi que les valeurs correspondantes sont conservées dans la session, ce qui permet de les référencer dans plusieurs appels de service pour le même cluster. 

### <a name="to-disable-environment-check"></a>Pour désactiver la vérification de l’environnement

Par défaut, les outils HDInsight vérifient l’environnement et installent des packages dépendants lors de l’envoi de requêtes PySpark interactives. Pour désactiver la vérification de l’environnement, définissez **hdinsight.disablePysparkEnvironmentValidation** sur **yes** (Oui) sous **USER SETTINGS** (PARAMÈTRES UTILISATEUR).

   ![Définir la vérification de l’environnement à partir des paramètres](./media/hdinsight-for-vscode/hdi-azure-hdinsight-environment-check.png)

Vous pouvez également cliquer sur le bouton **Désactiver la validation** lorsque la boîte de dialogue s’affiche.

   ![Définir la vérification de l’environnement à partir de la boîte de dialogue](./media/hdinsight-for-vscode/hdi-azure-hdinsight-environment-check-dialog.png)

### <a name="pyspark3-is-not-supported-with-spark2223"></a>PySpark3 n’est pas pris en charge avec Spark2.2/2.3

PySpark3 n’est plus pris en charge avec un cluster Spark2.2 ou Spark 2.3. Seul « PySpark » est pris en charge pour Python. L’échec de l’envoi à Spark 2.2/2.3 avec Python3 est un problème connu.

   ![Erreur obtenue lors de l’envoi à Python3](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Pour utiliser Python2.x, procédez comme suit : 

1. Installez Python 2.7 sur ordinateur local et ajoutez-le au chemin d’accès système.

2. Redémarrez VSCode.

3. Basculez vers Python 2 en cliquant sur **Python XXX** dans la barre d'état, puis en choisissant la cible Python.

   ![Sélectionner la version de Python](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)

## <a name="submit-pyspark-batch-job"></a>Envoi de la tâche de traitement par lots PySpark

1. Créez un dossier de travail et un fichier de script avec l’extension .py si vous n’en avez pas.

2. Connectez-vous à votre compte Azure si ce n’est déjà fait.

3. Copiez et collez le code suivant dans le fichier de script :

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```
4. Cliquez avec le bouton droit sur l’éditeur de script, puis cliquez sur **HDInsight : PySpark Batch**, ou utilisez le raccourci **Ctrl+Alt+H**. 

5. Sélectionnez un cluster auquel envoyer votre travail PySpark. 

   ![Résultat de l’envoi du travail Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Une fois que vous avez envoyé un travail Python, les journaux d’envoi apparaissent dans la fenêtre **Output** (Sortie) dans VS Code. **L’URL de l’interface utilisateur Spark** et **l’URL de l’interface utilisateur Yarn** s’affichent également. Vous pouvez ouvrir l’URL dans un navigateur web pour suivre l’état du travail.

## <a name="apache-livy-configuration"></a>Configuration d’Apache Livy

La configuration d’[Apache Livy](https://livy.incubator.apache.org/) est prise en charge et peut être définie à l’emplacement **.VSCode\settings.json** dans le dossier de l’espace de travail. Actuellement, la configuration Livy prend uniquement en charge le script Python. Pour plus d’informations, consultez [Lisez-moi Livy](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Comment déclencher une configuration Livy**
   
Dans le menu **File** (Fichier), vous pouvez sélectionner **Preferences** (Préférences), puis choisir **Settings** (Paramètres) dans le menu contextuel. Cliquez sur l’onglet **WORKSPACE SETTINGS** (PARAMÈTRES DE L’ESPACE DE TRAVAIL), puis commencez à définir la configuration Livy.

Vous pouvez également envoyer un fichier. Notez que le dossier .vscode est ajouté automatiquement au dossier de travail. Vous pouvez trouver la configuration Livy en cliquant sur **.vscode\settings.json**.

+ Paramètres du projet :

    ![Configuration de Livy](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>Pour les paramètres **driverMomory** et **executorMomry**, définissez la valeur en spécifiant l’unité, par exemple 1 g ou 1 024 m. 

+ Configurations Livy prises en charge :   

    **POST /batches**   
    Corps de la requête

    | Nom | description | Type | 
    | :- | :- | :- | 
    | file | Fichier contenant l’application à exécuter | chemin (obligatoire) | 
    | proxyUser | Utilisateur auquel emprunter l’identité lors de l’exécution de la tâche | chaîne | 
    | className | Classe principale Java/Spark de l’application | chaîne |
    | args | Arguments de ligne de commande pour l’application | liste de valeurs string | 
    | jars | Fichiers JAR à utiliser dans cette session | Liste de chaînes | 
    | pyFiles | Fichiers Python à utiliser dans cette session | Liste de chaînes |
    | fichiers d'entrée | Fichiers à utiliser dans cette session | Liste de chaînes |
    | driverMemory | Quantité de mémoire à utiliser pour le processus de pilote | chaîne |
    | driverCores | Nombre de cœurs à utiliser pour le processus de pilote | int |
    | executorMemory | Quantité de mémoire à utiliser par processus de l’exécuteur | chaîne |
    | executorCores | Nombre de cœurs à utiliser pour chaque exécuteur | int |
    | numExecutors | Nombre d’exécuteurs à lancer pour cette session | int |
    | archives | Archives à utiliser dans cette session | Liste de chaînes |
    | file d'attente | Nom de la file d’attente YARN vers laquelle effectuer l’envoi | chaîne |
    | Nom | Nom de cette session | chaîne |
    | conf | Propriétés de configuration Spark. | Map of key=val |

    Corps de réponse   
    Objet Batch créé

    | Nom | description | Type | 
    | :- | :- | :- | 
    | id | ID de la session | int | 
    | appId | ID d’application de cette session |  Chaîne |
    | appInfo | Informations détaillées sur l’application | Map of key=val |
    | log | Lignes du journal | liste de valeurs string |
    | state |   État du lot | chaîne |

>[!NOTE]
>La configuration Livy attribuée s’affiche dans le volet de sortie lors de l’envoi du script.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Intégrer avec Azure HDInsight à partir de l’Explorateur

Azure HDInsight a été ajouté au panneau gauche. Vous pouvez accéder au cluster et le gérer directement.

1. Développez **AZURE HDINSIGHT**. Si vous n’êtes pas connecté, un lien **Se connecter à Azure...**  s’affiche.

    ![Image du lien de connexion](./media/hdinsight-for-vscode/hid-azure-hdinsight-sign-in.png)

2. Cliquez sur **Se connecter à Azure**. Cela a pour effet d’afficher un lien et un code de connexion.

    ![Instructions de connexion pour un autre environnement](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin-code.png)

3. Cliquez sur le bouton **Copier et ouvrir** pour ouvrir le navigateur, collez le code, puis cliquez sur le bouton **Continuer** pour afficher l’indicateur de succès de la connexion.

4. Une fois la connexion établie, les abonnements disponibles et les clusters (Spark, Hadoop et HBase sont pris en charge) s’affichent dans **AZURE HDINSIGHT**. 

   ![Abonnement Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

5. Développez le cluster pour afficher le schéma de table et la base de données de métadonnées Hive.

   ![Cluster Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)

## <a name="additional-features"></a>Fonctionnalités supplémentaires

HDInsight pour VS Code prend en charge les fonctionnalités suivantes :

- **Saisie semi-automatique IntelliSense**. Fenêtre contextuelle de suggestions de mots-clés, de méthodes, de variables, etc. Des icônes différentes représentent des types d’objets différents.

    ![types d’objets IntelliSense dans HDInsight Tools pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Marqueur d’erreurs IntelliSense**. Le service de langage souligne les erreurs de saisie dans le script Hive.     
- **Coloration syntaxique**. Le service de langage utilise plusieurs couleurs pour différencier les variables, les mots-clés, le type des données, les fonctions, etc. 

    ![coloration syntaxique dans HDInsight Tools pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Étapes suivantes

### <a name="demo"></a>Démonstration
* HDInsight pour VS Code : [Vidéo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Outils et extensions

* [Utiliser Azure Toolkit for IntelliJ pour déboguer des applications Apache Spark à distance par VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser Azure Toolkit for IntelliJ pour déboguer des applications Apache Spark à distance par SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utiliser HDInsight Tools dans Azure Toolkit for Eclipse pour créer des applications Apache Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Utiliser des blocs-notes Apache Zeppelin avec un cluster Apache Spark sur HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Noyaux accessibles à Jupyter Notebook dans le cluster Apache Spark pour HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualiser des données Apache Hive à l’aide de Microsoft Power BI dans Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Visualiser des données Interactive Query Hive à l’aide de Power BI dans Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [ Définir l’environnement interactif de PySpark pour Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Utiliser Apache Zeppelin pour exécuter des requêtes Apache Hive dans Azure HDInsight](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Scénarios
* [Apache Spark avec BI : effectuer une analyse interactive des données avec Spark sur HDInsight avec des outils décisionnels](spark/apache-spark-use-bi-tools.md)
* [Apache Spark avec Machine Learning : utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide des données des systèmes HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark avec Machine Learning : utiliser Spark dans HDInsight pour prédire les résultats de l’inspection d’aliments](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide d’Apache Spark dans HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Créer et exécuter des applications
* [Créer une application autonome avec Scala](spark/apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance avec Apache Livy sur un cluster Apache Spark](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](spark/apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](spark/apache-spark-job-debugging.md)



