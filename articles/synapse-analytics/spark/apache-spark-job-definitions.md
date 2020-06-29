---
title: 'Tutoriel : Créer une définition de travail Apache Spark dans Synapse Studio'
description: 'Tutoriel : utiliser Azure Synapse Analytics pour créer des définitions de travaux Spark et les envoyer à un pool Apache Spark pour Azure Synapse Analytics.'
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.openlocfilehash: 3311a9a92cc5e63a6fa20e4dd0d2af00fdacc95c
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194482"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>Tutoriel : Créer une définition de travail Apache Spark dans Synapse Studio

Ce tutoriel montre comment utiliser Azure Synapse Studio pour créer des définitions de travaux Apache Spark et les envoyer à un pool Apache Spark.

Ce tutoriel décrit les tâches suivantes :

* Créer une définition de travail Apache Spark pour PySpark (Python)
* Créer une définition de travail Apache Spark pour Spark(Scala)
* Créer une définition de travail Apache Spark pour .NET Spark(C#)
* Envoyer une définition de travail Apache Spark en tant que traitement par lots
* Ajouter une définition de travail Apache Spark dans le pipeline

## <a name="prerequisites"></a>Prérequis

Avant de commencer le didacticiel, veillez à disposer des éléments suivants :

* Un espace de travail Azure Synapse Analytics. Pour obtenir des instructions, consultez [Créer un espace de travail Azure Synapse Analytics](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).
* Un pool Apache Spark.
* Un compte de stockage ADLS Gen2. Vous devez être le propriétaire des données Blob du stockage du système de fichiers ADLS Gen2 que vous souhaitez utiliser. Si ce n’est pas le cas, vous devez ajouter l’autorisation manuellement.

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>Créer une définition de travail Apache Spark pour PySpark (Python)

Dans cette section, vous allez créer une définition de travail Apache Spark pour PySpark (Python).

1. Ouvrez [Azure Synapse Studio](https://web.azuresynapse.net/).

2. Vous pouvez accéder aux [exemples de fichiers pour la création de définitions de travaux Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python), et télécharger les fichiers **wordcount.jar** et **shakespear.txt**. Téléchargez ensuite ces fichiers dans le stockage Azure : Cliquez sur **Données**, sélectionnez **Comptes de stockage**, puis chargez les fichiers associés à votre système de fichiers ADLS Gen2. Ignorez cette étape si vos fichiers se trouvent déjà dans le stockage Azure. 

     ![chargement du fichier python](./media/apache-spark-job-definitions/upload-python-file.png)

3. Cliquez sur le hub **Développer**, sélectionnez **Définitions de travaux Spark** dans le volet gauche, cliquez sur le nœud d’action « ... » en regard de **Définitions de travaux Spark**, puis sélectionnez **Nouvelle définition de travail Spark** dans le menu contextuel.

     ![création d’une définition pour python](./media/apache-spark-job-definitions/create-new-definition.png)

4. Sélectionnez **PySpark (Python)** dans la liste déroulante Langage de la fenêtre principale de la définition de travail Apache Spark.

5. Renseignez les informations pour la définition de travail Apache Spark. Vous pouvez copier les exemples d’informations.

     |  Propriété   | Description   |  
     | ----- | ----- |  
     |Nom de la définition de travail| Entrez un nom pour votre définition de travail Apache Spark. Ce nom peut être mis à jour à tout moment jusqu’à ce qu’il soit publié. Exemple : `job definition sample`|
     |Fichier de définition principal| Fichier principal utilisé pour le travail. Sélectionnez un fichier PY à partir de votre stockage. Vous pouvez sélectionner **Charger le fichier** pour charger le fichier sur un compte de stockage. Exemple : `abfss://azureportaldeploy@storageaccountname.dfs.core.windows.net/synapse/workspaces/workspace name/batchjobs/python/fileexists.py`|
     |Arguments de ligne de commande| Arguments facultatifs du travail. Exemple : `shakespeare.txt`|
     |Fichiers de référence| Fichiers supplémentaires utilisés en guise de référence dans le fichier de définition principal. Vous pouvez sélectionner **Charger le fichier** pour charger le fichier sur un compte de stockage. Exemple : `abfss://azureportaldeploy@storageaccountname.dfs.core.windows.net/synapse/workspaces/workspace name/batchjobs/python/shakespeare.txt`|
     |Pool Spark| Le travail sera envoyé au pool Apache Spark sélectionné.|
     |Version de Spark| Version d’Apache Spark exécutée par le pool Apache Spark.|
     |Exécuteurs| Nombre d’exécuteurs à attribuer dans le pool Apache Spark spécifié pour le travail.|
     |Taille de l’exécuteur| Nombre de cœurs et mémoire à utiliser pour les exécuteurs dans le pool Apache Spark spécifié du travail.|  
     |Taille du pilote| Nombre de cœurs et mémoire à utiliser pour le pilote dans le pool Apache Spark spécifié du travail.|

     ![Définir les valeurs de la définition de travail Spark pour Python](./media/apache-spark-job-definitions/create-py-definition.png)

6. Sélectionnez **Publier** pour enregistrer la définition de travail Apache Spark.

     ![publication de la définition py](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Créer une définition de travail Apache Spark pour Apache Spark(Scala)

Dans cette section, vous allez créer une définition de travail Apache Spark pour Apache Spark(Scala).

 1. Ouvrez [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Vous pouvez accéder aux [exemples de fichiers pour la création de définitions de travaux Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala), et télécharger les fichiers **wordcount.jar** et **shakespear.txt**. Téléchargez ensuite ces fichiers dans le stockage Azure : Cliquez sur **Données**, sélectionnez **Comptes de stockage**, puis chargez les fichiers associés à votre système de fichiers ADLS Gen2. Ignorez cette étape si vos fichiers se trouvent déjà dans le stockage Azure. 
 
     ![préparation de la structure scala](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. Cliquez sur le hub **Développer**, sélectionnez **Définitions de travaux Spark** dans le volet gauche, cliquez sur le nœud d’action « ... » en regard de **Définitions de travaux Spark**, puis sélectionnez **Nouvelle définition de travail Spark** dans le menu contextuel.
     ![création d’une définition pour scala](./media/apache-spark-job-definitions/create-new-definition.png)

 4. Sélectionnez **Spark(Scala)** dans la liste déroulante Langage de la fenêtre principale de la définition de travail Apache Spark.

 5. Renseignez les informations pour la définition de travail Apache Spark. Vous pouvez copier les exemples d’informations.

     |  Propriété   | Description   |  
     | ----- | ----- |  
     |Nom de la définition de travail| Entrez un nom pour votre définition de travail Apache Spark. Ce nom peut être mis à jour à tout moment jusqu’à ce qu’il soit publié. Exemple : `job definition sample`|
     |Fichier de définition principal| Fichier principal utilisé pour le travail. Sélectionnez un fichier JAR à partir de votre stockage. Vous pouvez sélectionner **Charger le fichier** pour charger le fichier sur un compte de stockage. Exemple : `abfss://sparkjob@storageaccountname.dfs.core.windows.net/scala/wordcount/wordcount.jar`|
     |Main class name| Identificateur complet ou classe principale qui se trouve dans le fichier de définition principal. Exemple : `WordCount`|
     |Arguments de ligne de commande| Arguments facultatifs du travail. Exemple : `abfss://sparkjob@storageaccountname.dfs.core.windows.net/scala/wordcount/shakespeare.txt abfss://sparkjob@storageaccountname.dfs.core.windows.net/scala/wordcount/result`|
     |Fichiers de référence| Fichiers supplémentaires utilisés en guise de référence dans le fichier de définition principal. Vous pouvez sélectionner **Charger le fichier** pour charger le fichier sur un compte de stockage.|
     |Pool Spark| Le travail sera envoyé au pool Apache Spark sélectionné.|
     |Version de Spark| Version d’Apache Spark exécutée par le pool Apache Spark.|
     |Exécuteurs| Nombre d’exécuteurs à attribuer dans le pool Apache Spark spécifié pour le travail.|  
     |Taille de l’exécuteur| Nombre de cœurs et mémoire à utiliser pour les exécuteurs dans le pool Apache Spark spécifié du travail.|
     |Taille du pilote| Nombre de cœurs et mémoire à utiliser pour le pilote dans le pool Apache Spark spécifié du travail.|

     ![Définir les valeurs de la définition de travail Spark pour Scala](./media/apache-spark-job-definitions/create-scala-definition.png)

 6. Sélectionnez **Publier** pour enregistrer la définition de travail Apache Spark.

     ![publication de la définition scala](./media/apache-spark-job-definitions/publish-scala-definition.png)


## <a name="create-an-apache-spark-job-definition-for-net-sparkc"></a>Créer une définition de travail Apache Spark pour .NET Spark(C#)

Dans cette section, vous allez créer une définition de travail Apache Spark pour .NET Spark(C#).
 1. Ouvrez [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Vous pouvez accéder aux [exemples de fichiers pour la création de définitions de travaux Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET), et télécharger les fichiers **wordcount.zip** et **shakespear.txt**. Téléchargez ensuite ces fichiers dans le stockage Azure : Cliquez sur **Données**, sélectionnez **Comptes de stockage**, puis chargez les fichiers associés à votre système de fichiers ADLS Gen2. Ignorez cette étape si vos fichiers se trouvent déjà dans le stockage Azure. 

     ![préparation de la structure dotnet](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. Cliquez sur le hub **Développer**, sélectionnez **Définitions de travaux Spark** dans le volet gauche, cliquez sur le nœud d’action « ... » en regard de **Définitions de travaux Spark**, puis sélectionnez **Nouvelle définition de travail Spark** dans le menu contextuel.

     ![création d’une définition pour dotnet](./media/apache-spark-job-definitions/create-new-definition.png)

 4. Sélectionnez **.NET Spark(C#/F#)** dans la liste déroulante Langage de la fenêtre principale Définition de travail Apache Spark.

 5. Renseignez les informations pour la définition de travail Apache Spark. Vous pouvez copier les exemples d’informations.
     |  Propriété   | Description   |  
     | ----- | ----- |  
     |Nom de la définition de travail| Entrez un nom pour votre définition de travail Apache Spark. Ce nom peut être mis à jour à tout moment jusqu’à ce qu’il soit publié. Exemple : `job definition sample`|
     |Fichier de définition principal| Fichier principal utilisé pour le travail. Sélectionnez un fichier ZIP contenant votre application .NET pour Apache Spark (c’est-à-dire, le fichier exécutable principal, les DLL contenant les fonctions définies par l’utilisateur et d’autres fichiers nécessaires) à partir de votre stockage. Vous pouvez sélectionner **Charger le fichier** pour charger le fichier sur un compte de stockage. Exemple : `abfss://sparkjob@storageaccountname.dfs.core.windows.net/dotnet/wordcount/wordcount.zip`|
     |Fichier exécutable principal| Fichier exécutable principal dans le fichier ZIP de définition principal. Exemple : `WordCount`|
     |Arguments de ligne de commande| Arguments facultatifs du travail. Exemple : `abfss://sparkjob@storageaccountname.dfs.core.windows.net/dotnet/wordcount/shakespeare.txt abfss://sparkjob@storageaccountname.dfs.core.windows.net/dotnet/wordcount/result`|
     |Fichiers de référence| Fichiers supplémentaires nécessaires aux nœuds worker pour l’exécution de l’application .NET pour Apache Spark qui n’est pas incluse dans le fichier ZIP de définition principal (autrement dit, les fichiers jar dépendants, les DLL supplémentaires de fonctions définies par l’utilisateur et d’autres fichiers de configuration). Vous pouvez sélectionner **Charger le fichier** pour charger le fichier sur un compte de stockage.|
     |Pool Spark| Le travail sera envoyé au pool Apache Spark sélectionné.|
     |Version de Spark| Version d’Apache Spark exécutée par le pool Apache Spark.|
     |Exécuteurs| Nombre d’exécuteurs à attribuer dans le pool Apache Spark spécifié pour le travail.|  
     |Taille de l’exécuteur| Nombre de cœurs et mémoire à utiliser pour les exécuteurs dans le pool Apache Spark spécifié du travail.|
     |Taille du pilote| Nombre de cœurs et mémoire à utiliser pour le pilote dans le pool Apache Spark spécifié du travail.|

     ![Définir les valeurs de la définition de travail Spark pour dotnet](./media/apache-spark-job-definitions/create-net-definition.png)

 6. Sélectionnez **Publier** pour enregistrer la définition de travail Apache Spark.

      ![publication de la définition dotnet](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Envoyer une définition de travail Apache Spark en tant que traitement par lots

Après avoir créé une définition de travail Apache Spark, vous pouvez l’envoyer à un pool Apache Spark. Vérifiez que vous êtes bien le propriétaire des données Blob du stockage du système de fichiers ADLS Gen2 que vous souhaitez utiliser. Si ce n’est pas le cas, vous devez ajouter l’autorisation manuellement.

### <a name="scenario-1-submit-apache-spark-job-definition"></a>Scénario 1 : Envoyer une définition de travail Apache Spark
 1. Ouvrez une fenêtre de définition de travail Apache Spark en cliquant sur la définition concernée.

      ![Ouvrez la définition de travail Spark à envoyer ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. Cliquez sur l’icône **Envoyer** pour envoyer votre projet au pool Apache Spark sélectionné. Vous pouvez cliquer sur l’**URL de supervision Spark** pour voir les informations LogQuery de l’application Apache Spark.

    ![Cliquez sur le bouton Envoyer pour envoyer la définition de travail Spark](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Boîte de dialogue Spark Submission (Envoi Spark)](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>Scénario 2 : Afficher la progression de l’exécution du travail Apache Spark

 1. Cliquez sur **Superviser**, puis sélectionnez l’option **Applications Spark**. Vous pouvez trouver l’application Apache Spark envoyée.

     ![Affichez l’application Spark](./media/apache-spark-job-definitions/view-spark-application.png)

 2. Cliquez ensuite sur l’application Apache Spark pour ouvrir la fenêtre **LogQuery**. Vous pouvez voir la progression de l’exécution du travail dans **LogQuery**.
     
     ![Affichez les informations LogQuery de l’application Spark](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scénario 3 : Vérifier le fichier de sortie

 1. Cliquez sur **Données**, puis sélectionnez **Comptes de stockage**. Après une exécution réussie, vous pouvez accéder au stockage ADLS Gen2 et vérifier que les sorties sont générées.

     ![Affichez le fichier de sortie](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Ajouter une définition de travail Apache Spark dans le pipeline

Dans cette section, vous allez ajouter une définition de travail Apache Spark dans le pipeline.

 1. Ouvrez une définition de travail Apache Spark existante.

 2. Cliquez sur l’icône en haut à droite de la définition de travail Apache Spark, puis choisissez **Pipeline existant** ou **Nouveau pipeline**. Vous pouvez consulter la page Pipeline pour plus d’informations.

     ![ajout au pipeline](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![ajout au pipeline](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel montre comment utiliser Azure Synapse Studio pour créer des définitions de travaux Apache Spark et les envoyer à un pool Apache Spark. À présent, vous pouvez utiliser Azure Synapse Studio pour créer des jeux de données Power BI et gérer des données Power BI.

