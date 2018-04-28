---
title: Installer Presto sur des clusters Azure HDInsight Linux | Documents Microsoft
description: Apprenez à installer Presto et Airpal sur des clusters Hadoop HDInsight sous Linux, à l’aide des actions de script.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 32b7925b7414f00dfdd7d5c8a45b3601bf58942e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="install-and-use-presto-on-hdinsight-hadoop-clusters"></a>Installer et utiliser Presto sur des clusters Hadoop HDInsight

Dans cet document, vous allez apprendre à installer Presto sur des clusters Hadoop HDInsight à l’aide des actions de script. Vous allez également apprendre à installer Airpal sur un cluster HDInsight Presto existant.

> [!IMPORTANT]
> Les étapes décrites dans ce document nécessitent un **cluster Hadoop HDInsight version 3.5** qui utilise Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez la page [Versions de HDInsight](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>Qu’est-ce que Presto ?
[Presto](https://prestodb.io/overview.html) est un moteur de requête SQL rapide distribué pour les Big Data. Presto convient à l’exécution interactive de requêtes de pétaoctets de données. Pour plus d’informations sur les composants de Presto et leur manière de fonctionner ensemble, consultez la page [Concepts Presto](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst).

> [!WARNING]
> Les composants fournis avec le cluster HDInsight bénéficient d’une prise en charge totale, et le support Microsoft vous aidera à identifier et à résoudre les problèmes liés à ces composants.
> 
> Les composants personnalisés, tels que Presto, bénéficient d'un support commercialement raisonnable pour vous aider à résoudre le problème de manière plus approfondie. Cela signifie SOIT que le problème pourra être résolu, SOIT que vous serez invité à affecter les ressources disponibles pour les technologies Open Source. Vous pouvez, par exemple, utiliser de nombreux sites de communauté, comme le [forum MSDN sur HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [http://apache.org](http://apache.org). Par exemple: [Hadoop](http://hadoop.apache.org/).
> 
> 


## <a name="install-presto-using-script-action"></a>Installer Presto à l’aide d’une action de script

Cette section explique comment utiliser l’exemple de script dans le cadre de la création d’un cluster à l’aide du portail Azure. 

1. Commencez à approvisionner un cluster en suivant la procédure décrite sur la page [Provision Linux-based HDInsight clusters](hdinsight-hadoop-create-linux-clusters-portal.md) (Approvisionner des clusters HDInsight sous Linux). Veillez à créer le cluster à l’aide du flux de création de cluster **personnalisé**. Le cluster doit répondre aux exigences suivantes.

    * Ce doit être un cluster Hadoop créé avec la version 3.5 de HDInsight.

    * Il doit utiliser Stockage Azure comme banque de données. Il n’est pas encore possible d’utiliser Presto sur un cluster qui utilise Azure Data Lake Store comme option de stockage. 

    ![Créer un cluster HDInsight à l’aide d’options personnalisées](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. Dans la zone **Paramètres avancés**, sélectionnez **Actions de script**, puis indiquez les informations ci-dessous :
   
   * **NAME**: saisissez un nom convivial pour l’action de script.
   * **URI de script bash** : `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **HEAD**: cochez cette option.
   * **WORKER** : cochez cette option
   * **ZOOKEEPER** : décochez cette case
   * **PARAMETERS**: laissez ce champ vide.


3. En bas de la zone **Actions de script**, cliquez sur le bouton **Sélectionner** pour enregistrer la configuration. Pour terminer, cliquez sur le bouton **Sélectionner** au bas de la zone **Paramètres personnalisés** afin d’enregistrer les informations de configuration.

4. Continuez l’approvisionnement du cluster, comme décrit dans la section [Approvisionner des clusters HDInsight sous Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]
    > Azure PowerShell, l'interface de ligne de commande Azure (CLI), le Kit de développement logiciel (SDK) .NET HDInsight ou les modèles Azure Resource Manager peuvent également être utilisés pour appliquer des actions de script. Vous pouvez également appliquer les actions de script aux clusters qui sont déjà en cours d’exécution. Pour plus d’informations, consultez la page [Personnaliser des clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Utiliser Presto avec HDInsight

Pour travailler avec Presto dans un cluster HDInsight, procédez comme suit :

1. Connectez-vous au cluster HDInsight à l’aide de SSH :
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Démarrez l’interface Presto à l’aide de la commande suivante.
   
        presto --schema default

3. Exécutez une requête sur un exemple de table, comme **hivesampletable**, disponible sur tous les clusters HDInsight par défaut.
   
        select count (*) from hivesampletable;
   
    Par défaut, les connecteurs [Hive](https://prestodb.io/docs/current/connector/hive.html) et [TPCH](https://prestodb.io/docs/current/connector/tpch.html) pour Presto sont déjà configurés. Le connecteur Hive est configuré pour utiliser l’installation Hive qui est installée par défaut. De ce fait, toutes les tables venant de Hive seront automatiquement visibles dans Presto.

    Pour plus d’informations, consultez la [documentation Presto](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Utiliser Airpal avec Presto

[Airpal](https://github.com/airbnb/airpal#airpal) est une interface de requête web open source pour Presto. Pour plus d’informations sur Airpal, consultez la [documentation Airpal](https://github.com/airbnb/airpal#airpal).

Pour installer Airpal sur le nœud de périphérie, procédez comme suit :

1. À l’aide de SSH, connectez-vous au nœud principal du cluster HDInsight, sur lequel Presto est déjà installé :
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Une fois que vous êtes connecté, exécutez la commande suivante.

        sudo slider registry  --name presto1 --getexp presto 
   
    Vous voyez une sortie similaire au JSON suivant :

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. À partir de la sortie, notez la valeur pour la propriété de **valeur**. Cette valeur sera nécessaire lors de l’installation de Airpal sur le nœud de périmètre du cluster. À partir de la sortie ci-dessus, vous aurez besoin de la valeur **10.0.0.12:9090**.

4. Utilisez le modèle **[ici](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)** pour créer un nœud de périmètre du cluster HDInsight et fournir les valeurs telles qu’elles sont indiquées dans la capture d’écran suivante.

    ![HDInsight installe Airpal sur un cluster Presto](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Cliquez sur **Achat**.

6. Une fois que les modifications sont appliquées à la configuration du cluster, vous pouvez accéder à l’interface web de Airpal en réalisant les étapes suivantes.

    1. À partir de la boîte de dialogue du cluster, cliquez sur **Applications**.

        ![HDInsight lance Airpal sur un cluster Presto](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    2. À partir de la zone **Applications installées**, cliquez sur **Portail**, situé à l’opposé de airpal.

        ![HDInsight lance Airpal sur un cluster Presto](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    3. Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur que vous avez spécifié lors de la création du cluster Hadoop HDInsight.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Personnaliser une installation Presto sur un cluster HDInsight

Pour personnaliser l’installation, procédez comme suit :

1. À l’aide de SSH, connectez-vous au nœud principal du cluster HDInsight, sur lequel Presto est déjà installé :
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Apportez vos modifications de configuration dans le fichier `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Pour en savoir plus sur la configuration de Presto, consultez la page [Presto configuration for YARN-based clusters](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html) (Configuration de Presto pour des clusters sous Yarn).

3. Arrêter et supprimer l’instance de Presto en cours d’exécution.

        sudo slider stop presto1 --force
        sudo slider destroy presto1 --force

4. Démarrer une nouvelle instance de Presto grâce à la personnalisation.

       sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json

5. Attendez que la nouvelle instance soit prête et notez l’adresse du coordinateur Presto.


       sudo slider registry --name presto1 --getexp presto

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Générer des données de point de référence pour les clusters HDInsight qui exécutent Presto

Les normes industrielles TPC-DS mesure la performance de nombreux systèmes de support de décision, incluant les systèmes de Big Data. Vous pouvez utiliser Presto afin de générer des données et d’évaluer la manière dont elles se comparent avec vos propres données de point de référence HDInsight. Vous pourrez trouver plus d’informations [ici](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="see-also"></a>Voir aussi
* [Installer et utiliser Hue sur les clusters HDInsight](hdinsight-hadoop-hue-linux.md). Hue est une interface utilisateur qui permet de créer, d’exécuter et d’enregistrer facilement des travaux Pig et Hive.

* [Installation de Giraph sur des clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md). Utilisez la personnalisation de clusters pour installer Giraph sur des clusters HDInsight Hadoop. Giraph permet de traiter des graphiques à l’aide de Hadoop et peut être utilisé avec Azure HDInsight.

* [Installation de Solr sur des clusters HDInsight](hdinsight-hadoop-solr-install-linux.md). Utilisez la personnalisation de clusters pour installer Solr sur des clusters HDInsight Hadoop. Solr vous permet d’effectuer de puissantes opérations de recherche sur des données stockées.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
