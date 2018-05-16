---
title: 'Guide de démarrage rapide : Bien démarrer avec Hadoop et Hive dans Azure HDInsight à l’aide du modèle Resource Manager | Microsoft Docs'
description: Découvrez comment créer des clusters HDInsight et interroger des données avec Hive.
keywords: prise en main de hadoop,hadoop sur linux,démarrage rapide de hadoop,prise en main de hive,démarrage rapide de hive
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
ms.assetid: 6a12ed4c-9d49-4990-abf5-0a79fdfca459
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc
ms.devlang: na
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: 4cf20dacf66ee334dcd455ce1770609c175d3b88
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-get-started-with-hadoop-and-hive-in-azure-hdinsight-using-resource-manager-template"></a>Guide de démarrage rapide : Bien démarrer avec Hadoop et Hive dans Azure HDInsight à l’aide du modèle Resource Manager

Dans cet article, vous allez apprendre à créer des clusters [Hadoop](http://hadoop.apache.org/) dans HDInsight à l’aide d’un modèle Resource Manager, puis à exécuter des travaux Hive dans HDInsight. La plupart des tâches Hadoop sont des tâches de traitements par lots. Vous créez un cluster, exécutez certaines tâches, puis supprimez le cluster. Dans cet article, vous allez effectuer les trois tâches.

Dans ce guide de démarrage rapide, vous allez utiliser un modèle Resource Manager pour créer un cluster HDInsight Hadoop. Vous pouvez également utiliser le [Portail Azure](apache-hadoop-linux-create-cluster-get-started-portal.md) pour cela.

HDInsight est actuellement fournie avec [sept types de cluster](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Chaque type de cluster prend en charge un ensemble de composants bien spécifiques. Tous les types de cluster prennent en charge Hive. Pour obtenir la liste des composants pris en charge dans HDInsight, consultez [Nouveautés des versions de cluster Hadoop fournies par HDInsight](../hdinsight-component-versioning.md)  

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

<a name="create-cluster"></a>
## <a name="create-a-hadoop-cluster"></a>Créer un cluster Hadoop

Dans cette section, vous allez créer un cluster Hadoop dans HDInsight à l’aide d’un modèle Azure Resource Manager. Aucune expérience avec le modèle Resource Manager n’est requise dans le cadre de cet article. 

1. Cliquez sur le bouton **Déployer sur Azure** pour vous connecter à Azure et ouvrir le modèle Resource Manager sur le Portail Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Entrez ou sélectionnez les valeurs indiquées dans la capture d’écran suivante :

    > [!NOTE]
    > Les valeurs saisies doivent être uniques et suivre les instructions d’affectation de noms. Le modèle n’effectue pas de contrôles de validation. Si les valeurs saisies sont déjà utilisées ou ne suivent pas les instructions, vous obtiendrez une erreur après avoir envoyé le modèle.       
    > 
    >
    
    ![Bien démarrer avec HDInsight Linux – Modèle Resource Manager sur le Portail](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Déployer un cluster Hadoop dans HDInsight à l’aide du Portail Azure et d’un modèle de gestionnaire de groupes de ressources")

    Entrez ou sélectionnez les valeurs suivantes :
    
    |Propriété  |Description  |
    |---------|---------|
    |**Abonnement**     |  Sélectionnez votre abonnement Azure. |
    |**Groupe de ressources**     | Sélectionnez un groupe de ressources existant ou créez-en un.  Un groupe de ressources est un conteneur de composants Azure.  Dans ce cas, le groupe de ressources contient le cluster HDInsight et le compte de stockage Azure dépendant. |
    |**Lieu**     | Sélectionnez l’emplacement Azure où vous souhaitez créer votre cluster.  Choisissez un emplacement proche de vous pour obtenir des performances optimales. |
    |**Type du cluster**     | Sélectionnez **hadoop**. |
    |**Nom du cluster**     | Donnez un nom au cluster Hadoop. Étant donné que tous les clusters HDInsight partagent le même espace de noms DNS, ce nom doit être unique. Le nom peut comporter jusqu’à 59 caractères, dont des lettres, des chiffres et des traits d’union. Le premier et le dernier caractères du nom ne peuvent pas être des traits d’union. |
    |**Nom de connexion et mot de passe du cluster**     | Le nom de connexion par défaut est **admin**. Le mot de passe doit comporter au moins 10 caractères et inclure au moins un chiffre, une lettre majuscule, une lettre minuscule et un caractère non alphanumérique (à l’exception de ’ " ` \). Veillez à **ne pas indiquer** des mots de passe courants comme « Pass@word1 ».|
    |**Nom d’utilisateur et mot de passe SSH**     | Le nom d’utilisateur par défaut est **sshuser**.  Vous pouvez renommer le nom d’utilisateur SSH.  Le mot de passe SSH de l’utilisateur a les mêmes exigences que le mot de passe de connexion du cluster.|
       
    Certaines propriétés ont été codées en dur dans le modèle.  Vous pouvez configurer ces valeurs à partir du modèle. Pour consulter une présentation de ces propriétés, voir [Création de clusters Hadoop basés sur Linux dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Sélectionnez **J’accepte les conditions générales mentionnées ci-dessus** et **Épingler au tableau de bord**, puis **Acheter**. Une nouvelle vignette intitulée **Soumission du déploiement en cours** apparaît sur le tableau de bord du Portail. La création d’un cluster prend environ 20 minutes.

    ![Déploiement du modèle en cours](./media/apache-hadoop-linux-tutorial-get-started/deployment-progress-tile.png "Déploiement du modèle Azure en cours")

4. Une fois le cluster créé, la vignette change de légende pour afficher le nom du groupe de ressources que vous avez spécifié. La vignette indique également le cluster HDInsight créé au sein du groupe de ressources. 
   
    ![Prise en main de HDInsight sous Linux - Groupe de ressources](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-resource-group.png "Groupe de ressources de cluster Azure HDInsight")
    
5. La vignette indique également le stockage par défaut associé au cluster. Chaque cluster possède une dépendance [compte de stockage Azure](../hdinsight-hadoop-use-blob-storage.md) ou [compte Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Elle est désignée comme compte de stockage par défaut. Le cluster HDInsight et son compte de stockage par défaut doivent figurer dans la même région Azure. La suppression de clusters n’a pas pour effet de supprimer le compte de stockage.
    

> [!NOTE]
> Pour obtenir d’autres méthodes de création de cluster et comprendre les propriétés utilisées dans ce didacticiel, consultez [Création de clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).       
> 
>

## <a name="run-hive-queries"></a>Exécuter des requêtes Hive

[Apache Hive](hdinsight-use-hive.md) est le composant le plus populaire utilisé dans HDInsight. Il existe de nombreuses façons d’exécuter des tâches Hive dans HDInsight. Dans ce didacticiel, vous allez utiliser l’affichage Ambari Hive à partir du portail. Pour d’autres méthodes d’envoi de tâches Hive, consultez la page [Utilisation de Hive et HiveQL avec Hadoop dans HDInsight pour l’analyse d’un exemple de fichier Apache log4j](hdinsight-use-hive.md).

1. Pour ouvrir Ambari, sélectionnez **Tableau de bord du cluster** à partir de la capture d’écran précédente.  Vous pouvez également accéder à **https://&lt;ClusterName>.azurehdinsight.net**, où &lt;ClusterName> désigne le cluster que vous avez créé dans la section précédente.

    ![Bien démarrer avec HDInsight Linux – Tableau de bord du cluster](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-open-cluster-dashboard.png "Bien démarrer avec HDInsight Linux – Tableau de bord du cluster")

2. Entrez le nom d’utilisateur Hadoop et le mot de passe que vous avez spécifiés lors de la création du cluster. Le nom d’utilisateur par défaut est **admin**.

3. Ouvrez l’ **affichage Hive** comme illustré dans la capture d’écran suivante.
   
    ![Sélection des vues Ambari](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "Menu Affichage Hive dans HDInsight")

4. Dans l’onglet **REQUÊTE**, collez les instructions HiveQL suivantes dans la feuille de calcul :
   
        SHOW TABLES;

    ![Affichages Hive dans HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "Éditeur de requête de l’affichage Hive dans HDInsight")
   
   > [!NOTE]
   > Hive requiert l’utilisation d’un point-virgule.       
   > 
   > 

5. Sélectionnez **Exécuter**. Un onglet **RÉSULTATS** apparaît sous l’onglet **REQUÊTE** et affiche des informations sur le travail. 
   
    Une fois la requête terminée, l’onglet **REQUÊTE** affiche les résultats de l’opération. Vous devriez voir une table appelée **hivesampletable**. Cet exemple de table Hive est fourni avec les clusters HDInsight.
   
    ![Affichages Hive dans HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "Éditeur de requête de l’affichage Hive dans HDInsight")

6. Répétez les étapes 4 et 5 pour exécuter la requête suivante :
   
        SELECT * FROM hivesampletable;
   
7. Vous pouvez également enregistrer les résultats de la requête. Sélectionnez le bouton de menu à droite et spécifiez si vous souhaitez télécharger les résultats sous la forme d’un fichier CSV ou les stocker dans le compte de stockage associé au cluster.

    ![Enregistrer le résultat de la requête Hive](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Enregistrer le résultat de la requête Hive")

Une fois que vous avez terminé une tâche Hive, vous pouvez [exporter les résultats dans une base de données SQL Azure ou SQL Server](apache-hadoop-use-sqoop-mac-linux.md). Vous pouvez également [visualiser les résultats à l’aide d’Excel](apache-hadoop-connect-excel-power-query.md). Pour plus d’informations sur l’utilisation de Hive dans HDInsight, consultez [Utilisation de Hive et HiveQL avec Hadoop dans HDInsight pour l’analyse d’un exemple de fichier Apache log4j](hdinsight-use-hive.md).

## <a name="troubleshoot"></a>Résolution des problèmes

Si vous rencontrez des problèmes lors de la création de clusters HDInsight, reportez-vous aux [exigences de contrôle d’accès](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="clean-up-resources"></a>Supprimer des ressources
Après avoir terminé ce tutoriel, vous souhaiterez peut-être supprimer le cluster. Avec HDInsight, vos données sont stockées Azure Storage, pour que vous puissiez supprimer un cluster en toute sécurité s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés. 

> [!NOTE]
> Si vous passez *immédiatement* au tutoriel suivant pour apprendre à exécuter des opérations ETL à l’aide de Hadoop sur HDInsight, vous pouvez garder le cluster en cours d’exécution. En effet, vous devrez à nouveau créer un cluster Hadoop dans le tutoriel. Toutefois, si vous ne passez pas immédiatement au tutoriel suivant, vous devez supprimer le cluster maintenant.
> 
> 

**Pour supprimer le cluster et/ou le compte de stockage par défaut**

1. Revenez à l’onglet du navigateur dans lequel se trouve le portail Azure. Vous devez être sur la page de vue d’ensemble du cluster. Sélectionnez **Supprimer** si vous souhaitez seulement supprimer le cluster, mais conserver le compte de stockage par défaut.

    ![HDInsight suppression du cluster](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Suppression du cluster HDInsight")

2. Si vous souhaitez supprimer le cluster ainsi que le compte de stockage par défaut, sélectionnez le nom du groupe de ressources (encadré dans la capture d’écran précédente) pour ouvrir la page du groupe de ressources.

3. Sélectionnez **Supprimer le groupe de ressources** pour supprimer le groupe de ressources, qui contient le cluster et le compte de stockage par défaut. Notez que la suppression du groupe de ressources aura pour effet de supprimer le compte de stockage. Si vous souhaitez conserver le compte de stockage, choisissez de supprimer uniquement le cluster.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à créer un cluster HDInsight Linux à l’aide d’un modèle Resource Manager et à effectuer des requêtes Hive de base. Dans l’article suivant, vous apprendrez à effectuer une opération d’extraction, de transformation et de chargement (ETL) à l’aide de Hadoop sur HDInsight.

> [!div class="nextstepaction"]
>[Extraire, transformer et charger des données à l’aide d’Apache Hive sur HDInsight](../hdinsight-analyze-flight-delay-data-linux.md)

Si vous êtes prêt à utiliser vos propres données et que vous avez besoin d’en savoir plus sur la façon dont HDInsight stocke les données ou sur l’ajout de données dans HDInsight, consultez les articles suivants :

* Pour plus d’informations sur la façon dont HDInsight utilise le stockage Azure, consultez la page [Use Azure Storage with HDInsight](../hdinsight-hadoop-use-blob-storage.md) (Utilisation du stockage Azure avec HDInsight).
* Pour plus d’informations sur le téléchargement de données dans HDInsight, consultez la page [Téléchargement de données dans HDInsight](../hdinsight-upload-data.md).

Pour en savoir plus sur l’analyse des données avec HDInsight, consultez les articles suivants :

* Pour en savoir plus sur l’utilisation de Hive avec HDInsight, y compris comment exécuter des requêtes Hive à partir de Visual Studio, consultez la page [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md).
* Pour en savoir plus sur Pig, un langage utilisé pour transformer les données, consultez la page [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md).
* Pour en savoir plus sur MapReduce, un moyen d’écrire des programmes pour traiter les données sur Hadoop, consultez la page [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md).
* Pour en savoir plus sur l’utilisation des outils HDInsight pour Visual Studio pour analyser les données sur HDInsight, consultez la page [Prise en main des outils Hadoop de Visual Studio pour HDInsight](apache-hadoop-visual-studio-tools-get-started.md).



Si vous voulez en savoir plus sur la création ou la gestion d’un cluster HDInsight, consultez les articles suivants :

* Pour en savoir plus sur la gestion de votre cluster HDInsight Linux, consultez la page [Gestion des clusters HDInsight à l’aide d’Ambari](../hdinsight-hadoop-manage-ambari.md).
* Pour en savoir plus sur les options que vous pouvez sélectionner pendant la création d’un cluster HDInsight, consultez la page [Création de clusters Hadoop basés sur Linux dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


