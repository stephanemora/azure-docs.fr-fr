---
title: 'Didacticiel : créer une application Scala Maven pour Spark dans HDInsight à l’aide d’IntelliJ | Microsoft Docs'
description: Créez une application Spark écrite en Scala avec Apache Maven comme système de génération et un archétype Maven existant pour Scala fourni par IntelliJ IDEA.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: ed90e50167f7e86c464b1571b91dc27435437e9b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627414"
---
# <a name="tutorial-create-a-scala-maven-application-for-spark-in-hdinsight-using-intellij"></a>Didacticiel : créer une application Scala Maven pour Spark dans HDInsight à l’aide d’IntelliJ

Dans ce didacticiel, vous découvrirez comment créer une application Spark écrite en Scala à l’aide de Maven avec IntelliJ IDEA. Dans cet article, Apache Maven est utilisé comme système de génération. Le document présente dans un premier temps un archétype Maven existant pour Scala fourni par IntelliJ IDEA.  La création d’une application Scala avec IntelliJ IDEA implique les étapes suivantes :

* Utiliser Maven en tant que le système de génération.
* Mettre à jour le fichier de modèle objet du projet (POM) pour résoudre les dépendances de module Spark.
* Écrire votre application dans Scala.
* Générer un fichier jar qui peut être soumis à des clusters HDInsight Spark.
* Exécuter l’application à distance sur le cluster Spark à l’aide de Livy.

> [!NOTE]
> HDInsight propose également un outil de plug-in IntelliJ IDEA pour faciliter le processus de création et d’envoi des applications à un cluster HDInsight Spark sous Linux. Pour plus d’informations, consultez [Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA afin de créer et d’envoyer des applications Spark](apache-spark-intellij-tool-plugin.md).
> 

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Utiliser IntelliJ pour développer une application Scala Maven

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.


## <a name="prerequisites"></a>Prérequis

* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Kit de développement logiciel (SDK) Oracle Java. Vous pouvez l’installer à partir d’ [ici](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IDE Java. Cet article utilise IntelliJ IDEA 18.1.1. Vous pouvez l’installer à partir [d’ici](https://www.jetbrains.com/idea/download/).

## <a name="use-intellij-to-create-application"></a>Utilisez IntelliJ pour créer l’application

1. Démarrez IntelliJ IDEA et créez un projet. Dans la boîte de dialogue **Nouveau projet** , procédez comme suit : 

   a. Sélectionnez **HDInsight** > **Spark sur HDInsight (Scala)**.

   b. Dans la liste des **outils de génération**, sélectionnez l’une des options suivantes, en fonction de vos besoins :

      * **Maven**, pour la prise en charge de l’Assistant de création de projets Scala
      * **SBT**, pour gérer les dépendances et la génération du projet Scala

   ![Boîte de dialogue Nouveau projet](./media/apache-spark-create-standalone-application/create-hdi-scala-app.png)

2. Sélectionnez **Suivant**.

3. L’assistant de création de projets Scala détecte automatiquement si vous avez installé le plug-in Scala. Sélectionnez **Installer**.

   ![Vérification de l’installation du plug-in Scala](./media/apache-spark-create-standalone-application/Scala-Plugin-check-Reminder.PNG) 

4. Sélectionnez **OK** pour télécharger le plug-in Scala. Suivez les instructions pour redémarrer IntelliJ. 

   ![Boîte de dialogue d’installation du plug-in Scala](./media/apache-spark-create-standalone-application/Choose-Scala-Plugin.PNG)

5. Dans la fenêtre **Nouveau projet**, procédez comme suit :  

    ![Sélection du kit de développement logiciel (SDK) Spark](./media/apache-spark-create-standalone-application/hdi-new-project.png)

   a. Entrez un nom de projet et un emplacement.

   b. Dans la liste déroulante **Kit de développement logiciel (SDK) de projet**, sélectionnez **Java 1.8** pour le cluster Spark 2.x ou **Java 1.7** pour le cluster Spark 1.x.

   c. Dans la liste déroulante **Version Spark**, l’assistant de création de projets Scala intègre la version correcte pour le kit de développement logiciel Spark et le kit de développement logiciel Scala. Si la version du cluster spark est antérieure à la version 2.0, sélectionnez **Spark 1.x**. Sinon, sélectionnez **Spark 2.x**. La version utilisée dans cet exemple est **Spark 2.0.2 (Scala 2.11.8)**.

6. Sélectionnez **Terminer**.

## <a name="install-scala-plugin-for-intellij-idea"></a>Installer le plug-in Scala pour IntelliJ IDEA
Pour installer le plug-in Scala, procédez comme suit :

1. Ouvrez IntelliJ IDEA.
2. Sur l’écran d’accueil, sélectionnez **Configurer**, puis **Plug-ins**.
   
    ![Activer le plug-in Scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
3. Sélectionnez **Installer le plug-in JetBrains** dans le coin inférieur gauche. 
4. Dans la boîte de dialogue **Parcourir les plug-ins JetBrains**, recherchez **Scala**, puis sélectionnez **Installer**.
   
    ![Installer le plug-in Scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
5. Une fois que le plug-in est bien installé, vous devez redémarrer l’IDE.

## <a name="create-a-standalone-scala-project"></a>Créer un programme Scala autonome
1. Ouvrez IntelliJ IDEA.
2. À partir du menu **Fichier**, sélectionnez **Nouveau > Projet** pour créer un projet.
3. Dans la boîte de dialogue du nouveau projet, choisissez les options suivantes :
   
    ![Créer un projet Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * Sélectionnez **Maven** comme type de projet.
   * Spécifiez un **projet SDK**. Sélectionnez **Nouveau**, puis accédez au répertoire d’installation Java, généralement `C:\Program Files\Java\jdk1.8.0_66`.
   * Sélectionnez l’option **Créer à partir d'un archétype** .
   * Dans la liste des archétypes, sélectionnez **org.scala-tools.archetypes:scala-archetype-simple**. Cet archétype crée la structure de répertoire appropriée et télécharge les dépendances requises par défaut pour écrire le programme Scala.
4. Sélectionnez **Suivant**.
5. Indiquez les valeurs appropriées pour **GroupId**, **ArtifactId** et **Version**. Les valeurs suivantes sont utilisées dans ce didacticiel :

    - GroupId : com.microsoft.spark.example
    - ArtifactId : SparkSimpleApp
6. Sélectionnez **Suivant**.
7. Vérifiez les paramètres, puis sélectionnez **Suivant**.
8. Vérifiez le nom et l’emplacement du projet, puis sélectionnez **Terminer**.
9. Dans le volet gauche, sélectionnez **src > test > scala > com > microsoft > spark > example**, cliquez avec le bouton droit sur **MySpec**, puis sélectionnez **Supprimer**. Ce fichier est inutile pour l’application.
  
10. Dans les étapes suivantes, vous allez mettre à jour le fichier pom.xml pour définir les dépendances de l’application Spark Scala. Ces dépendances sont téléchargées et résolues automatiquement, c’est pourquoi vous devez configurer Maven en conséquence.
   
    ![Configurer Maven pour les téléchargements automatiques](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. Dans le menu **Fichier**, sélectionnez **Paramètres**.
   2. Dans la boîte de dialogue **Paramètres**, accédez à **Génération, exécution, déploiement** > **Outils de génération** > **Maven** > **Importation**.
   3. Sélectionnez l'option **Importer les projets Maven automatiquement**.
   4. Sélectionnez **Apply** (Appliquer), puis **OK**.
11. Dans le volet gauche, sélectionnez **src > main > scala > com.microsoft.spark.example**, puis double-cliquez sur **Application** pour ouvrir App.scala.

12. Remplacez le code existant par le code suivant et enregistrez les modifications. Ce code lit les données du fichier HVAC.csv (disponible sur tous les clusters HDInsight Spark), récupère les lignes qui contiennent uniquement un chiffre dans la sixième colonne et écrit la sortie dans **/HVACOut** , sous le conteneur de stockage par défaut du cluster.

        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
13. Dans le volet gauche, double-cliquez sur **pom.xml**.
   
   1. Ajoutez les segments suivants à `<project>\<properties>` :
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. Ajoutez les segments suivants à `<project>\<dependencies>` :
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Enregistrez les modifications apportées au fichier pom.xml.
10. Créez le fichier .jar IntelliJ IDEA permet de créer le fichier JAR en tant qu’artefact d'un projet. Procédez comme suit.
    
    1. Dans le menu **Fichier**, sélectionnez **Structure de projet**.
    2. Dans la boîte de dialogue **Structure de projet**, sélectionnez **Artefacts**, puis le signe plus. Dans la boîte de dialogue contextuelle, sélectionnez **JAR**, puis **À partir de modules ayant des dépendances**.
       
        ![Créer un fichier jar](./media/apache-spark-create-standalone-application/create-jar-1.png)
    3. Dans la boîte de dialogue **Créer un fichier JAR à partir de modules**, cliquez sur le bouton de sélection (![ellipse](./media/apache-spark-create-standalone-application/ellipsis.png)) en regard de **Classe principale**.
    4. Dans la boîte de dialogue **Sélectionner une classe principale**, sélectionnez la classe qui s’affiche par défaut, puis sélectionnez **OK**.
       
        ![Créer un fichier jar](./media/apache-spark-create-standalone-application/create-jar-2.png)
    5. Dans la boîte de dialogue **Créer un fichier JAR à partir de modules**, assurez-vous que l’option **Extract to the target JAR** (Extraire vers le fichier JAR cible) est activée, puis sélectionnez **OK**.  Ce paramètre crée un fichier JAR unique contenant toutes les dépendances.
       
        ![Créer un fichier jar](./media/apache-spark-create-standalone-application/create-jar-3.png)
    6. L’onglet Disposition de la sortie répertorie tous les fichiers JAR inclus dans le cadre du projet Maven. Vous pouvez sélectionner et supprimer ceux sur lesquels l’application Scala n’a aucune dépendance directe. Pour l’application que vous créez ici, vous pouvez tous les supprimer sauf le dernier (**SparkSimpleApp compile output**). Sélectionnez les fichiers JAR à supprimer, puis sélectionnez l’icône **Supprimer** .
       
        ![Créer un fichier jar](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Assurez-vous que la case à cocher **Include in project build** (Inclure dans la build du projet) est activée, ce qui garantit la création du fichier JAR à chaque génération ou mise à jour du projet. Sélectionnez **Appliquer**, puis **OK**.
    7. Dans le menu **Générer**, sélectionnez **Générer les artefacts** pour créer le fichier JAR. Le fichier JAR de sortie est créé sous **\out\artifacts**.
       
        ![Créer un fichier jar](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Exécuter l’application sur le cluster Spark
Pour exécuter l’application sur le cluster, vous pouvez procéder comme suit :

* **Copiez le fichier JAR d'application sur l'objet blob Azure Storage** associé au cluster. Pour ce faire, vous pouvez utiliser l’utilitaire en ligne de commande [**AzCopy**](../../storage/common/storage-use-azcopy.md). De nombreux autres clients permettent également de charger des données. Pour en savoir plus à leur sujet, consultez [Téléchargement de données pour les travaux Hadoop dans HDInsight](../hdinsight-upload-data.md).
* **Utilisez Livy pour soumettre une tâche de l'application à distance** au cluster Spark. Les clusters Spark sur HDInsight incluent Livy qui expose des points de terminaison REST permettant d’envoyer à distance des travaux Spark. Pour plus d’informations, consultez [Envoi de travaux Spark à distance en utilisant Livy avec les clusters Spark sur HDInsight](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Étapes suivantes

Dans cet article, vous avez appris à créer une application Spark Scala. Passez à l’article suivant pour savoir comment exécuter cette application sur un cluster HDInsight Spark en utilisant Livy.

> [!div class="nextstepaction"]
>[Exécution de travaux à distance avec Livy sur un cluster Spark](./apache-spark-livy-rest-interface.md)

