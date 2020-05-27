---
title: 'Tutoriel – Apache Spark pour Azure Synapse Analytics : Définition de travaux Apache Spark pour Synapse'
description: 'Tutoriel : utiliser Azure Synapse Analytics pour créer des définitions de travaux Spark et les envoyer à un pool Apache Spark pour Azure Synapse Analytics.'
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 5fc9dffaa73d195c842381b6682a00e9834c0fe7
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587933"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>Tutoriel : Utiliser Azure Synapse Analytics afin de créer des définitions de travaux Apache Spark pour des pools Synapse Spark

Ce tutoriel montre comment utiliser Azure Synapse Analytics pour créer des définitions de travaux Spark, puis les envoyer à un pool Synapse Spark. Vous pouvez utiliser le plug-in de différentes manières :

* Développez et soumettez une définition de travail Spark sur un pool Synapse Spark.
* Affichez les détails du travail après l’envoi.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> * Développer et soumettre une définition de travail Spark sur un pool Synapse Spark.
> * Afficher les détails du travail après l’envoi.

## <a name="prerequisites"></a>Prérequis

* Un espace de travail Azure Synapse Analytics. Pour obtenir des instructions, consultez [Créer un espace de travail Azure Synapse Analytics](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).

## <a name="get-started"></a>Bien démarrer

Pour soumettre une définition de travail Spark, vous devez être le propriétaire des données Blob du stockage du système de fichiers ADLS Gen2 que vous souhaitez utiliser. Si ce n’est pas le cas, vous devez ajouter l’autorisation manuellement.

### <a name="scenario-1-add-permission"></a>Scénario 1 : Ajouter une autorisation

