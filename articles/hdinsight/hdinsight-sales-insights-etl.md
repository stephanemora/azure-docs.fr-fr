---
title: 'Tutoriel : Créer un pipeline d’extraction, transformation et chargement (ETL) de bout en bout pour dériver des insights de ventes'
description: Découvrez comment créer et utiliser des pipelines ETL avec Azure HDInsight pour dériver des insights à partir de données de ventes à l’aide de clusters à la demande Spark et de Power BI.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: 52509f886ac9882c372de401ca163ccef418837f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695704"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>Tutoriel : Créer un pipeline de données de bout en bout pour dériver des insights de ventes

Dans ce tutoriel, vous allez créer un pipeline de données de bout en bout qui effectue des opérations d’extraction, de transformation et de chargement. Le pipeline utilise des clusters Apache Spark et Apache Hive exécutés sur Azure HDInsight pour interroger et manipuler les données. Il fait également appel à des technologies telles que Data Lake Storage Gen2 pour le stockage des données et Power BI pour la visualisation.

Ce pipeline de données combine les données de tous les différents magasins, supprime les données indésirables, ajoute de nouvelles données et recharge le tout dans votre stockage afin que vous puissiez visualiser les insights métier. Apprenez-en davantage sur les pipelines ETL dans [Extraire, transformer et charger (ETL) à l’échelle](./hadoop/apache-hadoop-etl-at-scale.md).

