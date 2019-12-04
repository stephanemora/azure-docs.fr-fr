---
title: Utiliser Grafana sur Azure HDInsight
description: Découvrez comment accéder au tableau de bord Grafana avec des clusters Apache Hadoop dans Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: dae9c47f535d87214c9e1583562b4c0419cd44cf
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74305439"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Accéder à Grafana dans Azure HDInsight

[Grafana](https://grafana.com/) est un générateur open source de tableaux de bord et de graphes qui est couramment utilisé. Il est doté de nombreuses fonctionnalités : les utilisateurs peuvent créer des tableaux de bord personnalisables et pouvant être partagés, mais aussi se servir de tableaux de bord basés sur un script/modèle, de l’intégration LDAP, de multiples sources de données, etc.

Actuellement, dans Azure HDInsight, Grafana est pris en charge avec les types de cluster Hbase, Kafka et Interactive Query. Il n’est pas pris en charge pour les clusters avec le Pack Sécurité Entreprise activé.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-an-apache-hadoop-cluster"></a>Créer un cluster Apache Hadoop

Dans cette section, vous allez créer un cluster Interactive Query dans HDInsight à l’aide d’un modèle Azure Resource Manager. Aucune expérience avec le modèle Resource Manager n’est requise dans le cadre de cet article. 

1. Cliquez sur le bouton **Déployer sur Azure** pour vous connecter à Azure et ouvrir le modèle Resource Manager sur le Portail Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-grafana/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Entrez ou sélectionnez les valeurs indiquées dans la capture d’écran suivante :

    > [!NOTE]  
    > Les valeurs saisies doivent être uniques et suivre les instructions d’affectation de noms. Le modèle n’effectue pas de contrôles de validation. Si les valeurs saisies sont déjà utilisées ou ne suivent pas les instructions, vous obtiendrez une erreur après avoir envoyé le modèle.       
    > 
    >
    
    ![Prise en main de HDInsight sous Linux – Utilisation du modèle Resource Manager sur le portail](./media/hdinsight-grafana/hdinsight-linux-get-started-arm-template-on-portal.png "Déployer un cluster Hadoop dans HDInsight à l’aide du Portail Azure et d’un modèle de gestionnaire de groupes de ressources")

    Entrez ou sélectionnez les valeurs suivantes :
    
    |Propriété  |Description  |
    |---------|---------|
    |**Abonnement**     |  Sélectionnez votre abonnement Azure. |
    |**Groupe de ressources**     | Sélectionnez un groupe de ressources existant ou créez-en un.  Un groupe de ressources est un conteneur de composants Azure.  Dans ce cas, le groupe de ressources contient le cluster HDInsight et le compte de stockage Azure dépendant. |
    |**Lieu**     | Sélectionnez l’emplacement Azure où vous souhaitez créer votre cluster.  Choisissez un emplacement proche de vous pour obtenir des performances optimales. |
    |**Type du cluster**     | Sélectionnez **hadoop**. |
    |**Nom du cluster**     | Entrez un nom pour le cluster Apache Hadoop. Étant donné que tous les clusters HDInsight partagent le même espace de noms DNS, ce nom doit être unique. Le nom peut comporter jusqu’à 59 caractères, dont des lettres, des chiffres et des traits d’union. Le premier caractère et le dernier caractère du nom ne peuvent pas être des traits d’union. |
    |**Nom de connexion et mot de passe du cluster**     | Le nom de connexion par défaut est **admin**. Le mot de passe doit comporter au moins 10 caractères et inclure au moins un chiffre, une lettre majuscule, une lettre minuscule et un caractère non alphanumérique (à l’exception de ’ " ` \). Veillez à **ne pas indiquer** des mots de passe courants comme « Pass@word1 ».|
    |**Nom d’utilisateur et mot de passe SSH**     | Le nom d’utilisateur par défaut est **sshuser**.  Vous pouvez renommer le nom d’utilisateur SSH.  Le mot de passe SSH de l’utilisateur a les mêmes exigences que le mot de passe de connexion du cluster.|

    Certaines propriétés ont été codées en dur dans le modèle.  Vous pouvez configurer ces valeurs à partir du modèle. Pour consulter une présentation de ces propriétés, consultez [Créer des clusters Apache Hadoop dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Sélectionnez **J’accepte les conditions générales mentionnées ci-dessus** et **Épingler au tableau de bord**, puis **Acheter**. Une nouvelle vignette intitulée **Soumission du déploiement en cours** apparaît sur le tableau de bord du Portail. La création d’un cluster prend environ 20 minutes.

    ![Progression d’Azure Template deployment](./media/hdinsight-grafana/deployment-progress-tile.png "Progression d’Azure Template deployment")

4. Une fois le cluster créé, la vignette change de légende pour afficher le nom du groupe de ressources que vous avez spécifié. La vignette indique également le cluster HDInsight créé au sein du groupe de ressources.

    ![Prise en main de HDInsight sous Linux – Groupe de ressources](./media/hdinsight-grafana/hdinsight-linux-get-started-resource-group.png "Groupe de ressources de cluster Azure HDInsight")

5. La vignette indique également le stockage par défaut associé au cluster. Chaque cluster possède une dépendance [compte de stockage Azure](../hdinsight-hadoop-use-blob-storage.md) ou [compte Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Elle est désignée comme compte de stockage par défaut. Le cluster HDInsight et son compte de stockage par défaut doivent figurer dans la même région Azure. La suppression de clusters n’a pas pour effet de supprimer le compte de stockage.
    

> [!NOTE]  
> Pour obtenir d’autres méthodes de création de cluster et comprendre les propriétés utilisées dans cet article, consultez [Création de clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="access-the-grafana-dashboard"></a>Accéder au tableau de bord Grafana

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Sélectionnez **Clusters HDInsight**, puis le nom du cluster que vous avez créé dans la section précédente.

3. Sous **Liens rapides**, cliquez sur **Tableau de bord du cluster**.

    ![Tableau de bord de cluster HDInsight sur le portail](./media/hdinsight-grafana/hdinsight-portal-cluster-dashboard.png "Tableau de bord de cluster HDInsight sur le portail")

4. Dans le tableau de bord, cliquez sur la vignette **Grafana**. Vous pouvez également accéder au chemin d’accès `/grafana/` de l’URL de votre cluster. Par exemple : `https://<clustername>.azurehdinsight.net/grafana/`.

5. Entrez les informations d’identification utilisateur du cluster Hadoop.

6. Le tableau de bord Grafana s’affiche et se présente comme dans cet exemple :

    ![Tableau de bord web Grafana de HDInsight](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Tableau de bord Grafana de HDInsight")

## <a name="clean-up-resources"></a>Supprimer des ressources
Après avoir terminé ce tutoriel, vous souhaiterez peut-être supprimer le cluster. Avec HDInsight, vos données sont stockées Azure Storage, pour que vous puissiez supprimer un cluster en toute sécurité s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés. 

> [!NOTE]  
> Si vous passez *immédiatement* au tutoriel suivant pour apprendre à exécuter des opérations ETL à l’aide de Hadoop sur HDInsight, vous pouvez garder le cluster en cours d’exécution. En effet, vous devrez à nouveau créer un cluster Hadoop dans le tutoriel. Toutefois, si vous ne passez pas immédiatement au tutoriel suivant, vous devez supprimer le cluster maintenant.

**Pour supprimer le cluster et/ou le compte de stockage par défaut**

1. Revenez à l’onglet du navigateur dans lequel se trouve le portail Azure. Vous devez être sur la page de vue d’ensemble du cluster. Sélectionnez **Supprimer** si vous souhaitez seulement supprimer le cluster, mais conserver le compte de stockage par défaut.

    ![Icône de suppression du cluster dans le Portail Azure](./media/hdinsight-grafana/hdinsight-delete-cluster.png "Supprimer un cluster HDInsight")

2. Si vous souhaitez supprimer le cluster ainsi que le compte de stockage par défaut, sélectionnez le nom du groupe de ressources (encadré dans la capture d’écran précédente) pour ouvrir la page du groupe de ressources.

3. Sélectionnez **Supprimer le groupe de ressources** pour supprimer le groupe de ressources, qui contient le cluster et le compte de stockage par défaut. Notez que la suppression du groupe de ressources aura pour effet de supprimer le compte de stockage. Si vous souhaitez conserver le compte de stockage, choisissez de supprimer uniquement le cluster.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à créer un cluster HDInsight Linux à l’aide d’un modèle Resource Manager et à effectuer des requêtes Apache Hive de base. Dans l’article suivant, vous apprendrez à effectuer une opération d’extraction, de transformation et de chargement (ETL) à l’aide de Hadoop sur HDInsight.

> [!div class="nextstepaction"]
>[Extraire, transformer et charger des données à l’aide d’Interactive Query sur HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)

Si vous êtes prêt à utiliser vos propres données et que vous avez besoin d’en savoir plus sur la façon dont HDInsight stocke les données ou sur l’ajout de données dans HDInsight, consultez les articles suivants :

* Pour plus d’informations sur la façon dont HDInsight utilise Stockage Azure, consultez la page [Utilisation de Stockage Azure avec HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Pour plus d’informations sur le téléchargement de données dans HDInsight, consultez la page [Téléchargement de données dans HDInsight](../hdinsight-upload-data.md).

Pour en savoir plus sur l’analyse des données avec HDInsight, consultez les articles suivants :

* Pour en savoir plus sur l’utilisation de Hive avec HDInsight, notamment sur l’exécution des requêtes Hive à partir de Visual Studio, consultez [Utiliser Apache Hive avec HDInsight](../hdinsight-use-hive.md).
* Pour plus d’informations sur Pig, un langage utilisé pour transformer les données, consultez [Utiliser Apache Pig avec HDInsight](../hdinsight-use-pig.md).
* Pour en savoir plus sur MapReduce, un moyen d’écrire des programmes pour traiter les données sur Hadoop, consultez la page [Utilisation de MapReduce avec HDInsight](../hdinsight-use-mapreduce.md).
* Pour en savoir plus sur l’utilisation des outils HDInsight pour Visual Studio pour analyser les données sur HDInsight, consultez la page [Prise en main des outils Hadoop de Visual Studio pour HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).



Si vous voulez en savoir plus sur la création ou la gestion d’un cluster HDInsight, consultez les articles suivants :

* Pour en savoir plus sur la gestion de votre cluster HDInsight Linux, consultez la page [Gestion des clusters HDInsight à l’aide d’Ambari](../hdinsight-hadoop-manage-ambari.md).
* Pour en savoir plus sur les options que vous pouvez sélectionner pendant la création d’un cluster HDInsight, consultez la page [Création de clusters Hadoop basés sur Linux dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
