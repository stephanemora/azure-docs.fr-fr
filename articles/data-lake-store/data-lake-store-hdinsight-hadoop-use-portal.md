---
title: Utiliser le portail Azure pour créer des clusters Azure HDInsight avec Azure Data Lake Storage Gen1 | Microsoft Docs
description: Utiliser le portail Azure pour créer et utiliser les clusters HDInsight avec Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 28bed3578c653c8081868b3d950ab6332879784a
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124320"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Créer des clusters HDInsight avec Azure Data Lake Storage Gen1 à l’aide du portail Azure
> [!div class="op_single_selector"]
> * [Utiliser le portail Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Utiliser PowerShell (pour le stockage par défaut)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utiliser PowerShell (pour le stockage supplémentaire)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Utiliser Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Découvrez comment utiliser le portail Azure pour créer un cluster HDInsight avec un compte Azure Data Lake Storage Gen1 en tant que stockage par défaut ou stockage supplémentaire. Bien que le stockage supplémentaire soit facultatif pour les clusters HDInsight, il est recommandé de stocker vos données d’entreprise dans les comptes de stockage supplémentaires.

## <a name="prerequisites"></a>Prérequis
Avant de commencer le didacticiel, veillez à ce que vos mots de passe répondent aux exigences suivantes :

* **Un abonnement Azure**. Consultez [Créez votre compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/).
* **Un compte Data Lake Storage Gen1**. Suivez les instructions indiquées dans [Prise en main d’Azure Data Lake Storage Gen1 avec le portail Azure](data-lake-store-get-started-portal.md). Vous devez également créer un dossier racine sur le compte.  Dans ce didacticiel, un dossier racine appelé __/clusters__ est utilisé.
* **Un principal de service Azure Active Directory**. Ce didacticiel montre comment créer un principal de service dans Azure Active Directory (Azure AD). Toutefois, vous devez être administrateur Azure AD pour pouvoir créer un principal du service. Si vous êtes administrateur, vous pouvez ignorer ce prérequis et poursuivre le didacticiel.

    >[!NOTE]
    >Vous pouvez créer un principal de service uniquement si vous être administrateur Azure AD. Votre administrateur Azure AD doit créer un principal de service. Vous pouvez ensuite créer un cluster HDInsight avec Data Lake Storage Gen1. En outre, le principal de service doit être créé à l’aide d’un certificat, comme décrit dans [Créer un principal du service avec un certificat](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).
    >

## <a name="create-an-hdinsight-cluster"></a>Création d'un cluster HDInsight

Dans cette section, vous allez créer un cluster HDInsight avec des comptes Data Lake Storage Gen1 comme stockage par défaut ou stockage supplémentaire. Cet article traite uniquement de la configuration des comptes Data Lake Storage Gen1.  Pour obtenir des informations générales sur la création de clusters et les procédures associées, consultez la page [Création de clusters Hadoop dans HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Créer un cluster avec Data Lake Storage Gen1 en tant que stockage par défaut

**Pour créer un cluster HDInsight avec un compte Data Lake Storage Gen1 comme compte de stockage par défaut**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Suivez les indications présentées dans [Créer des clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) pour obtenir des informations générales sur la création de clusters HDInsight.
3. Sur le panneau **Stockage**, sous **Type de stockage principal**, sélectionnez **Azure Data Lake Storage Gen1**, puis entrez les informations suivantes :

    ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Ajouter un principal du service à un cluster HDInsight")

    - **Sélectionner un compte Data Lake Storage** : sélectionnez un compte Data Lake Storage Gen1 existant. Un compte Data Lake Storage Gen1 existant est requis.  Consultez les [Conditions préalables](#prerequisites).
    - **Chemin racine** : entrez un chemin d’accès où les fichiers spécifiques d’un cluster doivent être stockés. Sur la capture d’écran, il s’agit de __/clusters/myhdiadlcluster/__, qui doit comporter le dossier __/clusters__. Le portail crée alors un dossier *myhdicluster*.  *myhdicluster* désigne le nom du cluster.
    - **Accès à Data Lake Storage** : configurez l’accès entre le compte Data Lake Storage Gen1 et le cluster HDInsight. Pour obtenir des instructions, consultez [Configurer l’accès aux données Data Lake Storage Gen1](#configure-data-lake-store-access).
    - **Comptes de stockage supplémentaire** : ajoutez des comptes de stockage Azure en tant que comptes de stockage supplémentaire pour le cluster. Pour ajouter d’autres comptes Data Lake Storage Gen1, attribuez des autorisations de cluster sur les données de plusieurs comptes Data Lake Storage Gen1 lors de la configuration d’un compte Data Lake Storage Gen1 comme type de stockage principal. Consultez [Configurer l’accès aux données Data Lake Storage Gen1](#configure-data-lake-store-access).

4. Cliquez sur **Sélectionner** dans les panneaux **Accès à Data Lake Store** et poursuivez la création du cluster comme décrit dans [Création de clusters Hadoop dans HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).


### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Créer un cluster avec Data Lake Storage Gen1 en tant que stockage supplémentaire

Procédez comme suit pour créer un cluster HDInsight avec un compte de stockage Azure en tant que stockage par défaut, et un compte Data Lake Storage Gen1 en tant que stockage supplémentaire.

**Pour créer un cluster HDInsight avec un compte Data Lake Storage Gen1 comme compte de stockage supplémentaire**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Suivez les indications présentées dans [Créer des clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) pour obtenir des informations générales sur la création de clusters HDInsight.
3. Sur le panneau **Stockage**, sous **Type de stockage principal**, sélectionnez **Stockage Azure**, puis entrez les informations suivantes :

    ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Ajouter un principal du service à un cluster HDInsight")

    - **Méthode de sélection** : utilisez l’une des options suivantes :

        * Pour spécifier un compte de stockage qui fait partie de votre abonnement Azure, sélectionnez **Mes abonnements**, puis le compte de stockage.
        * Pour spécifier un compte de stockage qui est en dehors de votre abonnement Azure, sélectionnez **Clé d’accès**, puis fournissez les informations du compte de stockage externe.

    - **Conteneur par défaut** : utilisez la valeur par défaut ou spécifiez votre propre nom.

    - Comptes de stockage supplémentaire : ajoutez des comptes de stockage Azure en tant que stockage supplémentaire.
    - Accès à Data Lake Storage : configurez l’accès entre le compte Data Lake Storage Gen1 et le cluster HDInsight. Pour obtenir des instructions, consultez [Configurer l’accès aux données Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Configurer l’accès aux données Data Lake Storage Gen1 

Dans cette section, vous configurez l’accès à Data Lake Storage Gen1 à partir des clusters HDInsight au moyen d’un principal du service Azure Active Directory. 

### <a name="specify-a-service-principal"></a>Spécifier un principal du service

Dans le portail Azure, vous pouvez utiliser un principal du service existant ou en créer un.

**Créer un principal du service dans le portail Azure**

1. Cliquez sur **Accès à Data Lake Storage** dans le panneau Stockage.
2. Sur le panneau **Accès à Data Lake Storage Gen1**, cliquez sur **Créer**.
3. Cliquez sur **Principal du service**, puis suivez les instructions pour créer un principal du service.
4. Téléchargez le certificat si vous souhaitez l’utiliser à l’avenir. Le téléchargement du certificat est utile si vous souhaitez utiliser le même principal de service lorsque vous allez créer des clusters HDInsight supplémentaires.

    ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Ajouter un principal du service à un cluster HDInsight")

4. Cliquez sur **Accès** pour configurer l’accès au dossier.  Reportez-vous à [Configurer les autorisations des fichiers](#configure-file-permissions).


**Utiliser un principal du service existant dans le portail Azure**

1. Cliquez sur **Accès à Data Lake Store**.
1. Sur le panneau **Accès à Data Lake Storage Gen1**, cliquez sur **Utiliser existant**.
2. Cliquez sur **Principal du service**, puis sélectionnez un principal du service. 
3. Chargez le certificat (fichier .pfx) associé au principal du service sélectionné, puis indiquez le mot de passe du certificat.

    ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Ajouter un principal du service à un cluster HDInsight")

4. Cliquez sur **Accès** pour configurer l’accès au dossier.  Reportez-vous à [Configurer les autorisations des fichiers](#configure-file-permissions).


### <a name="configure-file-permissions"></a>Configurer les autorisations des fichiers

La configuration diffère selon que le compte est utilisé en tant que compte de stockage par défaut ou compte de stockage supplémentaire :

- Utilisation en tant que stockage par défaut

    - Autorisation au niveau racine du compte Data Lake Storage Gen1
    - Autorisation au niveau racine du stockage de cluster HDInsight. Exemple : dossier __/clusters__ utilisé précédemment dans le didacticiel.
- Utilisation en tant que stockage supplémentaire

    - Autorisation au niveau des dossiers pour lesquels vous avez besoin d’un accès aux fichiers.

**Pour assigner des autorisations au niveau racine du compte Data Lake Storage Gen1**

1. Sur le panneau **Accès à Data Lake Storage Gen1**, cliquez sur **Accès**. Le panneau **Sélectionner des autorisations de fichiers** s’ouvre. Il répertorie les comptes Data Lake Storage Gen1 dans votre abonnement.
2. Placez le pointeur de la souris (sans cliquer) sur le nom du compte Data Lake Storage Gen1 pour afficher la case à cocher, puis cochez cette case.

    ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Ajouter un principal du service à un cluster HDInsight")

  Par défaut, __READ__, __WRITE__ ET __EXECUTE__ sont tous sélectionnés.

3. Cliquez sur **Sélectionner** en bas de la page.
4. Cliquez sur **Exécuter** pour assigner des autorisations.
5. Cliquez sur **Done**.

**Assigner des autorisations au niveau racine du cluster HDInsight**

1. Sur le panneau **Accès à Data Lake Storage Gen1**, cliquez sur **Accès**. Le panneau **Sélectionner des autorisations de fichiers** s’ouvre. Il répertorie les comptes Data Lake Storage Gen1 dans votre abonnement.
1. Dans le panneau **Sélectionner des autorisations de fichiers**, cliquez sur le nom du Data Lake Storage Gen1 pour afficher son contenu.
2. Sélectionnez la racine de stockage du cluster HDInsight en cochant la case située à gauche du dossier. Comme indiqué sur la capture d’écran précédente, la racine de stockage du cluster est le dossier __/clusters__ que vous avez spécifié lors de la sélection de Data Lake Storage Gen1 comme stockage par défaut.
3. Définissez les autorisations sur le dossier.  Par défaut, les autorisations en lecture, écriture et exécution sont toutes sélectionnées.
4. Cliquez sur **Sélectionner** en bas de la page.
5. Cliquez sur **Exécuter**.
6. Cliquez sur **Done**.

Si vous utilisez Data Lake Storage Gen1 en tant que stockage supplémentaire, vous devez assigner des autorisations uniquement pour les dossiers auxquels vous souhaitez accéder à partir du cluster HDInsight. Par exemple, sur la capture d’écran ci-dessous, vous donnez uniquement accès au dossier **mynewfolder** dans un compte Data Lake Storage Gen1.

![Affecter des autorisations de principal de service au cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Affecter des autorisations de principal de service au cluster HDInsight")


## <a name="verify-cluster-set-up"></a>Vérification de la configuration de cluster

Une fois que l’installation du cluster est terminée, accédez au panneau du cluster pour vérifier vos résultats en effectuant l’une des opérations suivantes, ou les deux :

* Pour vérifier que le stockage associé au cluster est le compte Data Lake Storage Gen1 que vous avez spécifié, cliquez sur **Comptes de stockage** dans le panneau gauche.

    ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Ajouter un principal du service à un cluster HDInsight")

* Pour vérifier que le principal du service est correctement associé au cluster HDInsight, cliquez sur **Accès à Data Lake Storage Gen1** dans le volet gauche.

    ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Ajouter un principal du service à un cluster HDInsight")


## <a name="examples"></a>Exemples

Une fois que vous avez configuré le cluster avec Data Lake Storage Gen1 comme stockage, voici quelques exemples de l’utilisation du cluster HDInsight pour analyser les données stockées dans Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Exécuter une requête Hive sur des données stockées dans Data Lake Storage Gen1 (comme stockage principal)

Pour exécuter une requête Hive, utilisez l’interface des vues Hive du portail Ambari. Pour obtenir des instructions sur l'utilisation des vues Hive Ambari, consultez [Utiliser la vue Hive avec Hadoop dans HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Lorsque vous utilisez les données dans Data Lake Storage Gen1, quelques modifications doivent être apportées.

Si vous utilisez, par exemple, le cluster que vous avez créé avec Data Lake Storage Gen1 comme stockage principal, le chemin d’accès aux données est le suivant : *adl://<nom_compte_Data_Lake_Storage_Gen1>/azuredatalakestore.net/path/to/file*. Une requête Hive permettant de créer une table à partir des exemples de données stockés dans le compte Data Lake Storage Gen1 ressemble à l’instruction suivante :

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Descriptions :
* `adl://hdiadlsg1storage.azuredatalakestore.net/` est la racine du compte Data Lake Storage Gen1.
* `/clusters/myhdiadlcluster` est la racine des données du cluster que vous avez spécifiée lors de la création du cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` est l’emplacement de l’exemple de fichier que vous utilisez dans la requête.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Exécuter une requête Hive sur des données stockées dans Data Lake Storage Gen1 (comme stockage supplémentaire)

Si le cluster que vous avez créé utilise Stockage Blob comme stockage par défaut, les exemples de données ne se trouvent pas dans le compte Data Lake Storage Gen1 utilisé en tant que stockage supplémentaire. Dans ce cas, commencez par transférer les données de Stockage Blob vers le compte Data Lake Storage Gen1, puis exécutez les requêtes selon la procédure décrite ci-dessus.

Pour plus d’informations sur la copie de données de Stockage Blob vers Data Lake Storage Gen1, consultez les articles suivants :

* [Utiliser Distcp pour copier des données entre des objets blob Stockage Azure et Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Copier des données de Stockage Blob Azure vers Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Utiliser Data Lake Storage Gen1 avec un cluster Spark
Vous pouvez utiliser un cluster Spark pour exécuter des tâches Spark sur des données stockées dans un compte Data Lake Storage Gen1. Pour plus d’informations, consultez [Utiliser le cluster HDInsight Spark pour analyser les données dans Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Utiliser Data Lake Storage Gen1 dans une topologie Storm
Vous pouvez utiliser le compte Data Lake Storage Gen1 pour écrire des données à partir d’une topologie Storm. Pour obtenir des instructions sur la réalisation de ce scénario, consultez [Utiliser Azure Data Lake Storage Gen1 avec Apache Storm sur HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Voir aussi
* [Utiliser Data Lake Storage Gen1 avec des clusters Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell : créer un cluster HDInsight pour utiliser Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