![Architecture ETL](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

Téléchargez [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) pour visualiser les insights métier à la fin de ce tutoriel.

## <a name="create-resources"></a>Créer des ressources

### <a name="clone-the-repository-with-scripts-and-data"></a>Cloner le dépôt avec des scripts et des données

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Ouvrez Cloud Shell à partir de la barre de menus supérieure. Sélectionnez votre abonnement pour créer un partage de fichiers si vous y êtes invité par Azure Cloud Shell.

    ![Ouvrez Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. Sélectionnez « bash » dans le menu déroulant « Sélectionner l’environnement ».
1. Connectez-vous à votre compte Azure et définissez l’abonnement 
1. Configurez le groupe de ressources pour le projet.
    1. Choisissez un nom de groupe de ressources unique.
    1. Exécutez l’extrait de code ci-dessous dans Azure Cloud Shell pour définir les variables qui seront utilisées lors des étapes ultérieures.

        ```azurecli-interactive 
        resourceGroup="<RESOURCE GROUP NAME>"
        subscriptionID="<SUBSCRIPTION ID>"
        
        az account set --subscription $subscriptionID
        az group create --name $resourceGroup --location westus
        ```

1. Téléchargez les données et les scripts de ce tutoriel à partir du [dépôt ETL HDInsight d’insights de ventes](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) en tapant les commandes suivantes dans Cloud Shell :

        ```azurecli-interactive 
        git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
        cd hdinsight-sales-insights-etl
        ```

1. Tapez `ls` à l’invite de l’interpréteur de commandes pour vérifier que les fichiers et les répertoires suivants ont été créés :

    ```output
    /salesdata/
    /scripts/
    /templates/
    ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Déployer les ressources Azure nécessaires pour le pipeline 

1. Ajoutez des autorisations d’exécution pour le script `chmod +x scripts/*.sh`.
1. Utilisez la commande `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` pour exécuter le script afin de déployer les ressources suivantes dans Azure :

    1. Un compte Stockage Blob Azure : ce compte contiendra les données de ventes de l’entreprise.
    2. Un compte Azure Data Lake Storage Gen2 : ce compte servira de compte de stockage pour les deux clusters HDInsight. Pour en savoir plus sur HDInsight et Data Lake Storage Gen2, consultez [Azure HDInsight integration with Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    3. Une identité managée affectée par l’utilisateur : ce compte donne aux clusters HDInsight l’accès au compte Data Lake Storage Gen2.
    4. Un cluster Apache Spark : ce cluster sera utilisé pour nettoyer et transformer les données brutes.
    5. Un cluster Interactive Query Apache Hive : ce cluster autorisera l’interrogation des données de ventes afin de les visualiser avec Power BI.
    6. Un réseau virtuel Azure pris en charge par des règles de groupe de sécurité réseau (NSG) : ce réseau virtuel permet aux clusters de communiquer et de sécuriser leurs communications. 

La création du cluster peut prendre environ 20 minutes.

Le script `resources.sh` contient la commande suivante, qui utilise un modèle Resource Manager (`resourcestemplate.json`) pour créer les ressources spécifiées avec la configuration souhaitée.

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

Le script `resources.sh` charge également les fichiers csv de données de ventes dans le compte de stockage d’objets blob nouvellement créé à l’aide de la commande suivante :

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

Le mot de passe par défaut utilisé pour l’accès SSH aux clusters est `Thisisapassword1`. Si vous souhaitez modifier le mot de passe, accédez au fichier `resourcesparameters.json`, puis modifiez le mot de passe pour les paramètres `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` et `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Vérifier le déploiement et recueillir des informations sur les ressources

1. Si vous souhaitez vérifier l’état de votre déploiement, accédez au groupe de ressources dans le portail Azure. Cliquez sur **Déploiements** sous **Paramètres**. Cliquez sur le nom de votre déploiement, `ResourcesDeployment`. Ici, vous pouvez voir les ressources qui ont été déployées avec succès et celles qui sont toujours en cours de déploiement.
1. Une fois le déploiement terminé, accédez au portail Azure > **Groupes de ressources** > < NOM_GROUPE_RESSOURCES >.
1. Recherchez le nouveau compte Stockage Azure qui a été créé pour stocker les fichiers de ventes. Le nom du compte de stockage commence par `blob`, puis contient une chaîne aléatoire. 
    1. Prenez note du nom du compte de stockage ; vous en aurez besoin plus tard.
    1. Cliquez sur le nom du compte de stockage d’objets blob.
    1. Sur le côté gauche du portail, sous **Paramètres**, cliquez sur **Clés d’accès**.
    1. Copiez la chaîne de la zone **Clé1** et enregistrez-la pour une utilisation ultérieure.
1. Recherchez le compte Data Lake Storage Gen2 qui a été créé en tant que stockage pour les clusters HDInsight. Ce compte se trouve dans le même groupe de ressources que le compte de stockage d’objets blob, mais son nom commence par `adlsgen2`.
    1. Notez le nom du compte Data Lake Storage Gen2.
    1. Cliquez sur le nom du compte Data Lake Storage Gen2.
    1. Sur le côté gauche du portail, sous **Paramètres**, cliquez sur **Clés d’accès**.
    1. Copiez la chaîne de la zone **Clé1** et enregistrez-la pour une utilisation ultérieure.

> [!Note]
> Une fois que vous connaissez les noms des comptes de stockage, vous pouvez également obtenir les clés de comptes en exécutant la commande suivante à l’invite Azure Cloud Shell :
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-an-azure-data-factory"></a>Créer une fabrique de données Azure

Azure Data Factory est un outil qui permet d’automatiser des pipelines Azure. Ce n’est pas l’unique moyen d’effectuer ces tâches, mais c’est un excellent moyen d’automatiser ces processus. Pour plus d’informations sur Azure Data Factory, consultez la [Documentation Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Cette fabrique de données Azure aura un pipeline avec deux activités : 

1. La première activité copiera les données de Stockage Blob Azure dans le compte de stockage Data Lake Storage Gen2 afin d’imiter l’ingestion des données.
2. La deuxième activité transformera les données dans le cluster Spark. Le script transforme les données en supprimant les colonnes indésirables, ainsi qu’en ajoutant une nouvelle colonne qui calcule le chiffre d’affaires généré par une transaction unique.

Pour configurer votre pipeline Azure Data Factory, exécutez le script `adf.sh` :

1. Ajoutez des autorisations d’exécution sur le fichier à l’aide de `chmod +x adf.sh`.
1. Exécutez le script avec `./adf.sh`. 

Ce script effectue les opérations suivantes :

1. Il crée un principal de service avec des autorisations `Storage Blob Data Contributor` sur le compte de stockage Data Lake Storage Gen2.
1. Il obtient un jeton d’authentification pour autoriser les requêtes POST à l’[API REST FileSystem Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Il renseigne le nom réel de votre compte de stockage Data Lake Storage Gen2 dans les fichiers `sparktransform.py` et `query.hql`.
1. Il obtient les clés de stockage pour le compte Data Lake Storage Gen2 et le compte Stockage Blob Azure.
1. Il crée un autre déploiement de ressources pour créer un pipeline Azure Data Factory, avec ses services liés et ses activités associés. Il transmet les clés de stockage en tant que paramètres au fichier de modèle pour que les services liés puissent accéder correctement aux comptes de stockage.

Le pipeline ADF est déployé à l’aide de la commande suivante :

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Exécuter le pipeline de données

### <a name="trigger-the-adf-activities"></a>Déclencher les activités ADF

La première activité dans le pipeline ADF que vous avez créée déplace les données du stockage d’objets blob vers Data Lake Storage Gen2. La deuxième activité applique les transformations Spark aux données et enregistre les fichiers csv transformés à un nouvel emplacement. L’exécution du pipeline entier peut prendre quelques minutes.

Pour déclencher les pipelines, vous pouvez effectuer l’une des deux actions suivantes :

1. Exécutez les commandes ci-dessous pour déclencher les pipelines ADF dans PowerShell. 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

1. Vous pouvez également ouvrir la fabrique de données, sélectionner Créer et surveiller, et déclencher le pipeline de copie, puis le pipeline Spark à partir du portail. Pour plus d’informations sur le déclenchement de pipelines par le biais du portail, consultez [Créer des clusters Apache Hadoop à la demande dans HDInsight avec Azure Data Factory](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-create-linux-clusters-adf#trigger-a-pipeline).

Pour vérifier que les pipelines ont été exécutés, vous pouvez effectuer l’une des opérations suivantes :

1. Accédez à la section **Superviser** de votre fabrique de données Azure par le biais du portail.
2. Accédez à l’Explorateur de stockage de votre compte de stockage Data Lake Storage Gen2, accédez au système de fichiers `files`, puis accédez au dossier `transformed` et vérifiez son contenu pour voir si le pipeline a réussi.

Pour découvrir d’autres façons de transformer des données à l’aide d’HDInsight, consultez cet article sur l’utilisation de [Jupyter Notebook](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Créer une table sur le cluster Interactive Query pour afficher les données sur Power BI

1. Copiez le fichier query.hql sur le cluster LLAP en utilisant SCP :

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Utilisez SSH pour vous connecter au cluster LLAP à l’aide de la commande suivante, puis entrez votre mot de passe. Si vous n’avez pas modifié le fichier `resourcesparameters.json`, le mot de passe est `Thisisapassword1`.

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. Exécutez la commande suivante pour exécuter le script :

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Ce script crée une table managée sur le cluster Interactive Query, à laquelle vous pouvez accéder à partir de Power BI. 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Créer un tableau de bord Power BI à partir de données de ventes

1. Ouvrez Power BI Desktop.
1. Sélectionnez **Obtenir des données**.
1. Recherchez **Cluster HDInsight Interactive Query**.
1. Collez l’URI de votre cluster à cet emplacement. Elle doit être au format `https://<LLAP CLUSTER NAME>.azurehdinsight.net`. Tapez `default` pour la base de données.
1. Entrez le nom d’utilisateur et le mot de passe que vous utilisez pour accéder au cluster.

Une fois les données chargées, vous pouvez faire des essais avec le tableau de bord que vous souhaitez créer. Pour bien démarrer avec les tableaux de bord Power BI, consultez les liens suivants :

* [Introduction aux tableaux de bord pour les concepteurs Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Tutoriel : Bien démarrer avec le service Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez toutes les ressources en effectuant les étapes suivantes afin de ne pas être facturé.

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Extraire, transformer et charger (ETL) à l’échelle](./hadoop/apache-hadoop-etl-at-scale.md)
