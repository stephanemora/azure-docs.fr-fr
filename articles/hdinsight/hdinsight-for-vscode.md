---
title: Azure HDInsight Tools - utiliser Visual Studio Code pour Hive, LLAP ou PySpark
description: Découvrez comment utiliser Azure HDInsight Tools pour Visual Studio Code pour créer et envoyer des requêtes et des scripts.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: d114a1e62ae0d28e7d4a3ad453d5d7bd3e1d5b7a
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427694"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Utiliser les Azure HDInsight Tools pour Visual Studio Code

Découvrez comment utiliser Azure HDInsight Tools pour Visual Studio Code pour créer et envoyer des tâches Apache Hive de traitement par lots, des requêtes Hive interactives et des scripts PySpark pour Apache Spark. Nous décrivons d’abord comment installer les outils HDInsight dans Visual Studio Code, puis nous expliquons comment envoyer des tâches Hive et Spark.  

Vous pouvez installer Azure HDInsight Tools sur les plateformes prises en charge par Visual Studio Code, qui sont Windows, Linux et MacOS. Les prérequis pour les différentes plateformes sont détaillés ci-dessous.


## <a name="prerequisites"></a>Conditions préalables

Avant de poursuivre cet article, vérifiez que vous avez les éléments nécessaires suivants :

