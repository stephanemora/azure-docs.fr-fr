---
title: 'Démarrage rapide : Créer des clusters Apache Hadoop à l’aide du gestionnaire des ressources – Azure HDInsight'
description: Dans ce guide de démarrage rapide, vous créez un cluster Apache Hadoop dans Azure HDInsight avec un modèle Resource Manager
keywords: prise en main de hadoop,hadoop sur linux,démarrage rapide de hadoop,prise en main de hive,démarrage rapide de hive
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: 420a5bb4cec6101f3db36212a9b8fd6690e0e077
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207259"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Démarrage rapide : Créer un cluster Apache Hadoop dans Azure HDInsight avec un modèle Resource Manager

Lors de ce démarrage rapide, vous allez apprendre à créer un cluster Apache Hadoop dans Azure HDInsight à l’aide d’un modèle Resource Manager.

Vous pouvez aussi voir des modèles similaires dans les [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular). La référence de modèle est disponible [ici](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/allversions).  Vous pouvez également utiliser le [portail Azure](apache-hadoop-linux-create-cluster-get-started-portal.md) pour cela.  

HDInsight est actuellement fournie avec [sept types de cluster](../hdinsight-overview.md#cluster-types-in-hdinsight). Chaque type de cluster prend en charge un ensemble de composants bien spécifiques. Tous les types de cluster prennent en charge Hive. Pour obtenir la liste des composants pris en charge dans HDInsight, consultez [Nouveautés des versions de cluster Hadoop fournies par HDInsight](../hdinsight-component-versioning.md)  

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

<a name="create-cluster"></a>
## <a name="create-a-hadoop-cluster"></a>Créer un cluster Hadoop

1. Sélectionnez le bouton **Déployer sur Azure** pour vous connecter à Azure et ouvrir le modèle Resource Manager sur le Portail Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Entrez ou sélectionnez les valeurs suivantes :

    |Propriété  |Description  |
    |---------|---------|
    |**Abonnement**     |  Sélectionnez votre abonnement Azure. |
    |**Groupe de ressources**     | Sélectionnez un groupe de ressources existant ou créez-en un.  Un groupe de ressources est un conteneur de composants Azure.  Dans ce cas, le groupe de ressources contient le cluster HDInsight et le compte de stockage Azure dépendant. |
    |**Lieu**     | Sélectionnez l’emplacement Azure où vous souhaitez créer votre cluster.  Choisissez un emplacement proche de vous pour obtenir des performances optimales. |
    |**Nom du cluster**     | Entrez un nom pour le cluster Hadoop. Étant donné que tous les clusters HDInsight partagent le même espace de noms DNS, ce nom doit être unique. Le nom peut contenir uniquement des lettres minuscules, des chiffres et des traits d’union, et doit commencer par une lettre.  Chaque trait d’union doit être précédé et suivi d’un caractère autre qu’un tiret.  Le nom doit avoir entre 3 et 59 caractères. |
    |**Type du cluster**     | Sélectionnez **hadoop**. |
    |**Nom de connexion et mot de passe du cluster**     | Le nom de connexion par défaut est **admin**. Le mot de passe doit comporter au moins 10 caractères et inclure au moins un chiffre, une lettre majuscule, une lettre minuscule et un caractère non alphanumérique (à l’exception de ’ " ` \). Veillez à **ne pas indiquer** des mots de passe courants comme « Pass@word1 ».|
    |**Nom d’utilisateur et mot de passe SSH**     | Le nom d’utilisateur par défaut est **sshuser**.  Vous pouvez renommer le nom d’utilisateur SSH.  Le mot de passe SSH de l’utilisateur a les mêmes exigences que le mot de passe de connexion du cluster.|

    Certaines propriétés ont été codées en dur dans le modèle.  Vous pouvez configurer ces valeurs à partir du modèle. Pour consulter une présentation de ces propriétés, consultez [Créer des clusters Apache Hadoop dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > Les valeurs saisies doivent être uniques et suivre les instructions d’affectation de noms. Le modèle n’effectue pas de contrôles de validation. Si les valeurs saisies sont déjà utilisées ou ne suivent pas les instructions, vous obtiendrez une erreur après avoir envoyé le modèle.  

    ![Bien démarrer avec HDInsight Linux – Modèle Resource Manager sur le portail](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Déployer un cluster Hadoop dans HDInsight à l’aide du Portail Azure et d’un modèle de gestionnaire de groupes de ressources")

3. Sélectionnez **J’accepte les conditions générales mentionnées ci-dessus**, puis sélectionnez **Acheter**. Vous recevez une notification indiquant que votre déploiement est en cours.  La création d’un cluster prend environ 20 minutes.

4. Une fois le cluster créé, vous recevez une notification **Déploiement réussi** avec un lien **Accéder au groupe de ressources**.  Votre page **Groupe de ressources** liste votre nouveau cluster HDInsight et le stockage par défaut associé au cluster. Chaque cluster a une dépendance de [compte de stockage Azure](../hdinsight-hadoop-use-blob-storage.md) ou de [compte Azure Data Lake Storage](../hdinsight-hadoop-use-data-lake-store.md). Elle est désignée comme compte de stockage par défaut. Le cluster HDInsight et son compte de stockage par défaut doivent figurer dans la même région Azure. La suppression de clusters n’a pas pour effet de supprimer le compte de stockage.

> [!NOTE]  
> Pour obtenir d’autres méthodes de création de cluster et comprendre les propriétés utilisées dans ce guide de démarrage rapide, consultez [Créer des clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

Après avoir suivi ce guide de démarrage rapide, vous souhaiterez peut-être supprimer le cluster. Avec HDInsight, vos données sont stockées Azure Storage, pour que vous puissiez supprimer un cluster en toute sécurité s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés.

> [!NOTE]  
> Si vous passez *immédiatement* au tutoriel suivant pour apprendre à exécuter des opérations ETL à l’aide de Hadoop sur HDInsight, vous pouvez garder le cluster en cours d’exécution. En effet, vous devrez à nouveau créer un cluster Hadoop dans le tutoriel. Toutefois, si vous ne passez pas immédiatement au tutoriel suivant, vous devez supprimer le cluster maintenant.

**Pour supprimer le cluster et/ou le compte de stockage par défaut**

1. Revenez à l’onglet du navigateur dans lequel se trouve le portail Azure. Vous devez être sur la page de vue d’ensemble du cluster. Sélectionnez **Supprimer** si vous souhaitez seulement supprimer le cluster, mais conserver le compte de stockage par défaut.

    ![HDInsight suppression du cluster](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Suppression du cluster HDInsight")

2. Si vous souhaitez supprimer le cluster ainsi que le compte de stockage par défaut, sélectionnez le nom du groupe de ressources (encadré dans la capture d’écran précédente) pour ouvrir la page du groupe de ressources.

3. Sélectionnez **Supprimer le groupe de ressources** pour supprimer le groupe de ressources, qui contient le cluster et le compte de stockage par défaut. Notez que la suppression du groupe de ressources aura pour effet de supprimer le compte de stockage. Si vous souhaitez conserver le compte de stockage, choisissez de supprimer uniquement le cluster.

## <a name="next-steps"></a>Étapes suivantes

Lors de ce démarrage rapide, vous avez appris à créer un cluster Apache Hadoop dans HDInsight à l’aide d’un modèle Resource Manager. Dans l’article suivant, vous apprendrez à effectuer une opération d’extraction, de transformation et de chargement (ETL) à l’aide de Hadoop sur HDInsight.

> [!div class="nextstepaction"]
>[Extraire, transformer et charger des données à l’aide d’Interactive Query sur HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)