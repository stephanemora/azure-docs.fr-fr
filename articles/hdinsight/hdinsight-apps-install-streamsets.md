---
title: Installer une application publiée - StreamSets Data Collector - Azure HDInsight
description: Installez et utiliser l’application Apache Hadoop tierce StreamSets Data Collector.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: ac287f2ee50501d703b7d7b79a436ecb5335d1bd
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52497388"
---
# <a name="install-published-application---streamsets-data-collector"></a>Installer une application publiée - StreamSets Data Collector

Cet article explique comment installer et exécuter l’application [Apache Hadoop publiée StreamSets](https://streamsets.com/) [Data Collector pour HDInsight](https://hadoop.apache.org/) sur Azure HDInsight. Pour obtenir une vue d’ensemble de la plateforme d’application HDInsight et la liste des applications publiées d’éditeurs de logiciels indépendants (ISV), consultez [Installer des applications Apache Hadoop tierces sur Azure HDInsight](hdinsight-apps-install-applications.md). Pour des instructions d’installation de votre propre application, consultez la page [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md).

## <a name="about-streamsets-data-collector"></a>À propos de StreamSets Data Collector

StreamSets Data Collector se déploie sur une application Azure HDInsight. StreamSets Data Collector offre un environnement de développement intégré complet qui vous permet de concevoir, tester, déployer et gérer des pipelines d’ingestion universels. Ces pipelines maillent les flux et groupent les données, tout en incluant diverses transformations intégrées au flux, sans aucune écriture de code personnalisé.

StreamSets Data Collector vous permet de générer des flux de données à l’aide de nombreux composants Big Data comme [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html), [Apache Kafka](https://kafka.apache.org/), [Apache Solr](https://lucene.apache.org/solr/), [ Apache Hive](https://hive.apache.org/), [Apache HBase](https://hbase.apache.org/) et [Apache Kudu](https://kudu.apache.org/). Une fois que StreamSets Data Collector est en cours d’exécution sur le serveur Edge ou dans votre cluster Hadoop, vous bénéficiez d’une surveillance en temps réel des anomalies de données et des opérations sur les flux de données. Cette surveillance comprend les alertes basées sur des seuils, la détection des anomalies et la correction automatique des enregistrements d’erreur.

StreamSets Data Collector est conçu pour isoler de manière logique chacune des étapes dans un pipeline, ce qui vous permet de satisfaire les nouvelles exigences métiers en ajoutant de nouveaux processeurs et connecteurs sans codage supplémentaires, et avec un temps d’arrêt minimal.

### <a name="streamsets-resource-links"></a>Liens vers les ressources StreamSets

* [Documentation](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html)
* [Blog](https://streamsets.com/blog/)
* [Didacticiels](https://github.com/streamsets/tutorials)
* [Forum Developer Support](https://groups.google.com/a/streamsets.com/forum/#!forum/sdc-user)
* [Canal Slack public StreamSets](https://streamsetters.slack.com/)
* [Code source](https://github.com/streamsets)

## <a name="prerequisites"></a>Prérequis

Pour installer cette application sur un cluster existant ou sur un nouveau cluster HDInsight, la configuration suivante est nécessaire :

* Niveau(x) de cluster : Standard ou Premium
* Version(s) de cluster : 3.5 ou ultérieures

## <a name="install-the-streamsets-data-collector-published-application"></a>Installer l’application publiée StreamSets Data Collector

Pour obtenir des instructions détaillées sur l’installation de cette application et d’autres applications ISV disponibles, consultez [Installer des applications Apache Hadoop tierces sur Azure HDInsight](hdinsight-apps-install-applications.md).

## <a name="launch-streamsets-data-collector"></a>Lancer Streams Data Collector

1. Après l’installation, vous pouvez lancer StreamSets à partir de votre cluster dans le portail Azure en accédant au volet **Paramètres**, puis en cliquant sur **Applications** sous la catégorie **Général**. Le volet Applications installées répertorie les applications installées.

    ![Applications StreamSets installées](./media/hdinsight-apps-install-streamsets/streamsets.png)

2. Quand vous sélectionnez StreamSets Data Collector, un lien vers la page web apparaît, ainsi que le chemin du point de terminaison SSH. Sélectionnez le lien WEBPAGE (page web).

3. Dans la boîte de dialogue de connexion, utilisez les informations d’identification suivantes pour vous connecter : `admin` et `admin`.

4. Dans la page de prise en main, cliquez sur **Créer un pipeline**.

    ![Créer un pipeline](./media/hdinsight-apps-install-streamsets/get-started.png)

5. Dans la fenêtre Nouveau pipeline, saisissez un nom pour le pipeline (« Hello World »), entrez éventuellement une description, puis sélectionnez **Enregistrer**.

6. La console Data Collector s’affiche. Le panneau Propriétés affiche les propriétés du pipeline.
 
    ![Console Data Collector](./media/hdinsight-apps-install-streamsets/pipeline-canvas.png)

7. Vous êtes maintenant prêt à suivre le [didacticiel StreamSets](https://streamsets.com/documentation/datacollector/latest/help/#Tutorial/Tutorial-title.html). Il vous indique une procédure pas à pas pour la création de votre premier pipeline.

## <a name="next-steps"></a>Étapes suivantes

* [Documentation StreamSets Data Collector](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html#concept_htw_ghg_jq).
* [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md) : découvrez comment déployer des applications HDInsight inédites vers HDInsight.
* [Publier des applications HDInsight dans la Place de marché Azure](hdinsight-apps-publish-applications.md): découvrez comment publier vos applications HDInsight personnalisées sur la Place de marché Azure.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN : installer une application HDInsight) : découvrez comment définir des applications HDInsight.
* [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md) : apprenez à utiliser l’action de script pour installer des applications supplémentaires.
* [Utiliser les nœuds de périphérie vides sur les clusters Hadoop dans HDInsight](hdinsight-apps-use-edge-node.md) : apprenez à utiliser un nœud de périphérie vide pour accéder aux clusters HDInsight, ainsi que pour tester et héberger des applications HDInsight.