1. Ouvrez [Microsoft Azure](https://ms.portal.azure.com), puis ouvrez le compte de stockage.

2. Cliquez sur **Conteneurs**, puis créez un **système de fichiers**. Ce didacticiel utilise `sparkjob`.

    ![Cliquez sur le bouton Envoyer pour envoyer la définition de travail Spark](./media/apache-spark-job-definitions/open-azure-container.png)

    ![Boîte de dialogue Spark Submission (Envoi Spark)](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. Ouvrez `sparkjob`, cliquez sur **Contrôle d’accès (IAM)** , puis cliquez sur **Ajouter** et sélectionnez **Ajouter une attribution de rôle**.

    ![Cliquez sur le bouton Envoyer pour envoyer la définition de travail Spark](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![Cliquez sur le bouton Envoyer pour envoyer la définition de travail Spark](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. Cliquez sur **Attributions de rôles**, entrez un nom d’utilisateur, puis vérifiez le rôle d’utilisateur.

    ![Cliquez sur le bouton Envoyer pour envoyer la définition de travail Spark](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>Scénario 2 : Préparer la structure de dossiers

Avant d’envoyer une définition de travail Spark, l’une des tâches que vous devez effectuer consiste à charger des fichiers sur ADLS Gen2 et à y préparer la structure de dossiers. Nous utilisons le nœud Storage dans Synapse Studio pour stocker les fichiers.

1. Ouvrez [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Cliquez sur **Données**, sélectionnez **Comptes de stockage**, puis chargez les fichiers appropriés sur votre système de fichiers ADLS Gen2. Nous prenons en charge Scala, Java, .NET et Python. Ce tutoriel utilise l’exemple de la figure comme démonstration, mais vous pouvez changer la structure de projet comme vous le souhaitez.

    ![Définissez la valeur de la définition de travail Spark](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>Créer une définition de travail Spark

1. Ouvrez [Azure Synapse Analytics](https://web.azuresynapse.net/), puis sélectionnez **Développer**.

2. Sélectionnez **Définitions des travaux Spark** dans le volet gauche.

3. Cliquez sur le nœud **Actions** à droite de « Définitions des travaux Spark ».

     ![Créez une définition de travail Spark](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. Dans la liste déroulante **Actions**, sélectionnez **Nouvelle définition de travail Spark**.

     ![Créez une définition de travail Spark](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. Dans la fenêtre Nouvelle définition de travail Spark, sélectionnez Langage, puis indiquez les informations suivantes :  

   * Définissez **Langage** sur **Spark (Scala)** .

    |  Propriété   | Description   |  
    | ----- | ----- |  
    |Nom de la définition de travail| Entrez un nom pour votre définition de travail Spark.  Ce didacticiel utilise `job definition sample`. Ce nom peut être mis à jour à tout moment jusqu’à ce qu’il soit publié.|  
    |Fichier de définition principal| Fichier principal utilisé pour le travail. Sélectionnez un fichier JAR à partir de votre stockage. Vous pouvez sélectionner **Charger le fichier** pour charger le fichier sur un compte de stockage. |
    |Main class name| Identificateur complet ou classe principale qui se trouve dans le fichier de définition principal.|
    |Arguments de ligne de commande| Arguments facultatifs du travail.|
    |Fichiers de référence| Fichiers supplémentaires utilisés en guise de référence dans le fichier de définition principal. Vous pouvez sélectionner **Charger le fichier** pour charger le fichier sur un compte de stockage.|
    |Pool Spark| Le travail sera envoyé au pool Spark sélectionné.|
    |Version de Spark| Version de Spark exécutée par le pool Spark.|
    |Exécuteurs| Nombre d’exécuteurs à attribuer dans le pool Spark spécifié pour le travail.|
    |Taille de l’exécuteur| Nombre de cœurs et mémoire à utiliser pour les exécuteurs dans le pool Spark spécifié pour le travail.|  
    |Taille du pilote| Nombre de cœurs et mémoire à utiliser pour le pilote dans le pool Spark spécifié pour le travail.|

    ![Définissez la valeur de la définition de travail Spark](./media/apache-spark-job-definitions/create-scala-definition.png)

   * Définissez **Langage** sur **PySpark (Python)** .

    |  Propriété   | Description   |  
    | ----- | ----- |  
    |Nom de la définition de travail| Entrez un nom pour votre définition de travail Spark.  Ce didacticiel utilise `job definition sample`. Ce nom peut être mis à jour à tout moment jusqu’à ce qu’il soit publié.|  
    |Fichier de définition principal| Fichier principal utilisé pour le travail. Sélectionnez un fichier PY à partir de votre stockage. Vous pouvez sélectionner **Charger le fichier** pour charger le fichier sur un compte de stockage.|
    |Arguments de ligne de commande| Arguments facultatifs du travail.|
    |Fichiers de référence| Fichiers supplémentaires utilisés en guise de référence dans le fichier de définition principal. Vous pouvez sélectionner **Charger le fichier** pour charger le fichier sur un compte de stockage.|
    |Pool Spark| Le travail sera envoyé au pool Spark sélectionné.|
    |Version de Spark| Version de Spark exécutée par le pool Spark.|
    |Exécuteurs| Nombre d’exécuteurs à attribuer dans le pool Spark spécifié pour le travail.|
    |Taille de l’exécuteur| Nombre de cœurs et mémoire à utiliser pour les exécuteurs dans le pool Spark spécifié pour le travail.|  
    |Taille du pilote| Nombre de cœurs et mémoire à utiliser pour le pilote dans le pool Spark spécifié pour le travail.|

    ![Définissez la valeur de la définition de travail Spark](./media/apache-spark-job-definitions/create-py-definition.png)

   * Définissez **Langage** sur **.NET Spark(C#/F#)** .

    |  Propriété   | Description   |  
    | ----- | ----- |  
    |Nom de la définition de travail| Entrez un nom pour votre définition de travail Spark.  Ce didacticiel utilise `job definition sample`. Ce nom peut être mis à jour à tout moment jusqu’à ce qu’il soit publié.|  
    |Fichier de définition principal| Fichier principal utilisé pour le travail. Sélectionnez un fichier ZIP contenant votre application .NET pour Spark (c’est-à-dire, le fichier exécutable principal, les DLL contenant les fonctions définies par l’utilisateur et d’autres fichiers requis) à partir de votre stockage. Vous pouvez sélectionner **Charger le fichier** pour charger le fichier sur un compte de stockage.|
    |Fichier exécutable principal| Fichier exécutable principal dans le fichier ZIP de définition principal.|
    |Arguments de ligne de commande| Arguments facultatifs du travail.|
    |Fichiers de référence| Fichiers supplémentaires nécessaires aux nœuds worker pour l’exécution de l’application .NET pour Spark qui n’est pas incluse dans le fichier ZIP de définition principal (autrement dit, les fichiers jar dépendants, les DLL de fonctions définies par l’utilisateur supplémentaires et d’autres fichiers de configuration). Vous pouvez sélectionner **Charger le fichier** pour charger le fichier sur un compte de stockage.|
    |Pool Spark| Le travail sera envoyé au pool Spark sélectionné.|
    |Version de Spark| Version de Spark exécutée par le pool Spark.|
    |Exécuteurs| Nombre d’exécuteurs à attribuer dans le pool Spark spécifié pour le travail.|
    |Taille de l’exécuteur| Nombre de cœurs et mémoire à utiliser pour les exécuteurs dans le pool Spark spécifié pour le travail.|  
    |Taille du pilote| Nombre de cœurs et mémoire à utiliser pour le pilote dans le pool Spark spécifié pour le travail.|

    ![Définissez la valeur de la définition de travail Spark](./media/apache-spark-job-definitions/create-net-definition.png)

6. Sélectionnez **Publier** pour enregistrer la définition de travail Spark.

    ![Publiez la définition de travail Spark](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>Envoyer une définition de travail Spark

Après avoir créé une définition de travail Spark, vous pouvez l’envoyer à un pool Synapse Spark. Avant d’essayer les exemples fournis dans cette partie, assurez-vous d’avoir parcouru les étapes de la section **Bien démarrer**.

### <a name="scenario-1-submit-spark-job-definition"></a>Scénario 1 : Envoyer une définition de travail Spark

1. Ouvrez une fenêtre de définition de travail Spark en cliquant sur la définition concernée.

      ![Ouvrez la définition de travail Spark à envoyer ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. Cliquez sur l’icône **Envoyer** pour envoyer votre projet au pool Spark sélectionné. Vous pouvez cliquer sur l’**URL de supervision Spark** pour voir les informations LogQuery de l’application Spark.

    ![Cliquez sur le bouton Envoyer pour envoyer la définition de travail Spark](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Boîte de dialogue Spark Submission (Envoi Spark)](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>Scénario 2 : Afficher la progression de l’exécution du travail Spark

1. Cliquez sur **Superviser**, puis sélectionnez l’option **Applications Spark**. Vous pouvez trouver l’application Spark envoyée.

    ![Affichez l’application Spark](./media/apache-spark-job-definitions/view-spark-application.png)

2. Cliquez ensuite sur l’application Spark afin d’ouvrir la fenêtre **LogQuery**. Vous pouvez voir la progression de l’exécution du travail dans **LogQuery**.

    ![Affichez les informations LogQuery de l’application Spark](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scénario 3 : Vérifier le fichier de sortie

 1. Cliquez sur **Données**, puis sélectionnez **Comptes de stockage**. Après une exécution réussie, vous pouvez accéder au stockage ADLS Gen2 et vérifier que les sorties sont générées.

    ![Affichez le fichier de sortie](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a montré comment utiliser Azure Synapse Analytics pour créer des définitions de travaux Spark, puis les envoyer à un pool Synapse Spark. À présent, vous pouvez utiliser Azure Synapse Analytics pour créer des jeux de données Power BI et gérer des données Power BI. 

- [Se connecter aux données dans Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [Visualiser des données avec Power BI](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
