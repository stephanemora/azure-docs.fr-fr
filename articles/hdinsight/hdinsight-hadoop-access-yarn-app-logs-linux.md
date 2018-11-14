---
title: Accéder aux journaux des applications Apache Hadoop YARN dans HDInsight basé sur Linux - Azure
description: Découvrez comment accéder aux journaux des applications YARN sur un cluster HDInsight sous Linux (Apache Hadoop) à l’aide de la ligne de commande et d’un navigateur web.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.openlocfilehash: 302f2f96a7f17699411ab9fdbdb6ab1f9de149c8
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277594"
---
# <a name="access-apache-yarn-application-logs-on-linux-based-hdinsight"></a>Accéder aux journaux des applications Apache YARN dans HDInsight sous Linux

Découvrez comment accéder aux journaux des applications Apache YARN (Yet Another Resource Negotiator) sur un cluster Apache Hadoop dans Azure HDInsight.

> [!IMPORTANT]
> Les étapes décrites dans ce document nécessitent un cluster HDInsight utilisant Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.6 ou supérieure. Pour plus d’informations, consultez [Contrôle de version des composants HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="YARNTimelineServer"></a>YARN Timeline Server

[Apache YARN Timeline Server](http://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) fournit des informations génériques sur les applications terminées

YARN Timeline Server inclut le type de données suivant :

* ID d’application, identificateur unique d’une application
* Utilisateur ayant démarré l’application
* Informations sur les tentatives effectuées afin de terminer l’application
* Conteneurs utilisés par toute tentative d’application donnée

## <a name="YARNAppsAndLogs"></a>Applications et journaux YARN

YARN (Yet Another Resource Negotiator) prend en charge plusieurs modèles de programmation (dont MapReduce) en séparant la gestion des ressources de la planification et de l’analyse des applications. YARN utilise les éléments suivant : *ResourceManager* (RM) global, *NodeManagers* (NM) par nœud worker et *ApplicationMasters* (AM) par application. Le maître d'application, propre à chaque application, négocie les ressources nécessaires (processeur, mémoire, disque, réseau) pour exécuter l'application avec le gestionnaire de ressources. Le gestionnaire de ressources fonctionne avec les gestionnaires de nœuds pour octroyer ces ressources sous forme de *conteneurs*. Le maître d'application est chargé de suivre la progression des conteneurs qui lui sont assignés par le gestionnaire de ressources. Selon la nature de l'application, celle-ci peut nécessiter de nombreux conteneurs.

Chaque application peut comporter plusieurs *tentatives d’application*. Si une application échoue, vous pouvez la relancer. Il s’agit alors d’une nouvelle tentative. Chaque tentative est exécutée dans un conteneur. D’une certaine manière, un conteneur fournit le contexte pour l’unité de base du travail effectué par une application YARN. Tout le travail réalisé dans le contexte d’un conteneur est effectué sur l’unique nœud Worker auquel le conteneur a été alloué. Pour plus d’informations, consultez la rubrique [Concepts relatifs à YARN][YARN-concepts].

Les journaux des applications (et les journaux des conteneurs associés) sont essentiels pour déboguer des applications Hadoop problématiques. La fonctionnalité [Agrégation de journaux][log-aggregation] de YARN fournit une infrastructure adaptée à la collecte, à l’agrégation et au stockage des journaux des applications. La fonction d’agrégation de journaux rend l’accès aux journaux des applications plus déterministe. Il regroupe les journaux de tous les conteneurs sur un nœud Worker et les stocke dans un fichier journal agrégé par nœud Worker. Le journal est stocké sur le système de fichiers par défaut après la fin d’une application. Votre application peut utiliser des centaines voire des milliers de conteneurs, mais les journaux de tous les conteneurs exécutés sur un nœud worker unique sont toujours regroupés dans un fichier unique. Cela se traduit par l’existence d’un seul fichier journal par nœud worker utilisé par votre application. L’agrégation de journaux est activée par défaut sur les clusters HDInsight version 3.0 et versions ultérieures. Les journaux agrégés sont situés dans le stockage par défaut pour le cluster. Le chemin d’accès suivant est le chemin d’accès HDFS pour les journaux :

    /app-logs/<user>/logs/<applicationId>

Dans le chemin d’accès, `user` est le nom de l’utilisateur qui a démarré l’application. Le `applicationId` est l’identificateur unique affecté à une application par le Gestionnaire de ressources YARN.

Les journaux agrégés ne sont pas lisibles directement, car ils sont écrits dans un [TFile][T-file], un [format binaire][binary-format] indexé par conteneur. Utilisez les journaux ResourceManager YARN ou les outils CLI pour afficher ces journaux au format texte brut pour les applications ou les conteneurs qui présentent un intérêt.

## <a name="yarn-cli-tools"></a>Outils de l’interface de ligne de commande YARN

Pour utiliser les outils de l’interface de ligne de commande YARN, vous devez d’abord vous connecter au cluster HDInsight en utilisant le protocole SSH. Pour plus d’informations, consultez [Utiliser SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Vous pouvez afficher ces journaux en texte brut en exécutant l’une des commandes suivantes :

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Définissez les valeurs &lt;applicationId>, &lt;user-who-started-the-application>, &lt;containerId>, et &lt;worker-node-address> lorsque vous exécutez ces commandes.

## <a name="yarn-resourcemanager-ui"></a>Interface utilisateur de ResourceManager YARN

L’interface utilisateur ResourceManager YARN s’exécute sur le nœud principal du cluster. Il est accessible via l’interface utilisateur web d’Ambari. Procédez comme suit pour afficher les journaux YARN :

1. Dans votre navigateur web, accédez à https://CLUSTERNAME.azurehdinsight.net. Remplacez CLUSTERNAME par le nom de votre cluster HDInsight.
2. Dans la liste des services sur la gauche, sélectionnez **YARN**.

    ![Service YARN sélectionné](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. Dans la liste déroulante **Liens rapides**, sélectionnez un des nœuds principaux du cluster, puis **Journal ResourceManager**.

    ![Liens rapides YARN](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    Une liste de liens menant vers les journaux YARN s’affiche.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
