---
title: Installer Presto sur des clusters Azure HDInsight Linux
description: Découvrez comment installer Presto et Airpal sur des clusters Hadoop HDInsight sous Linux à l’aide des actions de script.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: hrasheed
ms.openlocfilehash: 60ff63a049f225886d69c1a89a2930671e533d78
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54910911"
---
# <a name="install-and-use-presto-on-hadoop-based-hdinsight-clusters"></a>Installer et utiliser Presto sur des clusters HDInsight Hadoop

Cet article explique comment installer Presto sur des clusters HDInsight Adobe basés sur Hadoop à l’aide d’actions de script. Vous allez également apprendre à installer Airpal sur un cluster HDInsight Presto existant.

HDInsight offre également l’application Starburst Presto pour des clusters Apache Hadoop. Pour plus d’informations, consultez [Installer des applications Apache Hadoop tierces sur Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-install-applications).

> [!IMPORTANT]  
> Les étapes décrites dans cet article nécessitent un cluster Hadoop HDInsight version 3.5 qui utilise Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez la page [Versions de HDInsight](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>Qu’est-ce que Presto ?
[Presto](https://prestodb.io/overview.html) est un moteur de requête SQL rapide distribué pour les Big Data. Presto convient à l’exécution interactive de requêtes de pétaoctets de données. Pour plus d’informations sur les composants de Presto et leur manière de fonctionner ensemble, consultez la page [Concepts Presto](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst).

> [!WARNING]  
> Les composants fournis avec le cluster HDInsight sont entièrement pris en charge. Le support Microsoft peut vous aider à identifier et résoudre les problèmes liés à ces composants.
> 
> Les composants personnalisés comme Presto bénéficient d’un support commercialement raisonnable pour vous aider à résoudre le problème. Le support Microsoft peut réussir à résoudre le problème. Ou alors, il peut vous inviter à faire appel à d’autres canaux qui offrent une expertise reconnue sur ces technologies open source. De nombreux sites de communauté sont disponibles, comme le [forum MSDN pour HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) et [Stack Overflow](https://stackoverflow.com). 
>
> Par ailleurs, les projets Apache ont des sites de projet sur le [site web Apache](https://apache.org). [Hadoop](https://hadoop.apache.org/) en est un exemple.


## <a name="install-presto-by-using-script-actions"></a>Installer Presto à l’aide d’actions de script

Cette section explique comment utiliser l’exemple de script quand vous créez un cluster à l’aide du portail Azure : 

1. Commencez à provisionner un cluster en effectuant les étapes décrites dans [Créer des clusters Linux dans HDInsight à l’aide du portail Azure](hdinsight-hadoop-create-linux-clusters-portal.md). Veillez à créer le cluster avec le flux de création de cluster **Personnalisé**. Le cluster doit répondre aux exigences suivantes :

    * Il doit s’agir d’un cluster Hadoop créé avec HDInsight version 3.6.

    * Il doit utiliser Stockage Azure comme banque de données. Il n’est pas possible d’utiliser Presto sur un cluster qui utilise Azure Data Lake Storage comme option de stockage.

    ![HDInsight, Personnalisé (taille, paramètres, applications)](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. Dans la zone **Paramètres avancés**, sélectionnez **Actions de script**. Fournissez les informations suivantes. Vous pouvez également choisir l’option **Installer Presto** comme type de script :
   
   * **NAME**. entrez un nom convivial pour l’action de script.
   * **URI de script Bash**. `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`.
   * **HEAD**. Sélectionnez cette option.
   * **WORKER**. Sélectionnez cette option.
   * **ZOOKEEPER**. Ne cochez pas cette case.
   * **PARAMETERS**. Laissez ce champ vide.


3. En bas de la zone **Actions de script**, choisissez le bouton **Sélectionner** pour enregistrer la configuration. Pour terminer, choisissez le bouton **Sélectionner** au bas de la zone **Paramètres personnalisés** afin d’enregistrer les informations de configuration.

4. Poursuivez le provisionnement du cluster en effectuant les étapes décrites dans [Créer des clusters Linux dans HDInsight à l’aide du portail Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]  
    > Azure PowerShell, Azure Classic CLI, le SDK HDInsight .NET ou les modèles Azure Resource Manager peuvent également être utilisés pour appliquer des actions de script. Vous pouvez également appliquer les actions de script aux clusters qui sont déjà en cours d’exécution. Pour plus d’informations, consultez [Personnaliser des clusters HDInsight Linux à l’aide d’actions de script](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Utiliser Presto avec HDInsight

Pour utiliser Presto dans un cluster HDInsight, suivez ces étapes :

1. Connectez-vous au cluster HDInsight à l’aide de SSH :
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Pour plus d’informations, consultez [Se connecter à HDInsight (Apache Hadoop) à l’aide de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Démarrez l’interface Presto en exécutant la commande suivante :
   
    `presto --schema default`

3. Exécutez une requête sur un exemple de table, comme **hivesampletable**, disponible sur tous les clusters HDInsight par défaut :
   
    `select count (*) from hivesampletable;`
   
    Par défaut, les connecteurs [Apache Hive](https://prestodb.io/docs/current/connector/hive.html) et [TPCH](https://prestodb.io/docs/current/connector/tpch.html) pour Presto sont déjà configurés. Le connecteur Hive est configuré pour utiliser l’installation Hive par défaut. Toutes les tables Hive sont donc automatiquement affichées dans Presto.

    Pour plus d’informations, consultez la [documentation Presto](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Utiliser Airpal avec Presto

[Airpal](https://github.com/airbnb/airpal#airpal) est une interface de requête web open source pour Presto. Pour plus d’informations sur Airpal, consultez la [documentation Airpal](https://github.com/airbnb/airpal#airpal).

Pour installer Airpal sur le nœud de périphérie, effectuez les étapes suivantes :

1. À l’aide de SSH, connectez-vous au nœud principal du cluster HDInsight sur lequel Presto est installé :
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Pour plus d’informations, consultez [Se connecter à HDInsight (Apache Hadoop) à l’aide de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Après vous être connecté, exécutez la commande suivante :

    `sudo slider registry  --name presto1 --getexp presto` 
   
    Vous voyez une sortie similaire au JSON suivant :

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. À partir de la sortie, notez la valeur pour la propriété de **valeur**. Cette valeur est nécessaire pour installer Airpal sur le nœud de périphérie du cluster. Vous devez utiliser la valeur **10.0.0.12:9090** indiquée dans la sortie précédente.

4. Utilisez [ce modèle](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json) pour créer un nœud de périphérie du cluster HDInsight. Entrez les valeurs affichées dans la capture d’écran suivante.

    ![Déploiement personnalisé](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Sélectionnez **Achat**.

6. Une fois que vous avez appliqué les modifications à la configuration du cluster, accédez à l’interface web d’Airpal en effectuant les étapes suivantes dans le [portail Azure](https://portal.azure.com) :

    1. Dans le menu de gauche, sélectionnez **Tous les services**.

    1. Sous **ANALYTIQUE**, sélectionnez **Clusters HDInsight**.

    1. Sélectionnez votre cluster dans la liste ; la vue par défaut s’affiche.

    1. Dans la vue par défaut, sous **Paramètres**, sélectionnez **Applications**.

        ![HDInsight, Applications](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    1. Dans la page **Applications installées**, recherchez l’entrée de table pour **airpal**. Sélectionnez **Portail**.

        ![Applications installées](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    1. Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur que vous avez spécifiées quand vous avez créé le cluster HDInsight Hadoop.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Personnaliser une installation Presto sur un cluster HDInsight

Pour personnaliser l’installation, effectuez les étapes suivantes :

1. À l’aide de SSH, connectez-vous au nœud principal du cluster HDInsight sur lequel Presto est installé :
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Pour plus d’informations, consultez [Se connecter à HDInsight (Apache Hadoop) à l’aide de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Apportez vos modifications de configuration dans le fichier `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Pour plus d’informations sur la configuration de Presto, consultez [Presto configuration options for YARN-based clusters](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html) (Options de configuration de Presto pour les clusters YARN).

3. Arrêtez et supprimez l’instance active de Presto :

    `sudo slider stop presto1 --force``sudo slider destroy presto1 --force`

4. Démarrez une nouvelle instance de Presto avec la personnalisation :

    `sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json`

5. Attendez que la nouvelle instance soit démarrée. Notez l’adresse du coordinateur Presto :


    `sudo slider registry --name presto1 --getexp presto`

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Générer des données de point de référence pour les clusters HDInsight qui exécutent Presto

Les standards TPC-DS mesurent la performance de nombreux systèmes d’aide à la décision, y compris les systèmes de Big Data. Vous pouvez utiliser Presto afin de générer des données et d’évaluer la manière dont elles se comparent avec vos propres données de point de référence HDInsight. Pour plus d’informations, consultez [tpcds-hdinsight](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="next-steps"></a>Étapes suivantes
* [Installez et utilisez Hue sur des clusters HDInsight Hadoop](hdinsight-hadoop-hue-linux.md). Hue est une interface utilisateur web qui permet de créer, d’exécuter et d’enregistrer facilement les tâches Apache Pig et Hive.

* [Installez Apache Giraph sur les clusters HDInsight Hadoop et utilisez Giraph pour traiter des graphiques à grande échelle](hdinsight-hadoop-giraph-install-linux.md). Utilisez la personnalisation de clusters pour installer Giraph sur des clusters HDInsight Hadoop. Giraph vous permet d’utiliser Hadoop pour le traitement des graphiques. Il peut également être utilisé avec Azure HDInsight.

* [Installez et utilisez Apache Solr sur des clusters HDInsight Hadoop](hdinsight-hadoop-solr-install-linux.md). Utilisez la personnalisation de clusters pour installer Solr sur des clusters HDInsight Hadoop. Avec Solr, vous pouvez effectuer des opérations de recherche avancée sur les données stockées.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
