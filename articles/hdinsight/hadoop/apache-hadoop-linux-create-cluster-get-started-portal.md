---
title: 'Démarrage rapide : Bien démarrer avec Apache Hadoop et Apache Hive à l’aide du Portail Azure - Azure HDInsight'
description: Dans ce guide de démarrage rapide, vous utilisez le portail Azure pour créer un cluster HDInsight Hadoop
keywords: prise en main de hadoop,hadoop sur linux,démarrage rapide de hadoop,prise en main de hive,démarrage rapide de hive
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: e178326c4a165a9bebbef6b0162cc79c1f9a0727
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67459513"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Démarrage rapide : créer un cluster Apache Hadoop dans Azure HDInsight à l’aide du portail Azure

Dans cet article, vous apprenez à créer des clusters [Apache Hadoop](https://hadoop.apache.org/) dans HDInsight à l’aide du portail Azure, puis à exécuter des travaux Apache Hive dans HDInsight. La plupart des tâches Hadoop sont des tâches de traitements par lots. Vous créez un cluster, exécutez certaines tâches, puis supprimez le cluster. Dans cet article, vous allez effectuer les trois tâches.

Dans ce guide de démarrage rapide, vous utilisez le portail Azure pour créer un cluster HDInsight Hadoop. Vous pouvez aussi créer un cluster à l’aide du [modèle Azure Resource Manager](apache-hadoop-linux-tutorial-get-started.md).

HDInsight est actuellement fournie avec [sept types de cluster](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Chaque type de cluster prend en charge un ensemble de composants bien spécifiques. Tous les types de cluster prennent en charge Hive. Pour obtenir la liste des composants pris en charge dans HDInsight, consultez [Quels sont les composants et versions Apache Hadoop disponibles avec HDInsight ?](../hdinsight-component-versioning.md)  

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-an-apache-hadoop-cluster"></a>Créer un cluster Apache Hadoop

Cette section vous permet de créer un cluster Hadoop dans HDInsight à l’aide du portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans le portail Azure, accédez à **Créer une ressource** > **Analytique** > **HDInsight**.

    ![Databricks sur le portail Azure](./media/apache-hadoop-linux-create-cluster-get-started-portal/create-hdinsight.png "Databricks sur le portail Azure")

1. Sous **HDInsight** > **Création rapide** > **Base**, entrez ou sélectionnez les valeurs suivantes :

    |Propriété  |Description  |
    |---------|---------|
    |Nom du cluster   | Entrez un nom pour le cluster Hadoop. Étant donné que tous les clusters HDInsight partagent le même espace de noms DNS, ce nom doit être unique. Le nom peut comporter jusqu’à 59 caractères, dont des lettres, des chiffres et des traits d’union. Le premier caractère et le dernier caractère du nom ne peuvent pas être des traits d’union. |
    |Abonnement    |  Sélectionnez votre abonnement Azure. |
    |Type de cluster     | Ignorez ceci pour le moment. Vous renseignerez cette entrée à l’étape suivante de cette procédure.|
    |Nom d’utilisateur et mot de passe du cluster    | Le nom de connexion par défaut est **admin**. Le mot de passe doit comporter au moins 10 caractères et inclure au moins un chiffre, une lettre majuscule, une lettre minuscule et un caractère non alphanumérique (à l’exception de ’ " ` \). Veillez à **ne pas indiquer** des mots de passe courants comme « Pass@word1 ».|
    |Nom d’utilisateur SSH (Secure Shell) | Le nom d’utilisateur par défaut est **sshuser**.  Vous pouvez fournir un autre nom pour le nom d’utilisateur SSH. |
    |Utiliser le mot de passe de connexion au cluster pour SSH| Cochez cette case pour utiliser le même mot de passe utilisateur SSH que celui fourni pour l’utilisateur de connexion au cluster.|
    |Groupe de ressources     | Sélectionnez un groupe de ressources existant ou créez-en un.  Un groupe de ressources est un conteneur de composants Azure.  Dans ce cas, le groupe de ressources contient le cluster HDInsight et le compte de stockage Azure dépendant. |
    |Location    | Sélectionnez l’emplacement Azure où vous souhaitez créer votre cluster.  Choisissez un emplacement proche de vous pour obtenir des performances optimales. |

    ![Bien démarrer avec HDInsight Linux en renseignant les informations de base du cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-basics.png "Renseigner les informations de base pour la création d’un cluster HDInsight")

1. Sélectionnez **Type de cluster** pour ouvrir la page **Configuration du cluster** et indiquez les valeurs suivantes :

    |Propriété  |Description  |
    |---------|---------|
    |Type de cluster     | Sélectionnez **Hadoop** |
    |Version     | Sélectionnez **Hadoop 2.7.3 (HDI 3.6)**|

    ![Bien démarrer avec HDInsight Linux en renseignant les informations de base du cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-configuration-hadoop.png "Renseigner les informations de base pour la création d’un cluster HDInsight")

    Sélectionnez **Sélectionner**, puis **Suivant** pour accéder aux paramètres de stockage.

1. À partir de l’onglet **Stockage**, indiquez les valeurs suivantes :

    |Propriété  |Description  |
    |---------|---------|
    |Type de stockage principal    | Pour cet article, sélectionnez Stockage Azure pour utiliser Azure Storage Blob en tant que compte de stockage par défaut. Vous pouvez aussi utiliser Azure Data Lake Storage comme stockage par défaut. |
    |Méthode de sélection     |  Pour cet article, sélectionnez **Mes abonnements** pour utiliser un compte de stockage à partir de votre abonnement Azure. Pour utiliser un compte de stockage à partir d’autres abonnements, sélectionnez **Clé d’accès**, puis indiquez la clé d’accès à ce compte. |
    |Sélectionner un compte de stockage   | Sélectionnez **Sélectionner un compte de stockage** pour sélectionner un compte de stockage existant, ou sélectionnez **Créer nouveau**.|

    Acceptez toutes les autres valeurs par défaut, puis sélectionnez **Suivant** pour aller à la page de résumé.

    ![Bien démarrer avec HDInsight Linux en renseignant les informations relatives au stockage du cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-storage.png "Renseigner les informations relatives au stockage pour la création d’un cluster HDInsight")

1. Dans l’onglet **Résumé**, vérifiez les valeurs que vous avez sélectionnées dans les étapes précédentes.

    ![Bien démarrer avec HDInsight Linux résumé du cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-summary.png "Bien démarrer avec HDInsight Linux résumé du cluster")

1. Sélectionnez **Créer**. La création d’un cluster prend environ 20 minutes.

1. Une fois que le cluster est créé, la page de vue d’ensemble du cluster s’affiche dans le portail Azure.

    ![Prise en main de HDInsight sous Linux - Paramètres du cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-overview.png "Propriétés de cluster HDInsight")    

    Chaque cluster possède une dépendance [compte de stockage Azure](../hdinsight-hadoop-use-blob-storage.md) ou [compte Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Elle est désignée comme compte de stockage par défaut. Le cluster HDInsight et son compte de stockage par défaut doivent figurer dans la même région Azure. La suppression de clusters n’a pas pour effet de supprimer le compte de stockage.

    > [!NOTE]  
    > Pour obtenir d’autres méthodes de création de cluster et comprendre les propriétés utilisées dans ce guide de démarrage rapide, consultez [Créer des clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="run-apache-hive-queries"></a>Exécuter des requêtes Apache Hive

[Apache Hive](hdinsight-use-hive.md) est le composant le plus populaire utilisé dans HDInsight. Il existe de nombreuses façons d’exécuter des tâches Hive dans HDInsight. Dans ce démarrage rapide, vous allez utiliser l’affichage Ambari Hive à partir du portail. Pour d’autres méthodes d’envoi de tâches Hive, consultez la page [Utilisation de Hive et HiveQL avec Hadoop dans HDInsight pour l’analyse d’un exemple de fichier Apache log4j](hdinsight-use-hive.md).

1. Pour ouvrir Ambari, sélectionnez **Tableau de bord du cluster** à partir de la capture d’écran précédente.  Vous pouvez également accéder à `https://ClusterName.azurehdinsight.net`, où `ClusterName` est le cluster que vous avez créé dans la section précédente.

    ![Bien démarrer avec HDInsight Linux – Tableau de bord du cluster](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-open-cluster-dashboard.png "Bien démarrer avec HDInsight Linux – Tableau de bord du cluster")

2. Entrez le nom d’utilisateur Hadoop et le mot de passe que vous avez spécifiés lors de la création du cluster. Le nom d’utilisateur par défaut est **admin**.

3. Ouvrez l’**affichage Hive** comme illustré dans la capture d’écran suivante :

    ![Sélection des vues Ambari](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "Menu Affichage Hive dans HDInsight")

4. Dans l’onglet **REQUÊTE**, collez les instructions HiveQL suivantes dans la feuille de calcul :

    ```sql
    SHOW TABLES;
    ```

    ![Affichages Hive dans HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "Éditeur de requête de l’affichage Hive dans HDInsight")

5. Sélectionnez **Exécuter**. Un onglet **RÉSULTATS** apparaît sous l’onglet **REQUÊTE** et affiche des informations sur le travail. 

    Une fois la requête terminée, l’onglet **REQUÊTE** affiche les résultats de l’opération. Vous devriez voir une table appelée **hivesampletable**. Cet exemple de table Hive est fourni avec les clusters HDInsight.

    ![Affichages Hive dans HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "Éditeur de requête de l’affichage Hive dans HDInsight")

6. Répétez les étapes 4 et 5 pour exécuter la requête suivante :

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. Vous pouvez également enregistrer les résultats de la requête. Sélectionnez le bouton de menu à droite et spécifiez si vous souhaitez télécharger les résultats sous la forme d’un fichier CSV ou les stocker dans le compte de stockage associé au cluster.

    ![Enregistrer le résultat de la requête Hive](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Enregistrer le résultat de la requête Hive")

Une fois que vous avez terminé une tâche Hive, vous pouvez [exporter les résultats dans une base de données Azure SQL ou SQL Server](apache-hadoop-use-sqoop-mac-linux.md). Vous pouvez également [visualiser les résultats à l’aide d’Excel](apache-hadoop-connect-excel-power-query.md). Pour plus d’informations sur l’utilisation de Hive dans HDInsight, consultez [Utilisation d’Apache Hive et HiveQL avec Apache Hadoop dans HDInsight pour l’analyse d’un exemple de fichier Apache log4j](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

Après avoir suivi ce guide de démarrage rapide, vous souhaiterez peut-être supprimer le cluster. Avec HDInsight, vos données sont stockées Azure Storage, pour que vous puissiez supprimer un cluster en toute sécurité s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés.

> [!NOTE]  
> Si vous passez *immédiatement* à l’article suivant pour apprendre à exécuter des opérations ETL à l’aide de Hadoop sur HDInsight, vous pouvez garder le cluster en cours d’exécution. En effet, vous devrez à nouveau créer un cluster Hadoop dans le tutoriel. Toutefois, si vous ne passez pas immédiatement à l’article suivant, vous devez supprimer le cluster maintenant.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Pour supprimer le cluster et/ou le compte de stockage par défaut

1. Revenez à l’onglet du navigateur dans lequel se trouve le portail Azure. Vous devez être sur la page de vue d’ensemble du cluster. Sélectionnez **Supprimer** si vous souhaitez seulement supprimer le cluster, mais conserver le compte de stockage par défaut.

    ![HDInsight suppression du cluster](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Suppression du cluster HDInsight")

2. Si vous souhaitez supprimer le cluster ainsi que le compte de stockage par défaut, sélectionnez le nom du groupe de ressources (encadré dans la capture d’écran précédente) pour ouvrir la page du groupe de ressources.

3. Sélectionnez **Supprimer le groupe de ressources** pour supprimer le groupe de ressources, qui contient le cluster et le compte de stockage par défaut. Notez que la suppression du groupe de ressources aura pour effet de supprimer le compte de stockage. Si vous souhaitez conserver le compte de stockage, choisissez de supprimer uniquement le cluster.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un cluster HDInsight Linux à l’aide d’un modèle Resource Manager et à effectuer des requêtes Hive de base. Dans l’article suivant, vous apprendrez à effectuer une opération d’extraction, de transformation et de chargement (ETL) à l’aide de Hadoop sur HDInsight.

> [!div class="nextstepaction"]
>[Extraire, transformer et charger des données à l’aide d’Interactive Query sur HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
