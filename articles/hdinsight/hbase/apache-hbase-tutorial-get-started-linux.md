---
title: Bien démarrer avec un exemple HBase sur HDInsight - Azure
description: Suivez cet exemple Apache HBase pour commencer à utiliser Hadoop sur HDInsight. Créez des tables à partir de l’interpréteur de commandes HBase et interrogez-les à l’aide de Hive.
keywords: commande hbase, exemple hbase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: hrasheed
ms.openlocfilehash: 9d94a976c08cdb5184ea4c5e2cd70ac039d78378
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384693"
---
# <a name="get-started-with-an-apache-hbase-example-in-hdinsight"></a>Prise en main d’un exemple Apache HBase dans HDInsight

Découvrez comment créer un cluster [Apache HBase](https://hbase.apache.org/) dans HDInsight, créer des tables HBase et les interroger à l’aide d’[Apache Hive](https://hive.apache.org/).  Pour obtenir des informations générales sur HBase, consultez [vue d’ensemble de HDInsight HBase](./apache-hbase-overview.md).

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables

* Un client SSH. Pour plus d’informations, consultez [Se connecter à HDInsight (Apache Hadoop) à l’aide de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. Les exemples de cet article utilisent l’interpréteur de commandes Bash sur Windows 10 pour les commandes de curl. Consultez [sous-système Windows pour Linux Installation Guide pour Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) pour les étapes d’installation.  Autres [interpréteurs de commandes Unix](https://www.gnu.org/software/bash/) fonctionne également.  Les exemples de curl, avec de légères modifications, peuvent travailler sur une invite de commandes de Windows.  Vous pouvez également utiliser l’applet de commande Windows PowerShell [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod).


## <a name="create-apache-hbase-cluster"></a>Créer un cluster Apache HBase

La procédure suivante utilise un modèle Azure Resource Manager pour créer un cluster HBase et le compte Stockage Azure dépendant par défaut. Pour comprendre les paramètres utilisés dans la procédure et d’autres méthodes de création de cluster, consultez [Création de clusters Hadoop basés sur Linux dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Pour plus d’informations sur l’utilisation de Data Lake Storage Gen2, consultez [Démarrage rapide : Configurer des clusters dans HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

1. Sélectionnez l’image suivante pour ouvrir le modèle dans le portail Azure. Le modèle se trouve dans [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Dans le panneau **Déploiement personnalisé**, entrez les valeurs suivantes :

    |Propriété |Description |
    |---|---|
    |Abonnement|Sélectionnez l’abonnement Azure utilisé pour créer le cluster.|
    |Groupe de ressources|Créez un groupe d’administration Azure Resource ou utilisez un groupe existant.|
    |Lieu|Spécifiez l’emplacement du groupe de ressources. |
    |ClusterName|Entrez un nom pour le cluster HBase.|
    |Mot de passe et nom de connexion de cluster|Le nom de connexion par défaut est **admin**.|
    |Mot de passe et nom d’utilisateur SSH|Le nom d’utilisateur par défaut est **sshuser**.|

    Tous les autres paramètres sont facultatifs.  

    Chaque cluster possède une dépendance de compte Stockage Azure. Après avoir supprimé un cluster, les données sont conservées dans le compte de stockage. Le nom du compte de stockage par défaut du cluster est le nom du cluster suivi du suffixe « store ». Il est codé en dur dans la section des variables du modèle.

3. Sélectionnez **J’accepte les conditions générales mentionnées ci-dessus**, puis sélectionnez **Acheter**. La création d’un cluster prend environ 20 minutes.

> [!NOTE]  
> Après la suppression d’un cluster HBase, vous pouvez créer un autre cluster HBase à l’aide du même conteneur d’objets blob par défaut. Le nouveau cluster utilise les tables HBase créées dans le cluster d’origine. Pour éviter toute incohérence, nous vous recommandons de désactiver les tables HBase avant de supprimer le cluster.

## <a name="create-tables-and-insert-data"></a>Créer des tables et insérer des données

Vous pouvez utiliser SSH pour vous connecter à des clusters HBase, puis utiliser [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) pour créer des tables HBase, et insérer et interroger des données. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

Pour la plupart des utilisateurs, les données s’affichent sous la forme tabulaire :

![Données tabulaires HBase HDInsight](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

Dans HBase (une implémentation de [Cloud BigTable](https://cloud.google.com/bigtable/)), certaines données ont l’aspect suivant :

![Données bigtable HBase HDInsight](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**Pour utiliser l’interpréteur de commandes HBase**

1. Utilisez la commande `ssh` pour vous connecter à votre cluster HBase. Modifiez la commande ci-dessous en remplaçant `CLUSTERNAME` par le nom de votre cluster, puis entrez la commande :

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Utilisez la commande `hbase shell` pour démarrer l'interpréteur de commandes interactif HBase. Entrez la commande suivante dans votre connexion SSH :

    ```bash
    hbase shell
    ```

1. Utilisez la commande `create` pour créer une table HBase avec deux familles de colonnes. Les noms de table et de colonne respectent la casse. Entrez la commande suivante :

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Utilisez la commande `list` pour répertorier toutes les tables contenues dans HBase. Entrez la commande suivante :

    ```hbase
    list
    ```

1. Utilisez la commande `put` pour insérer des valeurs dans une colonne et sur une ligne spécifiées d'une table particulière. Entrez les commandes suivantes :

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Utilisez la commande `scan` pour analyser et renvoyer les données de la table `Contacts`. Entrez la commande suivante :

    ```hbase
    scan 'Contacts'
    ```

    ![Interpréteur de commandes HBase Hadoop HDInsight](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Utilisez la commande `get` pour extraire le contenu d'une ligne. Entrez la commande suivante :

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Comme il n'y a qu'une seule ligne, vous obtenez des résultats semblables à ceux obtenus avec la commande `scan`.

    Pour plus d'informations sur le schéma de la table HBase, consultez [Introduction à la conception de schémas Apache HBase](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Pour plus de commandes HBase, consultez le [Guide de référence Apache HBase](https://hbase.apache.org/book.html#quickstart).

1. Utilisez la commande `exit` pour arrêter l'interpréteur de commandes interactif HBase. Entrez la commande suivante :

    ```hbaseshell
    exit
    ```

**Pour charger des données en bloc dans la table de contacts HBase**

HBase propose plusieurs méthodes pour charger des données dans des tables.  Pour en savoir plus, consultez la rubrique [Chargement en bloc](https://hbase.apache.org/book.html#arch.bulk.load).

Vous trouverez un exemple de fichier de données dans un conteneur blob public, `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  Le contenu du fichier de données est le suivant :

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

Si vous le souhaitez, vous pouvez créer un fichier texte et le télécharger dans votre propre compte de stockage. Pour obtenir des instructions, consultez [charger des données pour les travaux Apache Hadoop dans HDInsight](../hdinsight-upload-data.md).

> [!NOTE]  
> Cette procédure utilise la table de contacts HBase créée dans la dernière procédure.

1. À partir de votre open ssh connexion, exécutez la commande suivante pour transformer les données de fichiers à StoreFiles et stocker à un chemin d’accès relatif spécifié par `Dimporttsv.bulk.output`.

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Exécutez la commande suivante pour charger les données à partir de `/example/data/storeDataFileOutput` vers la table HBase :

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Vous pouvez ouvrir l’interpréteur de commandes HBase et utiliser le `scan` commande pour répertorier le contenu de la table.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Utiliser Apache Hive pour interroger Apache HBase

Vous pouvez interroger les données des tables HBase à l’aide d’[Apache Hive](https://hive.apache.org/). Dans cette section, vous créez une table Hive qui correspond à la table HBase et l’utilise pour interroger les données de votre table HBase.

1. À partir de votre ouvrir la connexion ssh, utilisez la commande suivante pour démarrer Beeline :

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Pour plus d’informations sur Beeline, consultez [Utilisation de Hive avec Hadoop dans HDInsight via Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Exécutez le script [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) suivant pour créer une table Hive correspondant à la table HBase. Avant d'exécuter cette instruction, vous devez avoir créé l'exemple de table référencé dans ce didacticiel en utilisant l’interpréteur de commandes HBase.

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. Exécutez le script HiveQL suivant pour interroger les données dans la table HBase :

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. Pour quitter Beeline, utilisez `!exit`.

1. Pour quitter votre ssh connexion, utilisez `exit`.

## <a name="use-hbase-rest-apis-using-curl"></a>Utilisation des API REST HBase à l’aide de Curl

L’API REST est sécurisée à l’aide de l’ [authentification de base](https://en.wikipedia.org/wiki/Basic_access_authentication). Vous devrez toujours effectuer les demandes à l’aide du protocole Secure HTTP (HTTPS) pour vous assurer que vos informations d’identification sont envoyées en toute sécurité au serveur.

1. Initier la variable d’environnement pour la facilité d’utilisation. Modifiez les commandes ci-dessous en remplaçant `MYPASSWORD` avec le mot de passe de connexion de cluster. Remplacez `MYCLUSTERNAME` par le nom de votre cluster HBase. Puis entrez les commandes.

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. Utilisez la commande suivante pour répertorier les tables HBase existantes :

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. Utilisez la commande suivante pour créer une table HBase avec deux familles de colonnes :

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    Le schéma est fourni au format JSon.
1. Utilisez la commande suivante pour insérer des données :

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    Vous devez coder en base64 les valeurs spécifiées dans le commutateur -d. Dans l’exemple :

   * MTAwMA==: 1 000
   * UGVyc29uYWw6TmFtZQ==: Personal:Name
   * Sm9obiBEb2xl: John Dole

     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) vous permet d’insérer plusieurs valeurs (par lot).

1. Utilisez la commande suivante pour obtenir une ligne :

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

Pour plus d’informations sur HBase Rest, voir le [Guide de référence Apache HBase](https://hbase.apache.org/book.html#_rest).

> [!NOTE]  
> Thrift n’est pas pris en charge par HBase dans HDInsight.
>
> Lorsque vous utilisez Curl ou toute autre communication REST avec WebHCat, vous devez authentifier les demandes en fournissant le nom d'utilisateur et le mot de passe de l'administrateur du cluster HDInsight. Vous devez également utiliser le nom du cluster dans l’URI (Uniform Resource Identifier) utilisé pour envoyer les demandes au serveur :
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    Vous devez recevoir une réponse similaire à celle-ci :
>   
>        {"status":"ok","version":"v1"}


## <a name="check-cluster-status"></a>Vérification du statut du cluster

HBase dans HDInsight est livré avec une interface utilisateur web pour la surveillance des clusters. Elle vous permet de demander des statistiques ou des informations sur les régions.

**Pour accéder à l’interface utilisateur principale HBase**

1. Connectez-vous à l’interface utilisateur Web Ambari au `https://Clustername.azurehdinsight.net`.
2. Dans le menu de gauche, cliquez sur **HBase**.
3. Cliquez sur **Liens rapides** en haut de la page, pointez vers le lien de nœud Zookeeper actif, puis cliquez sur **HBase Master UI (Interface utilisateur principale HBase)** .  L’interface utilisateur est ouverte dans un autre onglet de navigateur :

   ![Interface utilisateur principale HDInsight HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   L’interface utilisateur principale HBase comporte les sections suivantes :

   - serveurs de région
   - serveurs de sauvegarde
   - tables
   - tâches
   - attributs logiciels

## <a name="delete-the-cluster"></a>Supprimer le cluster

Pour éviter toute incohérence, nous vous recommandons de désactiver les tables HBase avant de supprimer le cluster.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Résolution des problèmes

Si vous rencontrez des problèmes lors de la création de clusters HDInsight, reportez-vous aux [exigences de contrôle d’accès](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer un cluster Apache HBase, à créer des tables et à afficher les données de ces tables à partir de l’interpréteur de commandes HBase. Vous avez également appris à utiliser une requête Hive pour interroger les données des tables HBase et à utiliser les API REST C# HBase pour créer une table HBase et en extraire les données.

Pour plus d'informations, consultez les rubriques suivantes :

* [Vue d’ensemble de HDInsight HBase](./apache-hbase-overview.md): Apache HBase est une base de données NoSQL open source Apache basée sur Apache Hadoop qui fournit un accès aléatoire et une forte cohérence pour de vastes quantités de données non structurées et semi-structurées.