- Un cluster HDInsight. Pour créer un cluster, consultez [Prise en main de HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono est nécessaire uniquement pour les plateformes Linux et MacOS.
- [Extension Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) pour Visual Studio Code.
- [Définir l’environnement interactif de PySpark pour Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Un répertoire local nommé **HDexample**.  Cet article utilise **C:\HD\HDexample**.

## <a name="install-azure-hdinsight-tools"></a>Installer Azure HDInsight Tools

Une fois que vous avez tous les éléments prérequis, installez Azure HDInsight Tools pour Visual Studio Code.  Procédez comme suit pour installer Azure HDInsight Tools :

1. Ouvrez Visual Studio Code.

2. À partir de la barre de menus, accédez à **Afficher** > **Extensions**.

3. Saisissez **HDInsight** dans la zone de recherche.

4. Sélectionnez **Azure HDInsight Tools** dans les résultats de recherche, puis sélectionnez **Installer**.  

   ![installation de Python avec HDInsight pour Visual Studio Code](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Sélectionnez **Recharger** pour activer l’extension **Azure HDInsight Tools** après son installation.


## <a name="open-hdinsight-work-folder"></a>Ouvrir le dossier de travail HDInsight

Procédez comme suit pour ouvrir un dossier de travail et créer un fichier dans Visual Studio Code :

1. À partir de la barre de menus, accédez à **Fichier** > **Ouvrir le dossier...**  > **C:\HD\HDexample**, puis sélectionnez le bouton **Sélectionner le dossier**. Le dossier s’affiche dans la vue **Explorer** sur la gauche.

2. À partir de la vue **Explorer**, sélectionnez le dossier, **HDexample**, puis l’icône **Nouveau fichier** en regard du dossier de travail.

   ![Nouveau fichier](./media/hdinsight-for-vscode/new-file.png)

3. Nommez le nouveau fichier avec l’extension de fichier .hql (requêtes Hive) ou .py (script Spark).  Cet exemple utilise **HelloWorld.hql**.

## <a name="connect-to-hdinsight-cluster"></a>Se connecter à un cluster HDInsight

Avant de pouvoir envoyer des scripts à des clusters HDInsight à partir de Visual Studio Code, vous devez vous connecter à votre compte Azure, ou lier un cluster (à l’aide du nom d’utilisateur/mot de passe Ambari ou d’un compte joint au domaine).  Suivez les étapes ci-dessous pour vous connecter à Azure :

1. À partir de la barre de menus, accédez à **Afficher** > **Palette de commandes...** , puis entrez **HDInsight : Connexion**.

    ![Connexion à HDInsight Tools pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Suivez les instructions de la connexion le **sortie** volet.
    + Pour l'environnement global Azure, la commande **HDInsight: Login** déclenchera l'action **Se connecter à Azure** dans l'explorateur HDInsight, et vice versa.

        ![Instructions de connexion pour Azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + Pour d’autres environnements, suivez les instructions de connexion.

        ![Instructions de connexion pour un autre environnement](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

   Une fois que vous êtes connecté, le nom de votre compte Azure s’affiche dans la barre d’état, dans l’angle inférieur gauche de la fenêtre Visual Studio Code.  
  

<h2 id="linkcluster">Créer un lien : Azure HDInsight</h2>

Vous pouvez lier un cluster normal à l’aide d’un nom d’utilisateur [Apache Ambari](https://ambari.apache.org/) managé ou lier un cluster de sécurité Hadoop de Pack Sécurité Entreprise à l’aide d’un nom d’utilisateur de domaine (par exemple : user1@contoso.com).

1. À partir de la barre de menus, accédez à **Afficher** > **Palette de commandes...** , puis entrez **HDInsight : Lier un cluster**.

   ![commande de lien du cluster](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Sélectionnez le type de cluster lié **Azure HDInsight**.

3. Entrez l’URL du cluster HDInsight.

4. Entrez le nom d’utilisateur Ambari, la valeur par défaut est **admin**.

5. Entrez le mot de passe Ambari.

6. Sélectionnez le type de cluster.

7. Passez en revue la vue **OUTPUT** à des fins de vérification.

   > [!NOTE]  
   > Le nom d’utilisateur lié et son mot de passe sont utilisés si le cluster est à la fois connecté à un abonnement Azure et lié à un cluster.  


## <a name="create-link-generic-livy-endpoint"></a>Créer un lien : Generic Livy Endpoint (point de terminaison Livy générique)

1. À partir de la barre de menus, accédez à **Afficher** > **Palette de commandes...** , puis entrez **HDInsight : Lier un cluster**.

2. Sélectionnez le type de cluster lié **Generic Livy Endpoint**.

3. Entrez le point de terminaison Livy générique, par exemple : http\://10.172.41.42:18080.

4. Sélectionnez le type d’autorisation **De base** ou **Aucune**.  Si vous choisissez **De base**, alors :  
    &emsp;a. Entrez le nom d’utilisateur Ambari, la valeur par défaut est **admin**.  
    &emsp;b. Entrez le mot de passe Ambari.

5. Passez en revue la vue **OUTPUT** à des fins de vérification.

## <a name="list-hdinsight-clusters"></a>Afficher la liste des clusters HDInsight

1. À partir de la barre de menus, accédez à **Afficher** > **Palette de commandes...** , puis entrez **HDInsight : Répertorier un cluster**.

2. Sélectionnez l’abonnement souhaité.

3. Passez en revue la vue **OUTPUT**.  La vue affiche votre ou vos clusters liés et tous les clusters sous votre abonnement Azure.

    ![Définir la configuration du cluster par défaut](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Définir le cluster par défaut

1. Ouvrez à nouveau le dossier **HDexample** créé [précédemment](#open-hdinsight-work-folder) si vous l’aviez fermé.  

2. Sélectionnez le fichier **HelloWorld.hql** créé [précédemment](#open-hdinsight-work-folder) pour l’ouvrir dans l’éditeur de script.

3. [Connectez-vous](#connect-to-hdinsight-cluster) à votre compte Azure, si ce n’est déjà fait.

4. Cliquez avec le bouton droit sur l’éditeur de script, puis sélectionnez **HDInsight : Définir le cluster par défaut**.  

5. Sélectionnez un cluster à utiliser comme cluster par défaut pour le fichier de script actuel. Les outils mettent automatiquement à jour le fichier de configuration **.VSCode\settings.json**. 

   ![Définir la configuration du cluster par défaut](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Configurer l’environnement Azure

1. [Connectez-vous](#connect-to-hdinsight-cluster) à votre compte Azure, si ce n’est déjà fait.

2. À partir de la barre de menus, accédez à **Afficher** > **Palette de commandes...** , puis entrez **HDInsight : Définir l’environnement Azure**.

3. Sélectionnez un environnement en tant que votre entrée de connexion par défaut.

4. Entre temps, l’outil a déjà enregistré votre entrée de connexion par défaut dans **.VSCode\settings.json**. Elle est également directement mise à jour dans ce fichier de configuration. 

   ![Définir la configuration de l’entrée de connexion par défaut](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Envoyer des requêtes, scripts de commandes par lot Hive interactifs

HDInsight Tools pour Visual Studio Code vous permet d’envoyer des requêtes, scripts de commandes par lot Hive interactifs à des clusters HDInsight.

1. Ouvrez à nouveau le dossier **HDexample** créé [précédemment](#open-hdinsight-work-folder) si vous l’aviez fermé.  

2. Sélectionnez le fichier **HelloWorld.hql** créé [précédemment](#open-hdinsight-work-folder) pour l’ouvrir dans l’éditeur de script.

3. [Connectez-vous](#connect-to-hdinsight-cluster) à votre compte Azure, si ce n’est déjà fait.

4. Copiez et collez le code suivant dans votre fichier Hive, puis enregistrez-le.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

5. Cliquez avec le bouton droit sur l’éditeur de script, puis cliquez sur **HDInsight : Hive Interactive** pour envoyer la requête, ou utilisez le raccourci **Ctrl+Alt+I**.  Sélectionnez **HDInsight : Hive Batch** pour envoyer le script, ou utilisez le raccourci clavier **Ctrl+Alt+H**.  

6. Sélectionnez un cluster autre que le cluster par défaut. HDInsight Tools vous permet également d’envoyer un bloc de code au lieu du fichier de script entier à partir du menu contextuel. Peu après, les résultats de la requête s'affichent dans un nouvel onglet.

   ![Résultat de la requête Hive interactive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Volet **RÉSULTATS** : Vous pouvez enregistrer le résultat complet dans un fichier CSV, JSON ou Excel dans un chemin local, ou enregistrer seulement certaines lignes du résultat.

    - Volet **MESSAGES** : Cliquez sur un numéro de **ligne** pour accéder à la première ligne du script en cours d’exécution.

## <a name="submit-interactive-pyspark-queries"></a>Envoyer des requêtes PySpark interactives

1. Ouvrez à nouveau le dossier **HDexample** créé [précédemment](#open-hdinsight-work-folder) si vous l’aviez fermé.  

2. Créez un nouveau fichier **HelloWorld.py** en suivant les étapes [précédentes](#open-hdinsight-work-folder).

3. Une boîte de dialogue de recommandation d’extension Python s’affiche si vous n’avez pas installé Python pour les prérequis.  Installez et rechargez Visual Studio Code pour terminer l’installation.

    >![installation de Python avec HDInsight pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

4. [Connectez-vous](#connect-to-hdinsight-cluster) à votre compte Azure, si ce n’est déjà fait.

5. Copiez et collez le code suivant dans le fichier de script :
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

6. Cliquez avec le bouton droit sur l’éditeur de script, puis cliquez sur **HDInsight : PySpark Interactive** pour envoyer la requête, ou utilisez le raccourci **Ctrl+Alt+I**.  

7. Sélectionnez un cluster autre que le cluster par défaut. HDInsight Tools vous permet également d’envoyer un bloc de code au lieu du fichier de script entier à partir du menu contextuel. Peu après, les résultats de la requête s'affichent dans un nouvel onglet.

   ![Résultat de l’envoi du travail Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 

8. L’outil prend également en charge la requêtes avec **Clause SQL**.

   ![Résultat de l’envoi du travail Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) L’état de l’envoi s’affiche en bas à gauche dans la barre d’état lors de l’exécution des requêtes. N’envoyez pas d’autres requêtes lorsque l’état est **PySpark Kernel (busy)** .  

>[!NOTE]  
>Les clusters peuvent mettre à jour les informations de session. La variable et la fonction définies, ainsi que les valeurs correspondantes sont conservées dans la session, ce qui permet de les référencer dans plusieurs appels de service pour le même cluster. 

### <a name="pyspark3-is-not-supported-with-spark2223"></a>PySpark3 n’est pas pris en charge avec Spark2.2/2.3

PySpark3 n’est plus pris en charge avec un cluster Spark2.2 ou Spark 2.3. Seul « PySpark » est pris en charge pour Python. L’échec de l’envoi à Spark 2.2/2.3 avec Python3 est un problème connu.

   ![Erreur obtenue lors de l’envoi à Python3](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Pour utiliser Python2.x, procédez comme suit : 

1. Installez Python 2.7 sur ordinateur local et ajoutez-le au chemin d’accès système.

2. Redémarrez Visual Studio Code.

3. Basculez vers Python 2 en cliquant sur **Python XXX** dans la barre d'état, puis en choisissant la cible Python.

   ![Sélectionner la version de Python](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)


## <a name="submit-pyspark-batch-job"></a>Envoi de la tâche de traitement par lots PySpark

1. Ouvrez à nouveau le dossier **HDexample** créé [précédemment](#open-hdinsight-work-folder) si vous l’aviez fermé.  

2. Créez un nouveau fichier **BatchFile.py** en suivant les étapes [précédentes](#open-hdinsight-work-folder).

3. [Connectez-vous](#connect-to-hdinsight-cluster) à votre compte Azure, si ce n’est déjà fait.

4. Copiez et collez le code suivant dans le fichier de script :

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

Une fois que vous avez envoyé un travail Python, les journaux d’activité d’envoi apparaissent dans la fenêtre **OUTPUT** (Sortie) dans Visual Studio Code. **L’URL de l’interface utilisateur Spark** et **l’URL de l’interface utilisateur Yarn** s’affichent également. Vous pouvez ouvrir l’URL dans un navigateur web pour suivre l’état du travail.

## <a name="apache-livy-configuration"></a>Configuration d’Apache Livy

La configuration [d’Apache Livy](https://livy.incubator.apache.org/) est prise en charge et peut être définie à l’emplacement **.VSCode\settings.json** dans le dossier de l’espace de travail. Actuellement, la configuration Livy prend uniquement en charge le script Python. Pour plus d’informations, consultez [Lisez-moi Livy](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Comment déclencher une configuration Livy**

Méthode 1  
1. À partir de la barre de menus, accédez à **Fichier** > **Préférences** > **Paramètres**.  
2. Dans la zone de texte **Paramètres de recherche**, entrez **Envoi de travail HDInsight : Livy Conf**.  
3. Sélectionnez **Modifier dans settings.json** pour le résultat de recherche pertinent.

Méthode 2   
Envoyez un fichier, notez que le dossier .vscode est ajouté automatiquement au dossier de travail. Vous pouvez trouver la configuration Livy en cliquant sur **.vscode\settings.json**.

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
    | proxyUser | Utilisateur auquel emprunter l’identité lors de l’exécution de la tâche | string | 
    | className | Classe principale Java/Spark de l’application | string |
    | args | Arguments de ligne de commande pour l’application | liste de valeurs string | 
    | jars | Fichiers JAR à utiliser dans cette session | Liste de chaînes | 
    | pyFiles | Fichiers Python à utiliser dans cette session | Liste de chaînes |
    | fichiers d'entrée | Fichiers à utiliser dans cette session | Liste de chaînes |
    | driverMemory | Quantité de mémoire à utiliser pour le processus de pilote | string |
    | driverCores | Nombre de cœurs à utiliser pour le processus de pilote | int |
    | executorMemory | Quantité de mémoire à utiliser par processus de l’exécuteur | string |
    | executorCores | Nombre de cœurs à utiliser pour chaque exécuteur | int |
    | numExecutors | Nombre d’exécuteurs à lancer pour cette session | int |
    | archives | Archives à utiliser dans cette session | Liste de chaînes |
    | file d'attente | Nom de la file d’attente YARN vers laquelle effectuer l’envoi | string |
    | Nom | Nom de cette session | string |
    | conf | Propriétés de configuration Spark. | Map of key=val |

    Corps de réponse   
    Objet Batch créé

    | Nom | description | Type | 
    | :- | :- | :- | 
    | id | ID de la session | int | 
    | appId | ID d’application de cette session |  Chaîne |
    | appInfo | Informations détaillées sur l’application | Map of key=val |
    | log | Lignes du journal | liste de valeurs string |
    | state |   État du lot | string |

>[!NOTE]
>La configuration Livy attribuée s’affiche dans le volet de sortie lors de l’envoi du script.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Intégrer avec Azure HDInsight à partir de l’Explorateur

**Azure HDInsight** a été ajouté à la vue Explorateur. Vous pouvez parcourir et gérer vos clusters directement via **Azure HDInsight**.

1. [Connectez-vous](#connect-to-hdinsight-cluster) à votre compte Azure, si ce n’est déjà fait.

2. À partir de la barre de menus, accédez à **Afficher** > **Explorateur**.

3. Dans le volet gauche, développez **AZURE HDINSIGHT**.  Les clusters et abonnements disponibles (Spark, Hadoop et HBase sont pris en charge) s’affichent. 

   ![Abonnement Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Développez le cluster pour afficher le schéma de table et la base de données de métadonnées Hive.

   ![Cluster Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="additional-features"></a>Fonctionnalités supplémentaires

HDInsight pour Visual Studio Code prend en charge les fonctionnalités suivantes :

- **La saisie semi-automatique IntelliSense**. Fenêtre contextuelle de suggestions de mots-clés, de méthodes, de variables, etc. Des icônes différentes représentent des types d’objets différents.

    ![types d’objets IntelliSense dans HDInsight Tools pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Marqueur d’erreurs IntelliSense**. Le service de langage souligne les erreurs de saisie dans le script Hive.     
- **Coloration syntaxique**. Le service de langage utilise plusieurs couleurs pour différencier les variables, les mots-clés, le type des données, les fonctions, etc. 

    ![coloration syntaxique dans HDInsight Tools pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)


## <a name="unlink-cluster"></a>Supprimer le lien du cluster

1. À partir de la barre de menus, accédez à **Afficher** > **Palette de commandes...** , puis entrez **HDInsight : Supprimer le lien du cluster**.  

2. Sélectionnez le cluster pour lequel supprimer le lien.  

3. Passez en revue la vue **OUTPUT** à des fins de vérification.  


## <a name="logout"></a>Déconnexion  

À partir de la barre de menus, accédez à **Afficher** > **Palette de commandes...** , puis entrez **HDInsight : Déconnexion**.  Une fenêtre contextuelle s’affiche dans l’angle inférieur droit signalant **Déconnexion réussie !** .


## <a name="next-steps"></a>Étapes suivantes
Pour une vidéo de démonstration sur l’utilisation de HDInsight pour Visual Studio Code, consultez [HDInsight pour Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
